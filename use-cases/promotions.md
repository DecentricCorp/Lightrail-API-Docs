# Creating Lightrail Promotions

## Introduction

This document is a quick guide to adding promotions to existing Lightrail Cards. The focus of this document is provide a quick step-by-step guide to implementing this use-case by directly calling the Lightrail API. 

- If you would like to learn more about Lightrail concepts, checkout the [Lightrail Object Model](https://jsapi.apiary.io/apis/giftbitcurrencyapi/introduction/getting-started/base-url.html#use-cases-anchor)
- If you are looking for client libraries specific to a language or technology, check out our [Client Libraries and Integrations](https://jsapi.apiary.io/apis/giftbitcurrencyapi/introduction/getting-started/base-url.html#integrations-anchor).

## Concepts

One of the powerful features of Lightrail is adding promotions to Cards. Promotions are auxiliary values that can be attached to existing Cards and are often temporary and subject to some conditions. Promotions are often used to encourage customers to spend their gift or account credit value by making time-limited additional value offers.

## High-Level Flow

You can start a promotion by creating a _Promotion Program_ in the Lightrail Web App. The Program specifies some overall attributes of the promotion such as currency, validity period, value range, and redemption rules. For example, you can create a _Back to School_ promotion Program with values in USD, valid only in the last week of August. 

Once you have a Promotion Program, you can create promotional values on its basis and attach them to existing Cards. The value will be subject to the rules and conditions set by the program.

## Detailed Flow

- If you don't already have one, create a new Promotion Program through the Lightrail Web App by going to the _Promotions_ menu, selecting _Add New Promotion Program_, and setting up the parameters.
- Determine the `programId` by going to the Program page and copying the _Promotion Program ID_ under _Program Details_.
- Determine the `cardId` of the Cards to which you would like to add a promotion. How you select the Cards which will receive the promotion highly depends on your system and your marketing strategies.
- Call the [Value Store Creation Endpoint](#create-new-promotion) by providing the `cardId` and the `programId`. This will create a new promotion and attach it to the Card.



## API Endpoints

### Create New Promotion

For creating a new promotion you need to provide the `programId` from an existing Promotion Program. Note that Lightrail values including promotions, are always created as part of a Program and cannot be created in isolation.

You also need to provide a `userSuppliedId` which is a client-side unique value to guarantee idempotency, i.e. to ensure that repeating the call with the same parameters will not lead to repeated server-side actions.

Currently, `currency` is also a required parameter which is for the purpose of consistency verification since it has to match the currency of the Promotion Program. Future versions of Lightrail may make this optional.

Optionally, you can specify an `initialValue`. This value must comply with the rules of the Promotion Program, e.g. be within the allowed value range.

Here is a sample request and response call: 

```json
POST https://api.lightrail.com/v1/cards/{cardId}/valueStores
{
  "userSuppliedId": "72xx81",
  "programId": "program-53xx36",
  "currency": "USD",
  "initialValue": 150
}
```
The response object to this call includes both the `userSuppliedId` and a server-generated `valueStoreId` which you can persist and use in other operations regarding the Value Store such as _canceling_ or _freezing_ it. 

```json
{
  "valueStore":{
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

