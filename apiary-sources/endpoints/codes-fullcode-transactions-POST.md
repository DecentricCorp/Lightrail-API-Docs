### Create Transaction [POST /codes/{fullcode}/transactions{?pin}]
Creates a transaction against a Card using the Gift Card's `fullcode`.

---
+ Parameters
    + fullcode (string, required) - The unique gift code.
    + pin (string, optional) - This is required if the fullcode has a pin.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

    + Attributes
        + value (number) - The value of the transaction. Must be negative when transacting against the fullcode. Note, value is in the smallest unit for the currency. If USD, then value represents cents. If the Transaction represents a $50 USD redemption value = -5000.
        + currency (required) - {{currency}}
        + metadata (Metadata, optional) - A key-value object to store additional information about the transaction. Note Lightrail's [Redemption Rules](https://giftbit.github.io/Lightrail-API-Docs/RedemptionRules) operate on Transaction metadata to determine whether a particular promotion can be spent. Example: `"metadata":{"checkout-cart":{"items":[{"id":"1"},{"id":"2"}]}}`. Also note, the `giftbit_*` namespace for keys is reserved.
        + pending (boolean, optional) - If `true`, the required value will be locked, however in order to complete the transaction the pending transaction must be captured.
        
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
                "transactionId":"transaction-622de8c7b5604808b7576985db59fe70",
                "value":-500,
                "userSuppliedId":"exmaple2",
                "dateCreated":"2017-07-31T18:38:02.449Z",
                "transactionType":"DRAWDOWN",
                "transactionAccessMethod":"RAWCODE",
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