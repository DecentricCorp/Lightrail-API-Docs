### Capture Pending Transaction [POST /cards/{cardId}/transactions/{transactionId}/capture]
Captures a pending Transaction. This locks in the value of the pending Transaction.

---
+ Parameters
    + cardId (string, required) - The Lightrail Card Id.
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
                "transactionId":"transaction-38b58e7a8ea4478c997e66d80239e052",
                "value":-50,
                "userSuppliedId":"transaction-901c9043830d4b96af2982d3f2b103f6-capture",
                "dateCreated":"2017-07-31T18:50:25.357Z",
                "transactionType":"DRAWDOWN",
                "transactionAccessMethod":"CARDID",
                "valueAvailableAfterTransaction":1349,
                "giftbitUserId":"user-1dfd10e5cb3c451382bc79774ab33232-TEST",
                "cardId":"card-76f0f09b52df40f29252d1abd886cbab",
                "currency":"USD",
                "parentTransactionId":"transaction-901c9043830d4b96af2982d3f2b103f6",
                "metadata":{
                  "giftbit_initial_transaction_id":"transaction-901c9043830d4b96af2982d3f2b103f6"
                }
              }
            }

