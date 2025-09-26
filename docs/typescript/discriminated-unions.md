[See documentation here](https://basarat.gitbook.io/typescript/type-system/discriminated-unions)

If you have a class with a literal member (e.g. `let foo = 'Hello'`, then you acn use that property to discriminate between union members.

As an example, consider the union of a `Square` and a `Rectangle`. Here we have member `kind` that exists on both union members and is of a particular literal type.

```typescript
interface Square {
	kind: 'square'
	size: number
}

interface Rectangle {
	kind: 'rectangle'
	width: number
	height: number
}

type Shape = Square | Rectangle
```

If you use a type guard style check on the *discriminant property*, TypeScript will realise that the object must be of the type that has that specific literal and do a type narrowing for you.

```typescript
function area(s: Shape) {
	if (s.kind === 'square') {
		// now typescript knows that s must be a square
		return s.size * s.size
	} else {
		// now typescript knows that s must be a rectangle
		return s.width * s.height
	}
}
```

## Exhaustive Checks

Quite commonly, you want to make sure that all members of a union have some code (action) against them. Say we add a `Circle` interface and whenever we encounter it, we want to throw an error.

```typescript
interface Square {
    kind: "square";
    size: number;
}

interface Rectangle {
    kind: "rectangle";
    width: number;
    height: number;
}

// Someone just added this new `Circle` Type
// We would like to let TypeScript give an error at any place that *needs* to cater for this
interface Circle {
    kind: "circle";
    radius: number;
}

type Shape = Square | Rectangle | Circle;
}
```

Things can go bad in 

```typescript
function area(s: Shape) {
    if (s.kind === "square") {
        return s.size * s.size;
    }
    else if (s.kind === "rectangle") {
        return s.width * s.height;
    }
    // Would it be great if you could get TypeScript to give you an error?
}
```

You can do that by simply adding a fall through and making sure that the inferred type in that block is compatible with the `never` type. For example, if you add the exhaustive check, you get a nice error.

The philosophy is that when we construct if or switch statements, we want to be explicit in our conditions. However, that means that we will have `never` blocks where the types should theoretically never go there.

```typescript
function area(s: Shape) {
    if (s.kind === "square") {
        return s.size * s.size;
    }
    else if (s.kind === "rectangle") {
        return s.width * s.height;
    }
    else {
        // ERROR : `Circle` is not assignable to `never`
        const _exhaustiveCheck: never = s;
    }
}
```

See a switch statement for comparison.

```typescript
function area(s: Shape) {
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.width * s.height;
        case "circle": return Math.PI * s.radius * s.radius;
        default: const _exhaustiveCheck: never = s;
    }
}
```

### strictNullChecks

If using *strictNullChecks*, TypeScript might complain that "not all code paths return a value". We can silence this by simply returning the `_exhaustiveCheck` variable.

```typescript
function area(s: Shape) {
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.width * s.height;
        case "circle": return Math.PI * s.radius * s.radius;
        default:
          const _exhaustiveCheck: never = s;
          return _exhaustiveCheck;
    }
}
```

### Throw in exhaustive checks

You can write a function that takes a `never` (and therefore can only be called with a variable that is inferred as `never`) and then throw an error if its body ever executes.

```typescript
function assertNever(x:never): never {
    throw new Error('Unexpected value. Should have been never.');
}
```

Example use case:

```typescript
function area(s: Shape) {
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.width * s.height;
        // If a new case is added at compile time you will get a compile error
        // If a new value appears at runtime you will get a runtime error
        default: return assertNever(s);
    }
}
```