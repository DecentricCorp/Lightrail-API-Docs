# Split-Tender with Lightrail and Stripe

## Introduction

This document is a quick step-by-step guide to splitting a transaction between a Lightrail Card and Stripe. The focus will be on implementing this use-case by programmatically calling the Stripe and Lightrail API. 

- For a more in-depth discussion of Lightrail value redemption, check out our guide to [Redeeming a Lightrail Value at Checkout](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/giftcode-checkout.md). 
- If you would like to learn more about Lightrail concepts, check out the section on Lightrail Object Model in the [Lightrail API Docs](https://www.lightrail.com/docs/).
- If you are looking for an implementation of this use-case in a specific language or technology, check out the section on Client Libraries and Integrations in the [Lightrail API Docs](https://www.lightrail.com/docs/).

## Concepts

A _split-tender transaction_ is when a transaction is broken down into two (or more) transactions each processed by a different payment methods. In this use-case we will focus on the common case of dividing a transaction between a Lightrail Card (Gift Card or Account Card) and a credit card processed by Stripe. For example, if you are processing a $134.25 transaction and the customer would like to redeem a $30 value from their Gift Card, you need to charge $30 to the Lightrail Card and the remaining $104.25 must be charged to Stripe.

The important requirement for split-tender transactions is to process them _atomically_. This means that you should either charge neither of the sub-transaction or make sure they all go through successfully. 

## High-Level Flow

The high-level steps for implementing a split-tender with Lightrail and Stripe is as the following:

- Determine the payment parameters for Lightrail and Stripe payment. 
- Determine the split, i.e. the respective shares of Lightrail and Stripe.
- Post a _pending_ transaction to charge the Lightrail Card with its share of the split-tender transaction.
- Attempt charging Stripe with its share of the transaction.
  - If this succeeds, _capture_ the Lightrail pending transaction and declare success for the split-tender.
  - If this fails, _void_ the Lightrail pending transaction and declare failure for the split-tender.

## Detailed Flow

### Determine the Parameters

You will need the following Lightrail parameters (check out the [Lightrail API Docs](https://www.lightrail.com/docs/) if you need to read more about these):

- Your Lightrail `apiKey`,
- A Lightrail `cardId` or `fullcode` to specify which Card you are charging. This is either a Gift Card or an Account Card.

For Stripe, you will need the following parameters (check out the [Stripe Docs](https://stripe.com/docs/charges) if you need to read more about these):
- Your Stripe `secretKey`,
- A Stripe `token` or `customerId` to specify which credit card you are going to charge via Stripe. Note that based on Stripe's flow you have to either use one of Stripe's browser-side methods to capture the credit card information and exchange it for a `token` or use a `customerId` for a previously-registered customer credit card.


### Determine the Split

Let's call these `lightrailShare` and `stripeShare` respectively. Depending on you use-case, there are different ways to determining the split between Lightrail and Stripe.  Here are some examples:

- You can allow a user to manually determine the split. For example, a customer can choose how much from their account credit they would like to use towards paying for a certain order.
- You can check the balance of the Lightrail card and use its maximum available value and charge the rest to Stripe. This is a more common user-experience for Gift Cards.


Note that Stripe has a minimum transaction value requirement so you have to make sure that after the `stripeShare` is not too small. More details on this edge case has been discussed in [Redeeming a Lightrail Value at Checkout](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/giftcode-checkout.md). 

### Post a Pending Transaction on Lightrail Card

If you have a Lightrail `cardId`, the request will look like the following. 

- Since this is a drawdown, the `value` should be a <u>negative</u> integer representing the value in the smallest unit of the currency, such as cents. 
- The `userSuppliedId` is a unique idempotency ID that you have to determine. We recommend you use the unique identifier of an object from your workflow such as the order ID, if this is an order checkout.   
- The `currency` must match the currency of the Card you are charging.

```json
POST https://api.lightrail.com/v1/cards/{cardId}/transactions
Authorization: Bearer {apiKey}
Content-Type: application/json; charset=utf-8

{
  "currency": "USD",
  "value": -{lightrailShare},
  "pending": true,
  "userSuppliedId": "07xx71",
}
```

If you have a Lightrail Gift Card `fullcode` the request is identical, but you will have to call a different endpoint:

```json
POST https://api.lightrail.com/v1/codes/{fullcode}/transactions
Authorization: Bearer {apiKey}
Content-Type: application/json; charset=utf-8

{
  "currency": "USD",
  "value" : {lightrailShare},
  "pending": true,
  "userSuppliedId": "07xx71"
}
```

A successful response to these calls will look like the following. Depending on the endpoint there may be some other attributes in the response object which are not of significant consequence in this use-case.

```Json
{
   "transaction": {
   "transactionId": "transaction-bxx4",
     "value": {lightrailShare},
     "userSuppliedId": "order-07xx71",
     "dateCreated": "2017-07-10T23:36:28.078Z",
     "transactionType": "PENDING_CREATE",
     "cardId": "card-6xxd",
     "currency": "USD"
     ...
}
```

From this response object, save the `transactionId` attribute. Also, if you used the `fullcode`, you now have the `cardId` in the response and you need to save this value as well.

```php
$lightrailPendingTransactionId = $response['transaction']['transactionId'];
$lightrailCardId = $response['transaction']['cardId'];
```

### Charge Stripe

To charge Stripe, if you have a `token` use the following call. Note that Stripe has many clients for different programming languages, so, you are likely to make this call using the client library. 

Note that unlike Lightrail, the value of the `amount` parameter in the Stripe call should be a <u>positive</u> integer. 

As you see in this example, we recommend that you use Stripe's `metadata` parameter to push additional information with this transaction indicating that this is part of a split-tender with lightrail and to record the `lightrailPendingTransactionId`.

```json
POST https://api.stripe.com/v1/charges
Authorization: Bearer {secretKey}
Content-Type: application/x-www-form-urlencoded

amount={stripeShare}&currency=USD&source={token}&metadata[split-tender-partner]=lightrail&metadata[split-tender-total]={total}&metadata[split-tender-partner-txn-id]={lightrailPendingTransactionId}
```

If instead of a token you have a Stripe `customerId`, you can make a similar call by replacing the `source` parameter with `customer={customerId}`.

The successful response to this call will look like the following:

```Json
{
  "id": "ch_1AxxTy",
  "object": "charge",
  "amount": {stripeShare},
  "captured": true,
  "currency": "usd",
  "metadata":{
    "split-tender-partner": "lightrail",
    "split-tender-total": {total},
    "split-tender-partner-txn-id": {lightrailPendingTransactionId}
  }
  ...
}
```

From this response object, save the transaction `id` in order to save it later on the Lightrail capture.

```php
$stripeTransactionId = $response['id'];
```

### Capture or Void Lightrail Pending Transaction

If the Stripe transaction went through successfully, proceed to capture the Lightrail pending transaction by making a call similar to the following. You will need the `cardId` and the `lightrailPendingTransactionId`.

Note that you need a new `userSuppliedId`; if you already have a unique identifier from your system that you used for posting the pending transaction, you add a `-capture` suffix to create a new `userSuppliedId` for this call.

As you see in this example, we recommend that you leverage Lightrail's `metadata` attribute to push additional information with this transaction indicating that this is part of a split-tender with Stripe and to record the `stripeTransactionId`.

```json
POST https://api.lightrail.com/v1/cards/{cardId}/transactions/{lightrailPendingTransactionId}/capture
{
  "userSuppliedId":"order-07xx71-capture",
  "metadata":{
    "split-tender-partner": "stripe",
    "split-tender-total": {total},
    "split-tender-partner-txn-id": {stripeTransactionId}
  }
}
```
If for any reason the Stripe payment call fails, you need to void the Lightrail pending transaction.
```json
POST https://api.lightrail.com/v1/cards/{cardId}/transactions/{lightrailPendingTransactionId}/void
{
  "userSuppliedId":"order-07xx71-void",
}
```
