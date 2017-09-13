## Transactions [/cards/{cardId}/transactions]
Use these endpoints to create and retrieve Transactions on Cards, based on their `cardId` or `fullcode`. 
Note that only Gift Cards have a `fullcode`.


---
{% include 'endpoints/cards-cardId-transactions-POST.md' %}

{% include 'endpoints/codes-fullcode-transactions-POST.md' %}

{% include 'endpoints/cards-cardId-transactions-transactionId-capture-POST.md' %}

{% include 'endpoints/cards-cardId-transactions-transactionId-void-POST.md' %}

{% include 'endpoints/cards-cardId-transactions-transactionId-refund-POST.md' %}


{% include 'endpoints/cards-cardId-transactions-GET.md' %}

{% include 'endpoints/codes-fullcode-transactions-GET.md' %}


{% include 'endpoints/cards-cardId-transactions-transactionId-GET.md' %}

{# ## Codes [/codes/{fullcode}/transactions]
These endpoints are used when interacting directly with the `fullcode` of a Gift Card.
#}

{% include 'endpoints/codes-fullcode-transactions-transactionId-GET.md' %}


