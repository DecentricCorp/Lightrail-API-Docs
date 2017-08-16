# Lightrail API

Welcome to the Lightrail API documentation. Lightrail is a powerful and flexible platform for digital account credits, gift cards, promotions, and points. 
Lightrail is carefully designed for rapid adoption and integration while maintaining flexibility to support various business use-cases and we would like this document 
to be part of that experience. In addition to being a fully interactive REST reference, we will have a quick overview of Lightrail's 
conceptual model, common use-cases, and common API calls with examples. 
Contact us anytime at hello@lightrail.com —we are here to help you solidify your use-case and implementation.

## Getting Started

### API Base URL

The base URL for the Lightrail API is `https://api.lightrail.com/v1/`.

### Lightrail User Creation and Authorization

In order to use the API endpoints, you need an API key for authentication. You can find your test or production API key in the _API_ tab under _Account Settings_ after you log in to your Lightrail account. 
If you do not have a Lightrail account yet, <em><a href="https://www.lightrail.com/#gform" target="_blank">contact us to request a demo</a></em>. 
Make sure to take the measures for keeping your API key secure and store it safely in your production configurations.

Note that in your user settings you can toggle to a test mode which provides you with a test `access_token`. 
This allows you to make calls to the API without affecting your Cards or the calculated statistics in live mode. Emails sent from test mode will be labeled as such.

Prefix your `access_token` with the word ‘Bearer’ (case sensitive) and a space and pass it on in the Authorization HTTP header. 
For example `Authorization: Bearer {access_token}`.

## The Lightrail Object Model
The full API object model is here for your reference. Depending on your use-case, you may not need to use every object/endpoint. Read on for use-case elaboration

![Lightrail Object Model](http://resources.giftbit.com/api/embeddedimages/Lightrail_Object_Model_(stacked).png)

### Lightrail Cards: The Core Object

The Card is a fundamental object in the Lightrail API and provides the functionality for managing branded currency. 
The concept of a Card can represent any sort of value that your business wishes to issue. 
Simply stated, a Card is a device for which value can be attached and transacted against. 

There are two types of Cards: Gift Cards and Account Cards. These are both represented as a Card but with a different `cardType` attribute. 

#### Gift Cards

Gift Cards, as you might guess, are used when implementing a gift card program. Gift Cards have a `fullcode` which is a unique and unguessable alpha-numeric code that can be distributed to a recipient. 
In a standard gift card program, the recipient would manually enter the `fullcode` during the checkout process.

#### Account Cards

Account Cards represent value that is attached to a Contact. 
They are used when implementing a customer account credit or points program and can be thought of as a customer's account.
Since Account Cards do not have a `fullcode`, their value is interacted with using the Card object. 

{#
## Common Use-Cases
This documentation will explore in detail the most common use-cases of the Lightrail API. 
Generally, our users are looking to implement either of the following programs:
<ol>
    <li>A gift card program</li>
    <li>A customer account credit or points program </li>
</ol>

Note that this is not an exhaustive list and some use-cases will require a combination of the two. 
Lightrail is designed to power and enhance any use-case of stored value.  We are here to work with you.

In each scenario, the power of Lightrail Promotions allows you to incentivize and motivate your users. Lightrail also provides a rich analysis interface which will give you more insight into your programs.
#}
## Common Use-Cases

The following sections dive into Lightrail's most common use-cases. Feel free to skip to the section relevant to you. If you're not sure which applies to you, please don't hesitate to ask!

Also note, the ability to incentivize your users or recipients through Lightrail Card Promotions is available for both Gift Cards and Account Cards. 
Detailed information for Card Promotions and how it can be applied to either use-case can be found in the following [Create and Attach Promotions](#create-and-attach-promotions-anchor) section. 

Looking to import your existing codes into the Lightrail system? We support that. Please let us know to find out more. 

### Use-Case 1: Powering Your Gift Card Program
 
A common use-case for the Lightrail API is one in which Lightrail powers your gift cards. 

For example, suppose your business is an online e-commerce platform and you want to enable your customers to purchase gift cards.
When a customer purchases a gift card, you would create a Gift Card in the Lightrail API. You would retrieve the Gift Card's `fullcode` and deliver it to the customer.
Your store's checkout process would need to allow customers the opportunity to enter the `fullcode` of any gift cards they have.
When completing the purchase you would create a Transaction using the customer entered `fullcode`, redeeming the required value from the Gift Card, completing the transaction. 

Is your store is running on an established e-commerce platform? 
Lightrail supports popular e-commerce platforms and has dedicated integrations teams here to support you.

Lightrail enables fine-grained control of the rules for which Gift Cards can be issued using Gift Card Programs.

#### Lightrail Gift Card Programs

A Gift Card Program describes the rules and restrictions that apply to the Gift Cards created from that Program. 
For example, a Gift Card Program might declare that all Gift Cards are issued in USD, with an `initialValue` in the range of $5 - 100, and never expire. 

To create a Gift Card, it must be created using a Gift Card Program. 
You may create Gift Card Programs within the <a href="https://www.lightrail.com/app/#/programs" target="_blank">Gift Card</a> section of the Lightrail web application. 
Gift Card Programs are also used to structure and organize related Gift Cards, and also improve tracking of activity within your account. 

Gift Card Programs also provide the flexibility to add integrators. Adding integrators allows you to lend permission so another party can create and issue Gift Cards from your Gift Card Program. 
For more information on this functionality, please contact hello@lightrail.com.

#### Common Requests in Gift Card Integration

| Action                        | Type   | Body                                                                                                                | Endpoint                            | 
|:------------------------------|--------|---------------------------------------------------------------------------------------------------------------------|-------------------------------------|
| Create Gift Card              | `POST` | `{'userSuppliedId':'gc1', 'cardType':'GIFT_CARD', 'programId':'program-123', 'initialValue':500, 'currency':'USD'}` | `/v1/cards`                         | 
| Retrieve `fullcode`           | `GET`  |                                                                                                                     | `/v1/cards/<cardId>/fullcode`       |
| Check Balance                 | `GET`  |                                                                                                                     | `/v1/codes/<fullcode>/card/balance` |
| Create Transaction            | `POST` | `{'userSuppliedId':'tx1', 'value':-10, 'currency':'USD'}`                                                           | `/v1/codes/<fullcode>/transactions` | 
| Retrieve Transaction History  | `GET`  |                                                                                                                     | `/v1/codes/<fullcode>/transactions` | 

Note, there may be additional endpoints you'd need to use depending on your use-case. 

### Use-Case 2: Power an Account Credits or Points Program

Another common use-case is one in which Lightrail manages value attached to your customers. 

For example, suppose you'd like to enable your users to earn points within your platform.
Once a user has earned enough points perhaps you'd like to allow them to spend their points and choose a reward.
In this use-case, you would create a Contact in the Lightrail API followed by creating an Account Card for that Contact.
This Account Card would be used to track the customer's points. 
As a customer earns or spends value, you would add or subtract value by creating Transactions against the Account Card in the Lightrail API.

#### Common Requests in Account Credits or Points Program

| Action                             | Type   | Body                                                                                               | Endpoint                                                                 | 
|:-----------------------------------|--------|----------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------|
| Create Contact                     | `POST` | `{'userSuppliedId':'ct1', 'email':'name@example.com'}`                                             | `/v1/contacts`                                                           | 
| Create Account Card                | `POST` | `{'userSuppliedId':'ac1', 'cardType':'ACCOUNT_CARD', 'contactId':'contact-123', 'currency':'XXX'}` | `/v1/cards`                                                              | 
| Check Balance                      | `GET`  |                                                                                                    | `/v1/cards/<cardId>/balance`                                             |
| Create Transaction                 | `POST` | `{'userSuppliedId':'tx1', 'value':-10, 'currency':'XXX'}`                                          | `/v1/cards/<cardId>/transactions`                                        | 
| Retrieve Transaction History       | `GET`  |                                                                                                    | `/v1/cards/<cardId>/transactions`                                        | 
| Retrieve Account Card from Contact | `GET`  |                                                                                                    | `/v1/cards?contactId=<contactId>&currency=<currency>&cardType=ACCOUNT_CARD`|

Note, in these example calls, the currency is set to `XXX` to represent points although `USD` or any other valid currency code could be used if you wanted to track a dollar figure.
Also, in this sort of use-case, we recommend storing the Account Card's `cardId` with your customer's account within your application. 
This will remove an unnecessary lookup each time you want to update a customer's points.

If this use-case fits your need for points, a great way to reward your customers is offer them gift cards from top brands through <a href="https://www.giftbit.com" target="_blank">Giftbit’s Rewards API.</a>

<a name="create-and-attach-promotions-anchor"></a>
## Create and Attach Promotions

In addition to the many capabilities Lightrail provides out of the box, Lightrail gives you the ability to attach Promotions to Cards, allowing you to incentivize and motivate your users or recipients. 
Promotions work by adding temporary value to your cards with special redemption rules. 
Promotions can be used in both the gift card and account credit use-cases. 

Promotions are created from a Promotion Program which can be created from the <a href="https://www.lightrail.com/app/#/promotions" target="_blank">Promotions</a> section of the Lightrail web application. 
Similar to a Gift Card Program, they enable you to restrict promotion properties such the allowed value ranges, currency, and custom expiry. 
A Card is always created with a `PRINCIPAL` ValueStore. When promotions are attached, they are represented as `ATTACHED` ValueStores.

### How Promotions Fit Into the Lightrail Object Model

For example, suppose you've created a $10 USD Card. It could be either a Gift Card or an Account Card. Below is a model of the newly created objects.

![Promotion Object Model Part1](http://resources.giftbit.com/api/embeddedimages/Lightrail_Promitions_Object_Model_p1.png)

Perhaps you want to add a $5 promotion that will be valid for the next 10 days to incentivize the recipient to spend their value. As you can see below, a new ValueStore is attached to Card. 

![Promotion Object Model Part2](http://resources.giftbit.com/api/embeddedimages/Lightrail_Promitions_Object_Model_p2.png)

Suppose the recipient spends $8 USD within the 10-day limit. Below you can see how the balances of the ValueStore and in turn the balance of the Card is affected.

![Promotion Object Model Part3](http://resources.giftbit.com/api/embeddedimages/Lightrail_Promitions_Object_Model_p3.png)

This example illustrates that when Transactions are created against Cards that have multiple ValueStores, any valid ValueStore will be transacted against in order of expiry. 
As you can see, Lightrail automatically handles the complexity of taking value from the correct ValueStore.

### Redemption Rules

Redemption rules are a powerful feature of Lightrail which enable setting extra conditions on promotions. 
When transacting against a card, redemption rules determine whether or not a promotion can be used for the transaction. 
The rules operate on the transaction details including `metadata`. 
The `metadata` field is a flexible JSON structure that can store any additional information about a transaction such as its payment method or cart items. 
Redemption rules, therefore, can unlock powerful marketing promotions such as: "$5 off when you buy a red hat," or "$20 off if you spend more than $100."

Check out the extended <a href="https://github.com/Giftbit/Lightrail-API-Docs/blob/master/feature-deep-dive/RedemptionRules.md" target="_blank">redemption rules documentation</a> for more information.

## Implementation Details

### Coding for Idempotency and the `userSuppliedId` Field

The API is fully idempotent on the `userSuppliedId` field, which is a required parameter for all endpoint operations that result in a change of state on the Lightrail side (POST, PUT, PATCH).  
Therefore, in the case of a non-received response from the API or other unknown condition, it is safe and recommended to retry the request with the same `userSuppliedId`.

For example, your customer is using a `fullcode` in your checkout.  
You POST to the _/codes/{fullcode}/transactions_ endpoint to mark the use, but get a network timeout and are unable to process the response. 
As a result, your system doesn't know if Lightrail successfully received your POST and recorded the transaction. 
With idempotency, you do not need any lookups or other complicated error handling to see if the original API call succeeded. 

You can simply retry the same call (as many times as needed) with the same `userSuppliedId`, and get the same 200 response upon success whether or not a previous request went through. 
Our server will do the operation once and only once for that `userSuppliedId`.

Given the above, it is important you think about how to structure your `userSuppliedId`s and error handling so that you are always sending a unique value for different logical requests, but the same one for any retries.  

Attempting to reuse a `userSuppliedId` but providing different request parameters will result in a 409 error.

### About Currency Value, Currency Type, and No-Currency Use (Such as Points)
Where currency type (eg. USD, CDN, AUD) is required or returned, the API expects/uses 3 character uppercase codes conforming to the ISO-4217 standard. 
Lightrail does not do any currency conversion nor does currency influence internal behavior; rather, currency allows you to issue and track cards in different currencies as you choose.

In all cases where value is concerned, you need to provide the amount in the smallest currency unit. 
For most, this is the amount in cents (or pence, penny, or similarly named unit). For example, to create a Card for USD1.00, you would set the initialValue=100 (100 cents).

For zero-decimal currencies or use with non-currency applications such as points, use the regular whole denomination. 
For example, for ¥1, you should set initialValue=1 (1 JPY), since ¥1 is the smallest currency unit.

### About Dates
The API expects all dates in request parameters to conform to the ISO-8601 format, specifically "yyyy-MM-dd'T'HH:mm:ss.SSSZ".  You can see examples in this documentation.

This allows you to control things such as Code expiry in fine granularity to the time zone of your choosing. 
All responses will always be given in this same format.

### Handling Error Responses

Clients should always check the HTTP status code of the response and act accordingly if the response is not a 200.

Error response JSON will be in the following format:
- status: (number) - will match the HTTP response code.    
- message: (string, optional) - a descriptive error message if one is available
- code: (string, optional) - a code that can be provided to Lightrail support if troubleshooting help is needed

#### Example error responses:
| Status | Description         | Message                                                                                                                                          | 
|:-------|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| `400`  | bad request         | Failed to create card due to error during code creation. Response from code creation: Bad Request. Missing Required Parameter 'initialValue'.    | 
| `401`  | unauthorized        | Unauthorized.                                                                                                                                    |
| `409`  | idempotency failure | A different transaction with the same userSuppliedId already exists.                                                                             |
| `429`  | throttled failure   | Too many requests.                                                              

### Legal Responsibilities
The Lightrail API provides flexibility to implement multiple currency solutions (gift cards, unique promo codes, credit refunds, etc). 
It is the responsibility of the API user and their organization to understand and follow the jurisdictions and laws that govern all aspects of their implementation.

### A Note on the Documentation
This interactive documentation will provide full code samples in many popular languages and let you try out all functionality.

The API Blueprint file that produces this documentation is in GitHub at https://github.com/Giftbit/Lightrail-API-Docs. 
If you find a mistake or have a suggestion for improvement, please submit it directly via pull request.
