## Transactions [/cards/{cardId}/transactions]
A feature the Lightrail API supports is the ability to create Pending Transactions. 
This means the value required for the Pending Transaction will be unavailable to be used for other Transactions. 
A Pending Transaction can either be captured, which confirms the Transaction, or it can be voided, freeing up their associated value. 

{% include 'endpoints/cards-cardId-transactions-GET.md' %}

{% include 'endpoints/cards-cardId-transactions-transactionId-GET.md' %}

{% include 'endpoints/cards-cardId-transactions-POST.md' %}

{% include 'endpoints/cards-cardId-transactions-transactionId-capture-POST.md' %}

{% include 'endpoints/cards-cardId-transactions-transactionId-void-POST.md' %}

{% include 'endpoints/cards-cardId-transactions-transactionId-refund-POST.md' %}

## Codes [/codes/{fullcode}/transactions]
These endpoints are used when interacting directly with the `fullcode` of a Gift Card.

---
{% include 'endpoints/codes-fullcode-transactions-GET.md' %}

{% include 'endpoints/codes-fullcode-transactions-transactionId-GET.md' %}

{% include 'endpoints/codes-fullcode-transactions-POST.md' %}

