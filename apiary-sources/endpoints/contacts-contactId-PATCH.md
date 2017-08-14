### Update Contact [PATCH /contacts/{contactId}/]
+ Parameters
    + contactId (string, required)
    
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
                    "contactId": "contact-0f06b54b9fa44727a7ba7043de5365cf",
                    "userSuppliedId": "12345678",
                    "email": "noreply@giftbit.com",
                    "firstName": "Jane",
                    "lastName": "Smith",
                    "dateCreated": "2016-12-09T00:06:00.000Z"
                }
            }

