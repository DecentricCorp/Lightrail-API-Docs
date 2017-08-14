### Create Account Card [POST /cards]
Create a Card of type `ACCOUNT_CARD` which is associated with an existing Contact.

---
+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
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

    + Body
    
            {
              "userSuppliedId":"accountCard1",
              "cardType":"ACCOUNT_CARD",
              "contactId":"contact-0cfafcd94b2d42a698771a7b63e37c86",
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
                "cardId":"card-d4e04d050acf48e1ae63657f4c710abf",
                "userSuppliedId":"giftcard1",
                "contactId":"contact-0cfafcd94b2d42a698771a7b63e37c86",
                "dateCreated":"2017-07-28T21:59:15.325Z",
                "categories":[
                  {
                    "categoryId":"category-597f1f7f98b64285b7763548f83fff67",
                    "key":"city",
                    "value":"seattle"
                  },
                  {
                    "categoryId":"category-3d4ff98bf941489e8216c9ffb9d4efc3",
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

            Authorization: Bearer <YOUR_ACCESS_TOKEN>
    + Attributes 
        + userSuppliedId (string, required) - Unique idempotency ID for the request.
        + cardType (string, required) - GIFT_CARD
        + initialValue (number, optional) - If not provided, will default to 0.
        + currency (string) - {{currency}}
        + programId (string, required) - The Lightrail Program ID.
        + categories (object, optional) - An object of key-value pairs. For example: `'categories': {'city':'Seattle', 'country':'USA'}`.
        + contactId (string, optional) - Note, the Contact must be created before the request to create the card.
        + expires (string, optional) - Defaults to never expires.
        + startDate (string, optional) - The date for which the ValueStore will become useable.
        + inactive (boolean, optional) - If `true` the Card's `PRINCIPAL` ValueStore will have an `INACTIVE` balance.

    + Body
    
            {
              "userSuppliedId":"giftcard1",
              "cardType":"GIFT_CARD",
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
                "cardId":"card-d4e04d050acf48e1ae63657f4c710abf",
                "userSuppliedId":"giftcard1",
                "contactId":null,
                "dateCreated":"2017-07-28T21:59:15.325Z",
                "categories":[
                  {
                    "categoryId":"category-597f1f7f98b64285b7763548f83fff67",
                    "key":"city",
                    "value":"seattle"
                  },
                  {
                    "categoryId":"category-3d4ff98bf941489e8216c9ffb9d4efc3",
                    "key":"giftbit_order",
                    "value":"2017-07-28"
                  }
                ],
                "cardType":"GIFT_CARD",
                "currency":"USD"
              }
            }

