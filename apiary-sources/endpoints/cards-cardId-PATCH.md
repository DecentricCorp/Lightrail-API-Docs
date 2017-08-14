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
