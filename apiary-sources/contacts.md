## Contacts [/contacts]
Contacts can be attached to Cards to help you keep track of who is interacting with your Cards.

---
### List Contacts [GET /contacts{?limit}{?offset}{?email}{?firstName}{?lastName}{?userSuppliedId}]
Retrieve a paginated list of Contacts.

---
+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
            
+ Parameters 
    + limit (number, optional) - For pagination.  The maximum number of results to return at once. Default 100.
    + offset (number, optional) - For pagination. The offset of the first results in the total results. Default 0.
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
                "contactId":"contact-0cfafcd94b2d42a698771a7b63e37c86",
                "userSuppliedId":"contact3",
                "email":"johnsmom@example.com",
                "firstName":"Sarah",
                "lastName":"Connor",
                "dateCreated":"2017-07-28T21:21:04.000Z"
              }
            }

### Create Contact [POST /contacts]
+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
    + Attributes 
        + userSuppliedId (string, required) - {{userSuppliedId}}.
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
                    "contactId": "contact-0f06b54b9fa44727a7ba7043de5365cf",
                    "userSuppliedId": "12345678",
                    "email": "noreply@giftbit.com",
                    "firstName": null,
                    "lastName": null,
                    "dateCreated": "2016-12-09T00:06:00.000Z"
                }
            }

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

### Retrieve Account Cards or Gift Cards for Contact [GET /cards{?contactId}{?cardType}{?currency}]
Retrieve a paginated list of a Contact's Cards.

---
+ Parameters 
    + contactId (string, required) - The Lightrail Contact ID.
    + cardType (string, required) - `ACCOUNT_CARD`, `GIFT_CARD`
    + currency (string, optional) - {{currency}}. Only needed if your Contact has Account Cards in multiple currencies.

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>
    
+ Response 200
    + Attributes
        + cards (array[Card])
        + pagination (Pagination)

    + Body
    
            {
              "cards":[
                {
                  "cardId":"card-f35b7fb3f7c5435385b709fc7b2bc7a5",
                  "userSuppliedId":"contact2-account",
                  "contactId":"contact-e56b00c7bb8e4f45ac61f941d3f557ba",
                  "dateCreated":"2017-07-03T21:21:00.565Z",
                  "categories":[
                    {
                      "categoryId":"category-3d4ff98bf941489e8216c9ffb9d4efc3",
                      "key":"giftbit_order",
                      "value":"2017-07-28"
                    },
                    {
                      "categoryId":"category-bf9bfbac78004199b2d544d6c830d3c2",
                      "key":"giftbit_program",
                      "value":"program-account-USD-user-1dfd10e5cb3c451382bc79774ab33232-TEST"
                    }
                  ],
                  "cardType":"ACCOUNT_CARD",
                  "currency":"USD"
                }
              ],
              "pagination":{
                "count":1,
                "limit":100,
                "maxLimit":1000,
                "offset":0,
                "totalCount":1
              }
            }