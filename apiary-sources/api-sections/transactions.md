## Transactions [/cards/{cardId}/transactions]
These endpoints are the various ways you can create and retrieve transactions on Cards based on either their `cardId` or their `fullcode`. 
Note that only Gift Cards have an associated `fullcode`.


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


