1/18/23

- bug related to a modal/form wizard where clicking back on Modal B triggered the Modal B&#39;s button event and the previous modal Modal A&#39;s button event
    - this was due to an issue with how Elm compiles and it can&#39;t recognize the difference between very similar nodes, so somehow it triggers both onClick callbacks
    - the fix was to use Elm Keyed Node to give the nodes a unique key
- instrumentation is done in `#track`
    - the preferred pattern is to call `track` from within `update` instead of calling it within `Main.elm` or `Module.elm`

1/19/23

- random UUID generation
    - investigate using Random package
    - investigate using Time package
    - both are effects that need to happen in Elm Runtime

1/24/23

- `ClickedLink` message in Main for navigation, calls pushUrl effect which triggers `ChangedUrl` message that updates the route

1/27/23

- chaining `Maybe`s is a pain… instead, try to follow these two rules

```
Rule 1: Separate code that checks for presence from code that calculates values.

Rule 2: The extracted function can return Maybe but it may not accept Maybe as any of its arguments.

```

- `Maybe.andThen` vs. `Maybe.map`

```
something -> Maybe somethingElse -- use andThen
something -> somethingElse -- Use map

```

2/6/23

- modal `LeavePostShift` has multiple &quot;variants&quot; with different data, specifically when the facop changes facility, and when the facop changes route
- possible solutions
    - Maybe values representing each datum
        - not great because there are impossible states where both Maybes are Just or both Maybes are Nothing
    - two modals
        - not great because the modal has the same content so it&#39;s very WET
    - modal Model variants
        - not great because duplicated &quot;state&quot; or data in the model
- best solution - two init functions and an &quot;intent&quot; or &quot;requested by&quot; type in the model
    - allow initializing the modal with the correct data by type
    - prevents impossible states

2/23/23

- decoder stuff - decode from a JSON list of objects to a list of Facilities ([commit](https://github.com/CareRevolutions/facility_app/pull/399/commits/da907dff6971a7d51c89f560df9087a31d180e75))
- [decode pipeline](https://package.elm-lang.org/packages/NoRedInk/elm-json-decode-pipeline/latest/) vs [decoder](https://package.elm-lang.org/packages/elm/json/latest/)
    - `Decode.field` for getting field from JSON object
    - `Decode.list` for getting from JSON array
    - pipeline is useful for decoding into a type alias (which is just a type constructor function)
- Functors
    - a functor is something that can be mapped over (where something is a set of values arranges in some shape, ie. array, object)
    - a map function must have signature `(a → b) → f a → f b`
- Functional Composition
    - instead of

```
someListOfAs
    |> List.map turnAtoB
    |> List.map turnBtoC
    |> List.map turnCtoD -- > List d

```

    - do

```
someListOfAs
    |> List.map (turnAtoB >> turnBtoC >> turnCtoD)

```

- Type Constructors are functions

2/26/23

- all functions are pure functions, all data must be immutable, functions cannot hold state
- functional programming
    - emphasis on what is being computed
    - immutable data
    - pure functions with no side effects
    - currying - all functions in Elm are curried where functions that take multiple arguments are converted to a sequence of functions that take a single argument
        - `add a b = a + b -- <function> : number -> number -> number`
        - `add 1 -- <function> : number -> number`
        - `add 1 2 -- 3 : number`
- compiler driven development
- unidirectional data flow helps keep the app organized
    - Model | View | Update
- Separator-leading lists (comma first)
- constrained type variables
    - `number` permits Int and Float
    - `appendable` permits String and List a
    - `comparable` permits Int, Float, Char, String and lists/tuples of comparable values
    - `compappend` permits String and List comparable

04/06/23

```elm
if you like to play golf, we can do a little beta reduction

(Maybe.map (\shift -> { shift | break = updatedBreak }))

is equivalent to

(\maybeShift -> maybeShift |> Maybe.map (\shift -> { shift | break = updatedBreak }))

whenever you see
(\someArg -> someFunction someArg)
that can simplify to someFunction

so
(\maybeShift ->  Maybe.map (\shift -> { shift | break = updatedBreak }) maybeShift )
could simplify

```

---

# Readings

- [ ] [http://reasonableapproximation.net/2019/10/20/the-effect-pattern.html](http://reasonableapproximation.net/2019/10/20/the-effect-pattern.html)
- [x] [https://sporto.github.io/elm-patterns/index.html](https://sporto.github.io/elm-patterns/index.html)
- [x] [https://github.com/dmy/elm-realworld-example-app](https://github.com/dmy/elm-realworld-example-app)
- [x] [https://engineering.rakuten.today/post/elm-at-rakuten/](https://engineering.rakuten.today/post/elm-at-rakuten/)
- [x] [http://blog.jenkster.com/2016/06/how-elm-slays-a-ui-antipattern.html](http://blog.jenkster.com/2016/06/how-elm-slays-a-ui-antipattern.html)
- [x] [https://sporto.github.io/elm-patterns/basic/impossible-states.html](https://sporto.github.io/elm-patterns/basic/impossible-states.html)
- [ ] [https://mostly-adequate.gitbook.io/mostly-adequate-guide/](https://mostly-adequate.gitbook.io/mostly-adequate-guide/)
- [ ] [https://flaviocorpa.com/haskell-for-elm-developers-giving-names-to-stuff-part-2-applicative-functors.html](https://flaviocorpa.com/haskell-for-elm-developers-giving-names-to-stuff-part-2-applicative-functors.html)
