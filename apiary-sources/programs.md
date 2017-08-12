## Programs [/programs/]
This endpoint can be used to retrieve information about your existing Gift Card and Promotion Programs.

---
### List Programs [GET /programs]
+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
    
+ Response 200

    + Body

            {
                "programs":[
                {
                "programId": "program-dedccd921d074f1eb6c0f75fc5ebee72",
                "userSuppliedId": "568fa33b-5cb34b3c-5c2bccf2",
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
                "programId": "program-d0c6328cacde469ba3b28105e9d89c04",
                "userSuppliedId": "527650f4-6b903ce9-4c792cf6",
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