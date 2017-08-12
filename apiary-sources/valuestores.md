## ValueStores: Managing Promotions [/cards/{cardId}/valueStores/]
These endpoints are used for managing Promotion Programs.

---
### List ValueStores [GET /cards/{cardId}/valueStores]
Retrieve a paginated list of a Card's ValueStores.

---
+ Parameters
    + cardId (string, required) - The Lightrail Card ID.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

+ Response 200
    + Attributes
        + valueStores (array[ValueStore])
        + pagination (Pagination)

    + Body

            {
                "valueStores":[
                {
                    "cardId": "card-9991e39b4526401f85a889cb20b1a465",
                    "valueStoreId": "value-0ff47ca176eb4708b08a8bb010584704",
                    "valueStoreType": "PRINCIPAL",
                    "currency": "XXX",
                    "dateCreated": "2017-06-05T15:39:25.392Z",
                    "programId": "program-dedccd921d074f1eb6c0f75fc5ebee72"
                }
                ],
                "pagination":{
                "count": 1,
                "limit": 100,
                "maxLimit": 1000,
                "offset": 0,
                "totalCount": 1
                }          
            }

### Show ValueStore [GET /cards/{cardId}/valueStores/{valueStoreId}]
+ Parameters
    + cardId (string, required)
    + valueStoreId (string, required)

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

    
+ Response 200
    + valueStore (ValueStore)

    + Body

            {
                "valueStore":{
                    "cardId": "card-9991e39b4526401f85a889cb20b1a465",
                    "valueStoreId": "value-0ff47ca176eb4708b08a8bb010584704",
                    "valueStoreType": "PRINCIPAL",
                    "currency": "XXX",
                    "dateCreated": "2017-06-05T15:39:25.392Z",
                    "programId": "program-dedccd921d074f1eb6c0f75fc5ebee72"
                }
            }

### Add ValueStore [POST /cards/{cardId}/valueStores]
Adds a ValueStore from a Promotion Program to a Card. 

---
+ Parameters
    + cardId (string, required)

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

    + Attributes
        + userSuppliedId (string, required) - {{userSuppliedId}}
        + currency (string, required) - {{currency}}
        + programId (string, required) - The unique id of the Promotion Program. Note the Program's `ValueStoreType` must be of type `ATTACHED`.
        + expires (string, optional) - Defaults to lifespan set by program.
        + startDate (string, optional) - The date for which the ValueStore will become useable.
        + initialValue (number, optional) - The initial value of the ValueStore.
        
    + Body 
    
            {
            "userSuppliedId":"promo1",
            "currency": "XXX",
            "programId":"program-d0c6328cacde469ba3b28105e9d89c04",
            "initialValue":150
            }
    
+ Response 200
    + Attributes
        + valueStore (ValueStore)

    + Body

            {
                "valueStore":{
                    "cardId": "card-9991e39b4526401f85a889cb20b1a465",
                    "valueStoreId": "value-806116e736854136867d17fbfb959382",
                    "valueStoreType": "ATTACHED",
                    "currency": "XXX",
                    "dateCreated": "2017-06-05T16:14:58.314Z",
                    "programId": "program-d0c6328cacde469ba3b28105e9d89c04",
                    "expires": "2017-06-13T06:59:59.000Z"
                }   
            }

### Freeze ValueStore [POST /cards/{cardId}/valueStores/{valueStoreId}/freeze]
Freeze a Card's ValueStore, preventing all transactions against that ValueStore until unfrozen. 

---
+ Parameters
    + cardId (string, required) - The Lightrail Card ID.
    + valueStoreId (string, required) - The Lightrail ValueStore ID.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

    + Attributes
        + userSuppliedId (string, required) - Unique idempotency ID for the request.
        
    + Body 
    
            {
                "userSuppliedId":"freeze-valuestore-1"
            }
    
+ Response 200
    + Attributes
        + transaction (Transaction)


    + Body

            {
                "transaction":{
                    "transactionId": "transaction-3f96b36c9b2845e2b3e61e3d74d955e1",
                    "value": -50,
                    "userSuppliedId": "freeze-valueStore-1",
                    "dateCreated": "2017-06-05T16:36:56.321Z",
                    "transactionType": "FREEZE",
                    "transactionAccessMethod": "CARDID",
                    "valueAvailableAfterTransaction": 0,
                    "giftbitUserId": "user-5022fccf827647ee9cfb63b779d62193-TEST",
                    "codeLastFour": "NKNA",
                    "cardId": "card-fb8e53946e784280a2bc8273dcbb0fda",
                    "currency": "XXX"
                }
            }

### Unfreeze ValueStore [POST /cards/{cardId}/valueStores/{valueStoreId}/unfreeze]
Unfreeze a Card's frozen ValueStore, re-enabling the creation of transactions against that ValueStore.

---
+ Parameters
    + cardId (string, required) - The Lightrail Card ID.
    + valueStoreId (string, required) - The Lightrail ValueStore ID.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

    + Attributes
        + userSuppliedId (string, required) - {{userSuppliedId}}
        
    + Body 
    
            {
                "userSuppliedId":"unfreeze-valuestore-1"
            }
    
+ Response 200
    + Attributes
        + transaction (Transaction)

    + Body

            {
                "transaction":{
                "transactionId": "transaction-f7726f6b6d52412980544ee217894c82",
                "value": 50,
                "userSuppliedId": "unfreeze-valueStore-1",
                "dateCreated": "2017-06-05T16:37:57.218Z",
                "transactionType": "UNFREEZE",
                "transactionAccessMethod": "CARDID",
                "valueAvailableAfterTransaction": 50,
                "giftbitUserId": "user-5022fccf827647ee9cfb63b779d62193-TEST",
                "codeLastFour": "NKNA",
                "cardId": "card-fb8e53946e784280a2bc8273dcbb0fda",
                "currency": "XXX"
                }
            }

### Cancel ValueStore [POST /cards/{cardId}/valueStores/{valueStoreId}/cancel]
Permanently cancels a Card's ValueStore.

---
+ Parameters
    + cardId (string, required) - The Lightrail Card ID.
    + valueStoreId (string, required) - The Lightrail ValueStore ID.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

    + Attributes
        + userSuppliedId (string, required) - {{userSuppliedId}}
        
    + Body 
    
            {
                "userSuppliedId":"cancel-valuestore-1"
            }
    
+ Response 200
    + Attributes
        + transaction (Transaction)

    + Body

            {
                "transaction":{
                "transactionId": "transaction-7f9c5dea0be24f57a902568a23a11c89",
                "value": -50,
                "userSuppliedId": "cancel-valueStore-1",
                "dateCreated": "2017-06-05T16:39:06.679Z",
                "transactionType": "CANCELLATION",
                "transactionAccessMethod": "CARDID",
                "valueAvailableAfterTransaction": 0,
                "giftbitUserId": "user-5022fccf827647ee9cfb63b779d62193-TEST",
                "codeLastFour": "NKNA",
                "cardId": "card-fb8e53946e784280a2bc8273dcbb0fda",
                "currency": "XXX"
                }
            }
