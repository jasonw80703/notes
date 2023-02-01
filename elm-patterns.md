# Elm Patterns

## Type Blindness
```elm
priceInDollars : Float
priceInDollars = 2.0

priceInEuros : Float
priceInEuros = 1
```

Both attributes are String so it's easy to mix up. Instead use unique types:
```elm
type Dollar = Dollar Float

priceInDollars : Dollar
priceInDollars =
	Dollar 2.0
```

## Minimize Boolean Usage

## Wrap Early, Unwrap Late

## Unwrap Maybe and Result Early
