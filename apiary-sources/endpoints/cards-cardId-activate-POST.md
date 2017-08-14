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
