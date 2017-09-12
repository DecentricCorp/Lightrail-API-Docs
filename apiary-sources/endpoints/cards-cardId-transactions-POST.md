<a name="post-transaction-by-cardid-anchor"></a>

### Create Transaction Based on Card ID [POST /cards/{cardId}/transactions]
Creates a transaction against a Card based on its `cardId`.
Transactions can be created as pending which locks the value required for the Transaction until it is either captured or voided. 

---
+ Parameters
    + cardId (string, required) - {{card.cardId}}

+ Request (application/json)
    + Headers
    
            {{header.authorization}}

    + Attributes
        + value (number) - {{transaction.value}}
        + currency (required) - {{currency}}
        + metadata (Metadata, optional) - {{transaction.metadata}}
        + pending (boolean, optional) - {{transaction.pending}}
        + userSuppliedId (string, required) - {{userSuppliedId}}        
        
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
                "transactionAccessMethod":"CARDID",
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

