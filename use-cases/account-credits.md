# Account Credits Powered by Lightrail

## Introduction

This document is a quick guide to implementing account credits powered by Lightrail. The focus of this document is implementing these use-cases by directly calling the Lightrail API. If you are looking for client libraries specific to a language or technology, check out our [integration projects](https://github.com/Giftbit/Lightrail-API-Docs/blob/usecases/Integrations.md).

## Concepts

Many business use-cases such as rewards programs require tracking values attached to a customer in the form of an account. This could be in the form of real currency values or virtual values such as points. Lightrail supports this by introducing two inter-connected concepts:

- **Contact** which represents a customer to whom the values are associated. 
- **Account Card** which represents an account, with a specific currency, which belongs to a specific Contact.

Cards provide a vehicle for implementing an account. Account creation, balanc-checking, and transacting against a customer account is, therefore, simply done by interacting with the Card. By associating different account Cards to a Contact, multiple accounts can be assigned to a given customer.

Each account Card has a fixed currency which is specified at the time of its creation. Since Lightrail does not support currency exchange, different currency values should each be stored in a separate account Card. Therefore, a Contact may have many Account Cards for different currencies —but no more than one account Card per currency. 

For example, a Contact may have two different accounts for `USD` and `CAD` values, as well as a points account (for which the standard currency symbol is `XXX`) to track reward points. Each of these accounts is represented by an account Card associated with that Contact.



## Use-Cases

In this section we review the overall steps for common account credit use-cases. Details for each API endpoint and request/response examples are given in the next section.

### Creating Account Credits

The first step to start an account credit is to create a new account. Based on your business workflows this is triggered by events such as signing up a new customer. After determining the currency for the new account, creating a new account requires the following steps: 

- Creating a new Contact using the [Contacts API Endpoint](#contacts-endpoint) if one does not already exist. Generally, you should also specify some basic information about the Contact such as name and email.
- Creating an Account Card for that Contact using the [Cards API Endpoint](#cards-endpoint). You need to provide the contact ID and specify the currency. Optionally, you can also specify an initial value for the card.

It is important to store the Contact ID after creating a Contact but you do not need to persist the Card IDs for the cards associated with the contact, as you can retrieve them using the API.  

### Retrieving a Contact's Account Cards

The [Cards API Endpoint](#cards-endpoint) allows searching for cards which can be used for retrieving all the account cards associated with a Contact. You can also request to find the Contact's card for a particular currency. 

### Balance-Check

As account credits are implemented as special virtual cards, checking the balance of an account is simply done by calling the [Card Balance API Endpoint](#cards-balance-endpoint). You will need to provide the account card ID to this endpoint in order to check the balance. 

Usually, in your business flow, you have the customer ID and you need to check the balance of that customer's account. So, you need to retrieve the corresponding account ID based on the customer's Contact ID first as discussed above, and then use the card ID to check the account's balance.

### Funding and Charging

Transactions to fund or charge an account are posted to the corresponding card by providing its Card ID and using the [Cards Transaction API Endpoint](#cards-transaction). If you do not have the account card ID directly in your business flow, you need to retrieve it based on the Contact ID as discussed above.

Funding an account is basically posting a transaction with a positive value to the corresponding card. It usually takes place when the customer earn some reward based on some activity which depends on your business model, such as spending more than a certain threshold in the store.

Charging an account is similarly done by posting a transaction against its corresponding account card with a negative value. The most common use-case for charging an account is redeeming the accumulated value at the store checkout. For the details on the redemption use-case, check out our detailed document on [Redemption at Checkout](https://github.com/Giftbit/Lightrail-API-Docs/blob/usecases/use-cases/giftcode-checkout.md).

## API Endpoints

### Contacts Endpoint

#### Creating a New Contact

To create a new contact, you need to at least provide a client-side unique identifier for the contact as the `userSuppliedId`. The `userSuppliedId` is a per-endpoint unique identifier, used to ensure idempotence. Ensuring idempotence means that if the same request is issued more than once, it will not result in repeated actions. Optionally, you can also provide an `email`, `firstName`, and `lastName`. Here is a sample request:

```json
POST https://www.lightrail.com/v1/contacts
{
  "userSuppliedId": "customer-9f50629d",
  "email": "test@test.ca",
  "firstName": "Test",
  "lastName": "McTest",
}
```

Sample response:

```json
{
  "contact":{
    "contactId": "contact-271a",
    "userSuppliedId": "customer-9f50629d",
    "email": "test@test.ca",
    "firstName": "Test",
    "lastName": "McTest",
    "dateCreated": "2017-07-26T23:50:04.000Z"
  }
}
```

The response objects will include both the `userSuppliedId` and a server-generated `contactId` which you can persist and use to retrieve the Contact later.

#### Retrieving a Contact

You can retrieve a Contact based on its `contactId` by directly making a `GET` call to the Contacts API Endpoint. The response to this call will be a `contact` object similar to the one shown above. 

```json
GET https://www.lightrail.com/v1/contacts/{contactId}
```

Alternatively, you can retrieve a contact based on its `userSuppliedId` by making a `GET` call to the same endpoint as a search:

```json
GET https://www.lightrail.com/v1/contacts?userSuppliedId={userSuppliedId}
```

Although the response to this call is a search result object with an array of `contact` objects, if you provide a `userSuppliedId` you are guaranteed to get at most one `contact` object. Here is a sample response:

```json
{
  "contacts":[
   {
    "contactId": "contact-271a",
    "userSuppliedId": "customer-9f50629d",
    "email": "test@test.ca",
    "firstName": "Test",
    "lastName": "McTest",
    "dateCreated": "2017-07-26T23:50:04.000Z"
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

### Cards Endpoint

#### Creating a New Account Card

For creating a new account card, you need to provide the `contactId` of the Contact with whom the card will be associated and specify the card `currency`. Note that since gift cards use the same endpoint, you have to also specify the `cardType` as  `ACCOUNT_CARD`.

```json
POST https://api.lightrail.com/v1/cards
{
  "userSuppliedId": "account-d37e",
  "contactId": "contact-271a",
  "cardType": "ACCOUNT_CARD",
  "currency": "USD"
}
```

The response objects will include both the `userSuppliedId` and a server-generated `cardId` which you can persist and use to retrieve the Account Card later.

```json
{
  "card":{
    "cardId": "card-1dea",
    "userSuppliedId": "account-d37e",
    "contactId": "contact-271a",
    "dateCreated": "2017-07-26T23:50:04.572Z",
    "cardType": "ACCOUNT_CARD",
    "currency": "USD"
    "categories":[
     {
      "categoryId": "category-bd12dd18dfc14089b0e59ec90eb10388",
      "key": "giftbit_order",
      "value": "2017-07-26"
     },
     {
      "categoryId": "category-956471f4bf82468bb3767f93fe814fc2",
      "key": "giftbit_program",
      "value": "program-account-USD-user-088e-TEST"
     }
    ]
  }
}
```

#### Retrieving a Contact's Cards

You can call the Cards Endpoint as a search to retrieve a Contact's Cards:

```json
GET https://api.lightrail.com/v1/cards?cardType=ACCOUNT_CARD&contactId={contactId}
```

The response is in the form of search results which includes an array of `card` objects similar to the following:

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
      "categoryId": "category-bd12dd18dfc14089b0e59ec90eb10388",
      "key": "giftbit_order",
      "value": "2017-07-26"
     },
     {
      "categoryId": "category-956471f4bf82468bb3767f93fe814fc2",
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

Note that you can also specify the `currency` as a search parameter in which case you will be guaranteed to get at most one `card` object back in the results since there can only be one card per currency per contact:

```json
GET https://api.lightrail.com/v1/cards?cardType=ACCOUNT_CARD&currency=USD&contactId={contactId}
```

### Cards Balance Endpoint

You can use the card balance endpoint to check the available value of an account card by providing  account `cardId`. If you do not have the `cardId` you can find it by based on the customer's  `contactId`  using the [Cards API Endpoint](#cards-endpoint), as discussed above.

```json
GET https://api.lightrail.com/v1/cards/{cardId}/balance
```
The balance is returned in the `currentValue` field of the `principal` object.

```json
{
  "balance":{
    "principal":{
      "currentValue": 100,
      "state": "ACTIVE",
      "expires": null,
      "startDate": null,
      "programId": "program-account-USD-user-088e-TEST",
      "valueStoreId": "value-68ec"
    },
    "currency": "USD",
    "cardType": "ACCOUNT_CARD",
    "balanceDate": "2017-07-27T23:26:06.079Z"
    "attached":[],
   }
}
```

### Cards Transaction Endpoint 

You can transact against an account by providing the corresponding account `cardId`, the transaction `value`, and its `currency`, as well as a `userSuppliedId`. The `userSuppliedId` is a per-endpoint unique identifier, used to ensure idempotence. Ensuring idempotence means that if the same request is issued more than once, it will not result in repeated actions. 

If you do not have the `cardId` you can find it by based on the customer's  `contactId`  using the [Cards API Endpoint](#cards-endpoint), as discussed above.

```json
POST https://api.lightrail.com/v1/cards/{cardId}/transactions
{
  "userSuppliedId": "tx-fe2d",
  "value" : 120,
  "currency": "USD"
}
```

The returned object includes both the `userSuppliedId` and a server-generated `transactionId` which you can use later to retrieve this transaction.

```Json
{
  "transaction":{
    "transactionId": "transaction-fec7",
    "value": 120,
    "userSuppliedId": "tx-fe2d",
    "dateCreated": "2017-07-27T23:51:12.228Z",
    "transactionType": "FUND",
    "transactionAccessMethod": "CARDID",
    "giftbitUserId": "user-088e-TEST",
    "cardId": "card-1dea",
    "currency": "USD"
  }
}
```

#### Authorize-Capture 

If the transaction is a charge, i.e. its value is negative, you can follow a authorize-capture flow by specifying that the transaction is `pending`:

```json
POST https://api.lightrail.com/v1/cards/{cardId}/transactions
{
  "userSuppliedId": "tx-fe2d",
  "value" : 120,
  "currency": "USD",
  "pending" : true
}
```

You need to retain the `transactionId` for the pending transaction from the response object in order to `void` or `capture` it later by calling one of the following methods:

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

The response object to these calls will be a new `transaction` object with its own `transactionId`. The original pending `transactionId` is included as `metadata` in the response object:

```json
{
    "transaction": {
        "transactionId": "transaction-a7",
        "value": 101,
        "userSuppliedId": "tx0771",
        "dateCreated": "2017-07-12T00:22:48.379Z",
        "transactionType": "PENDING_VOID",
        "transactionAccessMethod": "CARDID",
        "giftbitUserId": "user-08",
        "cardId": "card-1dea",
        "currency": "USD",
        "parentTransactionId": "transaction-46",
        "metadata": {
            "giftbit_initial_transaction_id": "transaction-46"
        }
    }
}
```
