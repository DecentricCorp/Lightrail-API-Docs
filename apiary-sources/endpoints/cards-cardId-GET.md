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
