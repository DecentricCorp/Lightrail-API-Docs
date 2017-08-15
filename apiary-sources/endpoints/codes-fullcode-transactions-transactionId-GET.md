### Show Transaction Based on Gift Code [GET /codes/{fullcode}/transactions/{transactionId}{?pin}]
Retrieves a particular Transaction by its ID based on the `fullcode`.

---
+ Parameters
    + transactionId (string, required) - {{transaction.transactionId}}
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
                    "transactionId": "transaction-3exx35",
                    "value": -50,
                    "userSuppliedId": "gift_card_4-inactive",
                    "dateCreated": "2017-06-05T15:39:25.411Z",
                    "transactionType": "INACTIVATE",
                    "transactionAccessMethod": "CARDID",
                    "valueAvailableAfterTransaction": 0,
                    "giftbitUserId": "user-50xx93",
                    "codeLastFour": "S9X5",
                    "cardId": "card-99xx65",
                    "currency": "XXX"
                }
            }

