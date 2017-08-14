### Void Pending Transaction [POST /cards/{cardId}/transactions/{transactionId}/void]
Voids a pending Transaction. This makes the value of the pending Transaction available to be used by other Transactions.

---
+ Parameters
    + cardId (string, required) - The Lightrail Card ID.
    + transactionId (string, required) - The Lightrail Transaction ID. Must be a pending Transaction.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
    + Attributes
        + userSuppliedId (string, required) - Unique idempotency ID for the request. 
    
+ Response 200
    + Attributes
        + transaction (Transaction)
        
    + Body

            {
              "transaction":{
                "transactionId":"transaction-7dfa13635e0a468ca6257ff02bc4023b",
                "value":50,
                "userSuppliedId":"transaction-fb3d3a0c76ac4d809dfbd4be3d77aa5a-reverse",
                "dateCreated":"2017-07-31T18:54:58.141Z",
                "transactionType":"PENDING_VOID",
                "transactionAccessMethod":"CARDID",
                "valueAvailableAfterTransaction":1299,
                "giftbitUserId":"user-1dfd10e5cb3c451382bc79774ab33232-TEST",
                "cardId":"card-76f0f09b52df40f29252d1abd886cbab",
                "currency":"USD",
                "parentTransactionId":"transaction-fb3d3a0c76ac4d809dfbd4be3d77aa5a",
                "metadata":{
                  "giftbit_initial_transaction_id":"transaction-fb3d3a0c76ac4d809dfbd4be3d77aa5a"
                }
              }
            }

