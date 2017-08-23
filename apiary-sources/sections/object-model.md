<a name="object-model-anchor"></a>
## The Lightrail Object Model
The full API object model is here for your reference. We will discuss these objects and their relationships in this section. Note that depending on your use-case you may only need some of these objects and the corresponding endpoints. If you prefer to start with something more hands-on, feel free to move on to [Common Use-Cases](#use-cases-anchor).   


![Lightrail Object Model](https://giftbit.github.io/Lightrail-API-Docs/assets/lightrail-objects.svg)

### Core Objects: Lightrail Cards and Value Stores: 

The Card is the core concept in the Lightrail model and provides the main interface for storing, maintaining, and interacting with any sort of value that your business wishes to issue. Currently, there are two types of cards in Lightrail, _Gift Cards_ and _Account Cards_, which are distinguished based on the value of the `cardType` attribute on the `card` object. We will discuss these two types of cards in a bit.

A Card aggregates different values each of which is represented by a _Value Store_. Value Stores represent a specific instance of issued value and its attributes, such as its amount and validity period.  

Each Card has at least one Value Store, known as the `principal` Value Store which holds the primary value of the Card and is issued at the time of card creation. Cards may also have many `attached` value stores which are created and attached to the Card in the course of different promotional programs. 

While attached Value Stores are somewhat temporary, the principal Value Store is tied to the Card throughout its lifetime. Attached Value Stores are added to the Card as different promotional programs are created and may eventually expire or get canceled. But if the Principal Value Store of a Card is expired or canceled, the Card itself is also considered expired or canceled.

For example, a customer can buy a gift card with a principal value of $30 which never expires. Later, and in order to encourage the recipient to spend the gift value,  you may attach a $5 promotional value to this Card as part of your _Back to School_ campaign, only valid in the last week of August. While this attached Value Store is active, the Card holder can spend $35 with the Card; when the attached Value Store expires at the end of August, the principal Value Store will still be valid and the Card can still spend up to $30.


### Programs
A Lightrail _Program_ is a template for issuing Lightrail values, i.e. Value Stores. Programs specify the general attributes of Value Stores that derive from them, such as currency, validity period, minimum/maximum amount, as well as the constraints that apply to spending them, known as _Redemption Rules_.

Lightrail recognizes that issuing value seldom happens in isolation and is usually part of a broader organized context which is encapsulated by the concept of Program. Therefore, when creating a new Value Store, you always need to specify its reference  `program` object to which it belongs. As an analogy, think of Programs as minting facilities and Value Stores as coins. Just as valid coins can only be created by a minting facility, Lightrail values can only be issued as part of a Program and are subject to its broader rules and restrictions. 

Lightrail currently supports two types of Programs which are differentiated based on their `type` attribute: 

- _Principal Programs_ are used to organize and create  `principal` Value Stores, namely to create new Cards, and
- _Promotional Programs_ are used to create `attached` Value Stores which can be added to existing cards and provide some additional promotional value to the card holder subject to more restrictive conditions.

Note that since Cards need to have at least one Value Store, i.e. the principal Value Store, Cards are connected to Programs through their Value Stores and cannot exist in isolation. Therefore, in order to create a Card, you need at least one Principal Program to create the Principal Value Stores of your Cards (which happens automatically at the time of Card creation). 

Since Lightrail does not handle currency exchange, as a general integrity constraint, it expects all the Value Stores in a Card to have the same currency, i.e. derive from Programs with the same currency. For example, if you create a Card with the principal Value Store in CAD, all subsequent attached promotions must derive from CAD Programs.

Programs are also a great way to organize, track, and analyze values. For example, you probably want to know how many people took advantage of your _Back to School_ promotions and how it affected your sales. The Lightrail Web App provides various reports, stats, and analyses for the values created in each Program. 

Currently, you can only create programs using the Lightrail Web App but the API has [an endpoint](#get-programs-anchor) for programmatically retrieving a list of your Programs.

### Account Cards and Contacts

Account Cards represent values associated with an individual customer, known in Lightrail as a _Contact_. Account Cards can essentially be thought of as a customer's account, making them suitable for implementing customer account credit or points programs. 

To further facilitate this, Lightrail enforces a one-card-per-currency-per-customer constraint on Account Cards so that each Contact can only have one account per each currency. This assumption makes handling transactions against account credits simpler as will be discussed in the [Account Credit Use-Case](#use-cases-account-credits-anchor).

Unlike Gift Cards, Account Cards do not have a `fullcode` and interaction with their value is only possible via the Card object interface.

Note that in order to simplify issuing them, unlike Gift Cards, Lightrail does not require specifying a Program for Account Cards creation. To keep these calls simpler, Lightrail creates a default Program under the hood for each currency; the principal Value Store of all of the Account Cards in each currency are derived from that default Program. 

### Gift Cards 

As the name implies, Gift Cards represent a value created as a gift. Lightrail Gift Cards also have a `fullcode`,  a confidential, unique, and unguessable alpha-numeric code, which can be used as the evidence of possession of the Gift Card by its recipient. Lightrail allows balance-checking and value redemption based on the `fullcode` to facilitate the checkout use-case in which the recipient of the Gift Card would enter the `fullcode` to redeem its value towards a purchase.

Since knowing the the `fullcode` implies possession of the Card, the `fullcode` is often delivered to the Gift Card's recipient in confidence. To further ensure the confidentiality of the `fullcode` and to minimize the risk of accidentally revealing it in the course of passing JSON objects around and to the browser, aside from [the one endpoint](#get-fullcode-anchor) designated specifically to retrieving the `fullcode`, no other Lightrail API endpoint returns the `fullcode` in its response object. For the same reasons, we also recommend that you do not persist the `fullcode` in your system.

While Contacts are not mandatory for Gift Cards, it is possible, and recommended to associate a Gift Card with a Contact if you know the individual who will receive of the Gift Card. This will enable you to keep track of all Lightrail values available to a customer both programmatically via the API and in the Lightrail Web App.

### Transactions

Various interactions with the Lightrail system take place in the form of _Transactions_. The most common such Transactions are _funding_, _drawdown_, and _refund_. Some other interactions such as Card or Value Store _activation_, _cancellation_, and _freezing_/_unfreezing_ are also modelled as Transactions.

Lightrail also supports a two-step _pending_ drawdown. A pending drawdown Transaction withholds the funds temporarily until eventually they are collected via a subsequent _capture_ Transaction, or canceled via a _void_ Transaction. 

Posting Transactions against a Card is [primarily](#post-transaction-by-cardid-anchor) done via its `cardId`. However, to facilitate processing Gift Card redemption at the checkout which is one of the most common Gift Card use-cases, Lightrail also provides [an endpoint](#post-transaction-by-fullcode-anchor) for posting Transactions against a Card by its `fullcode`. To improve security, this endpoint only allows drawdown Transactions.

One of the features of the Lightrail API is encapsulating the Card Value Stores behind a simple interface at the time of Transaction. While you can add many promotional attached Value Stores to Cards, at Transaction time, you do not need to worry about the logic of splitting the drawdown value against potentially many Value Stores; Lightrail transaction processing logic automatically handles that logic for you. 

For example, if there is $30 in the principal Value Store and a $5 attached Value Store from a promotional _Back to School_ program, when attempting a $8 drawdown, Lightrail automatically decides the break-down of this amount against existing Value Stores and you do not have to specify or even be aware of them. In this case, for example, Lightrail will prioritize the spending of the $5 value which is closer to its expiry date, and then, charges the remaining $10 from the principal Value Store.  

### Redemption Rules

Redemption Rules are a powerful feature of Lightrail which enable setting various sophisticated conditions in promotional programs, and thereby, on Value Stores that are derived from them. Redemption Rules can unlock powerful marketing promotions such as, "$10 off if you spend at least $100," or "$15 off if you buy two or more pairs of jeans." Currently, you can define such Redemption Rules at the time of creating a new Lightrail Program in the Lightrail Web App. 

When transacting against a Card, Redemption Rules determine whether or not each of the Card's Value Stores is redeemable in paying towards the transaction value. Every rule is essentially a Boolean expression which will be evaluated against the `metadata` provided by the transaction; the Value Store will be available for spending on that Transaction only if the rule evaluates to `true`. For example, the rule for a $5 promotion value if the customer spends at least $100 can be written as:

`metadata.cart.total >= 10000` 

in which the `cart` is a custom JSON object provided by your system as part of the `metadata` on Transactions. When this rule is added to a Program, a $5 Value Store derived from this Program will only be available to those Transactions which come with a `cart` object in which the `total` attribute is greater than or equal to $100 (i.e. 10000 cents). The following diagram depicts this process. Check out the in-depth <a href="https://github.com/Giftbit/Lightrail-API-Docs/blob/master/feature-deep-dive/RedemptionRules.md" target="_blank">Redemption Rules documentation</a> for further details.

![Transaction, Value Stores, and Redemption Rules](https://giftbit.github.io/Lightrail-API-Docs/assets/transaction-valustores.svg)

