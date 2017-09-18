# Card Details

## Introduction

This document is a quick step-by-step guide to retrieving Card details. The focus will be on implementing this use-case by programmatically calling the Lightrail API. 

- To learn more about Lightrail concepts, check out the section on the Lightrail Object Model in the [Lightrail API Docs](https://www.lightrail.com/docs/). 
- If you are looking for an implementation of this use-case in a specific language or technology, check out the section on Client Libraries and Integrations in the [Lightrail API Docs](https://www.lightrail.com/docs/).

## Concepts

It is common for businesses to provide a page in their online store where the recipient of a Gift Cards or a customer with an Account Credit can check its available value. This is usually a simple number indicating the Card's balance. Lightrail Cards, however, support a lot more than that. Aside from the principal value, a Lightrail Card can also have many attached promotions and some of these promotions may only be available under certain conditions detailed in their Redemption Rules. For example, a Gift Card can have a $30 principal value and an additional $5 attached promotion which is only available in a Transaction in which the customer buys two or more pairs of jeans.

By calling one of Lightrail's Card Detail Endpoints you can fetch all this information about a Card and show it to the user as you see fit. 

## Card Details

There are two Card detail Endpoints to fetch Card details based on either a `cardId` or `fullcode`:

```javascript
GET https://api.lightrail.com/v1/cards/{cardId}/details
GET https://api.lightrail.com/v1/codes/{fullcode}/details
```

For Gift Cards, you would usually use the one based on `fullcode` because that is what the customer will provide on a web form on your website. For Account Cards, you usually have the Account Card's `cardId` or if you do not directly persist that, you can use the API to retrieve the `cardId` of a customer's Account Card for a given currency based on the customer's Contact identifier. For further details on this check out the Lightrail [Account Credits Implementation Guide](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/account-credits.md).  

The response to these calls includes a Card `details` object similar to the following:

```json
{  
  "details":{  
    "valueStores":[  
      {  
        "valueStoreType":"PRINCIPAL",
        "value":3000,
        "state":"ACTIVE",
        "expires":null,
        "startDate":null,
        "programId":"program-1dxxa9",
        "valueStoreId":"value-2fxxf2",
        "restrictions":[]
      },
      {  
        "valueStoreType":"ATTACHED",
        "value":500,
        "state":"ACTIVE",
        "expires":null,
        "startDate":null,
        "programId":"program-c7xxe6",
        "valueStoreId":"value-79xxee",
        "restrictions":[  
          "If your cart total is at least $100."
        ]
      }
    ],
    "currency":"USD",
    "cardType":"GIFT_CARD",
    "asAtDate":"2017-09-14T18:09:09.520Z",
    "cardId":"card-6dxx89"
  }
}
```

This response shows that the principal value of the Card is $30 and there is an attached promotions with no expiry date which is only redeemable in a Transaction in which the cart total is at least $100. This can be shown to the customer using an interface similar to the following:

![redemption-seq-diagram](https://giftbit.github.io/Lightrail-API-Docs/assets/card-details.png)