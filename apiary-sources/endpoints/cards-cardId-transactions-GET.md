### List Card Transactions [GET /cards/{cardId}/transactions{?limit}{?offset}]
Retrieve a paginated list of a Card's Transactions.

---
+ Parameters
    + cardId (string, required) - {{card.cardId}}

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

+ Parameters
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
                },
                {
                  "transactionId":"transaction-00xxa5",
                  "value":2000,
                  "userSuppliedId":"anonymous-giftcard10",
                  "dateCreated":"2017-07-13T21:21:00.601Z",
                  "transactionType":"INITIAL_VALUE",
                  "transactionAccessMethod":"CARDID",
                  "valueAvailableAfterTransaction":2000,
                  "giftbitUserId":"user-1dxx32",
                  "cardId":"card-76xxab",
                  "currency":"USD"                
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

