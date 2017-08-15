### Retrieve Account Cards or Gift Cards for Contact [GET /cards{?contactId}{?cardType}{?currency}]
Retrieve a paginated list of a Contact's Cards.

---
+ Parameters 
    + contactId (string, required) - {{card.cardId}}
    + cardType (string, required) - {{card.cardType}}
    + currency (string, optional) - {{currency}} Only needed if the Contact has Account Cards in multiple currencies.

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
                  "cardId":"card-f3xxa5",
                  "userSuppliedId":"contact2-account",
                  "contactId":"contact-e5xxba",
                  "dateCreated":"2017-07-03T21:21:00.565Z",
                  "categories":[
                    {
                      "categoryId":"category-3dxxc3",
                      "key":"giftbit_order",
                      "value":"2017-07-28"
                    },
                    {
                      "categoryId":"category-bfxxc2",
                      "key":"giftbit_program",
                      "value":"program-account-USD-user-1dxx32"
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