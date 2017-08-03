# Issuing Lightrail Gift Cards

## Introduction

This document is a quick guide to creating and distributing new Lightrail gift cards. The focus of this document is implementing this use-case by directly calling the Lightrail API. If you are looking for client libraries specific to a language or technology, check out our [client libraries and integrations](https://github.com/Giftbit/Lightrail-API-Docs#client-libraries-and-integrations).

## Concepts

A Lightrail gift card is a virtual device for issuing gift values. Each gift card belongs to a Lightrail Program which determine some of the general parameters for its value, such as currency and expiration date. Programs also provide a vehicle to organize gift cards into groups for analysis and management purposes. For example, you can check the redemption stats for all of the gift cards created as part of the Boxing-Day 2017 Sale program. You can set up programs using the [Lightrail web application]().

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
- Send the code to the customer, which is usually done via email or text message.

## Detailed Flow

### Create the Gift Card

In order to programmatically create a new gift card, you can call the [Cards API Endpoint](#creating-a-new-gift-card). For this call, you need to determine the following information: 

- The `programId` of the program to which the card will belong. You can find this ID for an existing or a new program by checking the program page in the Lightrail web application.

Optionally, you can also specify the following information

- The `initialValue` for the card. If not specified this will default to zero.
- The validity period of the card, if applicable, by specifying `startDate` and `expires`.

For the response of this call, you need to retain the `cardId` which is necessary for the following call.

### Retrieve the Code

Retrieving the full code takes place by making a simple call to the [Full Code API Endpoint](retrieving-the-full-code) and providing the `cardId`.

### Distribute the Gift Code

Usually gift codes are sent to the recipient by email. If you are selling a gift code on your web site, you have to ask the customer who is purchasing the gift code to provide the gift recipient's email. 

For the contents of email, you should generally rely on a template and fill in the gift code and recipient's information, such as their name, to that template. 

Having determined the content of the email and its recipient's email address, you can use your email service of choice to send the email and deliver the gift code. 

Traditional email services provide an SMTP interface but for sending emails using a simpler RESTful interface, you may want to consider an email API service provider such as [SendWithUs](https://www.sendwithus.com/docs/quickstart) or [Amazon SES](http://docs.aws.amazon.com/ses/latest/DeveloperGuide/sending-email.html).

## API Endpoints

### Cards Endpoint

#### Creating a New Gift Card

For creating a new gift card, you need to provide the `programId` for the program to which the gift card will belong and specify that the card type is `GIFT_CARD`. 

You also need to provide a `userSuppliedId` which is a client-side unique value to guarantee idempotence, i.e. to ensure that repeating the call with the same parameters will not lead to repeated server-side actions. Initial value and start and expiry dates are optional parameters.

```json
POST https://api.lightrail.com/v1/cards
{
  "userSuppliedId": "account-d37e",
  "programId": "program-06d0",
  "initialValue" : 500,
  "cardType" : "GIFT_CARD",
  "startDate" : "2017-08-02T00:27:02.910Z",
  "expires" : "2017-10-02T00:27:02.910Z"
}
```

The response object to this call includes both the `userSuppliedId` and a server-generated `cardId` which you can persist and use to retrieve the gift card later. Note that if you are following this use-case, you need to store the `cardId` and use it in the next step.

```json
{
  "card":{
  "cardId": "card-6207",
  "userSuppliedId": "account-d37",
  "contactId": null,
  "dateCreated": "2017-08-02T22:15:42.123Z",
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
