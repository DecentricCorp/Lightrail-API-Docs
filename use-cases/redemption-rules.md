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

For activating Redemption Rules in your system, you need to take two steps: 

- Add any information that can potentially be used in the Redemption Rules in the `metadata` of a Transaction. We suggest that you proactively include a set of standard metadata used in most common promotion rules as discussed below.
- Create a Promotion Programs with Redemption Rules and attach promotions to Gift Cards or Account Cards. This can be done in the Lightrail Web App.

Note that writing new Redemption Rules can be done by your marketing team using the Lightrail Web App while adding Transaction metadata requires that your developers change the software code in your Lightrail integration. So, to minimize future changes to your integration, try to be as general as possible in your initial design of Transaction metadata and provide all the information that could be useful for potential future Redemption Rules. We suggest that at minimum, you include the [Suggested Metadata Structure](#suggested-metadata-structure) in this document.

As an example, suppose you want to boost your sales by giving a $5 promotional value to Gift Card holders who will spend their gift value in a purchase of $100 or more. Here are the steps necessary to activate this promotion:

- Add the total purchase to your Transaction metadata. If you follow our [Suggested Metadata Structure](#suggested-metadata-structure) you already have this in `metadata.cart.total`.
- Create a Promotion Program using the Lightrail Web App with the following Redemption Rule: `metadata.cart.total >= 10000`
- Identify any Account Card or Gift Card that is eligible for this promotion and create $5 promotion based on this Program and attach it to the Card.


When a Transaction is requested against a Card with this promotion, the Lightrail backend evaluates the Redemption Rules for the promotion against the Transaction object, therefore checking whether the value of `metadata.cart.total` is greater than or equal to $100. If this is the case, the additional $5 promotional value will be unlocked and can be spent in that Transaction.

## Suggested Metadata Structure



- `cart`: the main object to record the shopping cart and its contents.
  - `total` (integer): the total value of the purchase.
  - `items ` (array of objects of type `item`): 
    - `item`: 
      - `id` (string): the product ID.
      - `quantity` (integer): the quantity.
      - `unit_price` (integer): the unit price of the item.
      - `categories` (array of string): 

```json
{
  "cart": {
    "total": 25335,
    "item": [
      {
        "id": "B000F34ZKS",
        "quantity": 1,
        "unit_price": 20695,
        "categories": [
          "gear", "outdoor", 
        ]
      },
      {
        "id": "B009L1MF7A",
        "quantity": 2,
        "unit_price": 2320,
        "categories": [
          "apparel", "outdoor", 
        ]
      }
    ]
  }
}
```



## Common Rule Examples

