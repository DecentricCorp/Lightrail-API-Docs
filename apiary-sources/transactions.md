## Transactions [/cards/{cardId}/transactions]
A feature the Lightrail API supports is the ability to create Pending Transactions. 
This means the value required for the Pending Transaction will be unavailable to be used for other Transactions. 
A Pending Transaction can either be captured, which confirms the Transaction, or it can be voided, freeing up their associated value. 

### List Card Transactions [GET /cards/{cardId}/transactions]
Retrieve a paginated list of a Card's Transactions.

---
+ Parameters
    + cardId (string, required) - The Lightrail Card ID.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

    + Attributes
        + limit (number, optional) - For pagination.  The maximum number of results to return at once. Default 100.
        + offset (number, optional) - For pagination. The offset of the first results in the total results. Default 0.
    
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

### Create Transaction [POST /cards/{cardId}/transactions]
Creates a transaction against a Card. Transactions can be created as pending which locks the value required for the Transaction until it is either captured or voided. 

---
+ Parameters
    + cardId (string, required)

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

    + Attributes
        + value (number) - The value of the transaction. Can be negative or positive. Note, value is in the smallest unit for the currency. If USD, then value represents cents. If the Transaction represents a $50 USD redemption value = -5000.
        + currency (required) - {{currency}}
        + metadata (Metadata, optional) - {{metadata}}
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

### Refund Transaction [POST /cards/{cardId}/transactions/{transactionId}/refund]
Refund a Transaction, reverting its affects.

---
+ Parameters
    + cardId (string, required) - The Lightrail Card ID.
    + transactionId (string, required) -The Lightrail Transaction ID. Must be a Transaction with transactionType `DRAWDOWN`.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
    + Attributes
        + userSuppliedId (string, required) - {{userSuppliedId}}.
    
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
            
## Codes [/codes/{fullcode}/transactions]
These endpoints are used when interacting directly with the `fullcode` of a Gift Card.

---
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

### Show Transaction [GET /codes/{fullcode}/transactions/{transactionId}{?pin}]
+ Parameters
    + transactionId (string, required) - The Lightrail Transaction ID.
    + fullcode (string, required) - The unique unguessable gift code.
    + pin (string, optional) - This is required if the gift code has a pin.
    

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
    
+ Response 200
    + Attributes
        + transaction (Transaction)
        
    + Body 
    
            {
                transaction":{
                    "transactionId": "transaction-3ec8ad2afea14c04906159ee8f1ad735",
                    "value": -50,
                    "userSuppliedId": "gift_card_4-inactive",
                    "dateCreated": "2017-06-05T15:39:25.411Z",
                    "transactionType": "INACTIVATE",
                    "transactionAccessMethod": "CARDID",
                    "valueAvailableAfterTransaction": 0,
                    "giftbitUserId": "user-5022fccf827647ee9cfb63b779d62193-TEST",
                    "codeLastFour": "S9X5",
                    "cardId": "card-9991e39b4526401f85a889cb20b1a465",
                    "currency": "XXX"
                }
            }

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