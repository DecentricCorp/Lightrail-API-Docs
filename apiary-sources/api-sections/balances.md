## Balances [/cards/{cardId}/balance/]
These endpoints are used to get the available balance associated with a Card. 
Depending on the context of your use-case you can check the balance either based on the `cardId` or the `fullcode`. 
Note that only Gift Cards have an associated `fullcode`.

---
{% include 'endpoints/cards-cardId-balance-GET.md' %}

{% include 'endpoints/codes-fullcode-card-balance-GET.md' %}
