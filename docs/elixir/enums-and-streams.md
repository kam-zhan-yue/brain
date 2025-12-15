```
# Better to use the Enum class

IO.puts Enum.reduce([1, 2, 3], 0, fn x, acc -> x + acc end)
IO.puts Enum.map([1, 2, 3], fn x -> x * 2 end)

# Piping Enums together

odd? = fn x -> rem(x, 2) != 0 end
IO.puts 1..100_000 |> Enum.map(&(&1 * 3)) |> Enum.filter(odd?) |> Enum.sum()
```

Solution.solve("

7364324241225433445422232322233434224835321253334532333323166227675321322533332522422446233324232434


", 12)
