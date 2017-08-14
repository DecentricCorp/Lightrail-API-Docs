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
