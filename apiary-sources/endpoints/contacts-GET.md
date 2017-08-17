### List Contacts [GET /contacts{?email}{?firstName}{?lastName}{?userSuppliedId}{?limit}{?offset}]
Retrieve a paginated list of Contacts.

---
+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
            
+ Parameters 
    + email (string, optional) - The contact's email.
    + firstName (string, optional) - The contact's first name.
    + lastName (string, optional) - The contact's last name.
    + userSuppliedId (string, required) - Retrieve the Contact created with a specific `userSuppliedId`. Note that since `userSuppliedId`s are unique, this guarantees that exactly one Contact will be returned if it exists.
    + limit (number, optional) - {{pagination.limit}}
    + offset (number, optional) - {{pagination.offset}}
    
+ Response 200
    + Attributes
       + contact (Contact)
       + pagination (Pagination)

    + Body
    
            {
              "contacts":[
                {
                  "contactId":"contact-0cxx86",
                  "userSuppliedId":"contact3",
                  "email":"johnsmom@example.com",
                  "firstName":"Sarah",
                  "lastName":"Connor",
                  "dateCreated":"2017-07-28T21:21:04.000Z"
                },
                {
                  "contactId":"contact-5exx2c",
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

