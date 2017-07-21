# Redeeming a Lightrail Gift Code at Checkout

## Introduction

This document is a quick guide to adding gift code redemption at the order checkout. The focus of this document is implementing this use by directly calling the Lightrail API. If you are looking for client libraries specific to a language or technology, check out our [integration projects](https://github.com/Giftbit/Lightrail-API-Docs/blob/usecases/Integrations.md).

## Use-Case

Redemption at the checkout is perhaps the most common use-case for a gift code. After creating an order, a customer who has previously received a gift code can redeem it at the checkout so that the value of the gift code can pay for part, or all of the order balance.

The common flow for this is to provide the customer with a space to enter the gift code at the checkout page and determine the value of the gift code. Once the order is confirmed the gift code is charged up to its full value and any remainder is paid using a third-party payment method such as a credit card.
## High-Level Flow

Based on this, the high-level steps to implement this use-case are as follows:

- Determine the value of the gift code.
- Determine the gift code's share in paying for the order and whether or not there is any remainder to be charged to another payment method.
- Charge the gift code and complete the order payment if the gift code has enough value to cover the whole order.
- Otherwise, follow an auth-capture protocol: first post a pending charge on the gift code, then attempt to charge the third-party payment method for the remainder. If the third-party payment is successful, collect the pending transaction on the gift code and complete the order payment. If the third-party payment fails, cancel the pending transaction on the gift code and declare failure for the order payment.

## Detailed Flow

The detailed logic and API calls for handling this use-case are discussed below. More details for each API endpoint and sample requests/responses are provided in the next section but for a complete reference and further details, check out  [Lightrail API docs](https://www.lightrail.com/docs/).

**Step 1:** Determine `orderBalance` and `orderCurrency` for the order at hand and collect the `giftCode` from the customer.

**Step 2:** Use the [Balance API endpoint](#balance-endpoint) to determine the available value of the code (`giftCodeValue`).

**Step 3:** If `giftCodeValue >= orderBalance` , meaning the gift code value is enough to pay for the entire order, use the [Code Transaction API endpoint](#code-transaction-endpoint)  to charge the code with `orderBalance`. If this transaction completes successfully, mark the order as completed, and optionally record the transaction details such the `transactionId` for future reference. If there is an error with this transaction, notify the customer that the checkout was not successful. 

**Step 4:** If `giftCodeValue < orderBalance`, meaning the gift code value is not enough to pay for the entire order, the gift code will pay for part of the balance (`giftCodeShare`) and the remainder (i.e. `orderBalance - giftCodeShare`) will be paid via another payment method. Usually, `giftCodeShare` is the value of the gift code, i.e. the maximum it can pay.

After determining the `giftCodeShare`, proceed with the following steps: 

- Use the [Code Transaction API endpoint](#code-transaction-endpoint) to create a `pending` charge on the gift code with the value of `giftCodeShare`. Save the `transactionId` and the `cardId` from the response object.
- Attempt to charge the third-party payment method with the remainder of the order balance, i.e.`orderBalance - giftCodeShare`.
- If the the third-party payment is successful, use the [Card Transaction API endpoint](#card-transaction-endpoint) to capture the pending charge on the gift code. You need to provide the `cardId` and the original `transactionId` from the response to the pending transaction in this step.
- If the third-party payment fails, use the [Card Transaction API endpoint](#card-transaction-endpoint) to cancel the pending charge on the gift code.  You need to provide the `cardId` and the original `transactionId` from the response to the pending transaction in this step.

The following sequence diagram summarizes these steps.

![redemption-seq-diagram](https://giftbit.github.io/Lightrail-API-Docs/use-cases/assets/redemption-seq-diagram.svg)

### Errors and Edge Cases

**Step 2:**  At the time of checking the value of the gift code, read its `state`, and its `currency` from the response object and prompt the user with an error if:

- The gift code `state` is not `ACTIVE`, 
- its currency does not match `orderCurrency`, or
- its available value is zero.

**Step 3:** For most transactions, when the gift code value is not enough to pay for the entire order, the `giftCodeShare` is its value, i.e. the maximum it can pay. However, since some third-party payment methods have a restriction on the minimum transaction value, if the remainder is too small, it will lead to a failure in the third-party payment. In the edge case that the remainder is too small and would not meet the minimum-transaction-value requirements of the third-party payment method, you need to adjust the split a little bit to make sure the remainder is payable by the third-party payment method. Here is a code snippet based on our [Stripe Integration](https://github.com/Giftbit/lightrail-stripe-java) for handling this logic:

```Php
$giftCodeShare = min($orderBalance, $giftCodeValue);
$stripeShare = $orderBalance - $giftCodeShare;
if ($stripeShare > 0 && $stripeShare < STRIPE_MINIMUM_TRANSACTION_VALUE) {
  $differential = STRIPE_MINIMUM_TRANSACTION_VALUE - stripeShare;
  $giftCodeShare -= $differential;
  if ($giftCodeShare < 0)
    throw new Exception("The gift code value is too small for this transaction");
}
```

Note that there is a narrower edge case where the gift code value is too small to pay for the entire order, but also the order balance is so small that the remaining balance after taking out the gift code value will be too small for the third-party transaction. In these rare cases, the customer should be notified that the gift code value is too small for that transaction.

## API Endpoints

### Balance Endpoint

This endpoint provides a detailed breakdown of the gift code balance, as well as its currency and state, i.e. whether it is active. Usually, the balance of the gift code is returned in its `principal` value store, but if you are using gift codes with attached promotions (e.g. temporary promotional offers), these additional values are returned in the `attached` objects in the response. In such cases, the effective value of a gift code is the available value in its `principal` value store, plus the sum of all `attached` values that are currently active. So, in order to get the effective available value of a gift code you need to use the following logic:

```Php
$giftCodeValue = $response['balance']['principal']['currentValue'];
foreach ($response['balance']['attached'] as $attachedValue) {
  if ($attachedValue['state'] === 'ACTIVE')
  	$giftCodeValue += $attachedValue['currentValue'];
}
```

Request:

```json
GET https://api.lightrail.com/v1/codes/{giftCode}/card/balance
```

Response sample:

```JSON
{
    "balance": {
        "currency": "USD",
        "cardType": "GIFT_CARD",
        "balanceDate": "2017-07-11T00:40:52.104Z"
        "principal": {
            "currentValue": 541,
            "state": "ACTIVE",
            "expires": null,
            "startDate": null,
            "programId": "program-1d",
            "valueStoreId": "value-2f"
        },
        "attached": [
            {
                "currentValue": 500,
                "state": "ACTIVE",
                "expires": "2017-07-14T20:05:34.331Z",
                "startDate": null,
                "programId": "program-dd",
                "valueStoreId": "value-26"
            },
            {
                "currentValue": 0,
                "state": "ACTIVE",
                "expires": "2017-07-11T21:12:50.344Z",
                "startDate": null,
                "programId": "program-cc",
                "valueStoreId": "value-bb"
            }
        ]
    }
}
```

### Code Transaction Endpoint

This endpoint enables posting a transaction to withdraw some value from a gift code. The value should be expressed as an integer, representing the amount in the smallest unit for the currency, e.g. cents.

The `userSuppliedId` is a per-endpoint ID, used to ensure idempotence. Ensuring idempotence means that if the same request is issued more than once, it will not result in repeated actions. In the use-case redeeming a gift code at checkout, we recommend using the unique order ID from your system. For further details on this, see [Lightrail API docs](https://www.lightrail.com/docs/). 

If `pending` is set to `true` in the request body, the transaction will be considered a pre-authorized transaction, and will have to be captured or voided later. 

After receiving the response to a pending transaction, save the `cardId` and  `transactionId` from the response object; they will be needed later when trying to `capture` or `void` the pending transaction.

Request Sample:

```JSON
POST https://api.lightrail.com/v1/codes/{giftCode}/transactions
{
  "currency": "USD",
  "value": -101
  "pending": true,
  "userSuppliedId": "072f0701-0e68-4676-9472-c24a96f88571",
}
```

Response Sample:

```json
{
    "transaction": {
        "transactionId": "transaction-b4",
        "value": -101,
        "userSuppliedId": "072f0701-0e68-4676-9472-c24a96f88571",
        "dateCreated": "2017-07-10T23:36:28.078Z",
        "transactionType": "PENDING_CREATE",
        "transactionAccessMethod": "RAWCODE",
        "giftbitUserId": "user-08",
        "cardId": "card-6d",
        "currency": "USD",
        "codeLastFour": "7KKT"
    }
}
```

### Card Transaction Endpoint

After posting a `pending` transaction, you can use this endpoint to either capture or void it. Note that in order to call this endpoint you will need the `cardId` corresponding to the gift code and the `transactionId` for the original pending transaction which are provided in the response object from the original call to create the pending transaction.

Request Samples:

```JSON
POST https://api.lightrail.com/v1/cards/{cardId}/transactions/{transactionId}/void
{
  "userSuppliedId":"072f0701-0e68-4676-9472-c24a96f88571",
}
```

```JSON
POST https://api.lightrail.com/v1/cards/{cardId}/transactions/{transactionId}/capture
{
  "userSuppliedId":"072f0701-0e68-4676-9472-c24a96f88571",
}
```

Response Sample:

```JSON
{
    "transaction": {
        "transactionId": "transaction-a7",
        "value": 101,
        "userSuppliedId": "transaction-46-reverse",
        "dateCreated": "2017-07-12T00:22:48.379Z",
        "transactionType": "PENDING_VOID",
        "transactionAccessMethod": "CARDID",
        "giftbitUserId": "user-08",
        "cardId": "card-6d",
        "currency": "USD",
        "parentTransactionId": "transaction-46",
        "metadata": {
            "giftbit_initial_transaction_id": "transaction-46"
        },
        "codeLastFour": "7KKT"
    }
}

```

