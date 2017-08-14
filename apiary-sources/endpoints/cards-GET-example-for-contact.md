### Retrieve Account Cards or Gift Cards for Contact [GET /cards{?contactId}{?cardType}{?currency}]
Retrieve a paginated list of a Contact's Cards.

---
+ Parameters 
    + contactId (string, required) - The Lightrail Contact ID.
    + cardType (string, required) - {{cardType}}
    + currency (string, optional) - {{currency}} Only needed if your Contact has Account Cards in multiple currencies.

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