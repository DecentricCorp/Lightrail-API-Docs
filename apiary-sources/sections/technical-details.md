## Implementation Details

### Idempotency 

Ensuring idempotency means to have the option that if the same request is sent more than once, it will not result in repeated actions on the server. This is an important feature for RESTful APIs as it enables the client to safely repeat a potentially failed request without the fear of repeating its consequences. For example, if the call to post a _drawdown_ Transaction ends in a network failure, and you are not sure whether it was failed before or after reaching the server and updating the Card balance, you need to be able to repeat this request without worrying about charging the Card twice.

Lightrail defines`userSuppliedId`, a client-provided attribute which is a required in any API endpoint that is not naturally idempotent. When using the same `userSuppliedId`, Lightrail guarantees that the requested operation is only invoked once, regardless of how many times the request is repeated. This provides a mechanism for you to _retry_ when a request times out or fails for unknown reasons. 

Note that the `userSuppliedId` must also be unique; if you repeat the same `userSuppliedId` with a different request, you will receive an `HTTP 409` indicating idempotency failure. 

Since Lightrail persists and returns the `userSuppliedId` with the corresponding object, you can also use it as a client-side unique identifier to integrate your client-side objects with Lightrail objects. For example, you can use your local Customer ID as the `userSuppliedId` when creating a new Lightrail Contact to associate your local Customer object with the Lightrail Contact object (as opposed to saving the server-generated `contactId` with your local Customer object). You can use the`userSuppliedId` to retrieve the corresponding Contact object when necessary by searching for Contacts and providing the `userSuppliedId` as a search parameter. 

### Currencies 
Lightrail uses the three-character string currency codes from the ISO-4217 standard, e.g. `USD`,` CDN`, and `AUD`. The special value of `XXX` is defined by this standard for representing any non-currency values such as points.

All currency values are represented by the smallest currency unit such as cents for USD or CAD. For example, to create a Card for USD1.00, you would set the `initialValue=100`.

Note that Lightrail does not do any currency exchange and the specified currency does not influence the internal behaviour, so you have to ensure that you are using the right currency when interacting with Lightrail values. For example, if you create a Card with the principal Value Store in USD, you can only USD promotions to this Card and transact against it in USD.

### Dates
Lightrail uses the `yyyy-MM-dd'T'HH:mm:ss.SSSZ` format from the ISO-8601 standard for all dates. This allows you to control things such as value expiry dates in fine granularity to the time zone of your choosing. You can see examples in this documentation. 

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
| `400`  | bad request         | Missing required parameter 'userSuppliedId' |
| `401`  | unauthorized        | Unauthorized.                            |
| `409`  | idempotency failure | A different transaction with the same userSuppliedId already exists. |
| `429`  | rate-limit failure  | Too many requests.                       |
