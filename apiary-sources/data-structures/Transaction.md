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
+ metadata (object) - {{transaction.metadata}}
