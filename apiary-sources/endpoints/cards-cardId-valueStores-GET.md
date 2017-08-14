### List ValueStores [GET /cards/{cardId}/valueStores]
Retrieve a paginated list of a Card's ValueStores.

---
+ Parameters
    + cardId (string, required) - The Lightrail Card ID.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

+ Response 200
    + Attributes
        + valueStores (array[ValueStore])
        + pagination (Pagination)

    + Body

            {
                "valueStores":[
                {
                    "cardId": "card-9991e39b4526401f85a889cb20b1a465",
                    "valueStoreId": "value-0ff47ca176eb4708b08a8bb010584704",
                    "valueStoreType": "PRINCIPAL",
                    "currency": "XXX",
                    "dateCreated": "2017-06-05T15:39:25.392Z",
                    "programId": "program-dedccd921d074f1eb6c0f75fc5ebee72"
                }
                ],
                "pagination":{
                "count": 1,
                "limit": 100,
                "maxLimit": 1000,
                "offset": 0,
                "totalCount": 1
                }          
            }

