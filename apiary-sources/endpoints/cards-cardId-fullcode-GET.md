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
