{#
## Common Use-Cases
This documentation will explore in detail the most common use-cases of the Lightrail API. 
Generally, our users are looking to implement either of the following programs:
<ol>
    <li>A gift card program</li>
    <li>A customer account credit or points program </li>
</ol>

Note that this is not an exhaustive list and some use-cases will require a combination of the two. 
Lightrail is designed to power and enhance any use-case of stored value.  We are here to work with you.

In each scenario, the power of Lightrail Promotions allows you to incentivize and motivate your users. Lightrail also provides a rich analysis interface which will give you more insight into your programs.
#}
## Common Use-Cases

The following sections dive into Lightrail's most common use-cases. Feel free to skip to the section relevant to you. If you're not sure which applies to you, please don't hesitate to ask!

Also note, the ability to incentivize your users or recipients through Lightrail Card Promotions is available for both Gift Cards and Account Cards. 
Detailed information for Card Promotions and how it can be applied to either use-case can be found in the following [Create and Attach Promotions](#create-and-attach-promotions-anchor) section. 

Looking to import your existing codes into the Lightrail system? We support that. Please let us know to find out more. 

### Use-Case 1: Powering Your Gift Card Program
 
A common use-case for the Lightrail API is one in which Lightrail powers your gift cards. 

For example, suppose your business is an online e-commerce platform and you want to enable your customers to purchase gift cards.
When a customer purchases a gift card, you would create a Gift Card in the Lightrail API. You would retrieve the Gift Card's `fullcode` and deliver it to the customer.
Your store's checkout process would need to allow customers the opportunity to enter the `fullcode` of any gift cards they have.
When completing the purchase you would create a Transaction using the customer entered `fullcode`, redeeming the required value from the Gift Card, completing the transaction. 

Is your store is running on an established e-commerce platform? 
Lightrail supports popular e-commerce platforms and has dedicated integrations teams here to support you.

Lightrail enables fine-grained control of the rules for which Gift Cards can be issued using Gift Card Programs.

#### Lightrail Gift Card Programs

A Gift Card Program describes the rules and restrictions that apply to the Gift Cards created from that Program. 
For example, a Gift Card Program might declare that all Gift Cards are issued in USD, with an `initialValue` in the range of $5 - 100, and never expire. 

To create a Gift Card, it must be created using a Gift Card Program. 
You may create Gift Card Programs within the <a href="https://www.lightrail.com/app/#/programs" target="_blank">Gift Card</a> section of the Lightrail web application. 
Gift Card Programs are also used to structure and organize related Gift Cards, and also improve tracking of activity within your account. 

Gift Card Programs also provide the flexibility to add integrators. Adding integrators allows you to lend permission so another party can create and issue Gift Cards from your Gift Card Program. 
For more information on this functionality, please contact hello@lightrail.com.

#### Common Requests in Gift Card Integration

| Action                        | Endpoint                            | Verb   | Body                                                                                                                | 
|:------------------------------|-------------------------------------|--------|---------------------------------------------------------------------------------------------------------------------|
| Create Gift Card              | `/v1/cards`                         | `POST` | `{'userSuppliedId':'gc1', 'cardType':'GIFT_CARD', 'programId':'program-123', 'initialValue':500, 'currency':'USD'}` | 
| Retrieve `fullcode`           | `/v1/cards/<cardId>/fullcode`       | `GET`  |                                                                                                                     |
| Check Balance                 | `/v1/codes/<fullcode>/card/balance` | `GET`  |                                                                                                                     |
| Create Transaction            | `/v1/codes/<fullcode>/transactions` | `POST` | `{'userSuppliedId':'tx1', 'value':-10, 'currency':'USD'}`                                                           | 
| Retrieve Transaction History  | `/v1/codes/<fullcode>/transactions` | `GET`  |                                                                                                                     | 

Note, there may be additional endpoints you'd need to use depending on your use-case. 

### Use-Case 2: Power an Account Credits or Points Program

Another common use-case is one in which Lightrail manages value attached to your customers. 

For example, suppose you'd like to enable your users to earn points within your platform.
Once a user has earned enough points perhaps you'd like to allow them to spend their points and choose a reward.
In this use-case, you would create a Contact in the Lightrail API followed by creating an Account Card for that Contact.
This Account Card would be used to track the customer's points. 
As a customer earns or spends value, you would add or subtract value by creating Transactions against the Account Card in the Lightrail API.

#### Common Requests in Account Credits or Points Program

| Action                             | Endpoint                                                                   | Verb   | Body                                                                                               | 
|:-----------------------------------|----------------------------------------------------------------------------|--------|----------------------------------------------------------------------------------------------------|
| Create Contact                     | `/v1/contacts`                                                             | `POST` | `{'userSuppliedId':'ct1', 'email':'name@example.com'}`                                             | 
| Create Account Card                | `/v1/cards`                                                                | `POST` | `{'userSuppliedId':'ac1', 'cardType':'ACCOUNT_CARD', 'contactId':'contact-123', 'currency':'XXX'}` | 
| Check Balance                      | `/v1/cards/<cardId>/balance`                                               | `GET`  |                                                                                                    |
| Create Transaction                 | `/v1/cards/<cardId>/transactions`                                          | `POST` | `{'userSuppliedId':'tx1', 'value':-10, 'currency':'XXX'}`                                          | 
| Retrieve Transaction History       | `/v1/cards/<cardId>/transactions`                                          | `GET`  |                                                                                                    | 
| Retrieve Account Card from Contact | `/v1/cards?contactId=<contactId>&currency=<currency>&cardType=ACCOUNT_CARD`| `GET`  |                                                                                                    |

Note, in these example calls, the currency is set to `XXX` to represent points although `USD` or any other valid currency code could be used if you wanted to track a dollar figure.
Also, in this sort of use-case, we recommend storing the Account Card's `cardId` with your customer's account within your application. 
This will remove an unnecessary lookup each time you want to update a customer's points.

If this use-case fits your need for points, a great way to reward your customers is offer them gift cards from top brands through <a href="https://www.giftbit.com" target="_blank">Giftbit’s Rewards API.</a>

<a name="create-and-attach-promotions-anchor"></a>
## Create and Attach Promotions

In addition to the many capabilities Lightrail provides out of the box, Lightrail gives you the ability to attach Promotions to Cards, allowing you to incentivize and motivate your users or recipients. 
Promotions work by adding temporary value to your cards with special redemption rules. 
Promotions can be used in both the gift card and account credit use-cases. 

Promotions are created from a Promotion Program which can be created from the <a href="https://www.lightrail.com/app/#/promotions" target="_blank">Promotions</a> section of the Lightrail web application. 
Similar to a Gift Card Program, they enable you to restrict promotion properties such the allowed value ranges, currency, and custom expiry. 
A Card is always created with a `PRINCIPAL` ValueStore. When promotions are attached, they are represented as `ATTACHED` ValueStores.

### How Promotions Fit Into the Lightrail Object Model

For example, suppose you've created a $10 USD Card. It could be either a Gift Card or an Account Card. Below is a model of the newly created objects.

![Promotion Object Model Part1](http://resources.giftbit.com/api/embeddedimages/Lightrail_Promitions_Object_Model_p1.png)

Perhaps you want to add a $5 promotion that will be valid for the next 10 days to incentivize the recipient to spend their value. As you can see below, a new ValueStore is attached to Card. 

![Promotion Object Model Part2](http://resources.giftbit.com/api/embeddedimages/Lightrail_Promitions_Object_Model_p2.png)

Suppose the recipient spends $8 USD within the 10-day limit. Below you can see how the balances of the ValueStore and in turn the balance of the Card is affected.

![Promotion Object Model Part3](http://resources.giftbit.com/api/embeddedimages/Lightrail_Promitions_Object_Model_p3.png)

This example illustrates that when Transactions are created against Cards that have multiple ValueStores, any valid ValueStore will be transacted against in order of expiry. 
As you can see, Lightrail automatically handles the complexity of taking value from the correct ValueStore.

### Redemption Rules

Redemption rules are a powerful feature of Lightrail which enable setting extra conditions on promotions. 
When transacting against a card, redemption rules determine whether or not a promotion can be used for the transaction. 
The rules operate on the transaction details including `metadata`. 
The `metadata` field is a flexible JSON structure that can store any additional information about a transaction such as its payment method or cart items. 
Redemption rules, therefore, can unlock powerful marketing promotions such as: "$5 off when you buy a red hat," or "$20 off if you spend more than $100."

Check out the extended <a href="https://github.com/Giftbit/Lightrail-API-Docs/blob/master/feature-deep-dive/RedemptionRules.md" target="_blank">redemption rules documentation</a> for more information.
