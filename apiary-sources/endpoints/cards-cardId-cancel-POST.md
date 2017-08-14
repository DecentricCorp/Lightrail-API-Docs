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