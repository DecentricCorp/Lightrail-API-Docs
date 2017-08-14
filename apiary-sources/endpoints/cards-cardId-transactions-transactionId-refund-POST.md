### Refund Transaction [POST /cards/{cardId}/transactions/{transactionId}/refund]
Refund a Transaction, reverting its affects.

---
+ Parameters
    + cardId (string, required) - The Lightrail Card ID.
    + transactionId (string, required) -The Lightrail Transaction ID. Must be an existing Transaction with transactionType `DRAWDOWN`.

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
                "transactionId":"transaction-77e01a30d919402eafdf1b62a13b9634",
                "value":50,
                "userSuppliedId":"transaction-9efd0613b8514bfc8e983b626b72b3a3-reverse",
                "dateCreated":"2017-07-31T18:57:44.844Z",
                "transactionType":"DRAWDOWN_REFUND",
                "transactionAccessMethod":"CARDID",
                "valueAvailableAfterTransaction":1299,
                "giftbitUserId":"user-1dfd10e5cb3c451382bc79774ab33232-TEST",
                "cardId":"card-76f0f09b52df40f29252d1abd886cbab",
                "currency":"USD",
                "parentTransactionId":"transaction-9efd0613b8514bfc8e983b626b72b3a3",
                "metadata":{
                  "giftbit_initial_transaction_id":"transaction-9efd0613b8514bfc8e983b626b72b3a3"
                }
              }
            }
            
