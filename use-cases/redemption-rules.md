# Setting Up Lightrail Redemption Rules

## Introduction

This document is a step-by-step guide to setting up Lightrail Redemption Rules. 

- For a further details on how to write Redemption Rules check out [Redemption Rules Reference Documentation](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/feature-deep-dive/RedemptionRules.md).
- For a guide on adding promotions to Lightrail Cards, checkout the use-case guide for [Creating Lightrail Promotions](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/promotions.md).
- To learn more about Lightrail concepts, check out the section on the Lightrail Object Model in the [Lightrail API Docs](https://www.lightrail.com/docs/). 

## Concepts

Redemption Rules are a powerful feature of Lightrail which enable setting sophisticated conditions on how value can be spent. They can unlock powerful marketing promotions such as, "$10 off if you spend at least $100," or "$15 off if you buy two or more pairs of jeans."

Lightrail implements promotions as additional Value Stores that can be created and attached to an existing Card. Redemption rules are Boolean expressions which are defined at the time of creating a Promotion Program and apply to any Value Store derived from that Program. These rules set the conditions under which the Value Store can be unlocked for spending.

When transacting against a Card and looking into collecting funds from its Value Stores, Lightrail Transaction Manager evaluates each Value Store's Redemption Rule against the Transaction JSON object; the promotional value from that Value Store can be spent in that Transaction only if the rule evaluates to `true`. 

## Setting Up Your Redemption Rules

For activating Redemption Rules in your system, you need to take the following two steps: 

- Add any information that may be important in Redemption Rules in the `metadata` of Transaction requests. As discussed further below, we suggest that you proactively include a set of standard metadata most likely to be in your Redemption Rules. 
- Create Promotion Programs with Redemption Rules and attach promotions based on these Programs to Gift Cards or Account Cards. This can be done in the Lightrail Web App.

Collecting and including metadata in the Transaction request must be done programmatically in your integration module with Lightrail. It is important that you identify:

- What information needs to be included in the metadata, 
- what structure you will use to organize this information into a single `metadata` object, and
- how and from what source you can obtain this information. 

For example, some parts of the metadata, such as the ordered items and totals are usually available in your e-commerce platform in the form of a _cart_ or an _order_ object. For product labels and categories, you may have to fetch the _product_ object from your back-end database. Other information, such as the store and branch ID are part of the configuration of your online store.

Note that adding promotions with new Redemption Rules is a fairly easy task that can be done routinely by your marketing team using the Lightrail Web App. Adding new metadata and changing the Transaction request, on the other hand, requires that your software developer team update your Lightrail integration code which can be very costly and time-consuming. So, to minimize future developer involvement, try to be as general and proactive as possible in your initial design of Transaction metadata and provide all of the information that could potentially be used in future Redemption Rules. We suggest that at minimum, you include the [Sample Metadata Structure](#sample-metadata-structure) in this document.

### Balance-Check

Since availability of attached promotions depend on their Redemption Rules, the total available balance of a Lightrail Card varies depending on the context of the Transaction. For example, if a customer have a Card with $10 principal value and a  $5 attached promotion subject to some Redemption Rules, the total available value of the Card can be $10 or $15 depending on the metadata of the Transaction. Therefore, when checking the available Balance of a Card, you need to provide the full context by providing a complete `metadata` object. Lightrail `dryRun` endpoints are designed to make this possible as you will see in the Walk-Through Example below.

### Creating Transactions

All of the Transaction creation endpoints in Lightrail accept a `metadata` attribute to provide the metadata for the requested Transaction. In the case of split-tender where you need to post a pending Transaction first and capture it later, the metadata must be included on _both_ the initial pending Transaction and the capture Transaction request. Note that Lightrail evaluates the metadata again at the time of capture and if the Redemption Rules are not met (because of missing or incomplete metadata) the call to capture will fail. Check out our implementation guide for [Redeeming Lightrail Value at Checkout](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/giftcode-checkout.md) for further details about handling split-tender.

### Walk-Through Example

As an example, suppose you want to boost your sales by giving an additional $5 promotional value to customers who make a purchase of at least $100. Here are the steps necessary to activate this promotion:

- Make sure you have the total purchase value in your Transaction metadata. If you follow our [Suggested Metadata Structure](#suggested-metadata-structure) you already have this as `metadata.cart.total`.
- Create a Promotion Program using the Lightrail Web App with the following Redemption Rule: `metadata.cart.total >= 10000`.
- Attach a $5 promotion based on this Program to the Account Card of eligible customers.

#### Balance-Check

At the checkout, you check the available balance of the Card by sending a `dryRun` request with the value of `nfs` set to `false`. This tells the Lightrail to return a best-effort would-be Transaction instead of an error if there is not sufficient funds in the Card. 

For example, assuming the customer wants to check out a cart with $253.35 balance, the `dryRun` request will look like the following. This will ask the Lightrail API to simulate a Transaction with the value of $253.35 on the provided Gift Card and in case of insufficient funds, return the maximum the Card can spend in this Transaction: 

```javascript
POST https://www.lightrail.com/v1/codes/{fullcode}/transactions/dryRun
{
  "userSuppliedId":"order-s3xx30",
  "value":-27655,
  "currency":"USD",
  "nsf": false,
  "metadata": {
    "cart": {
      "total": 27655,
      "items": [
        {
          "id": "B000F34ZKS", //tent
          "quantity": 1,
          "unit_price": 20695,
          "tags": [
            "gear", "outdoor", "clearance", "Coleman"
          ]
        },
        {
          "id": "B009L1MF7A", //jacket
          "quantity": 3,
          "unit_price": 2320,
          "tags": [
            "apparel", "outdoor", "Klymit"
          ]
        }
      ]
    }  
  } 
}
```

The response will be similar to the following. Note that this is just a simulation and is NOT an actual Transaction and it does not have a `transactionId`. As you can see, in this case, the maximum value this Card can provide is $55. You can also see in the `transactionBreakdown`,  this is coming two different Value Stores, one with $50 value (which is the principal Value Store of the Card) and the other with $5 value (which is the attached promotion).

```javascript
{  
  "transaction":{  
    "value":-5500,
    "userSuppliedId":"order-s3xx30",
    "dateCreated":null,
    "transactionType":"DRAWDOWN",
    "transactionAccessMethod":"RAWCODE",
    "valueAvailableAfterTransaction":0,
    "cardId":"card-dcxx37",
    "currency":"USD",
    "transactionBreakdown":[  
      {  
        "value":-500,
        "valueAvailableAfterTransaction":0,
        "valueStoreId":"value-024e3a9902f341eb8983f4e7db64e76c"
      },
      {  
        "value":-5000,
        "valueAvailableAfterTransaction":0,
        "valueStoreId":"value-663b1647d8d547199b7879010439d6f2"
      }
    ],
    "transactionId":null,
    "metadata":{  
      "cart":{  
        "total":27655,
        "items":[  
          {  
            "quantity":1,
            "id":"B000F34ZKS",
            "unit_price":20695,
            "tags":["gear","outdoor","clearance","Coleman"]
          },
          {  
            "quantity":3,
            "id":"B009L1MF7A",
            "unit_price":2320,
            "tags":["apparel","outdoor","Klymit"]
          }
        ]
      }
    },
    "codeLastFour":"YNJC"
  }
}
```

If you send this request with different metadata, you may get a different result. For example, suppose that the customer removes one of the items from the cart, therefore changing the cart and cart total to $23.20:

```javascript
POST https://www.lightrail.com/v1/codes/{fullcode}/transactions/dryRun
{
  "userSuppliedId":"order-s3xx30",
  "value":-6960,
  "currency":"USD",
  "nsf": false,
  "metadata": {
    "cart": {
      "total": 2320,
      "items": [
        {
          "id": "B009L1MF7A", //jacket
          "quantity": 3,
          "unit_price": 2320,
          "tags": ["apparel", "outdoor", "Klymit"]
        }
      ]
    }  
  } 
}
```

 In this case, you see that the maximum the Card can pay for this Transaction is only $50. If you examine the `transactionBreakdown`, you can see only one $50 Value Store is spendable in this Transaction. This is because the metadata of this Transaction did not meet the Redemption Rules of the other Value Store (the $5 promotion) which is only spendable if the cart total is $100 or more.

```javascript
{  
  "transaction":{  
    "value":-5000,
    "userSuppliedId":"order-s3xx30",
    "dateCreated":null,
    "transactionType":"DRAWDOWN",
    "transactionAccessMethod":"RAWCODE",
    "valueAvailableAfterTransaction":0,
    "cardId":"card-dcxx37",
    "currency":"USD",
    "transactionBreakdown":[  
      {  
        "value":-5000,
        "valueAvailableAfterTransaction":0,
        "valueStoreId":"value-66xxf2"
      }
    ],
    "transactionId":null,
    "metadata":{  
      "cart":{  
        "total":6960,
        "items":[  
          {  
            "quantity":3,
            "id":"B009L1MF7A",
            "unit_price":2320,
            "tags":["apparel","outdoor","Klymit"]
          }
        ]
      }
    },
    "codeLastFour":"YNJC"
  }
}
```



As you can see, since the order total is greater than $10, the Redemption Rule for the attached Value Store (`metadata.cart.total >= 10000 `) evaluates to `true` and this promotion is unlocked for this Transaction. 

#### Transaction

As you can see, there is $50 available on the Card but checkout balance is $69.60. To proceed, you need to: 

- Post a pending Transaction on the Card for its maximum available value for this Transaction, i.e. $50.
- Charge the rest of the checkout balance on another payment method, e.g. a credit card.
- If the third-party payment succeeds, _capture_ the pending Transaction on the Lightrail Card; otherwise, _void_ it and tell the customer the payment failed.

For further details about handling split-tender at the checkout, see our implementation guide for [Redeeming Lightrail Value at Checkout](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/giftcode-checkout.md).

The request to create the pending Transaction will be similar to the following:

```javascript
POST https://www.lightrail.com/v1/codes/{fullcode}/transactions
{
  "userSuppliedId":"order-s3xx30",
  "value":-5000,
  "currency":"USD",
  "pending": true,
  "metadata": {
    "cart": {
      "total": 2320,
      "items": [
        {
          "id": "B009L1MF7A",
          "quantity": 3,
          "unit_price": 2320,
          "tags": ["apparel", "outdoor", "Klymit"]
        }
      ]
    }  
  } 
}
```

This will lead to a very similar response to that of the `dryRun` endpoint, except that it will include the `transactionId` and `dateCreated`. 

After attempting to charge the third-party, you will have to _capture_ or _void_ the pending Transaction. These requests will be similar to the following. Note that you have to repeat the metadata in the _capture_ request since Lightrail will verify that once more time before capturing the Transaction.

```javascript
POST https://api.lightrail.com/v1/cards/{cardId}/transactions/{pendingTransactionId}/capture
{  
  "userSuppliedId":"order-s3xx30-capture",
  "metadata":{  
    "cart":{  
      "total":2320,
      "items":[  
        {  
          "id":"B009L1MF7A",
          "quantity":3,
          "unit_price":2320,
          "tags":["apparel","outdoor","Klymit"]
        }
      ]
    }
  }
}
```

```javascript
POST https://api.lightrail.com/v1/cards/{cardId}/transactions/{pendingTransactionId}/void
{
  "userSuppliedId":"order-s3xx30-void"
}
```

## Sample Metadata Structure

We suggest that you include the following metadata structure in your Transaction requests. This will cover the basic information needed for most common Redemption Rules. 

Note that these are just for your reference and you can pick and choose from these samples or add any additional attributes as you see fit for your use-cases. 

- `cart` (object): the main object to record the shopping cart and its contents.
  - `total` (integer): the total value of the purchase. Whether this is pre- or post-tax depends on your decision.
  - `items ` (array of objects of type `item`): 
    - `item`: 
      - `id` (string): the product ID.
      - `quantity` (integer): the quantity.
      - `unit_price` (integer): the unit price of the item.
      - `tags` (array of string): a list of product categories.

Additionally, if you foresee that you will have Redemption Rules based on the payment method or the origin of the Transaction, here are some additional suggested metadata:

- `origin` (object): metadata about the origin of the transaction
  - `store_id` (string): the store ID in which the Transaction takes place. 
  - `tags` (array of string): other attributes of the origin.
- `payment` (object):
  - `payment_method_id` (string): the ID of the payment method.
  - `tags` (array of string): other attributes of the payment.

Here is an example of the metadata based on this structure:

```javascript
{
  "cart": {
    "total": 25335,
    "items": [
      {
        "id": "B000F34ZKS", //tent
        "quantity": 1,
        "unit_price": 20695,
        "tags": [
          "gear", "outdoor", "clearance", "Coleman"
        ]
      },
      {
        "id": "B009L1MF7A", //jacket
        "quantity": 2,
        "unit_price": 2320,
        "tags": [
          "apparel", "outdoor", "Klymit"
        ]
      }
    ]
  },
  "origin": {
  	"store_id": "A210",
    "tags": [
      "warehouse", "CA"
    ]
  },
  "payment":{
    "payment_method_id": "stripe",
    "tags": []
  }  
}
```

## Common Rule Examples

Here are some examples of common Redemption Rules based on the above metadata structure. Note that you can straightforwardly combine these rules using logical operators to create more complex rules.

We will provide examples for a $5 off subject to some Redemption Rule. Note that once you create a Promotion Program with the Redemption Rule, you can attach any value based on that Promotion Program to a Card. So, the $5 value in this section is only an example. 

#### Minimum Transaction Value

 Promotional value is available only if the Transaction value exceeds a minimum amount. For example, $5 off if you spend more than $100:

```javascript
metadata.cart.total >= 10000
```

#### Specific Product(s) 

You restrict a promotional value to a specific product. For example:

- $5 off if you buy a 3-person tent (with the product ID `B000F34ZKS`):

```javascript
//specific product
metadata.cart.items.some(item => item.id=='B000F34ZKS')
```
#### Specific Product Category

You can restrict a promotional value to a specific Product category.   

-  $5 off if you buy an _outdoor_ product:

```javascript
//specific product category
metadata.cart.items.some(item => item.tags.some(tag => tag=='outdoor'))
```

By dynamically tagging some products for a promotion, this mechanism can be used to dynamically enable a promotion for a group of products:

```javascript
//specific product category
metadata.cart.items
  .some(item => item.tags.some(tag => tag=='back-to-school-sale'))
```

You can also set more complicated rules based on product categories and create more powerful promotion rules. For example:  

-  $5 off if you buy an _outdoor_ product not in _clearance_:

```javascript
//specific product category
metadata.cart.items
  .some(item => (item.tags.some(tag => tag=='outdoor') 
                 && !item.tags.some(tag => tag=='clearance')))
```

#### Minimum Quantity

These rules require that the quantity of a particular item or category of items in the cart exceeds a minimum number in order for the promotional value to be available. For example:

- $5 off if you buy 3 or more pairs of any types of Jeans:

```javascript
//minimum product cateogry quantity
metadata.cart.items
  .filter(item => (item.tags.some(tag => tag=='jeans'))
  .map(item => item.quantity)
  .sum() >= 3
```

#### Restrictions on Payment

In some use-cases a promotional value depends on the conditions around the payment such as the payment method or other payment attributes. 

Note that in this context, payment refers to the third-party payment in split-tender where the remainder of an order is paid by a payment method other than Lightrail. In order to provide this metadata, your workflow should allow the user to commit to the third-party payment method before posting the Lightrail pending Transaction; otherwise, you will not be able to provide the right metadata to unlock such promotions. For example:

- $5 off if you pay with debit:

```javascript
metadata.payment.tags.some(tag => tag == 'debit')
```

#### Restrictions on Origin

If you have multiple stores or operate in different regions, you can set Redemption Rules based on different attributes on the origin of the Transaction such as type of store, region, or store ID. For example:

- $5 off if you make a purchase in the North Vancouver branch (with store ID `A210`): 

```javascript
metadata.origin.store_id == 'A210'
```

- $5 off if you order from any warehouse branch in California:

```javascript
metadata.origin.tags.some(tag => tag == 'warehouse' && tag == 'CA')
```



