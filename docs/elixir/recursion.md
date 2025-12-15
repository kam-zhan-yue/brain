Elixir does not provide loop constructs. Instead, it leverages recursion and high-levle functions for working with collections.

## Loops through recursion
Due to immutability, loops in Elixir are written differently from imperative languages.

```
defmodule Recursion do
  def print(msg, n) when n > 0 do
    IO.puts(msg)
    print(msg, n - 1)
  end

  def print(_msg, 0) do :ok end
end

Recursion.print("Hey!", 3)
```

```
defmodule Math do
  def sum_list([head | tail], accumulator) do
    sum_list(tail, head + accumulator)
  end

  def sum_list([], accumulator) do
    accumulator
  end

  def double_each([head | tail]) do
    [head * 2 | double_each(tail)]
  end

  def double_each([]) do
    []
  end
end

IO.puts Math.sum_list([1, 2, 3, 4, 5], 0)
IO.puts Math.double_each([1, 2, 3, 4, 5])
```
