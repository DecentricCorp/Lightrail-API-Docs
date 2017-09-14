# Redeeming Lightrail Value at Checkout

## Introduction

This document is a quick step-by-step guide to processing Lightrail value redemption (Gift Cards or Account Cards) at the order checkout. The focus will be on implementing this use-case by programmatically calling the Lightrail API. 

- To learn more about Lightrail concepts, check out the section on the Lightrail Object Model in the [Lightrail API Docs](https://www.lightrail.com/docs/). 
- If you are looking for an implementation of this use-case in a specific language or technology, check out the section on Client Libraries and Integrations in the [Lightrail API Docs](https://www.lightrail.com/docs/).

## Use-Case

Redemption at the checkout is perhaps the most common use-case for Gift Cards and Account Cards. After creating an order and getting to the checkout page, the customer can choose to redeem the value of an Account or Gift Card towards paying —fully or partially— the order balance.

For Gift Cards, the common flow is to provide a space for the customer to enter the Gift Card's `fullcode` at the checkout page. For Account Credits, usually the account's available balance is shown and the customer gets to choose how much of the order they would like to pay by redeeming value from their account.   

Once the order is confirmed the Account or Gift Card pays its share and any remainder is paid using a third-party payment method such as a credit card. This is known as a _split tender_.

We discuss the flow for processing a split tender at the checkout for Gift Cards first; the flow for Account Cards is very similar and will be briefly discussed [afterwards](#using-account-credits). If you only need to implement one of these cases, feel free to jump to the respective section. To learn more about Account Credits, check out the [Lightrail Account Credits Implementation Guide](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/account-credits.md)

## High-Level Flow For Gift Cards

To implement this use-case you need to follow these high-level steps: 

- Capture the `fullcode` by providing a space for the customer to enter it.
- Determine the metadata of the Transaction. If your system is using advanced promotions with [Redemption Rules](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/redemption-rules.md), identifying and including this metadata is very important for correct Transaction processing. 
- Call the API to determine the maximum available value of the Gift Card for this Transaction. Based on this, determine the Card's share in paying for the order and whether or not there is any remainder to be charged to another payment method.
- If the Card's share covers the whole order, charge the Card and complete the order payment, otherwise, follow a _preauthorize-capture_ protocol: 
  - Post a _pending_ charge on the Card.
  - Attempt to charge the third-party payment method for the remainder. 
  - If the third-party payment is successful, _capture_ the pending transaction on the gift code and complete the order payment. If the third-party payment fails, _void_ the pending transaction on the Card and declare that the order payment has failed.

## Detailed Flow

The API calls for handling this use-case are discussed below. More details for each API endpoint and sample requests/responses are provided in the next section. 

**Step 1:** Determine `orderTotal` and `orderCurrency` for the order at hand and collect the Gift Card's `fullcode` from the customer.

**Step 2:** Determine the `metadata` of the Transaction. If your system is using advanced promotions with Redemption Rules, check out the [Lightrail Redemption Rules Implementation Guide](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/redemption-rules.md) for a more detailed discussion of this metadata.

**Step 3:** Use the [Transaction Simulation API Endpoint](#transaction-simulation-and-balance-endpoint) to determine the maximum available value of the Gift Card; call it `giftValue`. Note that you need to provide the full `metadata` in order to get accurate results if you are using Redemption Rules. Based on the value of `giftValue`, determine whether there is any remainder to be charged to a third-party payment method:

`remainder = orderBalance - giftValue`

**Step 4:** If `remainder <= 0` , the entire order can be paid by the Gift Card, so use the [Code Transaction API endpoint](#code-transaction-endpoint) to create a drawdown Transaction on the Gift Card with the value of `orderTotal`. Note that you need to include the full `metadata` object in this request if you are using Redemption Rules. If this transaction completes successfully, mark the order as completed, and optionally record the transaction details such the `transactionId` for future reference. If this transaction fails, the checkout fails. 

If `remainder > 0`, the gift value is not enough to pay for the entire order, so the Gift Card pays as much as it can and the `remainder` must be paid by another payment method using the following steps:

- Use the [Code Transaction API endpoint](#code-transaction-endpoint) to create a `pending` drawdown Transaction on the Gift Card with the value of `giftValue`. Save the `transactionId` and the `cardId` from the response object.


- Attempt to charge `remainder` on the third-party payment method.
- If the the third-party payment succeeds, use the [Card Transaction API endpoint](#card-transaction-endpoint) to _capture_ the pending transaction on the Gift Card. You will need the `cardId` and the original `transactionId` from the response to the _pending_ transaction in this step. Note that you need to repeat the full `metadata` object in this request if you are using Redemption Rules. 
- If the third-party payment fails, use the [Card Transaction API endpoint](#card-transaction-endpoint) to _void_ the _pending_ transaction on the Gift Card. You need to provide the `cardId` and the original `transactionId` from the response to the pending transaction in this step. Notify the user that the checkout has failed.

The following sequence diagram summarizes these steps.

![redemption-seq-diagram](https://giftbit.github.io/Lightrail-API-Docs/assets/redemption-seq-diagram.svg)

### Errors and Edge Cases

**Step 3:**  When checking the value of the Gift Card using the simulate endpoint, prompt the user with an error message if the response is an error (e.g. the Card has expired) or the available value is zero.

**Step 4:** When the Gift Card value is not sufficient to cover the entire order, the share of the Gift Card is usually its maximum available value for the Transaction. However, some third-party payment methods have a restriction on their minimum transaction value, so, if the remainder is too small, the third-party payment will fail. In such edge cases, you need to adjust the split a little bit to make sure the remainder is just large enough to be payable by the third-party payment method. Here is a code snippet based on our [Stripe Integration](https://github.com/Giftbit/lightrail-stripe-java) for handling this logic:

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

Note that there is a narrower edge case where the Card value is too small to pay for the entire order, but also the order total is so small that the remaining balance after taking out any value from the Card will be too small for the third-party transaction. In these rare cases, the customer should be notified that the Card value is too small to be spent in that particular Transaction.

## Using Account Credits

Account credits are values associated with a customer modelled by the Lightrail concept of Account Card. The customer usually earns reward values based on different criteria and subsequently gets to redeem these values in the checkout process. To learn more about Account Cards check out the Lightrail Object Model in our [API Docs](https://www.lightrail.com/docs/). For more details on the account credits use-cases, check out the [Lightrail Account Credits Implementation Guide](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/account-credits.md).

### High-Level Flow

The high-level flow for redeeming a value from a customer's Account Card is very similar to the gift-code flow discussed above. 

- Determine the metadata of the Transaction. If your system is using advanced promotions with [Redemption Rules](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/redemption-rules.md), identifying and including this metadata is very important for correct Transaction processing. 

- Check the available value of the customer's Account Card and display it on the checkout page. Provide the customer with the option to decide  how much of their available credit they would like to use towards paying for the order. For example, if the customer has $25 worth of rewards in their account available for this Transaction, they can choose to only use $10 of it for paying towards the order at hand.

- Once the share of the Account Card is determined, if it covers the entire order balance, create a drawdown Transaction on the Card for this value and mark it as complete if this Transaction is successful. 

  If the Card share is less than the order balance, the remainder will have to be paid by a third-party payment method. Follow the _preauthorize-capture_ protocol as the following: 

  - Create a _pending_ transaction on the Account Card.
  - Attempt to charge the remaining value on a third-party payment method. 
  - If the third-party payment was successful _capture_ the pending Transaction on the Account Card; otherwise, _void_ that pending Transaction.

### Detailed Flow

**Step 1:** Determine `orderTotal` and `orderCurrency` for the order at hand and determine the Lightrail `contactId` corresponding to the the customer. Normally, the customer must be logged in for this to be possible.

**Step 2:** Use the [Card Search API endpoint](#card-search-endpoint) to retrieve the `cardId` of the customer's Account Card for the `orderCurrency`. Note that since account credits are currency-specific, if your system supports multiple currencies, you need to find the Account Card that corresponds to the currency of the order. If there are no account cards for that currency, then the customer has no account value compatible to the order at hand. 

**Step 3:** Determine the `metadata` of the Transaction. If your system is using advanced promotions with Redemption Rules, check out the [Lightrail Redemption Rules Implementation Guide](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/redemption-rules.md) for a more detailed discussion of this metadata.

**Step 4:** Use the [Transaction Simulation API Endpoint](#transaction-simulation-and-balance-endpoint) to determine the maximum available value of the Account Card for this Transaction. Note that you need to provide the full `metadata` in order to get accurate results if you are using Redemption Rules.

**Step 5:** Determine the `accountShare` either automatically or by giving the customer the choice to decide how much from their account they would like to spend. Based on the value of `accountShare`, determine whether there is any remainder to be charged to a third-party payment method:

`remainder = orderBalance - accountShare`

**Step 6:** If `remainder == 0` , the entire order can be paid by the Account Card, so, use the [Card Transaction API endpoint](#card-transaction-endpoint) to post a Transaction on the account Card for the value of `orderBalance`. Note that you need to repeat the full `metadata` object in this request if you are using Redemption Rules. If this Transaction succeeds, mark the order as completed and optionally record the transaction details such the `transactionId` for reference. If this Transaction fails, the checkout fails. 

If `remainder > 0`, proceed with the following steps: 

- Use the [Card Transaction API endpoint](#card-transaction-endpoint) to create a _pending_ transaction on the Account Card for the value of `accountShare`. Save the `transactionId` from the response object.
- Attempt to charge the third-party payment method for the value of `remainder`.
- If the the third-party payment is successful, use the [Card Transaction API endpoint](#card-transaction-endpoint) to _capture_ the pending Transaction. You need to provide the Account Card's `cardId` and the original `transactionId` from the response to the _pending_ Transaction in this step. Note that you need to repeat the full `metadata` object in this request if you are using Redemption Rules. 
- If the third-party payment fails, use the [Card Transaction API endpoint](#card-transaction-endpoint) to _void_ the _pending_ Transaction. You need to provide the Account Card's `cardId` and the original `transactionId` from the response to the _pending_ Transaction in this step.

## API Endpoints

This section provides a brief overview of the API calls necessary for this use-case with example requests and responses. For a complete reference and further details about the endpoints, check out the [Lightrail API docs](https://www.lightrail.com/docs/).

### Transaction Simulation and Balance Endpoint

This endpoint allows you to do a test-run for a Transaction by providing a request (including full `metadata` if you are using Redemption Rules). In the case of insufficient funds, it can also tell you the maximum value for which the Transaction _would be_ successful. For example, if you simulate a $35 drawdown Transaction, the endpoint can tell you that it _would be_ successful if it were only for $20. This provides a mechanism to check the available value of a Card for a particular Transaction context.

For this endpoint, you will need to provide the Card's `cardId`. For Account Cards, if you do not have the `cardId` you can retrieve it based on the customer's `contactId` using the [Card Search Endpoint](#card-search-endpoint), as discussed later below. For Gift Cards, you can use [a similar endpoint](#simulation-and-balance-check-based-on-gift-code) which takes the `fullcode` as discussed below.

In the request, you need to provide, the following: 

- The Transaction `value` you would like to drawdown.
- The Transaction `currency`.
- A `userSuppliedId` as an idempotency key. Note that since this is a simulation, if a Transaction with this `userSuppliedId` already exists, Lightrail will retrieve that.  
- The attribute `nsf` set to `false`. This tells Lightrail to return a best-effort would-be Transaction instead of a not-sufficient-funds (NSF) error, if the Card's available value is not enough for the suggested Transaction value. 

Request Sample:

```javascript
POST https://www.lightrail.com/v1/cards/{cardId}/transactions/dryRun
{
  "userSuppliedId":"order-s3xx30",
  "value":-6960,
  "currency":"USD",
  "nsf": false,
  "metadata": {
    "cart": {
      "total": 6960,
      "items": [
        {
          "id": "B009L1MF7A",
          "quantity": 3,
          "unit_price": 2320,
          "tags": ["apparel", "outdoor"]
        }
      ]
    }  
  } 
}
```
The response will be similar to the following. Note that this is just a simulation and NOT an actual Transaction; for instance, it does not have a `transactionId`. The response indicates that for this Transaction, the maximum value this Card can provide is $55. In other words, if you attempt to create a drawdown Transaction for up to $55, it will be successful.

More details about the promotions that are unlocked for this Transaction is provided in the `transactionBreakdown`. You can use this information display all the unlocked promotions for this order.  Check out the [Lightrail Redemption Rules Implementation Guide](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/redemption-rules.md) for further discussion of this object. 

```javascript
{  
  "transaction":{  
    "value":-5500,
    "userSuppliedId":"order-s3xx30",
    "dateCreated":null,
    "transactionType":"DRAWDOWN",
    "transactionAccessMethod":"CARDID",
    "valueAvailableAfterTransaction":0,
    "cardId":"card-dcxx37",
    "currency":"USD",
    "transactionBreakdown":[  
      {  
        "value":-500,
        "valueAvailableAfterTransaction":0,
        "valueStoreId":"value-02xx6c"
      },
      {  
        "value":-5000,
        "valueAvailableAfterTransaction":0,
        "valueStoreId":"value-66xxf2"
      }
    ],
    "transactionId":null,
    "metadata":{  
      "cart":{  
        "total":6960,
        "items":[  
          {  
            "quantity":3,
            "id":"B009L1MF7A",
            "unit_price":2320,
            "tags":["apparel","outdoor"]
          }
        ]
      }
    },
    "codeLastFour":"YNJC"
  }
}
```
#### Simulation and Balance-Check Based on Gift Code

A similar endpoint exists for simulating a Transaction based on a Gift Card's `fullcode`. This is particularly useful in the checkout use-case if you are processing a Gift Card and you collect a Gift Code from the customer for redemption. The request and response structures are identical to the above.

```javascript
POST https://www.lightrail.com/v1/codes/{fullcode}/transactions/dryRun
...
```

### Card Transaction Endpoint

#### Posting a New Transaction

This endpoint enables posting a transaction to withdraw some value from a Card based on its `cardId`. The value should be expressed as a negative integer, representing the amount in the smallest unit for the currency, e.g. cents.

For Account Cards, if you do not have the `cardId` you can retrieve it by based on the customer's `contactId` using the [Card Search Endpoint](#card-search-endpoint). For Gift Cards, you can use [a similar endpoint](#code-transaction-endpoint) which enables posting transactions directly based on `fullcode` without retrieving the `cardId`.

The `userSuppliedId` is a per-endpoint unique identifier, used to ensure idempotency. Ensuring idempotency means that if the same request is issued more than once, it will not result in repeated actions on the server. For further discussions on `userSuppliedId`, see [Lightrail API docs](https://www.lightrail.com/docs/). For this use-case, we recommend that you use the unique order ID from your system as the transaction `userSuppliedId`. 

If `pending` is set to `true` in the request body, the transaction will be considered a pre-authorized pending transaction and will have to be _captured_ or _voided_ later. Save the `cardId` and `transactionId` from the response of a pending transaction as you will need them for [capturing or voiding](#voiding-or-capturing-a-pending-transaction) the pending transaction later.

Note that if you are using Redemption Rules in your system, you need to provide a complete `metadata` object in order to make sure all the eligible promotions on the Card are unlocked. Check out the [Lightrail Redemption Rules Implementation Guide](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/redemption-rules.md) for further discussion of metadata. 

Request Sample:

```javascript
POST https://api.lightrail.com/v1/cards/{cardId}/transactions
{
  "currency": "USD",
  "value": -5500,
  "pending": true,
  "userSuppliedId": "07xx71",
  "metadata":{  
     "cart":{  
        "total":6960,
        "items":[  
          {  
            "quantity":3,
            "id":"B009L1MF7A",
            "unit_price":2320,
            "tags":["apparel","outdoor"]
          }
        ]
     }
  }
}
```

Response Sample:

```json
{  
  "transaction":{  
    "transactionId":"transaction-b4",
    "value":-5500,
    "userSuppliedId":"07xx71",
    "dateCreated":"2017-07-10T23:36:28.078Z",
    "transactionType":"PENDING_CREATE",
    "transactionAccessMethod":"RAWCODE",
    "giftbitUserId":"user-08",
    "cardId":"card-6d",
    "currency":"USD",
    "metadata":{  
      "cart":{  
        "total":6960,
        "items":[  
          {  
            "quantity":3,
            "id":"B009L1MF7A",
            "unit_price":2320,
            "tags":["apparel","outdoor"]
          }
        ]
      }
    },
    "codeLastFour":"7KKT"
  }
}
```
#### Code Transaction Endpoint

Use this endpoint to post drawdown Transaction directly based on a Gift Card's `fullcode`. The request and response objects are identical to that of the [Card Transaction Endpoint](#posting-a-new-transaction) discussed above. This is particularly useful in the checkout use-case if you are processing a Gift Card and you collect a Gift Code from the customer for redemption.

Request Sample:
```javascript
POST https://api.lightrail.com/v1/codes/{fullcode}/transactions
...
```

#### Capturing or Voiding a Pending Transaction

After posting a `pending` transaction, you can use this endpoint to either capture or void it. Note that in order to call this endpoint you will need the `cardId` (of the Account Card or Gift Card) and the `transactionId` of the original pending transaction. These are are provided in the response object from the original call to create the pending transaction.

Note that if you are using Redemption Rules in your system, you need to repeat the complete `metadata` object in the capture request in order to make sure all the eligible promotions on the Card are unlocked. Check out the [Lightrail Redemption Rules Implementation Guide](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/redemption-rules.md) for further details about this. 

Request Samples:
```javascript
POST https://api.lightrail.com/v1/cards/{cardId}/transactions/{transactionId}/capture
{  
  "userSuppliedId":"tx0771",
  "metadata":{  
    "cart":{  
      "total":6960,
      "items":[  
        {  
          "quantity":3,
          "id":"B009L1MF7A",
          "unit_price":2320,
          "tags":["apparel","outdoor"]
        }
      ]
    }
  }
}
```

```javascript
POST https://api.lightrail.com/v1/cards/{cardId}/transactions/{transactionId}/void
{
  "userSuppliedId":"tx0771"
}
```

Response Sample:

```json
{  
  "transaction":{  
    "transactionId":"transaction-a7",
    "value":101,
    "userSuppliedId":"transaction-46-reverse",
    "dateCreated":"2017-07-12T00:22:48.379Z",
    "transactionType":"PENDING_VOID",
    "transactionAccessMethod":"CARDID",
    "giftbitUserId":"user-08",
    "cardId":"card-6d",
    "currency":"USD",
    "parentTransactionId":"transaction-46",
    "codeLastFour":"7KKT",
    "metadata":{  
      "cart":{  
        "total":6960,
        "items":[  
          {  
            "quantity":3,
            "id":"B009L1MF7A",
            "unit_price":2320,
            "tags":["apparel","outdoor"]
          }
        ]
      },
      "giftbit_initial_transaction_id":"transaction-46"
    }
  }
}
```

### Card Search Endpoint

You can call the this endpoint with search parameters to retrieve the account Card belonging to a Contact for a particular currency:

```javascript
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
    "currency": "USD",
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
