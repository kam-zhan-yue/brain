[See Documentation](https://www.typescriptlang.org/docs/handbook/2/generics.html)

The "hello world" of generics is the identity function. This is a function that will return back whatever is passed in, similar to an `echo` command.

Without generics, we would have to specify the identity function with a specific type.

```typescript
function identity(arg: number): number {
	return arg;
}
```

We can use the *type variable* to capture the type of the argument in such a way that we can denote what is being returned.

```typescript
function identity<Type>(arg: Type): Type {
	return arg;
}
```

Once we've written the identity function, we can call it two ways
```typescript
// output: string
let output = identity<string>("myString");
// output; string
let output = identity("myString")

```

## Generic Type Variables

## `keyof`

Takes an object type and produces a string or numeric literal unions of its keys. The following type `P` is the same as `type P = "x" | "y"`

```typescript
type Point = { x: number, y: number };
type P = keyof Point;
```

If the type has a `string` or `number` index signature, `keyof` will return those types instead.

```typescript
type Arrayish = { [n: number]: unknown };
type A = keyof Arrayish;
// type A = number

type Mapish = { [k: string]: boolean };
type M = keyof Mapish
// type M = string | number
```

In the above example, `M` is `string | number` because JavaScript object keys are always coerced into a string, so `obj[0]` is always the same as `obj["0"]`

## `typeof`

Used in a *type* context to refer to the *type* of a variable or property.

```typescript
let s = "hello";
let n: typeof s;
// n: string
```

`typeof` can be used to conveniently express many patterns. For instance, the `ReturnType<T>` type takes a *function type* and produces its return type.

```typescript
type Predicate = (x: unknown) => boolean;
type K = ReturnType<Predicate>;
// type K = boolean

function f() {
	return { x: 10, y: 3 };
}
type P = ReturnType<typeof f>;
/*
	type P = {
		x: number;
		y: number;
	}
*/
```

## Mapped Types

Mapped types build on the syntax for index signatures, which are used to declare the types of properties which have not been declared ahead of time:

```typescript
type OnlyBoolsAndHorses = {
	[key: string]: boolean | Horse;
}

const conforms: OnlyBoolsAndHorses = {
	del: true,
	rodney: false,
}
```

A mapped type is a generic type which uses a union of `PropertyKey` (often created using `keyof`) to iterate through keys to create a type.

In the following example, `OptionsFlags` will take all the properties from the type `Type` and change their values to be a boolean.

```typescript
type OptionsFlags<Type> = {
	[Property in keyof Type]: boolean,
}

type Features = {
	darkMode: () => void;
	newUserProfile: () => void;
}

type FeatureOptions = OptionsFlags<Features>;
/*
	type FeatureOptions = {
		darkMode: boolean;
		newUserProfile: boolean;
	}
*/
```