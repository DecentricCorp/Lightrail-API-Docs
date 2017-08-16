## Cards [/cards/]
The `/cards` endpoint is for use by your system to create and manage your Gift Cards and Account Cards.
The `cardId` should be stored as it's used in many subsequent requests - such as transaction against a Card. 
In use cases where the recipient requires a gift code, the `fullcode` should be supplied. 

---
{% include 'endpoints/cards-GET.md' %}

{% include 'endpoints/cards-cardId-GET.md' %}

{% include 'endpoints/cards-POST.md' %}

{% include 'endpoints/cards-cardId-fullcode-GET.md' %}

{% include 'endpoints/cards-cardId-PATCH.md' %}

{% include 'endpoints/cards-cardId-activate-POST.md' %}

{% include 'endpoints/cards-cardId-freeze-POST.md' %}

{% include 'endpoints/cards-cardId-unfreeze-POST.md' %}

{% include 'endpoints/cards-cardId-cancel-POST.md' %}
