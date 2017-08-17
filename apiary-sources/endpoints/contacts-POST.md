### Create Contact [POST /contacts]
Creates a new Contact. 

---
+ Request (application/json)
    + Headers
    
            {{header.authorization}}
            
    + Attributes 
        + userSuppliedId (string, required) - {{userSuppliedId}}
        + email (string, optional) - The contact's email.
        + firstName (string, optional) - The contact's first name.
        + lastName (string, optional) - The contact's last name.
        
            
    + Body
    
            {
                "userSuppliedId": "12345678",
                "email": "noreply@giftbit.com"
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
                    "firstName": null,
                    "lastName": null,
                    "dateCreated": "2016-12-09T00:06:00.000Z"
                }
            }

