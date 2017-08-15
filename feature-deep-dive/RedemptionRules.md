# Redemption Rules

[Lightrail](https://www.lightrail.com/) redemption rules are extra conditions placed on promotions. A redemption rule is evaluated with the transaction data to determine if the promotion can be used for that transaction. This unlocks powerful marketing promotions such as: "$10 good for purchases of $50 or more" or "$5 off your purchase when you buy a red hat."

## Evaluation

Redemption rules are set for a promotion and evaluated using variables from the submitted drawdown transaction.  They are single statements that evaluate to a single value, not a full script to be executed.  If the rule evaluates to `true` then the promotion can be applied to the transaction.  If the rule evaluates to `false` then the promotion cannot be applied to the transaction.

Here's an example of a not particularly useful redemption rule:

```javascript
1 == 2
```

This rule is made up of two numbers, and one operator: equals.  This rule will evaluate to true when 1 equals 2, which is never.  This promotion can never be redeemed.

Here's a more realistic redemption rule:

```javascript
metadata.cartValue >= 5000
```

This rule evaluates to true if the value of the shopping cart is greater or equal to 5000.  In the currency `USD` this is the rule for "purchase of $50.00 or more."  In the currency `JPY` this would be "purchase of ¥5000 or more."

The four variables of a transaction that can be used for a rule are:
- `value`: a positive number for the value of the Lightrail transaction (which may not be the value of the full cart in split-tender transactions)
- `balance`: a positive number for the balance remaining on the promotion
- `currency`: a string for the ISO code of the currency of the transaction
- `metadata`: a map of arbitrary data that can be sent with the transaction

These variables are exactly as they are passed into the REST endpoint that created the transaction.  `metadata` is by far the most useful, and gets its own section.

## Metadata

The `metadata` variable of the transaction is the most flexible part of the transaction.  It can hold any JSON data and be as specific to your organization as you want.  We recommend evaluating what data you have available that might potentially be used for a promotion, and including that in `metadata`.  Don't worry about including data you have no plans to use in a promotion yet.  Any data not referenced in a redemption rule is safely ignored.  It's better to have data and not need it than need it and not have it.

Here's an example `metadata` JSON object for a doughnut store that includes elements we recommend:

```json
{
  "cartValue": 1960,
  "cart": [
    {
      "category": "doughnut",
      "itemId": "chocolate",
      "value": 150
    },
    {
      "category": "doughnut",
      "itemId": "mapleglazed",
      "value": 150
    },
    {
      "category": "doughnut",
      "itemId": "longjohn",
      "value": 150
    },
    {
      "category": "doughnut",
      "itemId": "bearclaw",
      "value": 250
    },
    {
      "category": "coffee",
      "itemId": "dripcoffee",
      "size": "medium",
      "value": 315
    },
    {
      "category": "coffee",
      "itemId": "dripcoffee",
      "size": "medium",
      "value": 315
    },
    {
      "category": "coffee",
      "itemId": "dripcoffee",
      "size": "medium",
      "value": 315
    },
    {
      "category": "coffee",
      "itemId": "dripcoffee",
      "size": "medium",
      "value": 315
    }
  ],
  "delivery": {
    "pickup": true
  }
}
```

This `metadata` includes a shopping cart with 8 items and their relevant attributes.  There are 4 different doughnuts and 4 identical coffees.  For some systems it might be more convenient to represent the coffees as a single item with a `"quantity": 4` attribute.  This makes writing some redemption rules slightly more complicated but still manageable.

Note what's not included: information about the customer.  To target a specific set of people apply the promotion to their gift cards or account cards when they qualify.  For example a promotion that gives customers $5 for signing up for a newsletter should be applied automatically after signing up.

With this `metadata` we can write redemption rules on cart item attributes, their value, their quantity, and the delivery type.  After introducing the syntax we'll come back to the doughnut shop example with some rules we can write.

## Syntax

Redemption rules follow the syntax of most programming languages.  Mathematical, logical, string and comparison operators behave as usual.

```javascript
-1
1 + 2
3 - 4
5 * 6
7 / 8
9 % 10
!true
true && false
true || false
'foo' + 'bar' == 'foobar'
1 < 2
3 <= 4
6 > 5
8 >= 7
9 == 9
10 != 11
true ? 'yes' : 'no'
```

Parentheses can be used to group operations.

```javascript
4 * (1 + 2)
(1 + 2 + 3) == 6
(9 < 5) || (3 < 5)
```

Lists (arrays) are accessed with brackets and are 0-indexed.

```javascript
myList[0]
myList[1]
myList[variableName]
```

Lists can be created with brackets.

```javascript
[]
[1, 2, 3]
['alpha', 'beta', 'charlie']
```

Maps (dictionaries, objects) can be accessed with dot notation or bracket notation.

```javascript
myMap.child
myMap['child']
myMap[variableName]
```

Functions are global.  Their names are case-sensitive.  They can be accessed like the global function they are, or treated like methods with the first argument acting as the object the method is on.  This second form is more convenient for chaining.  Some functions can accept a variable number of arguments or multiple types.  For a complete list of functions see the [functions appendix](#functions).

```javascript
max(1, 2)
max(1, 2, 3, 4)

sum(1, 2)
sum([1, 2])
sum([1, 2], 3, [4, [5, 6]])

size(['a', 'b', 'c'])
['a', 'b', 'c'].size()

size('hello world')
'hello world'.size()

sum([1, 2, 3, 4])
[1, 2, 3, 4].sum()

round(sum([1.23, 4.56, 7.89]))
[1.23, 4.56, 7.89].sum().round()
```

Some functions operate on collections and take a lambda parameter.  These are the most powerful functions.  Lambdas are written using fat arrow `=>` notation and are single statements that return a value, just like redemption rules.  The parameter names can be any combination of letters.  If the lambda only has one argument the parentheses can be skipped.

```javascript
[1, 2, 3, 4, 5].filter(x => x % 2 == 0) → [2, 4]
[1, 2, 3, 4, 5].find(x => x % 2 == 0) → 2
[1, 2, 3, 4, 5].findIndex(x => x % 2 == 0) → 1
[1, 2, 3, 4, 5].some(x => x % 2 == 0) → true
[1, 2, 3, 4, 5].every(x => x % 2 == 0) → false
[1, 2, 3, 4, 5].map(x => x * 2) → [2, 4, 6, 8, 10]
[1, 2, 3, 4, 5].reduce((accumulator, value) => accumulator + value, 0) → 15
```

Best practice is for `metadata` to be consistent in structure and data types.  The more predictable the better.  The rest of this section describes what happens when `metadata` doesn't match what you expected.

When attempting to access variables that don't exist, `null` will be returned.  This is true even when attempting to access children of `null` (this is known as null propagation).  Functions will return a default value when their inputs can't be used.

```javascript
metadata.doesNotExist → null
metadata.does.not.exist → null
find(null) → null
findIndex(null) → -1
some(null) → false
every(null) → false
map(null) → []
```

Operators and functions will coerce values to the type that best fits what's needed following the same rules as EcmaScript.  For more details see the [type coercion appendix](#type-coercion).

```javascript
!null → true
!0 → true
!196 → false
'1' == 1 → true
3 > '2' → true
4 + '5' → '45'
4 - '5' → -1
true + 1 → 2
max(1, 2, '3') → 3
max(null, null) → 0
```

## Examples

### Doughnut shop examples

In this `metadata` cart items are duplicated if more than one is bought.

```json
{
  "cartValue": 1960,
  "cart": [
    {
      "category": "doughnut",
      "itemId": "chocolate",
      "value": 150
    },
    {
      "category": "doughnut",
      "itemId": "mapleglazed",
      "value": 150
    },
    {
      "category": "doughnut",
      "itemId": "longjohn",
      "value": 150
    },
    {
      "category": "doughnut",
      "itemId": "bearclaw",
      "value": 250
    },
    {
      "category": "coffee",
      "itemId": "dripcoffee",
      "size": "medium",
      "value": 315
    },
    {
      "category": "coffee",
      "itemId": "dripcoffee",
      "size": "medium",
      "value": 315
    },
    {
      "category": "coffee",
      "itemId": "dripcoffee",
      "size": "medium",
      "value": 315
    },
    {
      "category": "coffee",
      "itemId": "dripcoffee",
      "size": "medium",
      "value": 315
    }
  ],
  "delivery": {
    "pickup": true
  }
}
```

#### Spend at least $10

```javascript
metadata.cartValue >= 1000
```

#### Buy any 5 items

```javascript
metadata.cart.size() >= 5
```

#### Canadian Special: discount maple glazed doughnuts

```javascript
metadata.cart.some(item => item.category == 'doughnut' && item.itemId == 'mapleglazed')
```

#### Buy a medium coffee and any doughnut

```javascript
metadata.cart.exists(item => item.category == 'coffee' && item.size == 'medium') && metadata.cart.exists(item => item.category == 'doughnut')
```

#### Buy 4 coffees

```javascript
metadata.cart.filter(item => item.category == 'coffee').size() >= 4
```

#### Buy 4 coffees and pickup in store

```javascript
metadata.cart.filter(item => item.category == 'coffee').size() >= 4 && metadata.delivery.pickup
```

#### Buy any 4 items (of value > $1.00)

```javascript
metadata.cart.filter(item => item.value > 100).size() >= 4
```

### Concert tees examples

In this `metadata` cart items have a `quantity`.

```json
{
  "cartValue": 9593,
  "cart": [
    {
      "category": "shirt",
      "itemId": "fce425c0-53a2-4a46-a37f-40bdfd732ef5",
      "size": "medium",
      "band": "ledzeppelin",
      "value": 3495,
      "quantity": 1
    },
    {
      "category": "shirt",
      "itemId": "6cd226e1-20eb-4acc-bee6-5573243a08b3",
      "size": "medium",
      "band": "rollingstones",
      "value": 3299,
      "quantity": 1
    },
    {
      "category": "cd",
      "itemId": "ba991060-2f57-4e76-838d-76088703cc7c",
      "band": "ledzeppelin",
      "value": 1799,
      "quantity": 1
    },
    {
      "category": "sticker",
      "itemId": "bd086f23-124b-44c6-a4f0-61c2d02a15a4",
      "band": "thewho",
      "value": 200,
      "quantity": 5
    }
  ]
}
```

### Buy any 5 items

```javascript
metadata.cart.map(item => item.quantity).sum() >= 5
```

### Buy any 2 t-shirts

```javascript
metadata.cart.filter(item => item.category == 'shirt').map(item => item.quantity).sum() >= 2
```

### Buy any 2 Led Zeppelin items worth at least $5

```javascript
metadata.cart.filter(item => item.band == 'ledzeppelin' && item.value >= 500).map(item => item.quantity).sum() >= 2
```

### Buy 4 stickers and a shirt

```javascript
metadata.cart.filter(item => item.category == 'sticker').map(item => item.quantity).sum() >= 4 && metadata.cart.some(item => item.category == 'shirt')
```

### Buy $10 worth of stickers

```javascript
metadata.cart.filter(item => item.category == 'sticker').map(item => item.quantity * item.value).sum() >= 1000
```

### Buy $20 worth of stickers or CDs

```javascript
metadata.cart.filter(item => item.category == 'sticker' || item.category == 'cd').map(item => item.quantity * item.value).sum() >= 2000
```

## Appendices

### Cheat sheet

For the developer short on time and long on experience:

- rules are evaluated using `value`, `currency`, `balance` and `metadata` of the transaction JSON
- rules are evaluated to a single value which is then coerced to a boolean
- if the final value is true the transaction can drawdown on the promotion
- syntax is modeled on EcmaScript
- types are dynamic with coercion following EcmaScript's rules
- there is no assignment, no looping, no recursion, no null pointer exception
- all operations on collections are done ala functional programming with lambdas
- all member access is null propagation (like the `?.` operator of C# or Groovy)

### Functions

#### abs

`abs(value: number) → number`

Get the absolute value of the number.

```javascript
abs(1) → 1
abs(-1) → 1
```

#### ceil

`ceil(value: number) → number`

Get the smallest integer that is greater than or equal to the number.

```javascript
ceil(1) → 1
ceil(1.2345) → 2
ceil(-12.34) → -12
```

#### every

`every(list: any[], condition: (listElement: any) => boolean) → boolean`

Test whether every item in a list meets the given condition.

```javascript
every([1, 2, 3], x => x > 0) → true
['a', 'b', 'c', 'd'].every(x => x == 'a') → false
```

#### filter

`filter(list: any[], condition: (listElement: any) => boolean) → any[]`

Get a subset of a list whose members meet the given condition.

```javascript
filter([1, 2, 3], x => x % 2 == 0) → [2]
['a', 'b', 'c', 'd'].filter(x => x != 'a') → ['b', 'c', 'd']
```

#### find

`find(list: any[], condition: (listElement: any) => boolean) → any`

Get the first element of a list that meets the given condition.  Returns null if the condition is never met.

```javascript
find([1, 2, 3], x => x % 2 == 0) → 2
['a', 'b', 'c', 'd'].find(x => x != 'a') → 'b'
['a', 'b', 'c', 'd'].find(x => x == 'e') → null
```

#### findIndex

`findIndex(list: any[], condition: (listElement: any) => boolean) → number`

Get the index of the first element of a list that meets a given condition.  Returns -1 if the condition is never met.

```javascript
findIndex([1, 2, 3], x => x % 2 == 0) → 1
['a', 'b', 'c', 'd'].findIndex(x => x != 'a') → 1
['a', 'b', 'c', 'd'].findIndex(x => x == 'e') → -1
```

#### floor

`floor(value: number) → number`

Get the greatest integer that is less than or equal to the number.

```javascript
floor(1) → 1
floor(1.2345) → 1
floor(-12.34) → -13
```

#### isNaN

`isNaN(value: number) → boolean`

Test whether the number is NaN. NaN is a special case of number resulting from the operation `0 / 0`.

```javascript
isNaN(1 / 0) → false
isNaN(0 / 0) → true
```

#### isNull

`isNull(variable: any) → boolean`

Test whether the value is null.

```javascript
isNull(123) → false
isNull('') → false
isNull(null) → true
```

#### keys

`keys(m: map) → string[]`

Get the keys of the map in a list.

```javascript
keys(null) → []
keys(metadata.foo) → ['itemId', 'size', 'quantity']
```

#### map

`map(list: any[], (listElement: any) => any) → any[]`

Create a new list by applying the operation to each member of the list.

```javascript
map(null) → []
map([1, 2, 3], x => x * 3) → [3, 6, 9]
['a', 'b', 'c'].map(x => x + x + x) → ['aaa', 'bbb', 'ccc']
```

#### max

`max(...values: number | number[]) → number`

Get the greatest number from the numbers and lists of numbers.

```javascript
max(0) → 0
max(1, -1) → 1
max(1, [2, -11]) → 2
max(1, [2, -11], [[99, -88], 23]) → 99
```

#### min

`min(...values: number | number[]) → number`

Get the smallest number from the numbers and lists of numbers.

```javascript
min(0) → 0
min(1, -1) → -1
min(1, [2, -11]) → -11
min(1, [2, -11], [[99, -88], 23]) → -88
```

#### reduce

`reduce(list: any[], callback: (accumulator: any, listElement: any, listElementIndex: number, list: any[]) => any, any) → any`

Get a single value from the list by applying an operation to each element of the list and an accumulator.

```javascript
reduce([1, 1, 2, 3, 5, 8], (accumulator, item) => accumulator + item, 0) → 20
[8, 16, 4, 32, 2, 64, 1].reduce((accumulator, item) => accumulator > item ? accumulator : item, 0) → 64
```

#### round

`round(value: number) → number`

Round the number to the nearest integer, where any number ending in exactly .5 is rounded up.

```javascript
round(1) → 1
round(1.49) → 1
round(12.5) → 13
round(13.5) → 14
```

#### roundBankers

`roundBankers(value: number) → number`

Round the number to the nearest integer, where any number ending in exactly .5 is rounded to the nearest even integer.

```javascript
roundBankers(1) → 1
roundBankers(1.49) → 1
roundBankers(12.5) → 12
roundBankers(13.5) → 14
```

#### size

`size(value: string | number) → number`

Get the length of the list or the number of characters in a string.

```javascript
size('asdf') → 4
size('') → 0
'hello world'.size() → 11
size([]) → 0
size([1, 2, 3]) → 3
['one', 'two', 'three'].size() → 3
```

#### some

`map(list: any[], condition: (listElement: any) => boolean) → boolean`

Test whether some item in a list meets the given condition.

```javascript
some([1, 2, 3], x => x > 0) → true
['a', 'b', 'c', 'd'].some(x => x == 'a') → true
```

#### substring

`substring(haystack: string, start: number = 0, end: number = maxint) → string`

Get a subset of the string.  If only one number is specified the subset will be from that index inclusive to the end.  If the two numbers are specified the subset will be from the lower inclusive to the higher exclusive.

```javascript
substring('foobar', 0) → 'foobar'
substring('foobar', 3) → 'bar'
'foobar'.substring(3, 5) → 'ba'
'foobar'.substring(3, 3) → ''
```

#### sum

`sum(...values: number | number[]) → number`

Get the sum total of the numbers and lists of numbers.

```javascript
sum(5) → 5
sum(5, 5, 5) → 15
[1, 2, 3, 4, 5, -10].sum() → 5
```

#### toLowerCase

`toLowerCase(s: string) → string`

Get the string with all lower case letters converted to upper case letters in the US locale.

```javascript
toLowerCase('Hello World') → 'hello world'
'HoW aRe YoU'.toLowerCase() → 'how are you'
```

#### toUpperCase

`toUpperCase(s: string) → string`

Get the string with all upper case letters converted to lower case letters in the US locale.

```javascript
toUpperCase('Hello World') → 'HELLO WORLD'
'HoW aRe YoU'.toUpperCase() → 'HOW ARE YOU'
```

#### values

`values(m: map) → any[]`

Get the values of the map in a list.

```javascript
values(null) → []
values(metadata.foo) → ['33bbb2bf-c270-41d9-ab42-9eeba99fa69c', 'medium', 6]
```

### Operator precedence

Operators are evaluated in the following order, with higher operators being evaluated first.

- grouping `()`
- member access `[]`
- member dot access `.`
- function call `()`
- unary plus `+`, unary minus `-`, logical not `!`
- multiplication `*`, division `/`, modulo `%`
- addition `+`, subtraction `-`
- less than `<`, less than or equal `<=`, greater than `>`, greater than or equal `>=`
- equal `==`, not equal `!=`
- logical and `&&`
- logical or `||`
- ternary conditional `? :`

### Types

- null (`null`)
- boolean (`false`, `true`)
- number (`0`, `1`, `-3`, `123.345`, ...)
- string (`'foo'`, `"bar"`, ...)
- list (`[]`, `[1, 2, 3]`, `['a', 'b', ['c', 'd']]`, ...)
- map (has no literal constructor)
- lambda (`a => a == 1`, ...) (only available as a function argument)

### Type coercion

| target  | source  | value        | condition          |
|-------- | ------- | ------------ | -------------------|
| boolean | list    | `true`       |                    |
|         | map     | `true`       |                    |
|         | null    | `false`      |                    |
|         | number  | `false`      | number == `0`      |
|         |         | `true`       | number != `0`      |
|         | string  | `false`      | string == `''`     |
|         |         | `true`       | string != `''`     |
| list    | *       | `[]`         |                    |
| map     | *       | an empty map |                    |
| number  | boolean | `0`          | boolean == `false` |
|         |         | `1`          | boolean == `true`  |
|         | list    | `0`          |                    |
|         | map     | `0`          |                    |
|         | null    | `0`          |                    |
|         | string  | †            | †                  |
| string  | boolean | `'false'`    | boolean == `false` |
|         |         | `'true'`     | boolean == `true`  |
|         | list    | ‡            |                    |
|         | map     | ‡            |                    |
|         | number  | ‡            |                    |
|         | null    | `''`         |                    |

† If a string represents a number in string form (eg: `'12'`, `'3.14159'`) then that parsed value will be used. If the string does not represent a number in string form (eg: `'jeff'`, `'twelve'`, `'m365'`) then the value is `0`.

‡ The contents of the list or map will be represented as a string using recursive calls to represent the children as strings.  A number will be represented in parsable form.