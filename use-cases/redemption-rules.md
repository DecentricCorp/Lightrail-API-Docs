# Setting Up Lightrail Redemption Rules

## Introduction

This document is a step-by-step guide to setting up Lightrail Redemption Rules. 

- For a further details on how to write Redemption Rules check out [Redemption Rules Reference Documentation](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/feature-deep-dive/RedemptionRules.md).
- For a guide on adding promotions to Lightrail Cards, checkout the use-case guide for [Creating Lightrail Promotions](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/promotions.md).
- To learn more about Lightrail concepts, check out the section on the Lightrail Object Model in the [Lightrail API Docs](https://www.lightrail.com/docs/). 

## Concepts

Redemption Rules are a powerful feature of Lightrail which enable setting sophisticated conditions on how value can be spent. They can unlock powerful marketing promotions such as, "$10 off if you spend at least $100," or "$15 off if you buy two or more pairs of jeans."

Lightrail implements promotions as additional Value Stores that can be created and attached to an existing Card. Redemption rules are boolean expressions defined by a Program and are applied to any Value Store created from that Program. These rules set the conditions under which the Value Store can be spent.

When transacting against a Card and looking to collect the funds from its Value Stores, Lightrail Transaction Manager evaluates the each Value Store's Redemption Rule against the Transaction JSON object; only if the rule evaluates to `true` the promotional value from that Value Store can be spent for that Transaction. 

## Setting Up Your Redemption Rules

For activating a Promotion with Redemption Rules, you need to take two steps: 

- Add any information that can be the basis of making decisions in Redemption Rules in the `metadata` of Transaction requests. As discussed further below, we suggest that you proactively include a set of standard metadata most likely to be in your Redemption Rules. 
- Create a Promotion Programs with Redemption Rules and attach promotions based on that Program to Gift Cards or Account Cards. This can be done in the Lightrail Web App.

Collecting and including metadata in the Transaction request must be done programmatically in your integration module with Lightrail. It is important that you identify:

- What information needs to be included in the metadata, 
- what structure you will use to organize this information into a single `metadata` object, and
- how and from what source you can obtain this information. 

For example, some parts of the metadata, such as the order items and totals are usually available in your e-commerce platform in the form of a _cart_ or an _order_ object. For product categories, you may have to fetch the _product_ object from your backend. Other information, such as the store and branch ID are part of the configuration of your online store.

Note that new Redemption Rules can be written by your marketing team using the Lightrail Web App which is a simple task, while changing the Transaction metadata requires that your software developer team update your Lightrail integration code which can be very costly and time-consuming. So, to minimize future developer involvement, try to be as general and proactive as possible in your initial design of Transaction metadata and provide all the information that could potentially be used in future Redemption Rules. We suggest that at minimum, you include the [Sample Metadata Structure](#sample-metadata-structure) in this document.

### Balance-Check

Since the availability of attached promotions depend on the Redemption Rules, the total available balance of a Lightrail Card depends on contextual information on which the evaluation of the Redemption Rules depends. For example, if a customer have a Card with $10 principal value and a  $5 attached promotion subject to some Redemption Rules, the total available value of the Card can be $10 or $15 depending on the metadata of the Transaction. 

Therefore, in order to check the total available balance of a Card when you need to provide the full context of the transaction by providing a complete `metadata` object in the Transaction object when requesting for the balance. 

### Creating Transactions

You can use the `metadata` attribute in the Transaction request object to include the metadata for a Transaction on any of the Transaction endpoints in Lightrail. 

In the case of split-tender where you need to post a pending Transaction first, and capture it later, the metadata must be included on _both_ the initial pending Transaction and the capture Transaction request. Note that Lightrail evaluates the metadata again at the time of capture and if the Redemption Rules is not met (because of missing or incomplete metadata) the call to capture will fail. Check out our implementation guide for [Redeeming Lightrail Value at Checkout](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/giftcode-checkout.md) for further details about handling split-tender.

### Walk-Through Example

As an example, suppose you want to boost your sales by giving an additional $5 promotional value to customers who make a purchase of at least $100. Here are the steps necessary to activate this promotion:

- Add the total purchase value to your Transaction metadata. If you follow our [Suggested Metadata Structure](#suggested-metadata-structure) you already have this in `metadata.cart.total`.
- Create a Promotion Program using the Lightrail Web App with the following Redemption Rule: `metadata.cart.total >= 10000`.
- Attach a $5 promotion based on this Program to the Account Card of eligible customers.

#### Balance-Check

When a user is at the checkout, you can send a balance-check request including the metadata as the following:

```Json
POST //....
{
  "metadata": {
    "cart": {
      "total": 25335,
      "items": [
        {
          "id": "B000F34ZKS", //tent
          "quantity": 1,
          "unit_price": 20695,
          "categories": [
            "gear", "outdoor", "clearance", "Coleman"
          ]
        },
        {
          "id": "B009L1MF7A", //sleeping bag
          "quantity": 2,
          "unit_price": 2320,
          "categories": [
            "apparel", "outdoor", "Klymit"
          ]
        }
      ]
    },
    "origin": {
      "country": "CA",
      "region": "BC",
  	  "city": "Vancouver",
  	  "store_id": "A210",
  	  "branch_id": null
    },
    "payment":{
      "payment_method_id": "stripe",
      "payment_attributes": null
    }  
  } 
}
```

 The response will be similar to the following:

```json

```

As you can see, since the order total is greater than $100 so the redemption rule for the attached Value Store (`metadata.cart.total >= 10000 `) evaluates to `true` and this promotion is unlocked for this Transaction. 

#### Transaction

Since the available value is not sufficient to cover the entire Transaction, we need to use the entire Lightrail value and charge the remaining balance to another payment method. For this purpose, we first post a pending Transaction on the Card for the entire value it can pay, then charge the remainder of a third-party payment method. If the third-party payment is successful, we will call to capture the Lightrail pending Transaction; otherwise we will void the pending Transaction. Check out our implementation guide for [Redeeming Lightrail Value at Checkout](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/giftcode-checkout.md) for further details about handling split-tender.

Posting Pending Transaction:

Posting Capture:

## Sample Metadata Structure

We suggest that you include the following metadata and in the following structure in your Transaction requests. This will cover the basic information needed for most common Redemption Rules. 

Note that these are just for your reference and you can pick and choose from these samples as you see fit for your use-cases. 

- `cart` (object): the main object to record the shopping cart and its contents.
  - `total` (integer): the total value of the purchase. Whether this is pre- or post-tax depends on your decision.
  - `items ` (array of objects of type `item`): 
    - `item`: 
      - `id` (string): the product ID.
      - `quantity` (integer): the quantity.
      - `unit_price` (integer): the unit price of the item.
      - `categories` (array of string): a list of product categories.
- `origin` (object): metadata about the origin of the transaction
  - `country` (string): the country in which the Transaction takes place.
  - `region` (string): the region in which the Transaction takes place.
  - `city` (string): the city in which the Transaction takes place.
  - `store_id` (string): the store ID in which the Transaction takes place. 
  - `origin_attributes` (array of string): other attributes of the origin.
- `payment` (object):
  - `payment_method_id` (string): the ID of the payment method.
  - `payment_attributes` (array of string): other attributes of the payment.

Here is an example of the metadata based on this structure:

```json
{
  "cart": {
    "total": 25335,
    "items": [
      {
        "id": "B000F34ZKS",
        "quantity": 1,
        "unit_price": 20695,
        "categories": [
          "gear", "outdoor", "clearance", "Coleman"
        ]
      },
      {
        "id": "B009L1MF7A",
        "quantity": 2,
        "unit_price": 2320,
        "categories": [
          "apparel", "outdoor", "Klymit"
        ]
      }
    ]
  },
  "origin": {
    "country": "CA",
  	"region": "BC",
  	"city": "Vancouver",
  	"store_id": "A210",
    "origin_attributes": [
      "major"
    ]
  },
  "payment":{
    "payment_method_id": "stripe",
    "payment_attributes": null
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

#### Specific Product 

You restrict a promotional value to a specific product. For example:

- $5 off if you buy a 3-person tent (with the product ID `B000F34ZKS`):

```javascript
//specific product
metadata.cart.items.exists(item => item.id=='B000F34ZKS')
```
#### Specific Product Category

You can restrict a promotional value to a specific Product category.   

-  $5 off if you buy an _outdoor_ product:

```javascript
//specific product category
metadata.cart.items.exists(item => item.categories.exists(category=>category=='outdoor'))
```

By dynamically labeling some products for a promotion, this mechanism can be used to dynamically enable a promotion for a group of products:

```javascript
//specific product category
metadata.cart.items.exists(item => item.categories.exists(category=>category=='back-to-school-sale'))
```

You can also set more complicated rules based on product categories and create more powerful promotion rules. For example:  

-  $5 off if you buy an _outdoor_ product not in _clearance_:

```javascript
//specific product category
metadata.cart.items.exists(item => (item.categories.exists(category=>category=='outdoor') && !item.categories.exists(category => category=='clearance')))
```

#### Minimum Quantity

These rules require that the quantity of a particular item or category of items in the cart exceeds a minimum number in order for the promotional value to be available. For example:

- $5 off if you buy 3 ore more pairs of Jeans:

```javascript
metadata.cart.items.filter(item => item.id == 'jeans').map(item => item.quantity).sum() >= 3
```

#### Restrictions on Payment

In some use-cases a promotional value depends on the conditions around the payment such as the payment method and other payment attributes. Note that the payment in this context refers to the case of split-tender where the remainder of an order is paid by a payment method other than Lightrail. In order to provide this metadata, your workflow should allow the user to commit to the third-party payment method before posting the Lightrail pending Transaction; otherwise, you will not be able to implement this use-case. For example:

- $5 off if you pay with debit:

```javascript
metadata.payment.payment_attributes.exists(attribute => attribute == 'debit')
```

#### Restrictions on Origin

If you have multiple stores or operate in different regions, you can make some promotions available only in specific locations or regions. For example:

- $5 off if you make a purchase in the North Vancouver branch (with store ID `A210`): 

```javascript
metadata.origin.store_id == 'A210'
```

- $5 off if you purchase from the warehouse:
- ​

```javascript
metadata.origin.origin_attributes.exists(attribute => attribute == 'warehouse')
```



