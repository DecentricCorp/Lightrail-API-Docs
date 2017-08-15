### Show Transaction Based on `cardId` [GET /cards/{cardId}/transactions/{transactionId}]
Retrieves a Transaction based on its ID.

---

+ Parameters
    + cardId (string, required) - {{card.cardId}}
    + transactionId (string, required) - {{transaction.transactionId}}

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
    
+ Response 200
    + Attributes
        + transaction (Transaction)

    + Body

        { 
            "transaction": {
                "transactionId":"transaction-62xx70",
                "value":-500,
                "userSuppliedId":"example2",
                "dateCreated":"2017-07-31T18:38:02.449Z",
                "transactionType":"DRAWDOWN",
                "transactionAccessMethod":"CARDID",
                "valueAvailableAfterTransaction":1500,
                "giftbitUserId":"user-1dxx32",
                "cardId":"card-76xxab",
                "currency":"USD",
                "metadata":{
                    "checkout-cart":{
                      "items":[
                        {
                          "id":"1"
                        },
                        {
                          "id":"2"
                        }
                      ]
                    }
                }
            }
        }

