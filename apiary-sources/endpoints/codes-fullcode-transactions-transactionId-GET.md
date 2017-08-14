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

