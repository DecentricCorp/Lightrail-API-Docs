### Get Balance by fullcode [GET /codes/{fullcode}/card/balance{?asAtDate}{?pin}]
Retrieve a Card's balance using the Gift Card's `fullcode`.

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
                "cardId": "card-6dxxx89",
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

