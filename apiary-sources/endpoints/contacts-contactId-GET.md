### Show Contact [GET /contacts/{contactId}]
+ Parameters
    + contactId (string, required) - The Lightrail Contact ID.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
            
    
+ Response 200
    + Attributes
        + contact (Contact)

    + Body
    
            {
              "contact":{
                "contactId":"contact-0cxx86",
                "userSuppliedId":"contact3",
                "email":"johnsmom@example.com",
                "firstName":"Sarah",
                "lastName":"Connor",
                "dateCreated":"2017-07-28T21:21:04.000Z"
              }
            }

