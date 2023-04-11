# Haskell
avoid success at all costs

## Features
- pure functional language
- all values are immutable
- all functions are pure functions (no side effects)
- type system with a type interface
- lazy language

## ghci
`ghci` is Haskell's REPL.

## Lists

### Simple Lists
Lists are all of one type!

```haskell
[1,2,3]
["one","two","three"]
```

### Ranges
```haskell
[1..10]
=> [1,2,3,4,5,6,7,8,9,10]
```

If starting and ending number are the same, you get one element.
```haskell
[10..10]
=> 10
```

If starting value is larger than the ending number, you get an empty list.
```haskell
[10..1]
=> []
```

If you start your range with two numbers instead of a single number, the range increments/decrements by the difference of the two starting numbers.
```haskell
[2,4..10]
=> [2,4,6,8,10]

[0,23..100]
=> [0,23,46,69,92]
```

## Tuples
```haskell
tuple = (2, "two")
thruple = (3, "three", "tres")
```

Working with two element tuples, there are helper functions.
```haskell
fst (2, "two")
=> 2
snd (2, "two")
=> "two"
```

## Printing Stuff
`putStrLn` puts string with a newline
`putStr` puts without newline

These print strings only. To print other types, use the show function.

```haskell
putStrLn (show 12)
```

Use `print` which combines show with putStrLn.

```haskell
print 12
```
