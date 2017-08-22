<a name="object-model-anchor"></a>
## The Lightrail Object Model
The full API object model is here for your reference. We will briefly discuss these objects and their relationships in this section. Note that depending on your use-case you may only need some of these objects and the corresponding endpoints. If you prefer to start with something more hands-on, feel free to move on to [Common Use-Cases](#use-cases-anchor).   


![Lightrail Object Model](https://giftbit.github.io/Lightrail-API-Docs/assets/lightrail-objects.svg)

### Core Objects: Lightrail Cards and Value Stores: 

The Card is the core concept in the Lightrail model and provides the main interface for storing, maintaining, and interacting with any sort of value that your business wishes to issue. Currently, there are two types of cards in Lightrail, _Gift Cards_ and _Account Cards_, which are distinguished based on the value of the `cardType` attribute on the `card` object. We will discuss these two types of cards in a bit.

A Card aggregates different values each of which is represented by a `valueStore` object. _Value Stores_ represent a specific instance of issued value and its attributes, such as its amount, validity period, and currency.

Each Card has at least one value store, known as the `principal` value store which holds the primary value of the Card and is issued at the time of card creation. Cards may also have many `attached` value stores which are created and attached to the Card as part of different promotional programs. 

Attached value stores may come and go as you create different promotional programs based on your business model. These attached value stores are added to the Card, and may eventually expire or get canceled. The principal value store, however, lasts throughout the lifetime of the Card. Once the principal value store is expired or canceled, the Card is also considered expired or canceled.

For example, a customer can buy a gift card with a principal value of $30 which never expires. Later, and in order to encourage the recipient to spend the gift value,  you may add an attached a promotional $5 value to this card as part of your _Back to School_ campaign, only valid for the last week of August. While this attached value store will expire at the end of August, the principal valueStore will still retain its value.


###Programs
A Lightrail _Program_ is a template for issuing Value Stores which specifies their general attributes, such as currency, validity period, minimum/maximum amount, and the constraints that apply to spending them, known as _redemption rules_. 

Lightrail recognizes that issuing value seldom happens in isolation and is usually part of a broader organized context which is encapsulated in the concept of Program. Therefore, when creating a new Value Store, you always need to specify its reference  `program` object to which it belongs. As an analogy, think of Programs as minting facilities and Value Stores as coins. Valid coins can only be created by a minting facility and are subject to its broader rules and restrictions.

Programs are broadly divided into two types which is encoded in their `type` attribute: 

- _Principal Programs_ are used to organize and create  `principal` value stores, namely to create new cards, and
- _Promotional Programs_ are used to create `attached` value stores which can be added to existing cards and provide some additional promotional value to the card holder.

Programs are also an great way to group, organize, and analyze values. For example, you may want to know how many people took advantage of your _Back to School_ promotions and how it affected your sales. The Lightrail Web App provides various stats and analyses for the values created in each Program. 

Currently, you can only create programs using the Lightrail Web App.

Related Endpoints:

### Transactions

Transactions are 

### Gift Cards 

As the name implies, Gift Cards represent a value created as a gift. Lightrail Gift Cards have a `fullcode`,  a confidential, unique, and unguessable alpha-numeric code, which can be used as the evidence of possession of the Gift Card by its recipient. Lightrail allows balance-checking and value redemption based on the `fullcode`. This facilitates the checkout use-case in which the recipient of the Gift Card would manually enter the `fullcode` to redeem its value towards a purchase.

Related Endpoints:

### Account Cards and Contacts 
Account Cards represent a value associated with an individual customer, known in Lightrail as a _Contact_. An Account Card can essentially be thought of as a customer's account, and therefore, are suitable for implementing customer account credit or points programs. To further facilitate this model, Lightrail enforces a one-card-per-currency-per-customer constraint on Account Cards so that each Contact can only have one account per each currency. This assumption makes handling transactions against account credits simpler as will be discussed in the [Accounts Credit Use-Case](#use-cases-account-credits-anchor).
Unlike Gift Cards, Account Cards do not have a `fullcode` and interactions with their value is only possible via the Card object interface.

Related Endpoints:



{#

### Lightrail Cards and Value Stores: The Core Objects

The `card` is the core concept in the Lightrail model and provides the main interface for 
storing, maintaining, and interacting with any sort of value that your business wishes to issue.

A `card` aggregates different types of values each of which is modeled as a `valueStore`. 
Each card has at least one value store, known as the `principal` value store which holds the primary 
value of the card and is issued at the time of card creation. Each card may also have many `attached` value stores 
which are created and attached to the card as part of different promotional programs.

For example, a customer can buy a gift card with a principal value of USD 30 which never expires. 
Later, and in order to encourage the recipient to spending the gift value,  
you can add an attached value of USD 5 to this card as part of your _Back to School_ campaign and 
only valid for the last week of August. 
While this attached `valueStore` will expire at the end of August, the principal `valueStore`
will still maintain its value.   

Attached value stores can come and go as you create different promotional programs suitable 
for you business, but the principal value store remains throughout the lifetime of the card. 
Once the principal value store is expired or canceled, the card is considered expired or canceled.

###Programs
Lightrail `programs` are templates for issuing values by specifying overall attributes 
of the value, such as its currency, its validity period, and the constraints that apply to its spending, known as 
_redemption rules_.

There are two types of Cards: Gift Cards and Account Cards. 
These are both represented as a Card but with a different `cardType` attribute. 

#### Gift Cards

Gift Cards, as you might guess, are used when implementing a gift card program. Gift Cards have a `fullcode` which is a unique and unguessable alpha-numeric code that can be distributed to a recipient. 
In a standard gift card program, the recipient would manually enter the `fullcode` during the checkout process.

#### Account Cards

Account Cards represent value that is attached to a Contact. 
They are used when implementing a customer account credit or points program and can be thought of as a customer's account.
Since Account Cards do not have a `fullcode`, their value is interacted with using the Card object. 
#}