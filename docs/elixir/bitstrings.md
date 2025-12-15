A bitstring is a fundamental data type denoted with the `<<>>` syntax. A bitstring is a contiguous sequence of bits in memory. By default, 8 bits is used to store each number in a bitstring, but you can manually specify the number.

```
<<42>> == <<42:8>>
> true
<<3::4>>
> <<3::size(4)>>
```

> The decimal number 3 when represented with 4 bits in base 2 would be `0011`

```
<<0::1, 0::1, 1::1, 1::1>> == <<3::4>>
> true
```

Any value that exceeds what can be stored by the number of bits provisioned is truncated
```
<<1>> == <<257>>
true
```

## Binaries
A binary is a bit string where the number of bits is divisible by 8. That means that every binary is a bitstring, but not every bit string is a binary. We can use the`is_bitstring/1` and `is_binary/1` functions to demonstrate this.

```
is_bitstring(<<3::4>>)
> true
is_binary(<<3::4>>)
> false
is_bitstring(<<0, 255, 42>>)
> true
is_binary(<<0, 255, 42>>)
> true
```

Unless you explicitly use `::` modifiers, each entry in the binary pattern is expected to match a single byte (exactly 8 bits). If we want to match on a binary of unknown size, we can use the `binary` modifiers at the end of the pattern.

```
<<0, 1, x>> = <<0, 1, 2>>
x
> 2

<<0, 1, x>> = <<0, 1, 2, 3>>
> MatchError

// we have to specify that x is a binary of unknown size
<<0, 1, x::binary>> = <<0, 1, 2, 3>>
> <<0, 1, 2, 3>>
x
> <<2, 3>
```

A string is a UTF-8 encoded binary, where the codepoint for each character is encoded using 1 to 4 bytes. Thus, every string is a binary, but due to the UTF-8 standard encoding rules, not every binary is a valid string.

```
is_binary("Hello")
> true

is_binary(<<239, 191, 19>>)
> true

String.valid?(<<239, 191, 19>>)
> false
```

The string concatenation operator `<>` is actually a binary concatenation operator.

```
"a" <> "ha"
> "aha"

<<0, 1>> <> <<2, 3>>
> <<0, 1, 2, 3>>
```

Given that strings are binaries, we can also pattern match on strings:

```
<<head, rest::binary>> = "banana"
> "banana"
head == ?b
> true
rest
> "anana"
```

The binary pattern matching works on bytes, so matching on a string with a heavy Unicoded character with multibyte characters won't match on the character, but the first byte of that character.

## Charlists

A charlist is a list of integers where all the integers are valid codepoints. In practice, you willnot come across them often.

```
~c"hello"
[?h, ?e, ?l, ?l, ?o]
> ~c"hello"
```

The `~c` sigil indicates the fact that we are dealing with a charlist.