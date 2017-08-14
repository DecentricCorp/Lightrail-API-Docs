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
