When you need to store key-value pairs, maps are the "go to" data structure in Elixir. A map is created using the `%{}` syntax.

```elixir
map = %{:a => 1, 2 => :b}
map[:a]
> 1

map[2]
> :b

map[:c]
> nil
```

- Maps allow any value as a key
- Maps have their own internal ordering, which is not guaranteed to be the same across different maps

Maps are very useful with pattern matching. When a map is used in a pattern, it will always match on a subset of the given calue.
```
%{} = %{:a => 1, 2 => :b}
${:a => a} = %{:a => 1, 2 => :b}
a
> 1
${:c => c} = %{:a => 1, 2 => :b}
> MatchError
```

The `Map` module can be used to add, remove, and update map keys
```
Map.get(%{:a => 1, 2 => :b}, :a)
> 1
Map.put(%{:a => 1, 2 => :b }, :c, 3)
> %{:a => 1, 2 => :b, :c => 3}
Map.to_list(%{:a => 1, 2 => :b})
> [{2, :b}, {:a, 1}]
```

## Maps of Predefined Keys
It is common to create maps with a predefined set of keys. Their values may be updated, but new keys are never added nor removed. This is useful when we know the shape of the data we are working with and, if we get a different key, it likely means a mistake was done elsewhere.k

```
> map = {%name: "John", age: 23}
> map.name
"John"
> map.age
KeyError
```

There is also syntax for updating keys
```
> %{map | name: "Mary"}
%{name: "Mary", age: 23}
> %{map | agee: 27}
KeyError
```

