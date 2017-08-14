### List Cards [GET /cards/{?limit}{?offset}{?categoryKey}{?categoryValue}{?contactId}{?cardType}{?currency}{?userSuppliedId}]
Retrieve a paginated list of Cards.

---
+ Request (application/json)
    + Headers
    
             Authorization: Bearer <YOUR_ACCESS_TOKEN>
   
+ Parameter
    + limit (number, optional) - {{pagination.limit}}
    + offset (number, optional) - {{pagination.limit}}
    + categoryKey (string, optional) - A key of a Category. 
    + categoryValue (string, optional) - A value of a Category. 
    + contactId (string, optional) - A contactId to filter by.
    + cardType (string, optional) - {{cardType}}
    + currency (string, optional) - {{currency}}
    + userSuppliedId (string, optional) - {{userSuppliedId}}

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

