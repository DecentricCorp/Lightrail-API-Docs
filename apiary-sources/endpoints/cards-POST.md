### Create Account Card [POST /cards]
Create a Card of type `ACCOUNT_CARD` which is associated with an existing Contact.

---
+ Request (application/json)
    + Headers
    
            {{header.authorization}}
            
    + Attributes 
        + userSuppliedId (string, required) - {{userSuppliedId}}
        + cardType (string, required) - ACCOUNT_CARD
        + currency (string, required) - {{currency}}
        + initialValue (number, optional) - If not provided, will default to 0.
        + categories (object, optional) - An object of key-value pairs. For example: `'categories': {'city':'Seattle', 'country':'USA'}`.
        + contactId (string, required) - Note, the Contact must be created before the request to create the card.
        + expires (string, optional) - Defaults to never expires.
        + startDate (string, optional) - The date for which the associated ValueStore will become usable.
        + inactive (boolean, optional) - If `true` the Card's `PRINCIPAL` ValueStore will have an `INACTIVE` balance.
        + metadata (Metadata, optional) - {{metadata}}
        
    + Body
    
            {
                "userSuppliedId":"accountCard1",
                "cardType":"ACCOUNT_CARD",
                "contactId":"contact-0cxx86",
                "currency":"USD",
                "initialValue":500,
                "categories": {
                    "city":"seattle"
                }
            }
        
+ Response 200
    + Attributes
        + card (Card)
        
    + Body

            {
              "card":{
                "cardId":"card-d4xxbf",
                "userSuppliedId":"giftcard1",
                "contactId":"contact-0cxx86",
                "dateCreated":"2017-07-28T21:59:15.325Z",
                "categories":[
                  {
                    "categoryId":"category-59xx67",
                    "key":"city",
                    "value":"seattle"
                  },
                  {
                    "categoryId":"category-3dxxc3",
                    "key":"giftbit_order",
                    "value":"2017-07-28"
                  }
                ],
                "cardType":"ACCOUNT_CARD",
                "currency":"USD"
              }
            }
            

### Create Gift Card [POST /cards]
Create a Card of type `GIFT_CARD`.

---
+ Request (application/json)

    + Headers

            {{header.authorization}}
            
    + Attributes 
        + userSuppliedId (string, required) - {{userSuppliedId}}
        + cardType (string, required) - GIFT_CARD
        + initialValue (number, optional) - If not provided, will default to 0.
        + currency (string) - {{currency}}
        + programId (string, required) - The Lightrail Program ID.
        + categories (object, optional) - An object of key-value pairs. For example: `'categories': {'city':'Seattle', 'country':'USA'}`.
        + contactId (string, optional) - Note, the Contact must be created before the request to create the card.
        + expires (string, optional) - Defaults to never expires.
        + startDate (string, optional) - The date for which the ValueStore will become usable.
        + inactive (boolean, optional) - If `true` the Card's `PRINCIPAL` ValueStore will have an `INACTIVE` balance.
        + metadata (Metadata, optional) - {{metadata}}

    + Body
    
            {
                "userSuppliedId": "account-d37e",
                "programId": "program-06d0",
                "initialValue" : 5000,
                "cardType" : "GIFT_CARD",
                "startDate" : "2017-08-01T00:27:02.910Z",
                "expires" : "2017-10-01T00:27:02.910Z", 
                "metadata": {
                    "orderId": "x72a3sx5e",
                    "transactionId": "tr2re3t0y2r3u0w6r",
                    "recipientEmail" : "test@test.ca",
                    "purchaserName" : "Alice Liddell",
                    "purchaserEmail" : "alice@wonderland.ca"
                 }
            }
        
+ Response 200
    + Attributes
        + card (Card)
        
    + Body

            {
                "card":{
                    "cardId": "card-6207",
                    "userSuppliedId": "account-d37",
                    "contactId": null,
                    "dateCreated": "2017-08-01T22:15:42.123Z",
                    "categories":[
                        {
                            "categoryId": "category-94d0",
                            "key": "giftbit_order",
                            "value": "2017-08-02"
                        },
                        {
                            "categoryId": "category-67fe",
                            "key": "giftbit_program",
                            "value": "program-06d0"
                        }
                    ],
                    "cardType": "GIFT_CARD",
                    "currency": "USD"
                }
            }

