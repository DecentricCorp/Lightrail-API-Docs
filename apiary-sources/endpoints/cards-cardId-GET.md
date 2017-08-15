### Retrieve Card by cardId [GET /cards/{cardId}]
+ Parameters 
    + cardId (string, required) - {{card.cardId}}

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

+ Response 200

    + Attributes 
        + card (Card)

    + Body
        
            {
              "card":{
                "cardId":"card-a0xx82",
                "userSuppliedId":"anonymous-giftcard28",
                "contactId":null,
                "dateCreated":"2017-07-28T21:21:00.606Z",
                "categories":[
                  {
                    "categoryId":"category-d2xx57",
                    "key":"giftbit_program",
                    "value":"program-93xxe3"
                  },
                  {
                    "categoryId":"category-3dxxc3",
                    "key":"giftbit_order",
                    "value":"2017-07-28"
                  }
                ],
                "cardType":"GIFT_CARD",
                "currency":"USD"
              }
            }
