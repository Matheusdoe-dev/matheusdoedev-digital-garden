# Narrowing

- Imagine we have a function called `padLeft`

```ts
function padLeft(padding: number | string, input: string): string {
  throw new Error("Not implemented yet!");
}
```

- If `padding` is a `number`, it will treat that as the number of spaces we want to prepend to `input`. If `padding` is a `string`, it should just prepend `padding` to `input`. Let's try to implement the logic for when `padLef` is passed a `number` for `padding`.

```ts
function padLeft(padding: number | string, input: string) {
  return new Array(padding + 1).join(" ") + input;
  // Operator '+' cannot be applied to types 'string | number' and 'number'.
}
```

- TypeScript is warning us that adding a `number` to a `number | string` might not give us what we want, and it's right. In other words, we haven't explicity checked if `padding` is a `number` first, nor are we handling the case where it's a `string`, so let's do exactly that.

```ts
function padLeft(padding: number | string, input: string) {
  if (typeof padding === "number") {
    return new Array(padding + 1).join(" ") + input;
  }

  return padding + input;
}
```

- Within our `if` check, TypeScript sees `typeof padding === "number"` and understands that as a special form of code called a _type guard_. TypeScript follows possible paths of execution that our programs can take to analyze the most specific possible type of a value at a given position. It looks at these special checks (called _type guards_) and assignments, and the process of refining types to more specific types than declared is called _narrowing_. In many editors we can observe these types as they changes, and we'll even do so in our examples.

## `typeof` Type Guards

- As we’ve seen, JavaScript supports a typeof operator which can give very basic information about the type of values we have at runtime. TypeScript expects this to return a certain set of strings:

  - "string"
  - "number"
  - "bigint"
  - "boolean"
  - "symbol"
  - "undefined"
  - "object"
  - "function"

- Like we saw with `padLeft`, this operator comes up pretty often in a number of JavaScript libraries, and TypeScript can understand it to narrow types in different branches.
- In TypeScript, checking against the value returned by `typeof` is a type guard. Because TypeScript encodes how `typeof` operates on different values, it knows about some of it's quirks in JavaScript.

```ts
function printAll(strs: string | string[] | null) {
  if (typeof strs === "object") {
    for (const s of strs) {
      // Object is possibly 'null'.
      console.log(s);
    }
  } else if (typeof strs === "string") {
    console.log(strs);
  } else {
    // do nothing
  }
}
```

## Truthiness narrowing

- In JavaScript, we can use any expression in conditionals, `&&`s, `||`s, `if` statements, and Boolean negations (`!`), and more. As an example, `if` statements don’t expect their condition to always have the type `boolean`.

```ts
function getUsersOnlineMessage(numUsersOnline: number) {
  if (numUsersOnline) {
    return `There are ${numUsersOnline} online now!`;
  }
  return "Nobody's here. :(";
}
```

- In JavaScript, constructs like `if` first "coerce" their conditions to `boolean`s to make sense of them, and then choose their branches depending on wheter the result is `true` or `false`.

- Values like `0`, `NaN`, `""` (the empty string), `0n` (the bigint version of zero), `null` and `undefined` all coerce to `false`, and other values get coerced true.

- You can always coerce values to `boolean`s by running them through the `Boolean` function, or by using the shorter double-Boolean negation.

```ts
// both of these result in 'true'
Boolean("hello");
!!"world";
```

- It's fairly popular to leverage this behavior, especially for guardig against values like `null` or `undefined`.

```ts
function printAll(strs: string | string[] | null) {
  if (strs && typeof strs === "object") {
    for (const s of strs) {
      console.log(s);
    }
  } else if (typeof strs === "string") {
    console.log(strs);
  }
}
```

## Equality narrowing

- TypeScript also uses `switch` statements and equality checks like `===`, `!==`, `==`, and `!=` to narrow types.

```ts
function example(x: string | number, y: string | boolean) {
  if (x === y) {
    // We can now call any 'string' method on 'x' or 'y'.
    x.toUpperCase();

(method) String.toUpperCase(): string
    y.toLowerCase();

(method) String.toLowerCase(): string
  } else {
    console.log(x);

(parameter) x: string | number
    console.log(y);

(parameter) y: string | boolean
  }
}
```

## The `in` operator narrowing

- JavaScript has an operator for determining if an object has a property with a name: the `in` operator. TypeScript takes this into account as a way to narrow down potential types.

```ts
type Fish = { swim: () => void };
type Bird = { fly: () => void };

function move(animal: Fish | Bird) {
  if ("swim" in animal) {
    return animal.swim();
  }

  return animal.fly();
}
```

- To re-iterate optional properties will exist in both sides for narrowing, for example a human could both swim and fly and thus should show up in both sides of the `in` check:

```ts
type Fish = { swim: () => void };
type Bird = { fly: () => void };
type Human = { swim?: () => void; fly?: () => void };

function move(animal: Fish | Bird | Human) {
  if ("swim" in animal) {
    animal;
    // (parameter) animal: Fish | Human
  } else {
    animal;
    // (parameter) animal: Bird | Human
  }
}
```

## `instanceof` narrowing

- JavaScript has an operator for checking whether or not a value is an "instance" of another value. More specifically, in JavaScript `x instance Foo` checks whether the _prototype chain_ of `x` contains `Foo.prototype`.

```ts
function logValue(x: Date | string) {
  if (x instanceof Date) {
    console.log(x.toUTCString());
    // (parameter) x: Date
  } else {
    console.log(x.toUpperCase());
    // (paramter) x: string
  }
}
```

## Assignments

- As we mentioned earlier, when we assign to any variable, TypeScript looks at the right side of the assignment and narrows the left side appropriately.

```ts
let x = Math.random() < 0.5 ? 10 : "hello world!";
// let x: string | number

x = 1;

console.log(x);
// let x: number

x = "goodbye";

console.log(x);
// let x: string
```

- Notice that each of these assignments is valid. Even though the observed type of `x` changed to `number` after our first assignment, we were still able to assign a `string` to `x`. This is because the _declare type_ of `x` - the type that `x` started with - is `string | number`, and assignability is always checked against the declared type.

- If we'd assigned a `boolean` to `x`, we'd have seen an error since that wasn't part of the declared type.

```ts
let x = Math.random() < 0.5 ? 10 : "hello world!";
// let x: string | number

x = 1;

console.log(x);
// let x: number

x = true;
// Type 'boolean' is not assignable to type 'string | number'

console.log(x);
// let x: string | number
```

## Control flow analysis

```ts
function padLeft(padding: number | string, input: string) {
  if (typeof padding === "number") {
    return new Array(padding + 1).join(" ") + input;
  }
  return padding + input;
}
```

- `padLeft` returns from within its first `if` block. TypeScript was able to analyze this code and see that the rest of the body (`return padding + input`); is _unreachable_ in the case where `padding` is a `number`. As a result, it was able to remove `number` from the type of `padding` (narrowing from `string | number` to string) for the rest of the function.

- This analysis of code based on reachability is called _control flow analysis_, and TypeScript uses this flow analysis to narrow types as it encounters type guards and assignments. When a variable is analyzed, control flow can split off and re-merge over and over again, and that variable can be observed to have a different type at each point.

## Using type predicates

- Sometimes you want more direct control over how types changes throughout your code.

- To define a user-defined type guard, we simply need to define a function whose return type is a _type predicate_

```ts
function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined;
}
```

- `pet is Fish` is our type predicate in this example. A predicate takes the form `parameterName is Type`, where `parameterName` must be the name of a parameter from the current function signature.

- Any time `isFish` is called with some variable, TypeScript will _narrow_ that variable to that specific type if the original type is compatible.

```ts
// Both calls to 'swim' and 'fly' are now okay
let pet = getSmallPet();

if (isFish(pet)) {
  pet.swim();
} else {
  pet.fly();
}
```

- Notice that TypeScript not only knows that `pet` is a `Fish` in the `if` branch; it also knows that in the `else` branch, you don't have a `Fish`, so you must have a `Bird`.

- You may use the type guard `isFish` to filter an array of `Fish | Bird` and obtain an array of `Fish`

```ts
const zoo: (Fish | Bird)[] = [getSmallPet(), getSmallPet(), getSmallPet()];
const underWater1: Fish[] = zoo.filter(isFish);
// or, equivalently
const underWater2: Fish[] = zoo.filter(isFish) as Fish[];

// The predicate may need repeating for more complex examples
const underWater3: Fish[] = zoo.filter((pet): pet is Fish => {
  if (pet.name === "sharkey") return false;
  return isFish(pet);
});
```

## Discriminated unions

```ts
interface Shape {
  kind: "circle" | "square";
  radius?: number;
  sideLength?: number;
}
```

- Notice we're using a union of string literal types: "circle" and "square" to tell us wheter we should treat the shape as a circle or square respectively. By using `"circle" | "square"` instead of `string`, we can avoid misspelling issues.

```ts
function handleShape(shape: Shape) {
  // oops!
  if (shape.kin === "rect") {
    // This condition will always return 'false' since the types '"circle" | "square"' and '"rect"' have no overlap.
    // ...
  }
}
```

- We can write a `getArea` function that applies the right logic based on if it's dealing with a circle or square. We'll first try deling with circles.

```ts
function getArea(shape: Shape) {
  return Math.PI * shape.radius ** 2;
  // Object is possibly 'undefined".
}
```

- Under `strictNullChecks` that gives us an error - which is appropriate since `radius` might not be defined. But what if we perform the appropriate checks on the `kind` property?

```ts
function getArea(shape: Shape) {
  if (shape.kind === "circle") {
    return Math.PI * shape.radius ** 2;
    // Object is possibly 'undefined"
  }
}
```

- Hmm, TypeScript still doesn't know what to do here. We've hit a point where we know more about our values than the type checker does. We could try to use a non-null assertion (a `!` after `shape.radius`) to say that `radius` is definitely present.

```ts
function getArea(shape: Shape) {
  if (shape.kind === "circle") {
    return Math.PI * shape.radius! ** 2;
  }
}
```

- But this doesn't feel ideal. We had to shout a bit at the type-checker with those non-null assertions (`!`) to convince it that `shape.radius` was defined, but those assertions are error-prone if we start to move code around. Additionaly, outside of `strictNullChecks` we're able to access any of those fields anyway (since optional properties are just assumed to always be present when reading them). We can definitely do better.

- The problem with this encoding of `Shape` is that the type-checker doesn't have any way to know whether or not `radius` or `sideLength` are present based on the `kind` property. We need to communicate what we know to the type checker. With that in mind, let's take another swing at defining `Shape`.

```ts
interface Circle {
  kind: "circle";
  radius: number;
}

interface Square {
  kind: "square";
  sideLength: number;
}

type Shape = Circle | Square;
```

- Here, we've properly separated `Shape` out into two types with different values for the `kind` property, but `radius` and `sideLength` are declared as required properties in their respective types.

```ts
function getArea(shape: Shape) {
  return Math.PI * shape.radius ** 2;
  // Property 'radius' does not exist on type 'Shape'.
  // Property 'radius' does not exist on type 'Square'.
}
```

- Like with our first definition of `Shape`, this is still an error. When `radius` was optional, we got an error (only in `strictNullChecks`) because TypeScrit couldn't tell whether the property was present. Now that `Shape` is a union, TypeScript is telling us that `shape` might be a `Square`, and `Square`s don't have `radius` defined on them! Both interpretations are correct, but only does our new enconding of `Shape` still cause an error outside of `strictNullChecks`.

```ts
function getArea(shape: Shape) {
  if (shape.kind === "circle") {
    return Math.PI * shape.radius ** 2;
    // (parameter) shape: Circle
  }
}
```

- That got rid of the error! When every type in a union contains a common property with literal types, TypeScript considers that to be a _discriminated union_, and can narrow out the members of the union.

- In this case, `kind` was that common property (which is what's considered a _discriminant_ property of `Shape`). Checking whether the `kind` property was `"circle"` got rid of every type in `Shape` that didn't have a `kind` property with the type `"circle"`. That narrowed `shape` down to the type `Circle`.

- The same checking works with `switch` statements as well.

```ts
function getArea(shape: Shape) {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    // (parameter) shape: Circle
    case "square":
      return shape.sideLength ** 2;
    // (parameter) shape: Square
  }
}
```

- The important thing here was the enconding of `Shape`. Communicating the right information to TypeScript - that `Circle` and `Square` were really two separate types with specific `kind` fields - was crucial. Doing that let us write type-safe TypeScript code that loks no different than the JavaScript we would've written otherwise. From there, the type system was able to do the "right" thing and figure out the types in each branch of our `switch` statement.

- Discriminated unions are useful for more than just talking about circles and squares. They're good for representing any sort of messaging scheme in JavaScript, like when sending messages over the network (client/server communication), or enconding mutations in a state management framework.

## The `never` type

- When narrowing, you can reduce the options of a union to a point where you have removed all possibilities and have nothing left. In those cases, TypeScript will use `never` type to represent a state which shouldn't exist.

## Exhaustiveness checking

- The `never` type is assignable to every type; however, no type is assignable to `never` (except `never` itself). This means you can use narrowing and rely on `never` turning up to do exhaustive checking in a switch statement.

- For example, adding a `default` to our `getArea` function which tries to assign the shape to `never` will raise when every possible case has not been handled.

```ts
type Shape = Circle | Square;

function getArea(shape: Shape) {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.sideLength ** 2;
    default:
      const _exhaustiveCheck: never = shape;
      return _exhaustiveCheck;
  }
}
```

- Adding a new member to the `Shape` union, will cause a TypeScript error

```ts
interface Triangle {
  kind: "triangle";
  sideLength: number;
}

type Shape = Circle | Square | Triangle;

function getArea(shape: Shape) {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.sideLength ** 2;
    default:
      const _exhaustiveCheck: never = shape;
Type 'Triangle' is not assignable to type 'never'.

      return _exhaustiveCheck;
  }
}
```

## References

- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/2/basic-types.html#emitting-with-errors)
