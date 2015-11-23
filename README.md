# Rtype

Intuitive structural type notation for JavaScript.

> Dear JSDoc,
>
> We've had some good times.
> But it's over. I've moved on.
>
> I've switched to Rtype.
> Now my interface docs are alive!
>
> ~ Eric


## About Rtype

* Compiler-free type notation.
* Standing on the shoulders of giants: ES6, TypeScript, Haskell, Flow, & React

## What is Rtype?

Rtype is a JS-native representation of structural type interfaces with a TypeScript-inspired notation that's great for documentation.


## Status: RFC

Developer preview. [Please comment](https://github.com/ericelliott/rtype/issues/new).

Breaking changes expected.

In the future, the `rtype` library will parse rtype strings and return a predicate function for type checking.

If you're interested in using rtype to build interfaces in your standard JavaScript code, see [rfx](https://github.com/ericelliott/rfx).


## Why?

Perhaps the most important part of API documentation is to quickly grasp the function signatures and data structures required to work with the API. There are existing standards for this stuff, but I don't like them, for various reasons.

* JSDoc is too verbose, not intuitive, and painful to maintain.
* TypeScript's structural types are very appealing, but opting into TypeScript's JS superset and runtime limitations is not.

I want a type representation that is very clear to modern JavaScript developers (ES2015+), that could potentially be used at runtime with simple utilities.


## Why Not Just Use TypeScript?

I want the best of all worlds:

* A runtime accessible type system (even in production) with optional runtime type checks (like React.PropTypes -- only in development mode).
* An easy way to generate interface documentation like JSDoc.
* An intuitive way to describe interfaces for the purposes of documentation, particularly function signatures.
* [A great way to specify types in standard ES2015+ code](https://github.com/ericelliott/rfx#rfx) for live interactive development.
* A JS-native solution so I can use whatever ES-compatible transpiler I want (e.g., Babel)

TypeScript is great for compile-time and IDE features, and you could conceivably generate docs with it, but runtime features are lacking. For example, I want the ability to query function signatures inside the program at runtime, along with the ability to turn type checking on and off. AFAIK, that's not possible with TypeScript (yet - there is experimental runtime support using experimental features of the ES7+ `Reflect` API).



## Reading Function Signatures

Function types are described by a **function signature**. The function signature tells you each parameter and its type, separated by a colon, and the corresponding return type:

```js
(param: Type) => ReturnType
```

To make the signature familiar to readers, we use common JavaScript idioms such as destructuring, defaults, and rest parameters:

```js
({ count = 0: Number }) => Any
(...args: String[]) => Any
(myArray[]) => Any
```

Optionally, you may name the return value, similar to named parameters:

```js
(param: Type) => name: Type
```

e.g.:
```js
connect(options: Object) => connection: Object
```

You can omit the name of a parameter if need be:
```js
String.isString(Any) => Boolean
```

### Optional Parameters

Optional parameters can be indicated with `?`:

```js
(param: Type, optParam?: Type) => ReturnType
```

### Array Types

Arrays with typed contents can be represented like this:

```js
Number[]
```


### Union Types

Union types are denoted with the pipe symbol, `|`:

```js
(userInput: String|Number) => String|Number
```

### Builtin Types

```js
Any, Array, Boolean, Function, Number, Object, String, Void
```

Many builtin types are named after JavaScript constructors. Many syntax highlighters will make the types stand out when the signature is rendered in the docs.

#### The `Any` Type

The special type `Any` means that any type is allowed:

```js
(...args: Any[]) => Array
```

#### The `Void` Type

The special type `Void` should only be used to indicate that a function doesn’t return any value.

```js
element.setAttribute(name: String, value: String) => Void
```


### Throwing functions

To indicate that a function can throw an error you can use the `throws` keyword.

```js
functionName() => String, throws: TypeError|DOMException
```

For the generic `Error` type, you can optionally omit the throw type:

```js
functionName() => String, throws
```

Is equivalent to:

```js
functionName() => String, throws: Error
```



### Interface: User Defined Types

You can create your own types using `interface`:

```js
User, Record, Avatar, Cart
```

An interface spells out the structure of the object:

```js
interface User {
  name: String,
  avatarUrl?: Url,
  about?: String
}
```

Interfaces support object spread:

```js
interface User {
  name: String,
  avatarUrl?: Url,
  about?: String,
  ...properties?: Any
}
```


There's a shorthand for builtin literal types:

```js
interface User {
  name: /\w+/,
  description?: '',
  likes?: [],
  data?: {}
}
```

An interface that is a function can have a function signature:

```js
interface User {
  ({ name: String,  avatarUrl?: Url }) => User
}
```

A regular function signature is shorthand for a function interface:

```js
user({ name: String, avatarUrl?: Url }) => User
```

For polymorphic functions, use the `interface` keyword with multiple function signatures:

```js
interface Collection {
  (items: Array[]) => Array[],
  (items: Object[]) => Object[]
}
```

Note that named function signatures in an interface block indicate methods, rather than additional function signatures:

```js
interface Collection {
  (signatureParam: Any) => Any, // Collection() signature
  method1(items: Array[]) => Array[], // method
  method2(items: Object[]) => Object[] // method
}

// in JS:
// typeof Collection === 'function'
// typeof Collection.method1 === 'function'
// typeof Collection.method2 === 'function'
```

A one-line interface doesn't need brackets:

```js
interface Name: /\w+/
```


## References

Somewhat related ideas and inspiration sources.

* [TypeScript](http://www.typescriptlang.org/)
* [Flow](http://flowtype.org/)
* [Typed Objects](http://wiki.ecmascript.org/doku.php?id=harmony:typed_objects)
* [jsig](https://github.com/jsigbiz/spec)
