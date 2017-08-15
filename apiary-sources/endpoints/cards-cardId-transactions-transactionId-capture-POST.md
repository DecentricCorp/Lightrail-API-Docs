### Capture Pending Transaction [POST /cards/{cardId}/transactions/{transactionId}/capture]
Captures a pending Transaction. This locks in the value of the pending Transaction.

---
+ Parameters
    + cardId (string, required) - {{card.cardId}}
    + transactionId (string, required) - {{transaction.transactionId}} Must be a pending Transaction.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
            
    + Attributes
        + userSuppliedId (string, required) - {{userSuppliedId}}

+ Response 200
    + Attributes
        + transaction (Transaction)

    + Body

            {
              "transaction":{
                "transactionId":"transaction-38xx52",
                "value":-50,
                "userSuppliedId":"transaction-90xxf6-capture",
                "dateCreated":"2017-07-31T18:50:25.357Z",
                "transactionType":"DRAWDOWN",
                "transactionAccessMethod":"CARDID",
                "valueAvailableAfterTransaction":1349,
                "giftbitUserId":"user-1dxx32",
                "cardId":"card-76xxab",
                "currency":"USD",
                "parentTransactionId":"transaction-90xxf6",
                "metadata":{
                  "giftbit_initial_transaction_id":"transaction-90xxf6"
                }
              }
            }

