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

