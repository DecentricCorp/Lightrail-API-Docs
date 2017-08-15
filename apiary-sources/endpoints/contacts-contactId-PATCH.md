### Update Contact [PATCH /contacts/{contactId}/]
+ Parameters
    + contactId (string, required) - The Lightrail Contact ID.
    
+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
            
    + Attributes
        + email (string, optional) 
        + firstName (string, optional)
        + lastName (string, optional) 
            
    + Body
    
            {
                "firstName": "Jane",
                "lastName": "Smith"
            }
    
+ Response 200
    + Attributes 
        + contact (Contact)

    + Body
    
            {
                "contact":
                {
                    "contactId": "contact-0fxxcf",
                    "userSuppliedId": "12345678",
                    "email": "noreply@giftbit.com",
                    "firstName": "Jane",
                    "lastName": "Smith",
                    "dateCreated": "2016-12-09T00:06:00.000Z"
                }
            }

