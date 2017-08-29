sequenceDiagram
participant OrderManager
note left of OrderManager: determine orderTotal
note left of OrderManager: capture Gift Card fullcode 
OrderManager->>Lightrail:get balance(fullcode)
Lightrail-->>OrderManager:giftValue
note left of OrderManager: remainder= orderTotal—giftValue
alt remainder <= 0
OrderManager->>Lightrail: drawdown (fullcode, orderTotal)
Lightrail-->>OrderManager: result
note left of OrderManager: mark order as complete
else remainder > 0
OrderManager->>Lightrail: drawdown (fullcode, giftValue, pending=true)
Lightrail-->> OrderManager :transactionId, cardId 
OrderManager->>PaymentGateway: charge (remainder)
PaymentGateway-->>OrderManager: result
alt third-party payment successful
OrderManager->>Lightrail: capture (cardId, transactionId)
Lightrail-->>OrderManager: result
note left of OrderManager: mark order as complete
else third-party payment failed
OrderManager->>Lightrail: void (cardId, transactionId)
Lightrail-->>OrderManager: result
note left of OrderManager: order payment failed.
end
end

