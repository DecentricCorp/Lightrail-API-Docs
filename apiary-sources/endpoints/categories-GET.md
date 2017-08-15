### List Categories [GET /categories]
+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
    
+ Response 200
    + Attributes
        + categories (array[Category])

    + Body

            {
                "categories":[
                {
                "categoryId": "category-46xx15",
                "key": "giftbit_program",
                "value": "program-dexx72"
                },
                {
                "categoryId": "category-daxxd9",
                "key": "giftbit_order",
                "value": "2017-06-05"
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

