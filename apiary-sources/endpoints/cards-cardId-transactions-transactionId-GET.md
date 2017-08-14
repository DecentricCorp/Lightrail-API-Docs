### Show Transaction [GET /cards/{cardId}/transactions/{transactionId}]
+ Parameters
    + cardId (string, required) - The Lightrail Card ID.
    + transactionId (string, required) - The Lightrail Transaction ID.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
    
+ Response 200
    + Attributes
        + transaction (Transaction)

    + Body

            { "transaction": {
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
                }
            }

