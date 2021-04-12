# TypeScript Style Guide and Coding Conventions

> TypeScript Style Guide

Style Guide inspired by Google, Airbnb, Facebook and Typescript Style Guides

Key Sections:

- [Variable](#variable-and-function)
- [Class](#class)
- [Interface](#interface)
- [Type](#type)
- [Namespace](#namespace)
- [Enum](#enum)
- [`null` vs. `undefined`](#null-vs-undefined)
- [Functions] (#functions)
- [Ordering] (#ordering)
- [Formatting](#formatting)
- [Single vs. Double Quotes](#quotes)
- [Tabs vs. Spaces](#spaces)
- [Use semicolons](#semicolons)
- [Annotate Arrays as `Type[]`](#array)
- [File Names](#filename)
- [`type` vs `interface`](#type-vs-interface)

## Variable and Function

- Use `camelCase` for variable and function names

> Reason: Conventional JavaScript

**Bad**

```ts
let ExampleVar;
function ExampleFunc() {}
```

**Good**

```ts
let exampleVar;
function exampleFunc() {}
```

- General Types (Don’t ever use the types Number, String, Boolean, Symbol, or Object, instead use the types number, string, boolean, and symbol)

> Reason: These types refer to non-primitive boxed objects that are almost never used appropriately in Typescript code

**Bad**

```ts
function reverse(s: String): String;
```

**Good**

```ts
function reverse(s: string): string;
```

## Class

- Use `PascalCase` for class names.

> Reason: This is actually fairly conventional in standard JavaScript.

**Bad**

```ts
class example {}
```

**Good**

```ts
class Example {}
```

- Use `camelCase` of class members and methods

> Reason: Naturally follows from variable and function naming convention.

**Bad**

```ts
class Example {
  Index: number;
  GetIndex() {}
}
```

**Good**

```ts
class Example {
  index: number;
  getIndex() {}
}
```

## Interface

- Use `PascalCase` for name.

> Reason: Similar to class

- Use `camelCase` for members.

> Reason: Similar to class

- **Don't** prefix with `I`

> Reason: Unconventional. `lib.d.ts` defines important interfaces without an `I` (e.g. Window, Document etc). It used to be a good practice, but not anymore!

**Bad**

```ts
interface IExample {}
```

**Good**

```ts
interface Example {}
```

## Type

- Use `PascalCase` for name.

> Reason: Similar to class

- Use `camelCase` for members.

> Reason: Similar to class

## Namespace

- Use `PascalCase` for names

> Reason: Convention followed by the TypeScript team. Namespaces are effectively just a class with static members. Class names are `PascalCase` => Namespace names are `PascalCase`

**Bad**

```ts
namespace example {}
```

**Good**

```ts
namespace Example {}
```

## Enum

- Use `PascalCase` for enum names

> Reason: Similar to Class. Is a Type.

**Bad**

```ts
enum color {}
```

**Good**

```ts
enum Color {}
```

- Use `PascalCase` for enum member

> Reason: Convention followed by TypeScript team i.e. the language creators e.g `SyntaxKind.StringLiteral`. Also helps with translation (code generation) of other languages into TypeScript.

**Bad**

```ts
enum Color {
  red,
}
```

**Good**

```ts
enum Color {
  Red,
}
```

## Null vs. Undefined

- Prefer not to use either for explicit unavailability

> Reason: these values are commonly used to keep a consistent structure between values. In TypeScript you use _types_ to denote the structure

**Bad**

```ts
let plane = { x: 123, y: undefined };
```

**Good**

```ts
let plane: { x: number; y?: number } = { x: 123 };
```

- Use `undefined` in general (do consider returning an object like `{valid:boolean, value?:Foo}` instead)

**Bad**

```ts
return null;
```

**Good**

```ts
return undefined;
```

- Use `null` where it's a part of the API or conventional

> Reason: It is conventional in Node.js e.g. `error` is `null` for NodeBack style callbacks.

**Bad**

```ts
cb(undefined);
```

**Good**

```ts
cb(null);
```

- Use _truthy_ check for **objects** being `null` or `undefined`

**Bad**

```ts
if (error === null)
```

**Good**

```ts
if (!error)
```

## Functions

- Avoid Inlining Function Types

> Reason: For a medium-sized function, it can get very messy and hard to read if function types are inlined.

**Bad**

```ts
const getBooks=(
   searchParams
   :{
     title?:string;
     publishYear?:string;
     author?:string;
   }):Promise<({
     isbn:string;
     title:string;
     publishYear:string;
     author:string[];
    })[]>{
  return fetch(api + createQuery(searchParams:{
     title?:string;
     publishYear?:string;
     author?:string;
   })).then(res=>res.ok ? res.json(): Promise.reject(res))
}
```

**Good**

```ts
interface SearchParams = {
   title?:string;
   publishYear?:string;
   author?:string;
}
interface Book = {
   isbn:string;
   title:string;
   publishYear:string;
   author:string[];
}
type GetBooks (s:SearchParams) => Promise<Book[]>
const getBooks:(s:SearchParams) => Promise<Book[]> = searchParams =>
    fetch(api + createQuery(searchParams))
       .then(res=>res.ok ? res.json(): Promise.reject(res))
```

- Use Objects as Payload for Functions

> Reason: As Functions start to have more than two arguments, it becomes very hard to keep track of what the position of the argument is when calling the function. Also if there are multiple conditional parameters, calling function would have to send undefined if a required parameter is placed after conditional one.

**Bad**

```ts
const sendMail = (
  type: string,
  subject: string,
  body: string,
  to: string,
  attachment?: File
) => {
  return {
    payload: {
      attachment,
      body,
      subject,
      to,
    },
    type,
  };
};
```

**Good**

```ts
interface Payload {
  subject: string;
  body: string;
  to: string;
  attachment?: File;
}

const sendMail = (type: string, payload: Payload) => {
  return {
    payload,
    type,
  };
};
```

## Ordering

- Ordering imports (Node modules first, Alphabetical, Seperate custom modules from installed ones)

> Reason: Easier to find and differentiate imports

**Bad**

```ts
import { User } from "./interfaces";
import { moment } from "moment";
import { Circle } from "circle";
```

**Good**

```ts
import { Circle } from "circle";
import { moment } from "moment";

import { User } from "./interfaces";
```

- Ordering Class Members (Alphabetical)

> Reason: Easier to find members

**Bad**

```ts
class Employee {
  private id: string;
  private empCode: number;
  private empName: string;
}
```

**Good**

```ts
class Employee {
  private empCode: number;
  private empName: string;
  private id: string;
}
```

## Formatting

The TypeScript compiler ships with a very nice formatting language service. Whatever output it gives by default is good enough to reduce the cognitive overload on the team.

Use [`tsfmt`](https://github.com/vvakame/typescript-formatter) to automatically format your code on the command line. Also, your IDE (atom/vscode/vs/sublime) already has formatting support built-in.

Examples:

```ts
// Space before type i.e. foo:<space>string
const foo: string = "hello";
```

## Quotes

- Prefer single quotes (`'`) unless escaping.

> Reason: More JavaScript teams do this (e.g. [airbnb](https://github.com/airbnb/javascript), [standard](https://github.com/feross/standard), [npm](https://github.com/npm/npm), [node](https://github.com/nodejs/node), [google/angular](https://github.com/angular/angular/), [facebook/react](https://github.com/facebook/react)). It's easier to type (no shift needed on most keyboards). [Prettier team recommends single quotes as well](https://github.com/prettier/prettier/issues/1105)

> Double quotes are not without merit: Allows easier copy paste of objects into JSON. Allows people to use other languages to work without changing their quote character. Allows you to use apostrophes e.g. `He's not going.`. But I'd rather not deviate from where the JS Community is fairly decided.

- When you can't use double quotes, try using back ticks (\`).

> Reason: These generally represent the intent of complex enough strings.

## Spaces

- Use `2` spaces. Not tabs.

> Reason: More JavaScript teams do this (e.g. [airbnb](https://github.com/airbnb/javascript), [idiomatic](https://github.com/rwaldron/idiomatic.js), [standard](https://github.com/feross/standard), [npm](https://github.com/npm/npm), [node](https://github.com/nodejs/node), [google/angular](https://github.com/angular/angular/), [facebook/react](https://github.com/facebook/react)). The TypeScript/VSCode teams use 4 spaces but are definitely the exception in the ecosystem.

## Semicolons

- Use semicolons.

> Reasons: Explicit semicolons helps language formatting tools give consistent results. Missing ASI (automatic semicolon insertion) can trip new devs e.g. `foo() \n (function(){})` will be a single statement (not two). TC39 [warning on this as well](https://github.com/tc39/ecma262/pull/1062). Example teams: [airbnb](https://github.com/airbnb/javascript), [idiomatic](https://github.com/rwaldron/idiomatic.js), [google/angular](https://github.com/angular/angular/), [facebook/react](https://github.com/facebook/react), [Microsoft/TypeScript](https://github.com/Microsoft/TypeScript/).

## Array

- Annotate arrays as `foos: Foo[]` instead of `foos: Array<Foo>`.

> Reasons: It's easier to read. It's used by the TypeScript team. Makes easier to know something is an array as the mind is trained to detect `[]`.

## Filename

Name files with `camelCase`. E.g. `accordion.tsx`, `myControl.tsx`, `utils.ts`, `map.ts` etc.

> Reason: Conventional across many JS teams.

## type vs. interface

- Use `type` when you _might_ need a union or intersection:

```
type Foo = number | { someProperty: number }
```

- Use `interface` when you want `extends` or `implements` e.g.

```
interface Foo {
  foo: string;
}
interface FooBar extends Foo {
  bar: string;
}
class X implements FooBar {
  foo: string;
  bar: string;
}
```
