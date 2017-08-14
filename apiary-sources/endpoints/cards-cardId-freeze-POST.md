### Freeze Card [POST /cards/{cardId}/freeze]
Freeze a Card, preventing all transactions until unfrozen. 

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
                "userSuppliedId":"freeze-1"
            }
    
+ Response 200
    + Attributes
        + transaction (Transaction)

    + Body

            {
              "transaction":{
                "transactionId":"transaction-d2b9ded0e836471d8ea054865140ee06",
                "value":-500,
                "userSuppliedId":"freeze-1",
                "dateCreated":"2017-07-28T22:15:51.265Z",
                "transactionType":"FREEZE",
                "transactionAccessMethod":"CARDID",
                "valueAvailableAfterTransaction":0,
                "giftbitUserId":"user-1dfd10e5cb3c451382bc79774ab33232-TEST",
                "cardId":"card-8b2a4c9cb4b745fcb4ddc286038bc4a9",
                "currency":"USD",
                "codeLastFour":"99SY"
              }
            }        

