<a name="use-cases-anchor"></a>
## Common Use-Cases
This section reviews some of the most common use-cases supported by Lightrail in order to help you navigate and find the more detailed step-by-step guides discussing the detailed flow for each use-case. If your use-case is not listed here or if you need assistance about some particular details in your use-case feel free to contact us at hello@lightrail.com.

### Powering Gift Cards

Powering Gift Cards is one of the main of Lightrail. Some of the most common use-cases for Gift Cards are the following. For an step-by-step guide to implementing each use-case, follow the link: 

- [Issue a Gift Card](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/gift-card.md)
- Create and Attach a Promotion to a Gift Card
- View Card Details and Balance
- [Redeem a Gift Card value at the Checkout](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/giftcode-checkout.md)

#### Common Requests in Gift Card Integration

| Action                       | Endpoint                            | Verb   | Body                                     |
| :--------------------------- | ----------------------------------- | ------ | ---------------------------------------- |
| Create Gift Card             | `/v1/cards`                         | `POST` | `{'userSuppliedId':'gc1', 'cardType':'GIFT_CARD', 'programId':'program-123', 'initialValue':500, 'currency':'USD'}` |
| Retrieve `fullcode`          | `/v1/cards/<cardId>/fullcode`       | `GET`  |                                          |
| Check Balance                | `/v1/codes/<fullcode>/card/balance` | `GET`  |                                          |
| Create Transaction           | `/v1/codes/<fullcode>/transactions` | `POST` | `{'userSuppliedId':'tx1', 'value':-10, 'currency':'USD'}` |
| Retrieve Transaction History | `/v1/codes/<fullcode>/transactions` | `GET`  |                                          |

<a name="use-cases-account-credits-anchor"></a>

### Powering an Account Credits

Lightrail can easily power your account credit or customer rewards programs. Some of the most common use-cases for account credits are the following. 

- Creating a new Contact and Issuing New Accounts
- View Account Details and Balance
- Charge or Fund an Account
- Create and Attach a Promotion to an Account
- Redeem Account Value at the Checkout

For a step-by-step discussion of how to implement these use-cases, check out our [Account Credits Implementation Guide](https://github.com/Giftbit/Lightrail-API-Docs/blob/master/use-cases/account-credits.md). 

Note that points programs can be implemented identically by setting up Account Cards with their currency set to points, i.e. `XXX` according to the ISO-4217 standard. 

#### Common Requests in Account Credits or Points Program

| Action                             | Endpoint                                 | Verb   | Body                                     |
| :--------------------------------- | ---------------------------------------- | ------ | ---------------------------------------- |
| Create Contact                     | `/v1/contacts`                           | `POST` | `{'userSuppliedId':'ct1', 'email':'name@example.com'}` |
| Create Account Card                | `/v1/cards`                              | `POST` | `{'userSuppliedId':'ac1', 'cardType':'ACCOUNT_CARD', 'contactId':'contact-123', 'currency':'XXX'}` |
| Retrieve Account Card from Contact | `/v1/cards?contactId=<contactId>&currency=<currency>&cardType=ACCOUNT_CARD` | `GET`  |                                          |
| Check Balance                      | `/v1/cards/<cardId>/balance`             | `GET`  |                                          |
| Create Transaction                 | `/v1/cards/<cardId>/transactions`        | `POST` | `{'userSuppliedId':'tx1', 'value':-10, 'currency':'XXX'}` |
| Retrieve Transaction History       | `/v1/cards/<cardId>/transactions`        | `GET`  |                                          |
