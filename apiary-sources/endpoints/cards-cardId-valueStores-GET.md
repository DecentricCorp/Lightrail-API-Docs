### List ValueStores [GET /cards/{cardId}/valueStores]
Retrieve a paginated list of a Card's ValueStores.

---
+ Parameters
    + cardId (string, required) - {{card.cardId}}

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
                    "cardId": "card-99xx65",
                    "valueStoreId": "value-0fxx04",
                    "valueStoreType": "PRINCIPAL",
                    "currency": "XXX",
                    "dateCreated": "2017-06-05T15:39:25.392Z",
                    "programId": "program-dexx72"
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

