## Transaction (object)
+ transactionId (string) - The Lightrail Transaction ID.
+ value (number) - {{transaction.value}}
+ userSuppliedId (string) - {{userSuppliedId}}
+ dateCreated (string) - Lightrail system time of the creation in ISO-8601 format.
+ transactionType (string) - The type of the Transaction: `DRAWDOWN, FUND, INITIAL_VALUE, CANCELLATION, INACTIVATE, ACTIVATE, FREEZE, UNFREEZE, PENDING_CREATE, PENDING_VOID, PENDING_CAPTURE, DRAWDOWN_REFUND`.
+ transactionAccessMethod (string) - Indicates how the transaction was created. Either through the cardId or the fullcode. Possible values: ["CARDID", "RAWCODE"].
+ valueAvailableAfterTransaction (string) - Deprecated. Indicates the value available on the Card after the Transaction. 
+ giftbitUserId (string) - Deprecated.  
+ cardId (string) - The Lightrail Card ID.
+ currency (string) - {{currency}}
+ metadata (object) - A key-value object to store additional information about the transaction. Note Lightrail's Redemption Rules operate on Transaction Metadata to determine whether a particular promotion can be spent. Example: `"metadata":{"checkout-cart":{"items":[{"id":"1"},{"id":"2"}]}}`. Also note, the `giftbit_*` namespace for keys is reserved.
