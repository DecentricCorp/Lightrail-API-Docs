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

