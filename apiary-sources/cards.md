## Cards [/cards/]
The _/cards_ endpoint is for use by your system to create and manage your Gift Cards and Account Cards.
The `cardId` should be stored as it's used in many subsequent requests - such as transaction against a Card. 
In use cases where the recipient requires a gift code, the `fullcode` should be supplied. 

---
### List Cards [GET /cards/{?limit}{?offset}{?categoryKey}{?categoryValue}{?contactId}{?cardType}{?currency}{?userSuppliedId}]
Retrieve a paginated list of Cards.

---
+ Request (application/json)
    + Headers
    
             Authorization: Bearer <YOUR_ACCESS_TOKEN>
   
+ Parameter
    + limit (number, optional) - For pagination.  The maximum number of results to return at once. Default 100.
    + offset (number, optional) - For pagination. The offset of the first results in the total results. Default 0.
    + categoryKey (string, optional) - A key of a Category. 
    + categoryValue (string, optional) - A value of a Category. 
    + contactId (string, optional) - A contactId to filter by.
    + cardType (string, optional) - `ACCOUNT_CARD`, `GIFT_CARD`.
    + currency (string, optional) - {{currency}}.
    + userSuppliedId (string, optional) - {{userSuppliedId}}.

+ Response 200
    + Attributes 
        + cards (array[Card])
        + pagination (Pagination)

    + Body
    
            {
              "cards":[
                {
                  "cardId":"card-a063cbe39ab1402ebf997cf088e81b82",
                  "userSuppliedId":"anonymous-giftcard28",
                  "contactId":null,
                  "dateCreated":"2017-07-28T21:21:00.606Z",
                  "categories":[
                    {
                      "categoryId":"category-3d4ff98bf941489e8216c9ffb9d4efc3",
                      "key":"giftbit_order",
                      "value":"2017-07-28"
                    },
                    {
                      "categoryId":"category-d2318a92c00f411987076a64243a0b57",
                      "key":"giftbit_program",
                      "value":"program-93f8264813b44877a08e8b7d9ee205e3"
                    }
                  ],
                  "cardType":"GIFT_CARD",
                  "currency":"USD"
                },
                {
                  "cardId":"card-15b464bb6c164751b394f1a99df373bd",
                  "userSuppliedId":"anonymous-giftcard3",
                  "contactId":null,
                  "dateCreated":"2017-07-28T21:21:00.599Z",
                  "categories":[
                    {
                      "categoryId":"category-3d4ff98bf941489e8216c9ffb9d4efc3",
                      "key":"giftbit_order",
                      "value":"2017-07-28"
                    },
                    {
                      "categoryId":"category-d2318a92c00f411987076a64243a0b57",
                      "key":"giftbit_program",
                      "value":"program-93f8264813b44877a08e8b7d9ee205e3"
                    }
                  ],
                  "cardType":"GIFT_CARD",
                  "currency":"USD"
                }
              ],
              "pagination":{
                "count":2,
                "limit":2,
                "maxLimit":1000,
                "offset":0,
                "totalCount":94
              }
            }

### Retrieve Card by cardId [GET /cards/{cardId}]
+ Parameters 
    + cardId (string, required) - The Lightrail Card ID.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

+ Response 200

    + Attributes 
        + card (Card)

    + Body
        
            {
              "card":{
                "cardId":"card-a063cbe39ab1402ebf997cf088e81b82",
                "userSuppliedId":"anonymous-giftcard28",
                "contactId":null,
                "dateCreated":"2017-07-28T21:21:00.606Z",
                "categories":[
                  {
                    "categoryId":"category-d2318a92c00f411987076a64243a0b57",
                    "key":"giftbit_program",
                    "value":"program-93f8264813b44877a08e8b7d9ee205e3"
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

### Create Account Card [POST /cards]
Create a Card of type `ACCOUNT_CARD` which is associated with an existing Contact.

---
+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
    + Attributes 
        + userSuppliedId (string, required) - {{userSuppliedId}}.
        + cardType (string, required) - ACCOUNT_CARD
        + currency (string, required) - {{currency}}.
        + initialValue (number, optional) - If not provided, will default to 0.
        + categories (object, optional) - An object of key-value pairs. For example: `'categories': {'city':'Seattle', 'country':'USA'}`.
        + contactId (string, required) - Note, the Contact must be created before the request to create the card.
        + expires (string, optional) - Defaults to never expires.
        + startDate (string, optional) - The date for which the associated ValueStore will become useable.
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
        + currency (string) - {{currency}}.
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

### Retrieve fullcode for Gift Card [GET /cards/{cardId}/fullcode]
Retrieves the `fullcode` associated with a Gift Card.

---
+ Parameters 
    + cardId (string) - The Lightrail Card ID. Note, Card must have cardType = `GIFT_CARD`.
    
+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

+ Response 200
    + Attributes 
        + fullcode (Fullcode)

    + Body
    
            {
                "fullcode":{
                    "code" : "5JBUR-9V922-5EDL8-RP5R5-YYEVY"
                }
            }

### Update Contact on Card [PATCH /cards/{cardId}]
Update the Contact associated with a Card.

---
+ Parameters 
    + cardId (string, required) - The Lightrail Card ID.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
            
    + Attributes
        + contactId (string, required) - The Contact's unique identifier which you'd like to update the Card with.
            
    + Body
    
            {
                "contactId": "contact-592941d2335d402e9dc3d6a981bc3896"
            }

+ Response 200

    + Attributes 
        + card (Card)

    + Body
        
            {
              "card": {
                "cardId": "card-2235b727f65a46648138429b95fdaa7b",
                "userSuppliedId": "testCategoryCreation10-19",
                "contactId": "contact-592941d2335d402e9dc3d6a981bc3896",
                "dateCreated": "2017-05-16T16:49:33.027Z",
                "categories": [
                  {
                    "categoryId": "category-48ecc24b16944b40a1218e319facd24a",
                    "key": "giftbit_order",
                    "value": "testCategoryCreationId9"
                  }
                ],
                "cardType": "GIFT_CARD",
                "currency": "USD"
              }
            }

### Activate Card [POST /cards/{cardId}/activate]
Activate Cards that were created as inactive.

---
+ Parameters
    + cardId (string, required) - The Card must have been created with `"inactive":true`.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

    + Attributes
        + userSuppliedId (string, required) - Unique idempotency ID for the request.
        
    + Body 
    
            {
                "userSuppliedId":"activate-1"
            }
    
+ Response 200
    + Attributes
        + transaction (Transaction)

    + Body
    
            {
              "transaction":{
                "transactionId":"transaction-7ad3325fa13d4d53a193d8c2d75ecb2f",
                "value":500,
                "userSuppliedId":"activate-1",
                "dateCreated":"2017-07-28T22:12:19.924Z",
                "transactionType":"ACTIVATE",
                "transactionAccessMethod":"CARDID",
                "valueAvailableAfterTransaction":500,
                "giftbitUserId":"user-1dfd10e5cb3c451382bc79774ab33232-TEST",
                "cardId":"card-8b2a4c9cb4b745fcb4ddc286038bc4a9",
                "currency":"USD",
                "codeLastFour":"99SY"
              }
            }

### Freeze Card [POST /cards/{cardId}/freeze]
Freeze a Card, preventing all transactions until unfrozen. 

---
+ Parameters
    + cardId (string, required) - The Lightrail Card ID.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

    + Attributes
        + userSuppliedId (string, required)
    + Body 
    
            {
                "userSuppliedId":"freeze-1"
            }
    
+ Response 200
    + Attributes
        + transaction (Transaction)

    + Body

            {
              "transaction":{
                "transactionId":"transaction-d2b9ded0e836471d8ea054865140ee06",
                "value":-500,
                "userSuppliedId":"freeze-1",
                "dateCreated":"2017-07-28T22:15:51.265Z",
                "transactionType":"FREEZE",
                "transactionAccessMethod":"CARDID",
                "valueAvailableAfterTransaction":0,
                "giftbitUserId":"user-1dfd10e5cb3c451382bc79774ab33232-TEST",
                "cardId":"card-8b2a4c9cb4b745fcb4ddc286038bc4a9",
                "currency":"USD",
                "codeLastFour":"99SY"
              }
            }        

### Unfreeze Card [POST /cards/{cardId}/unfreeze]
Unfreeze a frozen Card, re-enabling the creation of transactions.

---
+ Parameters
    + cardId (string, required) - The Lightrail Card ID.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

    + Attributes
        + userSuppliedId (string, required)
    + Body 
    
            {
                "userSuppliedId":"unfreeze-1"
            }
    
+ Response 200
    + Attributes
        + transaction (Transaction)

    + Body

            {
              "transaction":{
                "transactionId":"transaction-a9506ed18c9b4b78afb98f6b31956ffa",
                "value":500,
                "userSuppliedId":"unfreeze-1",
                "dateCreated":"2017-07-28T22:16:38.956Z",
                "transactionType":"UNFREEZE",
                "transactionAccessMethod":"CARDID",
                "valueAvailableAfterTransaction":500,
                "giftbitUserId":"user-1dfd10e5cb3c451382bc79774ab33232-TEST",
                "cardId":"card-8b2a4c9cb4b745fcb4ddc286038bc4a9",
                "currency":"USD",
                "codeLastFour":"99SY"
              }
            }

### Cancel Card [POST /cards/{cardId}/cancel]
Permanently cancels a Card.

---
+ Parameters
    + cardId (string, required) - The Lightrail Card ID.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
            
    + Attributes
        + userSuppliedId (string, required) - Unique idempotency ID for the request.
    
    + Body
            
            {
                "userSuppliedId": "cancel-1"
            }
    
+ Response 200

    + Body

            {
              "card":{
                "cardId":"card-8b2a4c9cb4b745fcb4ddc286038bc4a9",
                "userSuppliedId":"giftcard2",
                "contactId":null,
                "dateCreated":"2017-07-28T22:11:09.431Z",
                "categories":[
                  {
                    "categoryId":"category-3d4ff98bf941489e8216c9ffb9d4efc3",
                    "key":"giftbit_order",
                    "value":"2017-07-28"
                  },
                  {
                    "categoryId":"category-597f1f7f98b64285b7763548f83fff67",
                    "key":"city",
                    "value":"seattle"
                  },
                  {
                    "categoryId":"category-660c0b26726d4615bb8b4bbd52ae17d2",
                    "key":"giftbit_status",
                    "value":"CANCELLED"
                  }
                ],
                "cardType":"GIFT_CARD",
                "currency":"USD"
              }
            }