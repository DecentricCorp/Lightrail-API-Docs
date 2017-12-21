## Implementation Details

### Idempotency 

Ensuring idempotency means providing the option of repeating a request in a way that it will not result in repeated actions on the server. This is an important feature for RESTful APIs as it enables the client to safely repeat a potentially failed request without the fear of repeated consequences. 

For example, if the call to post a _drawdown_ Transaction encounters a network failure and you are not sure whether it failed before or after reaching the server, you would want to be able to repeat this request without worrying about charging the Card multiple times. 

Lightrail supports idempotency via `userSuppliedId`, a unique string value provided by the client. This is a required parameter in every API endpoint which is not naturally idempotent. When using the same `userSuppliedId`, Lightrail guarantees that the requested operation is only invoked once, regardless of how many times the request is received. This provides a mechanism for you to _retry_ when a request times out or fails for unknown reasons. 

Note that the `userSuppliedId` must also be unique to the request; if you repeat the same `userSuppliedId` with a different request, you will receive an `HTTP 409` error indicating idempotency failure. 

Here is an example of a _drawdown_ request which includes a `userSuppliedId`. No matter how many time this request is received by the server, it will charge the Card only once. Moreover, if you try posting a different Transaction (e.g. with a different value) using this `userSuppliedId` you will get an error. 

```
POST https://www.lightrail.com/v1/cards/{cardId}/transactions
{
  "userSuppliedId": "tx-2403423",
  "value": -13500,
  "currency": "USD"
}
```

Since Lightrail persists and returns the `userSuppliedId` with the corresponding object, you can also use it as a client-side unique identifier to link your client-side objects to Lightrail objects. For example, you can use your local Customer ID as the `userSuppliedId` when creating a new Lightrail Contact to associate your local Customer object with the Lightrail Contact object (as an alternative to saving the server-generated `contactId` with your local Customer object). You can use the `userSuppliedId` to retrieve the corresponding Contact object when necessary by [searching Contacts](#contact-list-anchor) and providing the `userSuppliedId` as a search parameter. 

### Currencies 
Lightrail API uses the three-character currency codes from the ISO-4217 standard, e.g. `USD`,` CDN`, and `AUD`. The special value `XXX` is defined by this standard for representing any non-currency values such as points.

All currency values are represented by the smallest currency unit, e.g. cents for USD or CAD. For example, to create a Card for USD1.00, you would set the `initialValue=100`.

Lightrail does not do any form of currency exchange and only enforces the consistency of currencies among connected Lightrail objects. For example, if you create a Card with the principal Value Store in USD, you can only attach USD promotions to this Card and transact against it in USD. Note that Lightrail Transaction endpoints do not presume a default currency and you must provide the currency explicitly when posting a Transaction.

### Dates
Lightrail uses the `yyyy-MM-dd'T'HH:mm:ss.SSSZ` format from the ISO-8601 standard for all dates. This allows you to control things such as value expiry dates in fine granularity to the time zone of your choosing. You can see various examples of date values in the endpoint documentation. 

### Handling Error Responses

Clients should always check the HTTP status code of the response and act accordingly if the response is not a 200.

The response JSON object in case of an error will be in the following format:
- `status`: (number) - echoing the HTTP response code.    
- `message`: (string, optional) - a descriptive error message if one is available. 
- `messageCode`: (string, optional) - a code for the error that can be consumed programmatically.

Note that the error `message` is intended to be shown to an end user; it may change from time to time to improve clarity and might be translated into other languages. For programmatic use, you should use the `messageCode` which reliably corresponds to the particular error case and is not subject to change without notice.

#### Example error responses:
| Status | Description         | Message                                  |
| :----- | ------------------- | ---------------------------------------- |
| `400`  | bad request         | `Missing required parameter 'userSuppliedId'` |
| `401`  | unauthorized        | `Unauthorized.`                          |
| `409`  | idempotency failure | `A different transaction with the same userSuppliedId already exists.` |
| `429`  | rate-limit failure  | `Too many requests.`                     |
