<a name="object-model-anchor"></a>

## The Lightrail Object Model
The full API object model is here for your reference. Depending on your use-case, you may not need to use every object/endpoint. 
Read on for use-case elaboration

![Lightrail Object Model](http://resources.giftbit.com/api/embeddedimages/Lightrail_Object_Model_(stacked).png)

### Lightrail Cards: The Core Object

The Card is a fundamental object in the Lightrail API and provides the functionality for managing branded currency. 
The concept of a Card can represent any sort of value that your business wishes to issue. 
Simply stated, a Card is a device for which value can be attached and transacted against. 

There are two types of Cards: Gift Cards and Account Cards. These are both represented as a Card but with a different `cardType` attribute. 

#### Gift Cards

Gift Cards, as you might guess, are used when implementing a gift card program. Gift Cards have a `fullcode` which is a unique and unguessable alpha-numeric code that can be distributed to a recipient. 
In a standard gift card program, the recipient would manually enter the `fullcode` during the checkout process.

#### Account Cards

Account Cards represent value that is attached to a Contact. 
They are used when implementing a customer account credit or points program and can be thought of as a customer's account.
Since Account Cards do not have a `fullcode`, their value is interacted with using the Card object. 
