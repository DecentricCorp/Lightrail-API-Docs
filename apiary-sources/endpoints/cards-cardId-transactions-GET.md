### List Card Transactions [GET /cards/{cardId}/transactions]
Retrieve a paginated list of a Card's Transactions.

---
+ Parameters
    + cardId (string, required) - The Lightrail Card ID.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

    + Attributes
        + limit (number, optional) - {{pagination.limit}}
        + offset (number, optional) - {{pagination.offset}}
    
+ Response 200
    + Attributes
        + transactions (array[Transaction])
        + pagination (Pagination)

    + Body
    
            {
              "transactions":[
                {
                  "transactionId":"transaction-622de8c7b5604808b7576985db59fe70",
                  "value":-500,
                  "userSuppliedId":"example2",
                  "dateCreated":"2017-07-31T18:38:02.449Z",
                  "transactionType":"DRAWDOWN",
                  "transactionAccessMethod":"CARDID",
                  "valueAvailableAfterTransaction":1500,
                  "giftbitUserId":"user-1dfd10e5cb3c451382bc79774ab33232-TEST",
                  "cardId":"card-76f0f09b52df40f29252d1abd886cbab",
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

