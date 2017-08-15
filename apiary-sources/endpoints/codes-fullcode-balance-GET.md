### Get Balance by fullcode (Deprecated) [GET /codes/{fullcode}/balance{?asAtDate}{?pin}]
Note, this endpoint is deprecated and should no longer be used. 

---
+ Parameters
    + fullcode (string, required) - The unique unguessable gift code.
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
