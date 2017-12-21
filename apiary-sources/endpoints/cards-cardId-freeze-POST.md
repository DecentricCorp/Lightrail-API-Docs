### Freeze Card [POST /cards/{cardId}/freeze]
Freeze a Card, preventing all transactions until unfrozen. 

---
+ Parameters
    + cardId (string, required) - {{card.cardId}}

+ Request (application/json)
    + Headers
    
            {{header.authorization}}

    + Attributes
        + userSuppliedId (string, required) - {{userSuppliedId}}
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
                "transactionId":"transaction-d2xx06",
                "value":-500,
                "userSuppliedId":"freeze-1",
                "dateCreated":"2017-07-28T22:15:51.265Z",
                "transactionType":"FREEZE",
                "transactionAccessMethod":"CARDID",
                "valueAvailableAfterTransaction":0,
                "giftbitUserId":"user-1dxx32",
                "cardId":"card-8bxxa9",
                "currency":"USD",
                "codeLastFour":"99SY"
              }
            }        

