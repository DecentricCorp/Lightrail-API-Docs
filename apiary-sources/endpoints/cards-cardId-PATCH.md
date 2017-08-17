### Update Contact on Card [PATCH /cards/{cardId}]
Update the Contact associated with a Card, effectively transferring the Card to another Contact.

---
+ Parameters 
    + cardId (string, required) - {{card.cardId}}

+ Request (application/json)
    + Headers
    
            {{header.authorization}}
            
    + Attributes
        + contactId (string, required) - The unique identifier of the new Contact to whom the card must be assigned.
            
    + Body
    
            {
                "contactId": "contact-59xx96"
            }

+ Response 200

    + Attributes 
        + card (Card)

    + Body
        
            {
              "card": {
                "cardId": "card-22xx7b",
                "userSuppliedId": "testCategoryCreation10-19",
                "contactId": "contact-59xx96",
                "dateCreated": "2017-05-16T16:49:33.027Z",
                "categories": [
                  {
                    "categoryId": "category-48xx4a",
                    "key": "giftbit_order",
                    "value": "testCategoryCreationId9"
                  }
                ],
                "cardType": "GIFT_CARD",
                "currency": "USD"
              }
            }
