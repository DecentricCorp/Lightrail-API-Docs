## Balances [/cards/{cardId}/balance/]
## Balances [/cards/{cardId}/balance/]
These endpoints are used to get the available balance assoicated with a Card.

---
### Get Balance by cardId [GET /cards/{cardId}/balance{?asAtDate}]
Retrieve a Card's balance.

---
+ Parameters
    + cardId (string, required) - The Lightrail Card ID.
    + asAtDate (string, optional) - The date and time you'd like to retrieve the balance at.
+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
    
+ Response 200
    + Attributes (Balance)

    + Body

            {
                "balance":{
                "principal":{
                "currentValue": 150,
                "state": "ACTIVE",
                "expires": null,
                "startDate": null,
                "programId": "program-dedccd921d074f1eb6c0f75fc5ebee72",
                "valueStoreId": "value-18638d4486ad4feaab125a61f5df3079"
                },
                "attached":[
                ],
                "currency": "USD",
                "cardType":"GIFT_CARD",
                "balanceDate": "2017-06-05T17:11:36.999Z"
                }
            }

### Get Balance by fullcode [GET /codes/{fullcode}/card/balance{?asAtDate}{?pin}]
Retreive a Card's balance using the Gift Card's `fullcode`.

---
+ Parameters
    + fullcode (string, required) - The unique unguessable gift code.
    + pin (string, optional) - This is required if the fullcode has a pin.
    + asAtDate (string, optional) - The date and time you'd like to retrieve the balance at.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
    
+ Response 200
    + Attributes (Balance)

    + Body

            {
                "balance":{
                "principal":{
                "currentValue": 150,
                "state": "ACTIVE",
                "expires": null,
                "startDate": null,
                "programId": "program-dedccd921d074f1eb6c0f75fc5ebee72",
                "valueStoreId": "value-18638d4486ad4feaab125a61f5df3079"
                },
                "attached":[
                ],
                "currency": "USD",
                "cardType":"GIFT_CARD",
                "balanceDate": "2017-06-05T17:11:36.999Z"
                }
            }

### Get Balance by fullcode (Deprecated) [GET /codes/{fullcode}/balance{?asAtDate}{?pin}]
Note, this endpoint is deprecated and should no longer be used. 

---
+ Parameters
    + fullcode (string, required)
    + pin (string, optional) - This is required if the fullcode has a pin.
    + asAtDate (string, optional) - The date and time you'd like to retrieve the balance at.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
    
+ Response 200
    + Body

            {
                balance":{
                "currentValue": 150,
                "fundedValue": 0,
                "drawdownValue": 0,
                "cancellationValue": 0,
                "initialValue": 150,
                "frozenValue": 0,
                "inactiveValue": 0,
                "pendingValue": 0,
                "currency": "USD"
                }
            }
