# Redeeming a Lightrail Value at Checkout

## Introduction

This document is a quick step-by-step guide to processing Lightrail value redemption (Gift Cards or Account Credits) at the order checkout. The focus will be on implementing this use-case by programmatically calling the Lightrail API. 

- If you would like to learn more about Lightrail concepts, check out the section on Lightrail Object Model in the [Lightrail API Docs](https://www.lightrail.com/docs/).
- If you are looking for an implementation of this use-case in a specific language or technology, check out the section on Client Libraries and Integrations in the [Lightrail API Docs](https://www.lightrail.com/docs/).

## Use-Case

Redemption at the checkout is perhaps the most common use-case for Gift Cards and Account Credits. After creating an order and getting to the checkout page, the customer can choose to redeem the value of a Gift Card or Account towards fully or partially paying the order balance.

For Gift Cards, the common flow is to provide a space for the customer to enter the Gift Card's `fullcode` at the checkout page and redeeming its value towards paying for the order. For Account Credits, usually the account's available balance is shown and the customer gets to choose how much of the order they would like to pay by redeeming value from their account.   

Once the order is confirmed the Gift Card or the Account is charged and any remainder is paid using a third-party payment method such as a credit card.

We discuss the flow based on Gift Cards first; the flow for Account Credits is very similar and will be briefly discussed [afterwards](#using-account-credits). If you only need to implement one of these cases, feel free to jump to the respective section.

## High-Level Flow

To implement this use-case you need to follow these high-level steps: 

- Capture the Gift Card by its `fullcode` or determine `cardId` of the Account Card.
- Determine the value of the Gift Card or Account Card.
- Determine the Card's share in paying for the order and whether or not there is any remainder to be charged to another payment method.
- If the Card's share covers the whole order, charge the Card and complete the order payment, otherwise, follow an _preauthorize-capture_ protocol: 
  - Post a _pending_ charge on the Card.
  - Attempt to charge the third-party payment method for the remainder. 
  - If the third-party payment is successful, _capture_ the pending transaction on the gift code and complete the order payment. If the third-party payment fails, _void_ the pending transaction on the Card and declare that the order payment has failed.

## Detailed Flow

The API calls for handling this use-case are discussed below. More details for each API endpoint and sample requests/responses are provided in the next section. 

**Step 1:** Determine `orderTotal` and `orderCurrency` for the order at hand and collect the Gift Card's `fullcode` from the customer.

**Step 2:** Use the [Code Balance API endpoint](#code-balance-endpoint) to determine the available value of the Gift Card; call it `giftValue`. Based on the value of `giftValue`, determine whether there is any remainder to be charged to a third-party payment method:

`remainder = orderBalance - giftValue`

**Step 3:** If `remainder <= 0` , the entire order can be paid by the Gift Card, so use the [Code Transaction API endpoint](#code-transaction-endpoint) to charge the Gift Card for the amount of `orderTotal`. If this transaction completes successfully, mark the order as completed, and optionally record the transaction details such the `transactionId` for future reference. If the transaction fails, the checkout is not successful. 

If `remainder > 0`, the gift code value is not sufficient to pay for the entire order, so the Gift Card pays only for part of the order and the `remainder` must be paid another payment method vie the following steps:

- Use the [Code Transaction API endpoint](#code-transaction-endpoint) to create a `pending` drawdown transaction on the Gift Card with the value of `giftValue`. Save the `transactionId` and the `cardId` from the response object.


- Attempt to charge the third-party payment method with `remainder`.
- If the the third-party payment is successful, use the [Card Transaction API endpoint](#card-transaction-endpoint) to _capture_ the pending transaction on the Gift Card. You will need the `cardId` and the original `transactionId` from the response to the pending transaction in this step.
- If the third-party payment fails, use the [Card Transaction API endpoint](#card-transaction-endpoint) to _void_ the pending transaction on the Gift Card. You need to provide the `cardId` and the original `transactionId` from the response to the pending transaction in this step.

The following sequence diagram summarizes these steps.

![redemption-seq-diagram](https://giftbit.github.io/Lightrail-API-Docs/assets/redemption-seq-diagram.svg)

### Errors and Edge Cases

**Step 2:**  When checking the value of the Gift Card, read its `state`, and its `currency` from the response object and prompt the user with an error if:

- The `state` is not `ACTIVE`, 
- The currency does not match `orderCurrency`, or
- its available value is zero.

**Step 3:** When the Gift Card value is not sufficient to cover the entire order, the share of the Gift Card is usually the maximum it can contribute, i.e. its available balance. However, since some third-party payment methods have a restriction on the minimum transaction value, if the remainder is too small, the third-party payment will fail. In the edge case that the remainder is too small to meet the minimum-transaction-value requirements of the third-party payment method, you need to adjust the split a little bit to make sure the remainder is payable by the third-party payment method. Here is a code snippet based on our [Stripe Integration](https://github.com/Giftbit/lightrail-stripe-java) for handling this logic:

```php
$giftCardShare = min($orderBalance, $giftValue);
$stripeShare = $orderTotal - $giftCardShare;
if ($stripeShare > 0 && $stripeShare < STRIPE_MINIMUM_TRANSACTION_VALUE) {
  $differential = STRIPE_MINIMUM_TRANSACTION_VALUE - $stripeShare;
  $giftCardShare -= $differential;
  if ($giftCardShare < 0)
    throw new Exception("The gift card value is too small for this transaction");
}
```

Note that there is a narrower edge case where the Gift Card value is too small to pay for the entire order, but also the order total is so small that the remaining balance after taking out the Gift Card value will be too small for the third-party transaction. In these rare cases, the customer should be notified that the Gift Card value is too small to be spent towards that particular transaction.

## Using Account Credits

Account credits are values associated with a customer modelled by the Lightrail concept of Account Card. The customer usually earns reward values based on different criteria and subsequently gets to redeem these values in the checkout process. To learn more about Account Cards check out the Lightrail Object Model in our [API Docs](https://www.lightrail.com/docs/). For more details on the account credits use-cases, check out our Account Credits [Implementation Guide](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/account-credits.md).

### High-Level Flow

The high-level flow for redeeming a value from a customer's account credits is very similar to the gift-code flow discussed above. 

- On the order checkout page, the balance of the customer's account is displayed. The customer is provided with the option to decide whether or not they would like to redeem their account balance towards paying for the order. 
- Additionally, sometimes the customer is also given the option to select how much of the balance they would like to spend towards the order. For example, if the customer has accrued $25 worth of rewards in their account, they may be given the choice of only using $10 of it for paying towards the order at hand.
- Once the share of the account credit in the payment is determined, this value is charged to the account. If there is any remainder, charging the account should take place by following the _preauthorize-capture_ protocol: 
  - Post a _pending_ charge to the account.
  - Attempt to charge the remainder on a third-party payment method. 
  - If the third-party payment was successful _capture_ the pending transaction on the account, otherwise _void_ the pending transaction.

### Detailed Flow

**Step 1:** Determine `orderTotal` and `orderCurrency` for the order at hand and determine the Lightrail `contactId` corresponding to the the customer. Normally, the customer must be logged in for this to be possible.

**Step 2:** Use the [Card Search API endpoint](#card-search-endpoint) to retrieve the `cardId` of the customer's Account Card for the `orderCurrency`. Note that since account credits are currency-specific, if your system supports multiple currencies, you need to find the Account Card that corresponds to the currency of the order. If there are no account cards for that currency, then the customer has no account value compatible to the order at hand. 

**Step 3:** Use the [Card Balance API endpoint](#card-balance-endpoint) to determine the available balance of the account.

**Step 4:** Determine the `accountShare` either automatically or by giving the customer the choice to decide how much from their account they would like to spend. Based on the value of `accountShare`, determine whether there is any remainder to be charged to a third-party payment method:

`remainder = orderBalance - accountShare`

**Step 5:** If `remainder == 0` , the entire order can be paid by the account, so use the [Card Transaction API endpoint](#card-transaction-endpoint) to charge the account Card with `orderBalance`. If this transaction completes successfully, mark the order as completed, and optionally record the transaction details such the `transactionId` for future reference. If the transaction fails, the checkout is not successful. 

If `remainder > 0`, proceed with the following steps: 

- Use the [Card Transaction API endpoint](#card-transaction-endpoint) to create a `pending` transaction on the Account Card with the value of `accountShare`. Save the `transactionId` from the response object.
- Attempt to charge the third-party payment method for the value of `remainder`.
- If the the third-party payment is successful, use the [Card Transaction API endpoint](#card-transaction-endpoint) to _capture_ the pending transaction. You need to provide the Account Card's `cardId` and the original `transactionId` from the response to the pending transaction in this step.
- If the third-party payment fails, use the [Card Transaction API endpoint](#card-transaction-endpoint) to _void_ the pending transaction. You need to provide the Account Card's `cardId` and the original `transactionId` from the response to the pending transaction in this step.

## API Endpoints

This section provides a brief overview of the API calls necessary for this use-case with example requests and responses. For a complete reference and further details about the endpoints, check out the [Lightrail API docs](https://www.lightrail.com/docs/).

### Card Balance Endpoint

This endpoint provides a detailed breakdown of a Card's balance, as well as its `currency` and `state`, i.e. whether it is active. You will need the Card's `cardId` for this call. For an Account Card, if you do not have the `cardId` you can retrieve it based on the customer's `contactId` using the [Card Search Endpoint](#card-search-endpoint), as discussed later below. For Gift Cards, you can get the balance directly based on the `fullcode` from the similar [Code Balance Endpoint](#code-balance-endpoint) discussed below.

Usually, the balance of the Card is returned in its `principal` value store, but if you are using attached promotions, these additional values are returned in the `attached` objects in the response. To read more about attached promotions check out [Lightrail API docs](https://www.lightrail.com/docs/) and the [Implementation Guide](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/promotions.md) for creating promotions. 

The effective value of a Card is the value of its `principal` value store, plus the sum of all active `attached` promotions. So, in order to get the effective available value of a Card  you need to use the following logic:

```php
$cardValue = $response['balance']['principal']['currentValue'];
foreach ($response['balance']['attached'] as $attachedValue) {
  if ($attachedValue['state'] === 'ACTIVE')
  	$cardValue += $attachedValue['currentValue'];
}
```

Request:

```json
GET https://api.lightrail.com/v1/cards/{cardId}/balance
```

Response sample:

```json
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

### Code Balance Endpoint

You can use the code balance endpoint to check the available value of a Gift Card by providing its `fullcode`. 

```json
GET https://api.lightrail.com/v1/codes/{fullcode}/card/balance
```

The response from this endpoint and the logic required for computing the effective available balance is similar to what is discussed above.

### Card Transaction Endpoint

#### Posting a New Transaction

This endpoint enables posting a transaction to withdraw some value from a Card based on its `cardId`. The value should be expressed as a negative integer, representing the amount in the smallest unit for the currency, e.g. cents.

For Account Cards, if you do not have the `cardId` you can retrieve it by based on the customer's `contactId` using the [Card Search Endpoint](#card-search-endpoint). For Gift Cards, you can use [a similar endpoint](#code-transaction-endpoint) which enables posting transactions directly based on `fullcode` without retrieving the `cardId`.

The `userSuppliedId` is a per-endpoint unique identifier, used to ensure idempotency. Ensuring idempotency means that if the same request is issued more than once, it will not result in repeated actions on the server. For further discussions on `userSuppliedId`, see [Lightrail API docs](https://www.lightrail.com/docs/). For this use-case, we recommend that you use the unique order ID from your system as the transaction `userSuppliedId`. 

If `pending` is set to `true` in the request body, the transaction will be considered a pre-authorized pending transaction and will have to be _captured_ or _voided_ later. Save the `cardId` and `transactionId` from the response of a pending transaction as you will need them for [capturing or voiding](#voiding-or-capturing-a-pending-transaction) the pending transaction later.

Request Sample:

```json
POST https://api.lightrail.com/v1/cards/{cardId}/transactions
{
  "currency": "USD",
  "value": -101
  "pending": true,
  "userSuppliedId": "07xx71",
}
```

Response Sample:

```json
{
   "transaction": {
   "transactionId": "transaction-b4",
     "value": -101,
     "userSuppliedId": "07xx71",
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
#### Code Transaction Endpoint

This endpoint enables posting a transaction to withdraw some value from a Gift Card based on its `fullcode`. The request and response objects are identical to that of the [Card Transaction Endpoint](#posting-a-new-transaction).

Request Sample:
```json
POST https://api.lightrail.com/v1/codes/{fullcode}/transactions
{
  "currency": "USD",
  "value" : -101,
  "pending": true,
  "userSuppliedId": "07xx71d"
}
```

#### Voiding or Capturing a Pending Transaction

After posting a `pending` transaction, you can use this endpoint to either capture or void it. Note that in order to call this endpoint you will need the `cardId` (of the Account Card or Gift Card) and the `transactionId` of the original pending transaction. These are are provided in the response object from the original call to create the pending transaction.

Request Samples:

```json
POST https://api.lightrail.com/v1/cards/{cardId}/transactions/{transactionId}/void
{
  "userSuppliedId":"tx0771",
}
```

```json
POST https://api.lightrail.com/v1/cards/{cardId}/transactions/{transactionId}/capture
{
  "userSuppliedId":"tx0771",
}
```

Response Sample:

```json
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
     "codeLastFour": "7KKT",
     "metadata": {
       "giftbit_initial_transaction_id": "transaction-46"
     }
   }
}
```

### Card Search Endpoint

You can call the this endpoint with search parameters to retrieve the account Card belonging to a Contact for a particular currency:

```json
GET https://api.lightrail.com/v1/cards?cardType=ACCOUNT_CARD&currency={currency}&contactId={contactId}
```

The response is in the form of search results which includes an array of `card` objects. Note that since each Contact can only one Account Card per currency, it is guaranteed that there will be at most one `card` object in the results. Here is a sample response: 

```json
{
 "cards":[
  {
    "cardId": "card-1dea",
    "userSuppliedId": "account-d37e",
    "contactId": "contact-271a",
    "dateCreated": "2017-07-26T23:50:04.572Z",
    "cardType": "ACCOUNT_CARD",
    "currency": "USD"
    "categories":[
     {
      "categoryId": "category-bdxx88",
      "key": "giftbit_order",
      "value": "2017-07-26"
     },
     {
      "categoryId": "category-95xxc2",
      "key": "giftbit_program",
      "value": "program-account-USD-user-088e-TEST"
     }
    ]
  }
 ],
 "pagination":{
   "count": 1,
   "limit": 100,
   "maxLimit": 1000,
   "offset": 0,
   "totalCount": 1
 }
}
```
