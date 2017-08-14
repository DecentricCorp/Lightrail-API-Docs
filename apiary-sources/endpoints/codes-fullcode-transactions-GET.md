### List Transactions [GET /codes/{fullcode}/transactions{?pin}]
Retrieve a paginated list of a Card's Transactions using the Gift Card's `fullcode`.

---
+ Parameters
    + fullcode (string, required) - The unique gift code.
    + pin (string, optional) - This is required if the fullcode has a pin.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
    
+ Response 200
    + Attributes
        + transactions (array[Transaction])
        + pagination (Pagination)

    + Body

            {
              "transactions":[
                {
                  "transactionId":"transaction-e099b4fc262b4c98a65d79e42fd6f4f5",
                  "value":-599,
                  "userSuppliedId":"anonymous-giftcard10-transaction1",
                  "dateCreated":"2017-07-28T21:21:10.009Z",
                  "transactionType":"DRAWDOWN",
                  "transactionAccessMethod":"CARDID",
                  "valueAvailableAfterTransaction":1401,
                  "giftbitUserId":"user-1dfd10e5cb3c451382bc79774ab33232-TEST",
                  "cardId":"card-76f0f09b52df40f29252d1abd886cbab",
                  "currency":"USD",
                  "metadata":{
                    "giftbit_override_dateCreated":"2017-07-28T21:21:10.009Z"
                  }
                },
                {
                  "transactionId":"transaction-0094bdcc1be34b0ebc9e957d5dd924a5",
                  "value":2000,
                  "userSuppliedId":"anonymous-giftcard10",
                  "dateCreated":"2017-07-13T21:21:00.601Z",
                  "transactionType":"INITIAL_VALUE",
                  "transactionAccessMethod":"CARDID",
                  "valueAvailableAfterTransaction":2000,
                  "giftbitUserId":"user-1dfd10e5cb3c451382bc79774ab33232-TEST",
                  "cardId":"card-76f0f09b52df40f29252d1abd886cbab",
                  "currency":"USD",
                  "metadata":{
                    "giftbit_override_dateCreated":"2017-07-13T21:21:00.601Z"
                  }
                }
              ],
              "pagination":{
                "count":2,
                "limit":100,
                "maxLimit":1000,
                "offset":0,
                "totalCount":2
              }
            }

