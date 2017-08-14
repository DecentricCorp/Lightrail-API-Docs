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

