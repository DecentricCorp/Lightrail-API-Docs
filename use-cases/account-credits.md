# Account Credits Powered by Lightrail

## Introduction

This document is a quick step-by-step guide to implementing account credits powered by Lightrail. The focus will be on implementing this use-case by programmatically calling the Lightrail API. 

- If you would like to learn more about Lightrail concepts, check out the section on Lightrail Object Model in the [Lightrail API Docs](https://www.lightrail.com/docs/).
- If you are looking for an implementation of this use-case in a specific language or technology, check out the section on Client Libraries and Integrations in the [Lightrail API Docs](https://www.lightrail.com/docs/).

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

The first step to start an account credit is to create a new account. Based on your business workflows this is triggered by events such as signing up a new customer. After determining the currency for the new account, proceed with the following steps: 

- Create a new Contact using the [Contacts API Endpoint](#contacts-endpoint) if one does not already exist. Generally, you should specify some basic information about the Contact such as name and email.
- Create an Account Card for that Contact using the [Cards API Endpoint](#cards-endpoint). You need to provide the contact ID and specify the currency. Optionally, you can also specify an initial value for the card.

We recommend that you persist the Contact ID in your system so that you can find the Lightrail Contact corresponding to your customer easily. Alternatively, you can search for a Contact based on the `userSuppliedId` as will be discussed later. You do not need to persist the Account Cards IDs of a Contact since you can retrieve them by calling the API. Persisting these IDs, however, can help you avoid making that additional API call.

### Retrieving a Contact's Account Cards

In most business workflows, you usually have access to the customer ID (e.g. from login information). If you persist the Contact ID in your database as, or alongside, the customer ID, you can easily retrieve the Card IDs for the customer in question by providing the Contact ID using the [Cards API Endpoint](#cards-endpoint).  If your system supports multiple currencies, you also need to identify the effective currency of your workflow and pass it as an additional parameter to the Card search endpoint in order to uniquely retrieve the Account Card for that particular currency. 

### Balance-Check

For checking the balance of an account you can simply call the [Card Balance API Endpoint](#cards-balance-endpoint) and provide the corresponding Card ID. 

### Funding and Charging

Transactions to fund or charge an account are posted to the corresponding Account Card by providing its Card ID and using the [Cards Transaction API Endpoint](#cards-transaction-endpoint). If you do not have the Account Card ID directly in your workflow, you need to retrieve it using the API and based on the Contact ID as discussed above.

To fund an account, post a Transaction with a positive value to the corresponding Account Card. Depending on your business model, this usually takes place when the customer earns some reward based on some activity. To charge an account, post a Transaction against the corresponding Account Card with a negative value. The most common use-case for charging an account is redeeming the value at the store checkout. For the details on the redemption use-case, check out our detailed document on [Redemption at Checkout](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/giftcode-checkout.md).

## API Endpoints

### Contacts Endpoint

#### Creating a New Contact

To create a new Contact, you need to provide a client-side unique identifier known as the `userSuppliedId`. The `userSuppliedId` is a per-endpoint unique identifier used to ensure idempotence. Ensuring idempotence means that if the same request is issued more than once, it will not result in repeated actions. Optionally, you can also provide an `email`, `firstName`, and `lastName`. Here is a sample request:

```javascript
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

```javascript
GET https://www.lightrail.com/v1/contacts/{contactId}
```

Alternatively, you can retrieve a contact based on its `userSuppliedId` by making a `GET` call to the same endpoint as a search:

```javascript
GET https://www.lightrail.com/v1/contacts?userSuppliedId={userSuppliedId}
```

Although the response to this call is a search result object with an array of Contacts, if you provide a `userSuppliedId` you are guaranteed to get one `contact` object if it exists. Here is a sample response:

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

For creating a new Account Card, you need to provide the `contactId` of the Contact with whom the card will be associated and specify the card `currency`. Note that since gift cards use the same endpoint, you have to also specify the `cardType` as  `ACCOUNT_CARD`.

```javascript
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
    "currency": "USD",
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

You can call the Cards Endpoint as a search to retrieve all the account Cards beloging to a Contact:

```javascript
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
    "currency": "USD",
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

```javascript
GET https://api.lightrail.com/v1/cards?cardType=ACCOUNT_CARD&currency=USD&contactId={contactId}
```

### Cards Balance Endpoint

You can use the card balance endpoint to check the available value of an Account Card by providing the `cardId`. If you do not have the `cardId`, you can retrieve it based on the customer's `contactId` by calling the [Cards API Endpoint](#cards-endpoint), as discussed above.

```javascript
GET https://api.lightrail.com/v1/cards/{cardId}/balance
```
Usually, the balance is returned in the `currentValue` field of the `principal` object. If you are using attached promotions (e.g. temporary promotional values), these additional values are returned in the attached objects in the response. In such cases, the effective balance is the available value in its principal value store, plus the sum of all attached values that are currently active. So, in order to get the effective available balance of the account you need to use the following logic:

```php
    $accountBalance = $response['balance']['principal']['currentValue'];
    foreach ($response['balance']['attached'] as $attachedValue) {
      if ($attachedValue['state'] === 'ACTIVE')
      	$accountBalance += $attachedValue['currentValue'];
    }
```

Here is a sample response from this endpoint:

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
    "balanceDate": "2017-07-27T23:26:06.079Z",
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

### Cards Transaction Endpoint 

You can transact against an Account Card by providing the corresponding `cardId`, the transaction `value`, and its `currency`, as well as a `userSuppliedId`. The `userSuppliedId` is a per-endpoint unique identifier, used to ensure idempotence. Ensuring idempotence means that if the same request is issued more than once, it will not result in repeated actions. 

If you do not have the `cardId` you can retrieve it based on the customer's `contactId` using the [Cards API Endpoint](#cards-endpoint), as discussed above.

```javascript
POST https://api.lightrail.com/v1/cards/{cardId}/transactions
{
  "userSuppliedId": "tx-fe2d",
  "value" : 120,
  "currency": "USD"
}
```

The returned object includes both the `userSuppliedId` and a server-generated `transactionId` which you can use later to retrieve this transaction.

```json
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

For drawdown transactions, Lightrail supports the preauthorize-capture flow. By setting the value of the `pending` attribute, you can tell Lightrail to create a pending Transaction. The funds for a pending Transaction are withheld until it is _captured_ or _voided_ later:

```javascript
POST https://api.lightrail.com/v1/cards/{cardId}/transactions
{
  "userSuppliedId": "tx-fe2d",
  "value" : 120,
  "currency": "USD",
  "pending" : true
}
```

From the response to this call, you need to save the `transactionId` of the pending Transaction while you will need later, in order to `void` or `capture` it by calling one of the following methods:

```javascript
POST https://api.lightrail.com/v1/cards/{cardId}/transactions/{transactionId}/void
{
  "userSuppliedId":"tx0771"
}
```

```javascript
POST https://api.lightrail.com/v1/cards/{cardId}/transactions/{transactionId}/capture
{
  "userSuppliedId":"tx0771"
}
```

The response object from these calls will be a new Transaction object with a new `transactionId`. For reference, the ID of the original pending transaction is also included as `parentTransactionId` in the response object to these calls:

```json
{
    "transaction": {
        "transactionId": "transaction-axx7",
        "value": 101,
        "userSuppliedId": "tx0771",
        "dateCreated": "2017-07-12T00:22:48.379Z",
        "transactionType": "PENDING_VOID",
        "transactionAccessMethod": "CARDID",
        "giftbitUserId": "user-08",
        "cardId": "card-1dea",
        "currency": "USD",
        "parentTransactionId": "transaction-4xx6",
        "metadata": {
            "giftbit_initial_transaction_id": "transaction-46"
        }
    }
}
```
