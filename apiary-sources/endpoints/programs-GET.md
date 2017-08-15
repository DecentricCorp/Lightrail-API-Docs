### List Programs [GET /programs{?limit}{?offset}]
Retrieves a paginated list of all Lightrail Programs belonging to the user. 

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
    
+ Parameters
    + limit (number, optional) - {{pagination.limit}}
    + offset (number, optional) - {{pagination.offset}}
        
+ Response 200

    + Body

            {
                "programs":[
                    {
                        "programId": "program-dexx72",
                        "userSuppliedId": "tx56",
                        "name": "Gift Card Program 2017",
                        "active": true,
                        "currency": "USD",
                        "dateCreated": "2017-06-05T17:09:10.000Z",
                        "programExpiresDate": null,
                        "programStartDate": "2017-06-05T15:51:35.000Z",
                        "codeActivePeriodInDays": null,
                        "codeValueMin": 100,
                        "codeValueMax": 50000,
                        "fixedCodeValues": null,
                        "codeEngine": "SIMPLE_STORED_VALUE",
                        "codeConfig": "GFBT",
                        "valueStoreType": "PRINCIPAL",
                        "metadata": null,
                        "timeZone": "PST",
                        "cardType": null
                    },
                    {
                        "programId": "program-d0xx04",
                        "userSuppliedId": "tx527",
                        "name": "Spring Promotion 2017",
                        "active": true,
                        "currency": "XXX",
                        "dateCreated": "2017-06-05T16:14:46.000Z",
                        "programExpiresDate": null,
                        "programStartDate": "2017-06-05T15:51:35.000Z",
                        "codeActivePeriodInDays": 7,
                        "codeValueMin": 1,
                        "codeValueMax": 5000,
                        "fixedCodeValues": null,
                        "codeEngine": "SIMPLE_STORED_VALUE",
                        "codeConfig": "GFBT",
                        "valueStoreType": "ATTACHED",
                        "metadata": null,
                        "timeZone": "PST",
                        "cardType": null
                    }
                ],
                "pagination":{
                    "count": 2,
                    "limit": 100,
                    "maxLimit": 1000,
                    "offset": 0,
                    "totalCount": 2
                }
            }