# Issuing Lightrail Gift Cards

## Introduction

This document is a quick step-by-step guide to creating and distributing new Lightrail Gift Cards. The focus will be on implementing this use-case by programmatically calling the Lightrail API. 

- If you would like to learn more about Lightrail concepts, check out the section on Lightrail Object Model in the [Lightrail API Docs](https://www.lightrail.com/docs/).
- If you are looking for an implementation of this use-case in a specific language or technology, check out the section on Client Libraries and Integrations in the [Lightrail API Docs](https://www.lightrail.com/docs/).
- If you are looking for adding promotional values to existing Gift Cards, check out our implementation guide for [Creating Lightrail Promotions](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/promotions.md).

## Concepts

A Lightrail Gift Card is a virtual device for issuing gift values. Each Gift Card belongs to a Lightrail Program which determine some of the general parameters for its principal value, such as currency and validity period. Programs also provide a mechanism for organizing Gift Cards into groups for analysis and management purposes. For example, you can check the redemption stats for all of the Gift Cards created as part of the _Boxing-Day 2017 Sale_ program. You can set up Programs using the [Lightrail Web App](https://www.lightrail.com/app/).

Each gift card has:

-  a `currency` which is inherited from the program it belongs to, 
-  a `cardId` which uniquely identifies the card, and
-  a `fullCode` which is a unique unguessable alphanumeric string, also referred to as _gift code_, usually released to the gift recipient in confidence. 

Usually, when a Gift Card is issued, the `fullCode` is sent to the recipient by email. The recipient can later use this code to check the Gift Card's balance or redeem its value, for example, at a store checkout. Note that in order to improve the confidentiality of the the gift code and to avoid accidental leaks, only one Lightrail endpoint returns the `fullcode` and other endpoints only return the last four characters of the code when necessary.

The `cardId` on the other hand is used for all other administrative purposes such as funding the card, _freezing_ or _unfreezing_ its value, etc.

Note that aside from the principal value, Lightrail also supports attaching temporary promotional values to Gift Cards. To learn more, check out our implementation guide for [Creating Lightrail Promotions](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/promotions.md).

## Use-Case

The reason for issuing a Gift Card varies depending on your business model. For example, you may issue Gift Cards for existing customers and send it to them in December to boost your sales, or you may want to enable your customers to purchase a Gift Card at your online store and send it to their friends.

## High-Level Flow

Here are steps for issuing a new Gift Card:

- Create a new Gift Card Program if you have not set up one up already.
- Determine the initial value of the Gift Card and create it under that Program.
- Retrieve the _gift code_ corresponding to the new Gift Card from the Lightrail API.
- Send the gift code to the customer, usually via email or text message.

## Detailed Flow

### Create a Gift Card Program

Because programs are used to manage and organize Gift Cards, Lightrail recommends that you create a dedicated Program for each of your gift card use-cases. Creating Gift Card Programs is currently supported via [Lightrail Web App](https://www.lightrail.com/app/).

Here is an example of a consumer gift card program:

- **Program Name:** Consumer Gift Cards
- **Currency:** USD 
- **Program Start Sate:** immediate
- **Program End Date:** never
- **Value Range:** $5 - $1000
- **Card Expiry:** never

### Create the Gift Card

To create a new Gift Card programmatically, call the [Cards API Endpoint](#creating-a-new-gift-card). You need to determine the following parameters: 

- The `programId` of the Gift Card Program to which the card will belong. You can find this ID for a new or existing program by checking the program page in the Lightrail Web App.

Optionally, you can also specify the following information:

- The `initialValue` for the Gift Card. If not specified this will default to zero and you will need to post a transaction to fund the Card later. 
- If applicable, the Card's validity period. This is specified by stating either or both of `startDate` and `expires` parameters and must be within the validity range set by the Program.
- A set of `metadata` to record any significant information you would like to retain with respect to the issuance of the Card, e.g. the order and transaction IDs and the recipient's information if the card is being purchased. Technically, this metadata will be recorded with the initial transaction which funds the Card with its initial value.
- A `contactId` in case the recipient of the Gift Card is already a known customer in your system and is registered as a Lightrail Contact. To learn more about how to create Lightrail Contacts see the [Lightrail API Docs](https://www.lightrail.com/docs/).

Note that since Lightrail supports creating other types of Cards using the same endpoint, you need to specify that the `cardType` is `GIFT_CARD`. 

You also need to provide a `userSuppliedId` which is a client-side unique value to guarantee idempotency, i.e. to ensure that repeating the call with the same parameters will not lead to repeated server-side actions. Read more about `userSuppliedId`s in the Implementation Details section of the [Lightrail API Docs](https://www.lightrail.com/docs/). For this use-case we suggest that you use a unique ID from an object in your workflow such as the order ID if the Card is being purchased.

```javascript
POST https://api.lightrail.com/v1/cards
{
  "programId": "program-06d0",
  "cardType" : "GIFT_CARD",
  "userSuppliedId": "account-d37e",
  "initialValue" : 5000,
  "startDate" : "2017-08-01T00:27:02.910Z",
  "expires" : "2017-10-01T00:27:02.910Z", 
  "metadata": {
    "orderId": "x72a3sx5e",
    "transactionId": "tr2re3t0y2r3u0w6r",
    "recipientEmail" : "test@test.ca",
    "purchaserName" : "Alice Liddell",
    "purchaserEmail" : "alice@wonderland.ca"
   }
}
```

The response object to this call includes both the `userSuppliedId` and a server-generated `cardId` which you can persist and use to retrieve the Gift Card. You can also use the `userSuppliedId` to search and uniquely retrieve the Gift Card object.

From the response of this call, you need to retain the `cardId` which is necessary for the next step.

```json
{
  "card":{
    "cardId": "card-6207",
    "userSuppliedId": "account-d37",
    "contactId": null,
    "dateCreated": "2017-08-01T22:15:42.123Z",
    "cardType": "GIFT_CARD",
    "currency": "USD"
  }
}
```


### Retrieve the Card's Full Code

To retrieve the full code corresponding to a card, you can send a GET request to the full code endpoint and provide the `cardId`. Note that for security reasons this is the only endpoint in the Lightrail API that returns the `fullcode`, so you will not receive the `fullcode` in the `card` object in the response from the Card creation endpoint.  

```javascript
GET https://api.lightrail.com/v1/cards/{cardId}/fullcode
```

The response is a `fullcode` object similar to the following:

```json
{
  "fullcode": {
  "code": "A1B2C-E3F4G-A3B4C-E1F2G-VWXYZ"
}
```

### Distribute the Gift Code

Usually gift codes are sent to the recipient by email. If you are selling a gift code on your web site, you have to ask the customer who is purchasing the gift code to provide the gift recipient's email address.

For the contents of email, you should generally rely on a template and fill the gift code and the recipient's information (e.g. their name) in that template. 

Having determined the content of the email and its recipient's email address, you can use an email service of choice to send the email and deliver the gift code. 

Traditional email services provide an SMTP interface but for sending emails using a simpler RESTful interface, you may want to consider an email API service provider such as [SendWithUs](https://www.sendwithus.com/docs/quickstart) or [Amazon SES](http://docs.aws.amazon.com/ses/latest/DeveloperGuide/sending-email.html).
