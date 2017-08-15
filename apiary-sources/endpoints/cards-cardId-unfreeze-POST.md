### Unfreeze Card [POST /cards/{cardId}/unfreeze]
Unfreeze a frozen Card, re-enabling the creation of transactions.

---
+ Parameters
    + cardId (string, required) - {{card.cardId}}

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

    + Attributes
        + userSuppliedId (string, required): {{userSuppliedId}}
   
    + Body 
    
            {
                "userSuppliedId":"tx-122"
            }
    
+ Response 200
    + Attributes
        + transaction (Transaction)

    + Body

            {
              "transaction":{
                "transactionId":"transaction-a9xxfa",
                "value":500,
                "userSuppliedId":"unfreeze-1",
                "dateCreated":"2017-07-28T22:16:38.956Z",
                "transactionType":"UNFREEZE",
                "transactionAccessMethod":"CARDID",
                "valueAvailableAfterTransaction":500,
                "giftbitUserId":"user-1dxx32",
                "cardId":"card-8bxxa9",
                "currency":"USD",
                "codeLastFour":"99SY"
              }
            }
