# Data Structures

## Card (object)
+ cardId (string) - The Lightrail Card ID.
+ userSuppliedId (string) - {{userSuppliedId}}
+ contactId (string) - Lightrail assigned Contact identifier.
+ dateCreated (string) - Lightrail system time of creation in ISO-8601 format.
+ categories (Category) - A key-value object to store additional information about the Card. For example: `"categories": {"city": "san francisco", "special": "earlybird"}` 
+ cardType (string) - {{cardType}}
+ currency (string) - {{currency}}

## ValueStore (object)
+ cardId (string) - The Lightrail Card ID.
+ valueStoreId (string) - The Lightrail ValueStore ID.
+ valueStoreType (string) - `PRINCIPAL`, `ATTACHED`
+ currency (string) - {{currency}}
+ dateCreated (string) - Lightrail system time of the creation in ISO-8601 format.
+ programId (string) - The Lightrail Program ID.
+ expires (string) - The date when the ValueStore expires in ISO-8601 format.
+ startDate (string, optional) - The date for which the ValueStore will become useable.

## Fullcode (object)
+ code (string, required) - The unique unguessable gift code.
+ pin (string, optional) - Card Programs can be configured to generate a pin to be used with the code.

## Transaction (object)
+ transactionId (string) - The Lightrail Transaction ID.
+ value (number) - The value of the transaction. Can be negative or positive. Note, value is in the smallest unit for the currency. If USD, then value represents cents.
+ userSuppliedId (string) - {{userSuppliedId}}
+ dateCreated (string) - Lightrail system time of the creation in ISO-8601 format.
+ transactionType (string) - The type of the Transaciton: `DRAWDOWN, FUND, INITIAL_VALUE, CANCELLATION, INACTIVATE, ACTIVATE, FREEZE, UNFREEZE, PENDING_CREATE, PENDING_VOID, PENDING_CAPTURE, DRAWDOWN_REFUND`.
+ transactionAccessMethod (string) - Indicates how the transaction was created. Either through the cardId or the fullcode. Possible values: ["CARDID", "RAWCODE"].
+ valueAvailableAfterTransaction (string) - Deprecated. Indicates the value available on the Card after the Transaction. 
+ giftbitUserId (string) - Deprecated.  
+ cardId (string) - The Lightrail Card ID.
+ currency (string) - {{currency}}
+ metadata (object) - A key-value object to store additional information about the transaction. Note Lightrail's Redemption Rules operate on Transaction Metadata to determine whether a particular promotion can be spent. Example: `"metadata":{"checkout-cart":{"items":[{"id":"1"},{"id":"2"}]}}`. Also note, the `giftbit_*` namespace for keys is reserved.

## Pagination (object)
+ count (number) - The number of results.
+ offset (number) - The offset of the first results in the total results. Default 0.
+ limit (number) - The maximum number of results to return at once. Default 100.
+ maxLimit (number) - The maximum allowed limit that can be returned in a single page.
+ totalCount (number) - The total number of objects that matched the query.

## Code (object)
+ currency (string, required) - {{currency}}
+ initialValue (number, required) - The value of the code at creation in the smallest currency unit 
  (such as cents).
+ programId (string, required) - The id of the Program for which the code will be created from.
+ expires (string, optional) - The code's expiry date and time.  This field will be ommitted if the 
  code was not created with an expiry.  ISO-8601 format.
+ startDate (string, optional) - The code's start date and time.  This field will be ommitted if 
  the code was not created with an start date.  ISO-8601 format.
+ metadata (object) - A collection of key/value pairs of additional information about the code. 
  The `giftbit_*` namespace for keys is reserved. 

## ValueStoreBalance (object)
+ currentValue (number) - The current value of the ValueStore.
+ state (string) - `ACTIVE`, `INACTIVE`, `NOT_STARTED`, `EXPIRED`, `FROZEN`, `CANCELLED`
+ expires (string, optional) - The date when the ValueStore expires in ISO-8601 format.
+ startDate (string, optional) - The date for which the ValueStore will become useable in ISO-8601 format.
+ programId (string) - The Lightrail Program ID.
+ valueStoreId (string) - The Lightrail ValueStore ID.

## Balance (object)
+ principal (ValueStoreBalance)
+ attached (array[ValueStoreBalance])
+ currency (string) - {{currency}}
+ cardType (string) - {{cardType}}
+ balanceDate (string) - The time the balance was checked.
+ cardId (string) - The Lightrail Card ID.

## Category (object)
+ categoryKey (string) - The key of the category. Examples: "city", "special"
+ categoryValue (string) - The value of the category. Examples: "san francisco", "earlybird"

## Contact (object)
+ contactId (string, required) - The Lightrail Contact ID.
+ userSuppliedId (string, required) - {{userSuppliedId}}
+ dateCreated (string) - Lightrail system time of creation in ISO-8601 format.
+ email (string, optional) - The contact's email.
+ firstName (string, optional) - The contact's first name.
+ lastName (string, optional) - The contact's last name.

## Metadata (object)
+ A collection of key/value pairs of additional information about the transaction. The `giftbit_*` namespace for keys is reserved.
