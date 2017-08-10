# Issuing Lightrail Gift Cards

## Introduction

This document is a quick guide to creating and distributing new Lightrail gift cards. The focus of this document is implementing this use-case by directly calling the Lightrail API. If you are looking for client libraries specific to a language or technology, check out our [client libraries and integrations](https://github.com/Giftbit/Lightrail-API-Docs#client-libraries-and-integrations).

## Concepts

A Lightrail gift card is a virtual device for issuing gift values. Each gift card belongs to a Lightrail Program which determine some of the general parameters for its value, such as currency and expiration date. Programs also provide a mechanism for organizing gift cards into groups for analysis and management purposes. For example, you can check the redemption stats for all of the gift cards created as part of the Boxing-Day 2017 Sale program. You can set up programs using the [Lightrail web application](https://www.lightrail.com/app/).

Each gift card has:

-  a `currency` which is inherited from the program it belongs to, 
-  a `cardId` which uniquely identifies the card, and
-  a `fullCode` which is a unique unguessable alphanumeric string, usually released to the gift recipient in confidence. 

Generally, when a gift card is issued, the `fullCode` is sent to the recipient by email. The recipient can later provide this code as a proof that they *are* the legitimate holder of the card and check its balance or redeem its value, for example, at a store checkout. 

The `cardId` on the other hand is used for all administrative purposes such as funding the card, freezing or unfreezing its value, etc.

## Use-Case

Depending on your business model, various events can trigger issuing a new gift card for a customer. For example, you might want to create a new gift card to send it to your existing customers on their birthday, or you may want to enable your customers to purchase a gift card at your online store.

## High-Level Flow

Here are steps for issuing a new gift card:

- Create a new gift card program if you have not set up one up already.
- Create a new gift card under the program.
- Retrieve the gift code corresponding to the new card from the Lightrail API.
- Send the code to the customer, usually via email or text message.

## Detailed Flow

### Create a Gift Card Program

Because programs are used to manage and organize gift cards, Lightrail recommends that you create a dedicated program for each of your gift card use-cases. Creating Gift Card Programs is currently supported via [Lightrail web application](https://www.lightrail.com/app/).

Here is an example of a consumer gift card program:

- **Program Name:** Consumer Gift Cards
- **Currency:** USD 
- **Program Start Sate:** immediate
- **Program End Date:** never
- **Value Range:** $5 - $1000
- **Card Expiry:** never

### Create the Gift Card

In order to programmatically create a new gift card, you can call the [Cards API Endpoint](#creating-a-new-gift-card). For this call, you need to determine the following information: 

- The `programId` of the program to which the card will belong. You can find this ID for a new or existing program by checking the program page in the Lightrail web application.

Optionally, you can also specify the following information:

- The `initialValue` for the card. If not specified this will default to zero.
- If applicable, the validity period of the card. This is specified by stating either or both of `startDate` and `expires` parameters.
- A set of `metadata` to record any significant information you would like to retain with respect to the issuance of this card, e.g. the order and transaction IDs if the card is being purchased.  

From the response of this call, you need to retain the `cardId` which is necessary for the following call.

Here is an example of a gift card:

- **Program ID:** `<ID of the Consumer Gift Cards Program>`
- **Card Initial Value:** $50
- **Card Start Date:** Aug 1st, 2017
- **Card Expiry Date:** Oct 1st, 2017 
- **Metadata:** order ID for card purchase, transaction ID for the card purchase, purchaser's name and email address.

### Retrieve the Card's Full Code

Retrieving the full code takes place by making a simple call to the [Full Code API Endpoint](#retrieving-the-full-code) and providing the `cardId`.

### Distribute the Gift Code

Usually gift codes are sent to the recipient by email. If you are selling a gift code on your web site, you have to ask the customer who is purchasing the gift code to provide the gift recipient's email address.

For the contents of email, you should generally rely on a template and fill the gift code and the recipient's information (e.g. their name) in that template. 

Having determined the content of the email and its recipient's email address, you can use an email service of choice to send the email and deliver the gift code. 

Traditional email services provide an SMTP interface but for sending emails using a simpler RESTful interface, you may want to consider an email API service provider such as [SendWithUs](https://www.sendwithus.com/docs/quickstart) or [Amazon SES](http://docs.aws.amazon.com/ses/latest/DeveloperGuide/sending-email.html).

## API Endpoints

### Cards Endpoint

#### Creating a New Gift Card

For creating a new gift card, you need to provide the `programId` for the program to which the gift card will belong and specify that the card type is `GIFT_CARD`. 

You also need to provide a `userSuppliedId` which is a client-side unique value to guarantee idempotence, i.e. to ensure that repeating the call with the same parameters will not lead to repeated server-side actions. Initial value and start and expiry dates are optional parameters.

Note that you can provide a JSON object as `metadata`. Technically, this metadata will be recorded with the initial transaction which funds the card with its initial value.

```json
POST https://api.lightrail.com/v1/cards
{
  "userSuppliedId": "account-d37e",
  "programId": "program-06d0",
  "initialValue" : 5000,
  "cardType" : "GIFT_CARD",
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

The response object to this call includes both the `userSuppliedId` and a server-generated `cardId` which you can persist and use to retrieve the gift card later. Note that if you are following this use-case, you need to store the `cardId` and use it in the next step.

```json
{
  "card":{
  "cardId": "card-6207",
  "userSuppliedId": "account-d37",
  "contactId": null,
  "dateCreated": "2017-08-01T22:15:42.123Z",
  "categories":[
   {
    "categoryId": "category-94d0",
    "key": "giftbit_order",
    "value": "2017-08-02"
   },
   {
    "categoryId": "category-67fe",
    "key": "giftbit_program",
    "value": "program-06d0"
   }
  ],
  "cardType": "GIFT_CARD",
  "currency": "USD"
  }
}
```

#### Retrieving the Full Code

To retrieve the full code corresponding to a card, you can send a GET request to the full code endpoint and provide the `cardId`:

```json
GET https://api.lightrail.com/v1/cards/{cardId}/fullcode
```

The response is a `fullcode` object which includes the `code`:

```json
{
  "fullcode":{
  "code": "A1B2C-E3F4G-A3B4C-E1F2G-VWXYZ"
}
```
