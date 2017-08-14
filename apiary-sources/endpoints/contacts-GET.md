### List Contacts [GET /contacts{?limit}{?offset}{?email}{?firstName}{?lastName}{?userSuppliedId}]
Retrieve a paginated list of Contacts.

---
+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
            
+ Parameters 
    + limit (number, optional) - {{pagination.limit}}
    + offset (number, optional) - {{pagination.offset}}
    + email (string, optional) - The contact's email.
    + firstName (string, optional) - The contact's first name.
    + lastName (string, optional) - The contact's last name.
    + userSuppliedId (string, required) - {{userSuppliedId}}
    
+ Response 200
    + Attributes
       + contact (Contact)
       + pagination (Pagination)

    + Body
    
            {
              "contacts":[
                {
                  "contactId":"contact-0cfafcd94b2d42a698771a7b63e37c86",
                  "userSuppliedId":"contact3",
                  "email":"johnsmom@example.com",
                  "firstName":"Sarah",
                  "lastName":"Connor",
                  "dateCreated":"2017-07-28T21:21:04.000Z"
                },
                {
                  "contactId":"contact-5e5633f937894404901e3f5cbecf8e2c",
                  "userSuppliedId":"contact4",
                  "email":"whatshappening@example.com",
                  "firstName":"Bill",
                  "lastName":"Lumbergh",
                  "dateCreated":"2017-07-28T21:21:04.000Z"
                }
              ],
              "pagination":{
                "count":61,
                "limit":100,
                "maxLimit":1000,
                "offset":0,
                "totalCount":61
              }
            }

