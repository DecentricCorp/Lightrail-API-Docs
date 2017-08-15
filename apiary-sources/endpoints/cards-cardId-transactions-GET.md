### List Card Transactions Based on `cardId` [GET /cards/{cardId}/transactions{?limit}{?offset}]
Retrieve a paginated list of a Card's Transactions based on the Card ID.

---
+ Parameters
    + cardId (string, required) - {{card.cardId}}

+ Request (application/json)
    + Headers
    
            Authorization: Bearer <YOUR_ACCESS_TOKEN>

+ Parameters
    + limit (number, optional) - {{pagination.limit}}
    + offset (number, optional) - {{pagination.offset}}
    
+ Response 200
    + Attributes
        + transactions (array[Transaction])
        + pagination (Pagination)

    + Body
    
            {
              "transactions": [
                {
                    "transactionId": "transaction-c8xx2b",
                    "value": -13,
                    "userSuppliedId": "tx001184",
                    "dateCreated": "2017-08-15T21:59:01.571Z",
                    "transactionType": "DRAWDOWN",
                    "transactionAccessMethod": "CARDID",
                    "valueAvailableAfterTransaction": 26987,
                    "giftbitUserId": "user-08xx8e",
                    "cardId": "card-6dxx89",
                    "currency": "USD",
                    "parentTransactionId": "transaction-9axx70",
                    "metadata": {
                    "giftbit_initial_transaction_id": "transaction-9axx70"
                    }
                },
                {
                    "transactionId": "transaction-aexx63",
                    "value": 13,
                    "userSuppliedId": "tx001184-reverse",
                    "dateCreated": "2017-08-15T21:59:01.557Z",
                    "transactionType": "PENDING_CAPTURE",
                    "transactionAccessMethod": "CARDID",
                    "valueAvailableAfterTransaction": 27000,
                    "giftbitUserId": "user-08xx8e",
                    "cardId": "card-6dxx89",
                    "currency": "USD",
                    "parentTransactionId": "transaction-9axx70",
                    "metadata": {
                    "giftbit_initial_transaction_id": "transaction-9axx70"
                }
                },
                {
                    "transactionId": "transaction-9axx70",
                    "value": -13,
                    "userSuppliedId": "test001183",
                    "dateCreated": "2017-08-15T21:56:40.669Z",
                    "transactionType": "PENDING_CREATE",
                    "transactionAccessMethod": "CARDID",
                    "valueAvailableAfterTransaction": 26987,
                    "giftbitUserId": "user-08xx8e",
                    "cardId": "card-6dxx89",
                    "currency": "USD"
                },
                {
                    "transactionId": "transaction-72xx69",
                    "value": 13500,
                    "userSuppliedId": "test001182",
                    "dateCreated": "2017-08-15T21:45:33.547Z",
                    "transactionType": "FUND",
                    "transactionAccessMethod": "CARDID",
                    "valueAvailableAfterTransaction": 27000,
                    "giftbitUserId": "user-08xx8e",
                    "cardId": "card-6dxx89",
                    "currency": "USD"
                },
                {
                    "transactionId": "transaction-95xx1d",
                    "value": -15875,
                    "userSuppliedId": "11xx62-capture",
                    "dateCreated": "2017-08-10T00:08:54.697Z",
                    "transactionType": "DRAWDOWN",
                    "transactionAccessMethod": "CARDID",
                    "valueAvailableAfterTransaction": 0,
                    "giftbitUserId": "user-08xx8e-TEST",
                    "cardId": "card-6dxx89",
                    "currency": "USD",
                    "parentTransactionId": "transaction-a6xx9d",
                    "metadata": {
                    "giftbit_initial_transaction_id": "transaction-a6xx9d"
                    }
                }
              ],
              "pagination": {
                "count": 4,
                "limit": 100,
                "maxLimit": 1000,
                "offset": 0,
                "totalCount": 4
              }
            }