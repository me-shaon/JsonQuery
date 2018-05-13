# js-jsonq

**js-jsonq** is a simple, elegant Javascript package to Query over any type of Json Data.

This package is inspired from the awesome [jsonq](https://github.com/nahid/jsonq) package.

## Installation

```
npm install js-jsonq
```

## Usage

Just import/require the package before start using it.

```javascript
const jsonQ = require('js-jsonq');
```

You can start using this package right away by importing your Json data from a file:

```javascript
const Q = new jsonQ('data.json');
```

Or from a Json String:

```javascript
const Q = new jsonQ('{"id": 1, "name": "shaon"}');
```

Or from a Json Object:

```javascript
const Q = new jsonQ({ id: 1, name: 'shaon' });
```

You can start Query your data using the various query methods such as **find**, **where**, **orWhere**, **whereIn**, **whereStartsWith**, **whereEndsWith**, **whereContains** and so on. Also you can aggregate your data after query using **sum**, **count**, **groupBy**, **max**, **min** etc.

Let's see a quick example:

```javascript
// sample Json data
const JsonObject = {
    products: [
        {
            id: 1,
            city: 'bsl',
            name: 'iPhone',
            cat: 1,
            price: 80000.5
        },
        {
            id: 2,
            city: null,
            name: 'macbook pro',
            cat: 1,
            price: 150000
        },
        {
            id: 3,
            city: 'dhk',
            name: 'Redmi 3S Prime',
            cat: 2,
            price: 12000
        },
        {
            id: 4,
            city: 'bsl',
            name: 'macbook air',
            cat: 2,
            price: 110000
        }
    ]
};

const Q = new jsonQ(JsonObject);
const res = Q.from('products')
    .where('cat', '=', 2)
    .fetch();
console.log(res);

//This will print
/*
[
    {
        id: 3,
        city: 'dhk',
        name: 'Redmi 3S Prime',
        cat: 2,
        price: 12000
    },
    {
        id: 4,
        city: 'bsl',
        name: 'macbook air',
        cat: 2,
        price: 110000
    }
]
*/
```

Let's say we want to get the Summation of _price_ of the Queried result. We can do it easily by calling the **sum()** method instead of **fetch()**:

```Javascript
const res = Q.from('products')
    .where('cat', '=', 2)
    .sum('price');
console.log(res);
//It will print:
/*
122000
*/
```

Pretty neat, huh?

Let's explore the full API to see what else magic this library can do for you.
Shall we?

## API

Following API examples are shown based on the sample JSON data given [here]('./examples/data.json'). To get a better idea of the examples see that JSON data first. Also detailed examples of each API can be found [here]('./examples').

### `fetch()`

This method will execute queries and will return the resulted data. You need to call it finally after using some query methods. Details can be found in other API examples.

### `where(key, op, val)`

* `key` -- the property name of the data. Or you can pass a Function here to group multiple query inside it. See details in [example]('./examples/where.js')
* `val` -- value to be matched with. It can be a _int_, _string_, _bool_ or even _Function_ - depending on the `op`.
* `op` -- operand to be used for matching. The following operands are available to use:

    * `=` : For weak equality matching
    * `eq` : Same as `=`
    * `!=` : For weak not equality matching
    * `neq` : Same as `!=`
    * `==` : For strict equality matching
    * `seq` : Same as `==`
    * `!==` : For strict not equality matching
    * `sneq` : Same as `!==`
    * `>` : Check if value of given **key** in data is Greater than **val**
    * `gt` : Same as `>`
    * `<` : Check if value of given **key** in data is Less than **val**
    * `lt` : Same as `<`
    * `>=` : Check if value of given **key** in data is Greater than or Equal of **val**
    * `gte` : Same as `>=`
    * `<=` : Check if value of given **key** in data is Less than or Equal of **val**
    * `lte` : Same as `<=`
    * `null` : Check if the value of given **key** in data is **null** (`val` parameter in `where()` can be omitted for this `op`)
    * `notnull` : Check if the value of given **key** in data is **not null** (`val` parameter in `where()` can be omitted for this `op`)
    * `in` : Check if the value of given **key** in data is exists in given **val**. **val** should be a plain _Array_.
    * `notin` : Check if the value of given **key** in data is not exists in given **val**. **val** should be a plain _Array_.
    * `startswith` : Check if the value of given **key** in data starts with (has a prefix of) the given **val**. This would only works for _String_ type data.
    * `endswith` : Check if the value of given **key** in data ends with (has a suffix of) the given **val**. This would only works for _String_ type data.
    * `contains` : Check if the value of given **key** in data has a substring of given **val**. This would only works for _String_ type data.
    * `match` : Check if the value of given **key** in data has a Regular Expression match with the given **val**. The `val` parameter should be a **RegExp** for this `op`.
    * `macro` : It would try to match the value of given **key** in data executing the given `val`. The `val` parameter should be a **Function** for this `op`. This function should have a matching logic inside it and return **true** or **false** based on that.


    **example:**

    Let's say you want to get the value of _'cities'_ property of your Json Data. You can do it like this:

    ```Javascript
    const Q = new jsonQ(JsonObject).find('cities');
    ```

    If you want to traverse to more deep in hierarchy, you can do it like:

    ```Javascript
    const Q = new jsonQ(JsonObject).find('cities.1.name');
    ```

    See a detail example [here](./examples/find.js).

### `find(path)`

* `path` -- the path hierarchy of the data you want to find.

    You don't need to call `fetch()` method after this. Because this method will fetch and return the data by itself.

    **caveat:** You can't chain further query methods after it. If you need that, you should use `at()` or `from()` method.

    **example:**

    Let's say you want to get the value of _'cities'_ property of your Json Data. You can do it like this:

    ```Javascript
    const Q = new jsonQ(JsonObject).find('cities');
    ```

    If you want to traverse to more deep in hierarchy, you can do it like:

    ```Javascript
    const Q = new jsonQ(JsonObject).find('cities.1.name');
    ```

    See a detail example [here](./examples/find.js).

### `at(path)`

* `path` (optional) -- the path hierarchy of the data you want to start query from.

    By default, query would be started from the root of the JSON Data you've given. If you want to first move to a nested path hierarchy of the data from where you want to start your query, you would use this method. Skipping the `path` parameter or giving **'.'** as parameter will also start query from the root Data.

    Difference between this method and `find()` is that, `find()` method will return the data from the given path hierarchy. On the other hand, this method will return the Object instance, so that you can further chain query methods after it.

    **example:**

    Let's say you want to start query over the values of _'users'_ property of your Json Data. You can do it like this:

    ```Javascript
    const Q = new jsonQ(JsonObject).at('users').where('id', '=', 1).fetch();
    ```

    If you want to traverse to more deep in hierarchy, you can do it like:

    ```Javascript
    const Q = new jsonQ(JsonObject).at('users.5.visits').where('year', '=', 2011).fetch();
    ```

    See a detail example [here](./examples/at.js).

### `from(path)`

This is an alias method of `at()` and will behave exactly like that. See example [here]('./examples/from.js').

## Bugs and Issues

If you encounter any bugs or issues, feel free to [open an issue at
github](https://github.com/me-shaon/js-jsonq/issues).

Also, you can shoot me an email to
<mailto:shaon.cse81@gmail.com> for hugs or bugs.

## Credit

Speical thanks to [Nahid Bin Azhar](https://github.com/nahid) for the inspiration and guidance for the package.