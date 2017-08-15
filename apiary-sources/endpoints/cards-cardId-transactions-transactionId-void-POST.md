### Void Pending Transaction [POST /cards/{cardId}/transactions/{transactionId}/void]
Voids a pending Transaction. This makes the value of the pending Transaction available to be used by other Transactions.

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
                "transactionId":"transaction-7dxx3b",
                "value":50,
                "userSuppliedId":"transaction-fbxx5a-reverse",
                "dateCreated":"2017-07-31T18:54:58.141Z",
                "transactionType":"PENDING_VOID",
                "transactionAccessMethod":"CARDID",
                "valueAvailableAfterTransaction":1299,
                "giftbitUserId":"user-1dxx32",
                "cardId":"card-76xxab",
                "currency":"USD",
                "parentTransactionId":"transaction-fbxx5a",
                "metadata":{
                  "giftbit_initial_transaction_id":"transaction-fbxx5a"
                }
              }
            }

