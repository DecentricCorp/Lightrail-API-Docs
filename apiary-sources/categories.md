## Categories [/categories/]
These endpoints are used to retrieve information about your existing Categories.

---
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
                "categoryId": "category-46f7808dffa54c8ba0da83d448ead415",
                "key": "giftbit_program",
                "value": "program-dedccd921d074f1eb6c0f75fc5ebee72"
                },
                {
                "categoryId": "category-da55afb0c007420e9417e38bc47ca8d9",
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

### List Categories on Card [GET /cards/{cardId}/categories]
+ Parameters
    + cardId (string, required)
    
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
                    "categoryId": "category-46f7808dffa54c8ba0da83d448ead415",
                    "key": "giftbit_program",
                    "value": "program-dedccd921d074f1eb6c0f75fc5ebee72"
                    },
                    {
                    "categoryId": "category-da55afb0c007420e9417e38bc47ca8d9",
                    "key": "giftbit_order",
                    "value": "2017-06-05"
                    }
            }
