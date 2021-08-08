# Generics

- In languages like C# and Java, one of the main tools in the toolbox for creating reusable components is _generics_, that is, being able to create a component that can work over a variety of types rather than a single one. This allows users to consume these components and use their own types.

## Hello World of Generics

```ts
function identify<Type>(arg: Type): Type {
  return arg;
}
```

- We've now added a type variable `Type` to the identify function. This `Type` allows us to capture the type the user provides (e.g. `number`), so that we can use that information later. Here, we use `Type` again as the return type. On inspection, we can now see the same type is used for the argument and the return type. This allow us to traffic that type information in one side of the function and out the other.

- We say that this version of the `identify` function is generic, as it works over a range of types. Unlike using `any`, it's also just as precise (ie, it doesn't lose any information) as the first `identify` function that used numbers for the argument and return type.

- Once we've written the generic identify function, we can call it in one of two ways. The first way is to pass all of the arguments, including the type argument, to the function

```ts
let output = identify<string>("myString");
// let output: string
```

- Here we explicitly set `Type` to be `string` as one of the arguments to the function call, denoted using the `<>` around the arguments rather than `()`.

- The second way is also perhaps the most common. Here we use _type argument inference_ - that is, we want the compiler to set the value of `Type` for us automatically based on the type of the argument we pass in

```ts
let output = identify("myString");
// let output: string
```

- Notice that we didn't have to explicitly pass the type in the angle brackets (`<>`); the compiler just looked at the value "myString", and set `Type` to its type. While type argument inference can be a helpful tool to keep code shorter and more readable, you may need to explicitly pass in the type arguments as we did in the previous example when the compiler fails to infer the type, as may happen in more complex examples.

## Working with Generic Type Variables

- When you begin to use generics, you'll notice that when you create generic functions like `identify`, the compiler will enforce that you use any generically typed parameters in the body of the function conrrectly. That is, that you actually treat these parameters as if they could be any and all types.

Let's take our `identify` function from earlier:

```ts
function identify<Type>(arg: Type): Type {
  return arg;
}
```

What if we want to also log the length of the argument `arg` to the console with each call? We might be tempted to write this

```ts
function loggingIdentify<Type>(arg: Type): Type {
  console.log(arg.length);
  // Property 'length' does not exist on type 'Type'.
  return arg;
}
```

- When we do, the compiler will give us an error that we're using the `.length` member of `arg`, but nowhere have we said that `arg` has this membr. Remember, we said earlier that these types variables stand in for any and all types, so someone using this function could have passed in a `number` instead, which does not have a `.length` member.

### Generic Types

- The type of generic functions is just like those of non-generic functions, with the type parameters listed first, similarly to function declarations

```ts
function identity<Type>(arg: Type): Type {
  return arg;
}

let myIdentity: <Type>(arg: Type) => Type = identify;
```

- We could also have used a different name for the generic type parameter in the type, so long as the number of type variables and how the type variables are used line up.

```ts
function identify<Type>(arg: Type): Type {
  return arg;
}

let myIdentify: <Input>(arg: Input) => Input = identity;
```

- We can also write the generic type as a call signature of an object literal type

```ts
function identity<Type>(arg: Type): Type {
  return arg;
}

let myIdentity: { <Type>(arg: Type): Type } = identity;
```

- Which leads us to writing our first generic interface. Let's take the object literal from the previous example and move it to an interface.

```ts
interface GenericIdentityFn {
	<Type>(arg: Type): Type;
}

function identity<Type>(arg: Type) Type {
	return arg;
}

let myIdentity: GenericIdentityFn = identity;
```

- In a similar example, we may want to move the generic parameter to be a parameter of the whole interface. This lets us see what type(s) we're generic over (e.g. `Dictionary<string>` rather that just `Dictionary`). This makes the type parameter visible to all the other members of the interface.

```ts
interface GenericIdentityFn<Type> {
  (arg: Type): Type;
}

function identity<Type>(arg: Type): Type {
  return arg;
}

let myIdentity: GenericIdentityFn<number> = identity;
```

- Notice that our example has changed to be something slightly different. Instead of describing a generic function, we now have a non-generic function signature that is part of a generic type. When we use `GenericIdentifyFn`, we now will also need to specify the corresponding type argument (here: `number`), effectively locking in what the underlying call signature will use. Understanding when to put the type paramteter directly on the call signature and when to put it on the interface itself will be helpful in describing what aspects of a type are generic.

## Generic Classes

- A generic class has a similar shape to a generic interface. Generic classes have a generic type parameter list in angle brackets (`<>`) following the name of the class.

```ts
class GenericNumber<NumType> {
  zeroValue: NumType;
  add: (x: NumType, y: NumType) => NumType;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function (x, y) {
  return x + y;
};
```

- This is a pretty literal use of the `GenericNumber` class, but you may have noticed that nothing is restricting it to only use the `number` type. We could have instead used `string` or even more complex objects.

```ts
let stringNumeric = new GenericNumber<string>();
stringNumeric.zerovalue = "";
stringNumeric.add = function (x.y) {
	return x + y;
};

console.log(stringNumeric.add(stringNumeric.zeroValue, "test"));
```

- Just as with interface, putting the type parameter on the class itself lets us make sure all of the properties of the class are working with the same type.

## References

- [TypeScript docs/handbook, Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html)
