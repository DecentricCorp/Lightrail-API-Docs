### Create Transaction based on `fullcode` [POST /codes/{fullcode}/transactions{?pin}]
Creates a transaction against a Card based on the Gift Card's `fullcode`.

---
+ Parameters
    + fullcode (string, required) - The unique gift code.
    + pin (string, optional) - This is required if the fullcode has a pin.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

    + Attributes
        + value (number) - {{transaction.value}}
        + currency (required) - {{currency}}
        + metadata (Metadata, optional) - {{transaction.metadata}}
        + pending (boolean, optional) - {{transaction.pending}}
        
    + Body 
    
            {
              "userSuppliedId":"example2",
              "value":-500,
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
    
+ Response 200
    + Attributes
        + transaction (Transaction)

    + Body

            {
              "transaction":{
                "transactionId":"transaction-62xx70",
                "value":-500,
                "userSuppliedId":"exmaple2",
                "dateCreated":"2017-07-31T18:38:02.449Z",
                "transactionType":"DRAWDOWN",
                "transactionAccessMethod":"RAWCODE",
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