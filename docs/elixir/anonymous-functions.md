[See documentation here](https://hexdocs.pm/elixir/anonymous-functions.html)

Anonymous functions allow us to store and pass executable code around as if it were an integer or string. Elixir identified named functions by both their name and arity.

The arity of a function describes the number of arguments that the function takes. `trunc/1` identifies the function which is named `trunc` and takes `1` argument, where as `trunc/2` identifies a different function with the same name but with an arity of `2`.

We can also use this syntax to access documentation.
```shell
h trunc/1
```

All functions in the `Kernel` module are automatically imported into the namespace. 

## Defining Anonymous Functions

Anonymous functions in Elixir are delimited by the keywords `fn` and `end`
```shell
add = fn a, b -> a + b end
add.(1, 2)
```

The dot makes it clear when you are calling an anonymous function, stored in the variable `add`. This lets us distinguish between named functions.

We can check if a value is a function using `is_function/1` and its arity with `is_function/2`.

## Closures

Anonymous functions can also access variables that are in scope when the function is defined. This is typically referred to as closures, as they close over their scope.

```shell
double = fn a -> add.(a, a) end
double.(2)
> 4
```

## Clauses and Guards

Similar to `case/2`, we can pattern match on the arguments of anonymous functions as well as define multiple clauses and guards

```
f = fn
	x, y when x -> 0 x + y
	x, y -> x * y
	end

f.(1, 3)
> 4
f.(-1, 3)
-3
```

## The Capture Operator
The `name/arity` notation can be used to capture an existing function into a datatype that we can pass around.

```shell
fun = &is_atom/1
is_function(fun)
fun(:hello)j
```

Since operators are functions in Elixir, you can also capture operators.

```
add = &+/2
add.(1, 2)
```

The capture syntax can also be used as a shortcut for creating functions that wrap existing functions.

```
is_arity_2 = fn fun -
```