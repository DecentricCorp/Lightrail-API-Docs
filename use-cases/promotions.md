# Creating Lightrail Promotions

## Introduction

This document is a quick step-by-step guide to adding promotions to existing Lightrail Cards. The focus will be on implementing this use-case by programmatically calling the Lightrail API. 

- To learn more about Lightrail concepts, check out the section on the Lightrail Object Model in the [Lightrail API Docs](https://www.lightrail.com/docs/). 
- If you are looking for an implementation of this use-case in a specific language or technology, check out the section on Client Libraries and Integrations in the [Lightrail API Docs](https://www.lightrail.com/docs/).

Note that promotions can also be created from the Promotions section of the Lightrail Web App.

## Concepts

One of the powerful features of Lightrail is the ability to add promotions to Cards. Promotions are additional values that can be attached to existing Cards and are often temporary and subject to some conditions. 

Promotions are usually used to encourage customers to expedite spending their gift or account credit value, or to incentivize spending on certain items or in certain ways. For example, you can attach a $10 promotional value to an existing Gift Card, valid only for one week and redeemable only if the customer spends more than $100 on buying a certain brand of jeans and pays with a certain type of credit card. You can read more about Lightrail's powerful [Redemption Rules](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/feature-deep-dive/RedemptionRules.md) for promotions.

Promotional values are represented by an `attached` Value Stores on a Card. For creating a new Value Store you need to determine two things:

- The `cardId` of the Card to which the promotion will be attached.  
- The `programId` of an existing Promotion Program. 

Note that Lightrail values, including promotions, are always created as part of a Program and cannot be created in isolation. To learn more about the relationship among Cards, Value Stores, and Programs, check out the section on Lightrail Object Model in the [Lightrail API Docs](https://www.lightrail.com/docs/).

## High-Level Flow

You can start a promotion by creating a _Promotion Program_ in the Lightrail Web App. The Program specifies some overall attributes of the promotion such as currency, validity period, value range, and redemption rules. For example, you can create a _Back to School_ promotion Program with values in USD, valid only in the last week of August. 

Once you have a Promotion Program, you can create promotional values based on it and attach them to existing Cards. The value will be subject to the rules and conditions set by the program. 

## Detailed Flow

- If you don't already have one, create a new Promotion Program through the Lightrail Web App by going to the _Promotions_ menu, selecting _Add New Promotion Program_, and setting up the parameters.
- Determine the `programId` by going to the Program page and copying the _Promotion Program ID_ under _Program Details_.
- Determine the `cardId` of the Cards to which you would like to add a promotion. How you select the Cards which will receive the promotion highly depends on your system and your marketing strategies.
- Call the [Value Store Creation Endpoint](#create-a-new-value-store) by providing the `cardId` and the `programId`. This will create a new promotion and attach it to the Card.


## API Endpoints

### Create a New Value Store

For creating a promotion, you can make a call to the `valueStores` endpoint under `cards` and specifying the `cardId`. As discussed above, you also need to specify the `programId` of the reference Promotional Program from which the Value Store should be instantiated.  

You also need to provide a `userSuppliedId` which is a client-side unique value to guarantee idempotency, i.e. to ensure that repeating the call with the same parameters will not lead to repeated server-side actions.

Currently, `currency` is also a required parameter for the purpose of consistency verification since it has to match the currency of the Promotion Program. Future versions of Lightrail may make this optional.

Optionally, you can specify an `initialValue`. This value must comply with the rules of the Promotion Program, e.g. be within the allowed value range.

Other optional attributes in the request are `startDate` and `expires` which specify the validity period of the Promotion if it is different from the defaults set by the Program. Note that if specified, these values still need to be within the range set by the Promotion Program.

Here is a sample request and response call: 

```javascript
POST https://api.lightrail.com/v1/cards/{cardId}/valueStores
{
  "userSuppliedId": "72xx81",
  "programId": "program-53xx36",
  "currency": "USD",
  "initialValue": 150
}
```
The response object to this call includes a server-generated `valueStoreId` which you can persist and use in other operations regarding the Value Store such as _canceling_ or _freezing_ it.

```json
{
  "valueStore": {
    "cardId": "card-6dxx89",
    "valueStoreId": "value-e3xxce",
    "valueStoreType": "ATTACHED",
    "currency": "USD",
    "dateCreated": "2017-08-28T21:47:27.154Z",
    "programId": "program-53xx36",
    "expires": "2017-09-05T06:59:59.000Z"
  }
}
```

### Cancelling, Freezing and Unfreezing a Value Store

You can freeze/unfreeze a promotion or cancel it altogether. Freezing and unfreezing are normally used for investigating cases of potential fraud.

For these calls you need to provide the `cardId` of the Card to which the promotion is attached as well as the `valueStoreId` of the Value Store representing the promotion. These calls also require a `userSuppliedId`.

Here are sample requests for these actions:

```javascript
POST https://api.lightrail.com/v1/cards/{cardId}/valueStores/{valueStoreId}/freeze
{
  "userSuppliedId": "case-234"
}
POST https://api.lightrail.com/v1/cards/{cardId}/valueStores/{valueStoreId}/unfreeze
{
  "userSuppliedId": "case-324"
}
POST https://api.lightrail.com/v1/cards/{cardId}/valueStores/{valueStoreId}/cancel
{
  "userSuppliedId": "case-872"
}
```

 The response will be a transaction object which includes a `transactionId` for reference and also provides a `value` to report the value of the funds affected by the call. Here is an example: 

```json
{
  "transaction": {
    "transactionId": "transaction-7fxx89",
    "value": -50,
    "userSuppliedId": "case-872",
    "dateCreated": "2017-06-05T16:39:06.679Z",
    "transactionType": "CANCELLATION",
    "transactionAccessMethod": "CARDID",
    "valueAvailableAfterTransaction": 0,
    "giftbitUserId": "user-50xx93",
    "codeLastFour": "NKNA",
    "cardId": "card-fbxxda",
    "currency": "USD"
  }
}
```