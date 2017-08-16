## Implementation Details

### Coding for Idempotency and the `userSuppliedId` Field

The API is fully idempotent on the `userSuppliedId` field, which is a required parameter for all endpoint operations that result in a change of state on the Lightrail side (POST, PUT, PATCH).  
Therefore, in the case of a non-received response from the API or other unknown condition, it is safe and recommended to retry the request with the same `userSuppliedId`.

For example, your customer is using a `fullcode` in your checkout.  
You POST to the _/codes/{fullcode}/transactions_ endpoint to mark the use, but get a network timeout and are unable to process the response. 
As a result, your system doesn't know if Lightrail successfully received your POST and recorded the transaction. 
With idempotency, you do not need any lookups or other complicated error handling to see if the original API call succeeded. 

You can simply retry the same call (as many times as needed) with the same `userSuppliedId`, and get the same 200 response upon success whether or not a previous request went through. 
Our server will do the operation once and only once for that `userSuppliedId`.

Given the above, it is important you think about how to structure your `userSuppliedId`s and error handling so that you are always sending a unique value for different logical requests, but the same one for any retries.  

Attempting to reuse a `userSuppliedId` but providing different request parameters will result in a 409 error.

### About Currency Value, Currency Type, and No-Currency Use (Such as Points)
Where currency type (eg. USD, CDN, AUD) is required or returned, the API expects/uses 3 character uppercase codes conforming to the ISO-4217 standard. 
Lightrail does not do any currency conversion nor does currency influence internal behavior; rather, currency allows you to issue and track cards in different currencies as you choose.

In all cases where value is concerned, you need to provide the amount in the smallest currency unit. 
For most, this is the amount in cents (or pence, penny, or similarly named unit). For example, to create a Card for USD1.00, you would set the initialValue=100 (100 cents).

For zero-decimal currencies or use with non-currency applications such as points, use the regular whole denomination. 
For example, for ¥1, you should set initialValue=1 (1 JPY), since ¥1 is the smallest currency unit.

### About Dates
The API expects all dates in request parameters to conform to the ISO-8601 format, specifically "yyyy-MM-dd'T'HH:mm:ss.SSSZ".  You can see examples in this documentation.

This allows you to control things such as Code expiry in fine granularity to the time zone of your choosing. 
All responses will always be given in this same format.

### Handling Error Responses

Clients should always check the HTTP status code of the response and act accordingly if the response is not a 200.

Error response JSON will be in the following format:
- status: (number) - will match the HTTP response code.    
- message: (string, optional) - a descriptive error message if one is available
- code: (string, optional) - a code that can be provided to Lightrail support if troubleshooting help is needed

#### Example error responses:
| Status | Description         | Message                                                                                                                                          | 
|:-------|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| `400`  | bad request         | Failed to create card due to error during code creation. Response from code creation: Bad Request. Missing Required Parameter 'initialValue'.    | 
| `401`  | unauthorized        | Unauthorized.                                                                                                                                    |
| `409`  | idempotency failure | A different transaction with the same userSuppliedId already exists.                                                                             |
| `429`  | throttled failure   | Too many requests.                                                              
