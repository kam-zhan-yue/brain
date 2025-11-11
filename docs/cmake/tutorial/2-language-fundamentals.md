CMake provides a Turing-complete domain-specific language for describing the process of building software. This is known as CMakeLanguage or CMakeLang.

Every object in CMake is a string and lists themselves are strings which contain semicolons as separators. Any command which appears to operate on something other than a string (e.g. boolean, numbers) is still consuming a string, doing some internal conversion, then converting back to a string.

test.txt
```
set(var "World!")
message("Hello ${var}")
```

```
cmake -P test.txt
Hello World!
```

```
set(stooges "Moe;Larry")
list(APPEND stooges "Curly")

message("Stooges contains: ${stooges}")

foreach(stooge IN LISTS stooges)
  message("Hello, ${stooge}")
endforeach()

➜ cmake -P test.txt
Stooges contains: Moe;Larry;Curly
Hello, Moe
Hello, Larry
Hello, Curly
```

## Macros, Functions, and Lists
