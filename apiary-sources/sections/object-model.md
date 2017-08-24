<a name="object-model-anchor"></a>
## The Lightrail Object Model
The full API object model is here for your reference. We will discuss these objects and their relationships in this section. Depending on your use-case you may only need some of these objects and the corresponding endpoints. If you prefer to start with something more hands-on, feel free to move on to [Common Use-Cases](#use-cases-anchor).   


![Lightrail Object Model](https://giftbit.github.io/Lightrail-API-Docs/assets/lightrail-objects.svg)

### Core Objects: Lightrail Cards and Value Stores: 

The Card is the core concept in the Lightrail model and provides the main interface for storing, maintaining, and interacting with any sort of value that your business wishes to issue. Currently, there are two types of cards in Lightrail, _Gift Cards_ and _Account Cards_, which are distinguished based on the value of the `cardType` attribute on the `card` object. We will discuss these two types of cards in a bit.

A Card's value is stored in an object called _Value Store_ which represents a specific instance of issued value and its attributes, such as its amount and validity period.  

When a Card is created, a `principal` Value Store is automatically created and added to it. When additional promotions are added to a Card, they are represented as an `attached` Value Stores. Unlike `attached` Value Stores which are often short-lived, the `principal` Value Store is tied to the Card throughout its lifetime and represents the overall state of the Card. For example, if the Principal Value Store is expired or canceled, the Card is also considered expired to canceled.

For example, a customer can buy a gift card with a primary value of $30 which never expires. This is stored in its `principal` Value Store. Later, and in order to encourage the recipient to spend the gift value, you may attach a $5 promotional value to this Card as part of your _Back to School_ campaign, only valid in the last week of August. While this `attached` Value Store is active, the Card holder can spend $35 with the Card; when the attached Value Store expires at the end of August, the Principal Value Store will still be valid and the Card can still spend up to $30. See [the diagram below](#transaction-valustore-anchor) for a depiction of this example.


### Programs
A Lightrail _Program_ is a template for issuing Lightrail value, in the form of Value Stores. Programs specify the general attributes of Value Stores that derive from them, such as currency, validity period, minimum/maximum amount, as well as the constraints that apply to spending them, known as _Redemption Rules_.

Lightrail recognizes that issuing value seldom happens in isolation and is usually part of a broader context which we call a Program. Therefore, whenever a new Value Store is created (e.g. at the time of Card creation or attaching promotions) you need to specify the Program used for creating that Value Store. As an analogy, think of Programs as minting facilities and Value Stores as coins. Just as valid coins can only be created by a minting facility, Lightrail values can only be issued as part of a Program and are subject to its broader rules and restrictions. 

Lightrail currently supports two types of Programs which are differentiated based on their `type` attribute: 

- _Principal Programs_ are used to organize and create  `principal` Value Stores, namely to create new Cards, and
- _Promotional Programs_ are used to create `attached` Value Stores which can be added to existing cards and provide some additional promotional value to the card holder subject to more restrictive conditions.

Note that Cards are also connected to Programs through their Value Stores and cannot exist in isolation. Therefore, before you start creating Cards, you need to set up at least one Principal Program to be used for creating the Principal Value Stores of your Cards. The Principal Value Store is created automatically in the course of Card creation and you do not need to explicitly do create it, but you have to provide the `programId` in the Card creation request. Account Cards can be an exception to this as we will see in a bit.

Since Lightrail does not handle currency exchange, as a general integrity constraint, it expects all the Value Stores in a Card to have the same currency, i.e. derive from Programs with the same currency. For example, if you create a Card with the Principal Value Store in CAD, all subsequent attached promotions must derive from CAD Programs.

Programs are also a great way to organize, track, and analyze values. For example, you probably want to know how many people took advantage of your _Back to School_ promotions and how it affected your sales. The Lightrail Web App provides various reports, stats, and analyses for the values created in each Program. 

You can create programs using the Lightrail Web App; the API also has [an endpoint](#get-programs-anchor) for programmatically retrieving the list of your Programs.

### Contacts

Individual customers are represented by _Contact_ objects in Lightrail. You can store some basic information about the individual such as their name and email address on the Contact object. Contacts can be associated with Cards in order to track and analyze different Lightrail values held by a customer as we will discuss below.

### Gift Cards

As the name implies, Gift Cards represent a value created as a gift. Lightrail Gift Cards have a `fullcode`,  a  unique and unguessable alpha-numeric code which can be used by the Gift Card recipient to redeem its value.

Since anyone who knows the `fullcode` can redeem the Gift Card value, the `fullcode` is often delivered to the Gift Card's recipient in confidence. To minimize the risk of its exposure (e.g. in the course of passing JSON object to the browser) only [one specific Lightrail endpoint](#get-fullcode-anchor) returns the `fullcode` and other endpoints only return the last four characters of the code when necessary. For similar reasons, we recommend that you refrain from persisting the `fullcode` in your database or logs.

While Contacts are not mandatory for Gift Cards, it is possible, and recommended to associate a Gift Card with a Contact when you know the recipient. This will allow tracking all Lightrail values available to a customer both programmatically via the API and in the Lightrail Web App.

### Account Cards 

Account Cards represent values associated with an individual customer, represented by a linked Contact object. Account Cards can essentially be thought of as a customer's account, making them suitable for implementing customer account credit or points programs. Lightrail requires that a Contact has only one Account Card per currency. This makes handling transactions against account credits simpler as will be discussed in the [Account Credit Use-Case](#use-cases-account-credits-anchor).

Unlike Gift Cards, Account Cards do not have a `fullcode` and interaction with their value is only possible via the Card object interface.

To keep creation of Account Cards simpler, Lightrail does not require specifying a Program for Account Card creation and uses a default Program automatically created under the hood. The Principal Value Store of all of your Account Cards (in each currency) are derived from that default Program. 

### Transactions

Various interactions with the Lightrail system take place in the form of _Transactions_. Some common examples are _funding_, _drawdown_, and _refund_. Some other interactions such as Card or Value Store _activation_, _cancellation_, and _freezing_/_unfreezing_ are also modelled as Transactions.

Lightrail also supports a two-step _pending_ drawdown. A pending drawdown Transaction withholds the funds temporarily until eventually they are collected via a subsequent _capture_ Transaction, or canceled via a _void_ Transaction. 

Transactions are [primarily](#post-transaction-by-cardid-anchor) created by `cardId`.  But to simplify Gift Card redemption at the checkout, Lightrail also provides [an endpoint](#post-transaction-by-fullcode-anchor) for creating Transactions by a Card's `fullcode`. To improve security, this endpoint only allows drawdown Transactions.

One of the features of the Lightrail API is encapsulating the Card Value Stores behind a simple interface at the time of Transaction. While you can add many promotional attached Value Stores to Cards, at Transaction time, you do not need to worry about the logic of splitting the drawdown value against potentially many Value Stores; Lightrail transaction processing logic automatically handles that logic for you. 

For example, if there is $30 in the Principal Value Store and a $5 attached Value Store from a promotional _Back to School_ program, when attempting a $8 drawdown, Lightrail automatically decides the break-down of this amount against existing Value Stores and you do not have to specify or even be aware of them. In this case, for example, Lightrail will prioritize the spending of the $5 value which is closer to its expiry date, and then, charges the remaining $10 from the Principal Value Store.  

### Redemption Rules

Redemption Rules are a powerful feature of Lightrail which enable setting various sophisticated conditions defining when and under what conditions value can be spent. Redemption rules are defined on Programs and are applied to the Value Stores created from them.

Redemption Rules can unlock powerful marketing promotions such as, "$10 off if you spend at least $100," or "$15 off if you buy two or more pairs of jeans." Currently, you can define such Redemption Rules at the time of Program creation in the Lightrail Web App. 

When transacting against a Card and looking to collect the funds from its different Value Stores, Redemption Rules determine whether or not each of the Card's Value Stores is spendable for that Transaction. Every rule is a Boolean expression that operates on the Transaction request object's `metadata`; the Value Store will be available for spending on that Transaction only if the rule evaluates to `true`. 

Transaction `metadata` is a generic JSON object provided in the Transaction request object which represents any additional information you wish to provide and store, including contextual information on which basis redemption rules operate. This provides a very powerful and flexible mechanism to define any relevant metadata in the Transaction request and use this metadata to make decisions about unlocking promotional values. 

Check out the in-depth <a href="https://github.com/Giftbit/Lightrail-API-Docs/blob/master/feature-deep-dive/RedemptionRules.md" target="_blank">Redemption Rules documentation</a> for further details.

### Walk-Through Example

Suppose that you want to attach some promotions to some of your existing Gift Cards in order to encourage customers to spend them. Since you want to boost you sales, you want this promotional value to be available only to customers who would spend at least $100.

This rule can be formulated as the following. Note that `cart ` is a custom metadata object defined by you:

`metadata.cart.total >= 10000` 

After you create a new Promotion Program with this redemption rule, you can create a new $5 Value Store derived from this Program and attach it to some Gift Cards, thereby giving them a $5 promotional value subject to this redemption rule. 

At the checkout page, your e-commerece system examines the customer's cart and accordingly provides a `cart` object in the `metadata` attribute in the Transaction request object. For example:

```json
{"cart": 
 {
   "total":10350
 }
}
```

Once this Transaction request is received, Lightrail will iterate through the Card's Value Stores and evaluates their redemption rules against the Transaction `metadata`. In this case, if the `cart.total` is greater than or equal to $100 (i.e. 10000 cents), it unlocks the $5 Value Store for spending. The following diagram depicts this process.

<a name="transaction-valustore-anchor"></a>

![Transaction, Value Stores, and Redemption Rules](https://giftbit.github.io/Lightrail-API-Docs/assets/transaction-valustores.svg)

