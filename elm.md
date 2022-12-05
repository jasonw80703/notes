# ELM
https://frontendmasters.com/courses/intro-elm

## Benefits
* Measurable technical advantages
    * Smaller bundle sizes - fast
    * No production runtime exceptions
* Provides everything you need
    * Dialect, UI, state, async, utilities, packages
    * Elm, view, model, update, core, elm install

## Intro, Syntax, Semantics
Elm compiles to JS from Main.elm -> elm.js.

Elm uses a Virtual DOM for rendering. Elm uses function calls to represent the DOM instead of templates.

```elm
ul [ class "languages" ]
    [ li [] [ text "Elm" ]
    , li [] [ text "JS" ]
    ]
```

### Conditionals
Elm if/else are similar to JS ternaries where you must have the else clause.

```elm
if quantity == 1 then
    singular

else
    plural
```

### Functions
```elm
pluralize singular plural quantity =
    if quantity == 1 then
        singular

    else
        plural

main =
    text (pluralize "leaf" "leaves" 1)
```

### elm-stuff
Stuff the compiler uses, always safe to destroy.

### String Manipulation
"foo" ++ "bar" --> "foobar" to combine strings

### let Expression
```elm
pluralize singular plural quantity =
    let
        quantityStr =
            String.fromInt quantity

        prefix =
            quantityStr ++ " "
    in
    if quantity == 1 then
        prefix ++ singular

    else
        prefix ++ plural
```

### Lists
Not arrays, a linked list actually. One type (consistent entries) inside a list.

```elm
List.map (\str -> String.toUpper str ++ "!")
    [ "pow", "zap", "blam" ]
```

This uses an anon function.

#### Partial Application

```elm
List.map (\num -> pluralize "leaf" "leaves" num)
    [ 0, 1, 2 ]

-- equivalent to

List.map (pluralize "leaf" "leaves") [ 0, 1, 2 ]
```

Here, pluralize is missing an argument, so Elm returns an anon fn that can take in the missing arguments. It doesn't actually run the function.
We then pass the list to that new anon fn which satisfies it and runs the function.

To render a list to a view...

```elm
names =
    [ "A"
    , "B"
    , "C"
    ]

ul []
    [ li [] [ text "A" ]
    , li [] [ text "B" ]
    , li [] [ text "C" ]
    ]

viewName name =
    li [] [ text name ]

ul [] (List.map viewName names)
```

### Records
```elm
record =
    { name = "foo", x = 1, y = 3 }

record.name
record.x
record.y
```

Updating records (which are immutable)
```elm
newRecord =
    { record | name = "bar", x = 7 }
```

You can't use #map on records because types are not consistent. You can't iterate over records then.

### Booleans
```elm
True
False
x == y
not (x == y) -- x /= y
x && y
x || y
```

List membership
```elm
List.member 1 [ 1, 2, 3]
-- True

List.member 9 [ 1, 2, 3]
-- False
```

List filtering
```elm
isKeepable num =
    num > 1

List.filter isKeepable [ 1, 2, 3 ]
-- [ 2, 3 ]

List.filter (\num -> num > 1) [ 1, 2, 3, ]
-- [ 2, 3 ]
```

# The Elm Architecture - Model View Update

## View

```elm
view model =
    p [] [ text "hi" ]
```

View is a fn that takes in a model, which is the application state, and returns some HTML given the state.
Model is the entire application state.
Elm Runtime takes care of interaction, wiring and translating concepts to JS. It makes the page look as requested given the view function.

```elm
update msg model =
    { model | selectedTag = "elm" }
```

Update is a fn that takes in a msg and a model, which returns a new model. Transform between model states.
Message is an external value like user interaction, typing, server message that describes something that happens.
We can design the format of the message, ie.

```elm
{ description = "ClickedTag"
, data = "elm"
}

-- how to get this into update? Onclick handler

button
    [ onClick
        { description = "ClickedTag"
        , data = "elm"
        }
    ]
    [ text "elm" ]

-- when they click this button, this message is sent to update with the current model and transition to a new model
```

# Dependencies
In elm.json, direct dependencies are ones that we need, indirect dependencies are things that the direct ones need.


## Type annotations
Colons are for types!

```elm
totalPages : Int
totalPages = 5

round : Float -> Int

not : Bool -> Bool

String.length : String -> Int

searchResult : { name: String, stars: Int }
searchResult = { name = "blah", stars = 215 }

-- lists are a parameterized type
names : List String
names = [ "a", "b", "c" ]
```

Aliases are useful!

```elm
type alias Article =
    { title : String
    , tags : List String
    , body : String
    }

type alias Model =
    { selectedTag : String
    , articles : List Article
    }
```

With architecture...

```elm
type alias Msg =
    { description : String
    , data : String
    }

view : Model -> Html Msg
view model =

update : Msg -> Model -> Model
update msg model = 
```

# Custom Types
## Case Expressions
Indentation matters here...
```elm
case model.tab of
    "YourFeed" ->
        -- do something

    "GlobalFeed" ->
        -- do something

    _ ->
        -- do something
```

## Custom Type
Three variants:
```elm
type Tab =
    YourFeed | GlobalFeed | TagFeed

yours : Tab
yours =
    YourFeed

global : Tab
global =
    GlobalFeed

tag : Tab
tag =
    TagFeed
```

Elm values need to be defined with a lowercase letter. Custom type variants need to be defined with an uppercase letter.

Bool is a custom type!
```elm
type Bool
    = True
    | False
```

```elm
case model.tab of
    YourFeed ->
        --

    GlobalFeed ->
        --

    TagFeed ->
        --
```

No default branch needed because the custom type is completely satisfied. Adding a default will compile error because it's not reachable.
Generally better not to have an else case in Elm.

## Containers
```elm
type Tab
    = YourFeed
    | GlobalFeed
    | TagFeed String
```
This makes TagFeed now a function that takes in a string (from being a value of type Tab). This returns a Tab.

```elm
> TagFeed "happiness"
TagFeed "happiness" : Tab
```

```elm
yours : Tab
yours =
    YourFeed

happiness : Tab
happiness =
    TagFeed "happiness"

-- to get it out...

case model.tab of
    YourFeed ->
        -- show your feed

    GlobalFeed ->
        -- show global feed

    TagFeed selectedTag ->
        -- show tag feed using new string
```

Tab was an enumeration but now is a container! It situationally holds additional information only for TagFeed.

## Custom Type Messages
No good to use records for message types, better to use custom types where each holds contextual information.

```elm
type Msg
    = ClickedTag String
    | ClickedPage Int

-- no default branch needed based on Msg type!
update msg model =
    case msg of
        ClickedTag selectedTag ->
            -- use tag here
        ClickedPage page ->
            -- use page here

pageButton : Int -> Html Msg
pageButton pageNumber =
    button [ onClick (ClickedPage pageNumber) ]
        [ text (String.fromInt pageNumber) ]
```

# Maybe

```elm
first users =
    List.head users

first [ "sam", "jess" ]
-> Just "sam"

first []
-> Nothing

-- operating on first

first users =
    List.head users

case first newUsers of
    Just user ->
        String.length user

    Nothing ->
        0
```

Maybe is a parameterized type.
List.head List String -> Maybe String
List.head List Float -> Maybe Float

## Type variables

What is the type for List.head then? Type variables!

```elm
List.head : List elem -> Maybe elem
-- or
List.head : List v -> Maybe v
```

Lowercase means it's a variable, not a concrete type. They must also agree elem -> elem.

```elm
List.reverse : List item -> List item
List.reverse [ 1, 2, 3 ] -> [ 3, 2, 1 ]

List.singleton : val -> List val
List.singleton "foo" -> [ "foo" ]
```

What is maybe?

```elm
type Maybe val
    = Just val
    | Nothing
```

# Pipeline Style

```elm
List.reverse (List.filter (\x -> x < 5) [ 2, 4, 6 ])

[ 2, 4, 6 ]
    |> List.filter (\x -> x < 5)
    |> List.reverse
```

# Decoding

Json decoders are a flexible version of converting data.
decodeString returns a Result type, which is either Ok or Err.
Here, decodeString checks if "42" can be parsed into an integer.

```elm
case decodeString int "42" of
    Ok num ->
        -- Do something with num : Int
    Err error ->
        -- Do something with the error
```

This is similar to Maybe.

```elm
type Maybe val
    = Just val
    | Nothing

type Result okVal errVal
    = Ok okVal
    | Err errVal
```

Proper decoding from JSON.

```elm
{
    "user_id": 27,
    "first_name": "Al",
    "last_name": "Kai"
}

type alias User =
    { id : Int
    , firstName : String
    , lastName : String
    }

user : Decoder User
user =
    {-  this User is a record constructor (a function)
        if decoding succeeds, use User (function) to build the result
        required field called "user_id" and is an int
    -}
    Json.Decode.succeed User
        |> required "user_id" int
        |> required "first_name" string
        |> required "last_name" string
```

This is a DSL. Mapping of JSON structure to record type in Elm. The names don't need to match.

If decoding succeeds, you get an Ok. If it fails, you get an Err.

## Optional & Nullable

Elm doesn't have null but JSON does.
```elm
{
    "user_id": 27,
    "name": null
}

type alias User =
    { id : Int
    , name : Maybe String
    , email : String
    }

user : Decoder User
user =
    Json.Decode.succeed User
        |> required "user_id" int
        |> required "name" (nullable string) -- returns Maybe String, if null -> Nothing, if str -> Just String
        |> optional "email" string "me@foo.com" -- fallback if no email
```

# Tuples
Serves the same purpose of records but more concise. They use positions instead of names.

```elm
x = Tuple.first ( 5, 7 ) -- 5
( name, x, y ) = ( "foo", 5, 7 )
```

Records and tuples are nothing more than groups of values that travel together.

# Commands

```elm
pickGreeting : List String -> String -- this won't work b/c all fns are pure fns

pickGreeting : List String -> Cmd Msg
```

Elm Runtime uses Msg to talk to update such as user interaction.

Commands go from update -> elm runtime, then elm runtime sends a message to update.
A Command is a description of something you want done (not a side effect).

The Elm Runtime manages randomness! Use a command to ask the runtime to get a random number. Therefore update is still pure functions.

# Browser.element

```element
update : Msg -> Model -> ( Model, Cmd Msg )
```

# Side Effects vs Managed Effects

Functions are pure and cannot have side effects. It can have managed effects however, which are done via Commands.

# Http.getString

```elm
getString : String -> Request String
```
This returns a string if it succeeds, if it fails then there's an error type.

Use Http.send to translate a Request -> Cmd.
* translate failure into a Msg
* translate success into a Msg

```elm
Http.getString "/feed?tag=happiness"
    |> Http.send CompletedLoadFeed
```

# Http.get

```elm
-- success gives you a List of Articles
Http.get articlesDecoder url
```

# Subscriptions

Another way to send messages to update. Subscriptions are global event listeners.

Update is the brain of the elm app. It takes in messages from view, commands or subscriptions.

ie. websockets, stuff on a timer

A function called subscriptions takes in a model and returns any subscriptions we care about for that model.

# Talking with JS

Elm talks with JS using metaphorical client/server communication. As long as we're sending immutable data and receiving immutable data.

Allows for free communication with JS without sacrificing Elm's guarantees.

Elm sends data to JS. JS sends data to Elm. Using commands and subscriptions!

## Unbound Type Variables

```elm
[ 1, 2 ] : List Int
[ "a", "b" ] : List String
[] : List a -- unbound type variable
```

# Talking to JS

`port` keyword - `port module` should be at the top to know that it does Javscript interrupt.

Elm side

```elm
port storeSession : Maybe String -> Cmd msg
-- write the type only, Elm generates the function for you
-- make a function called storeSession that takes in a string and sends that string to Javascript
-- returns a Cmd that when executed, sends to JS and does nothing - fire and forget

port onSessionChange : (String -> msg) -> Sub msg
-- inbound, when Javascript sends a string, wrap it up in a msg type and sent to update in subscription
```

Note that it's a maybe string for logging in and logging out. Pass Nothing as the Maybe String when logging out.

Javascript side

```js
// starts up the Elm app
var app = Elm.Main.init({ flags: session });

// This executes everytime storeSession from Elm runs
app.ports.storeSession.subscribe(function(str) {
    // JS code whether str is null or not
});

// Send to Elm
app.ports.onSessionChange.send(someString);
````








