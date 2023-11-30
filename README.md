# lizard-lang

Lizard is a project of a high level programming language that tries to create meaningful source codes.

# Principles

- Be meaningful
- Be clear
- Be organized

# Features

Lizard splits it's features in two main domains: values and types. A value must be owned by someone, once that the owner is out of scope the value is droped.

The main operation over values are **declarations** (`decl`) which can assing a value expression to an identifier for later use. The main operation over types are **constructions** (`cons`) which can assing a type expression to an identifier for later use.

## Types

Types determine the set of values that can be represented so we have operations over types to build new types (and also let new values be representable)

### Derivate types

- **Array** `[s]T`: a segment of `s` elements of type `T`
- **Block** `[]T`: a segment of elements of type `T` but the time is determined in the runtime
- **Reader** `&T`: a read-only reference to a value of type `T`
- **Writer** `@T`: a read-write reference to a value of type `T`
- **Slice** `[&a..b]T` or `[@a..b]T`: a reference to a segment of an array or a block of `T`
- **Await** `...T`: an element of type `T` which value may or may not be avaliable at the moment (useful for parallel operations)
- **Nullable** `?T`: an element which value can be something or a `null`
- **Failable** `!T`: an element which value can be a type which is tagged as `#error`
- **Crashable** `~T`: an element which can crash the program (usually the output of a function which may call crash)
- ! **Sharable** `#T`: an element that the ownership can be shared
- **Lockable** `:T`: an element which access can be locked (a mutex, useful for parallel operations)
### Compound Types

Compounds group together different types into a brand new type. A basic compound can be cohersed into any other kind of compound.

- **Compound** `(T1, T2, ...)`: an indexable type, each field can be of a different type
- **Named Compound** `(T1 a1, T2 a2, ...)`: a compound type where indexes are names
- **Object** `{access T1 a1, access T2 a2, ...}`: a named compound where each field can be `read` (external environment can read it's value), `write` (same but for reading and writing) or `priv` (only internal operations can read or write over this field)

### Functional Types

In Lizard, functional types can be used as their output type but then we create a new function. It means, if a function returns a type `T` and receives values of types `A`, `B`, `C`, and instead of `B` we pass in another function that receives types `D` and `E` and returns type `B`, we can compound the functions and obtain a function that receives `D` and `E` and returns `T`.

- **Function** `(T1 a1, T2 a2, ...) -> T`: a function which receives a named compound of types `(T1, T2, ...)` and returns a type `T` and can take access to external values `b1, b2, ...` by reference or by taking ownership.
- **Pure Function** `pure (T1 a1, T2 a2, ...) -> T`: a pure function
- **Procedure** `proc (T1 a1, T2 a2, ...)`: a function with no return value, just modify the state of the program
- **Parallel** `prll ... -> ...T`: a function which call is non-blocking by returning an await type, can be a *function*, a *pure function* or a *procedure*. 
- **Operation**: `(&|@)self prll? (pure|proc)? (T1 a1, T2 a2, ...) -> (T|Self)`: an operation that can access data in a constructed type (the access is determined by the starting `&self`, `@self` or `self`) and can return any type (including the constructed type `Self`)

### Generic Types

Generics are type parameters for other types, represented by angle brackets (`<`, `>`). 

#### Generic Functions
- `(proc|pure)? <U1, U2, ... U>(T a, U1 a1, U2 a2, ...) -> U`

Any type involved in a functional type can be generic

#### Tags

Tags are used to let generic types less generics and ensure that they implement some functionality

`U #(tag1, tag2, ...)`

Tags can be created using **constructions**

### Constructions

Constructions, as mentioned, are used to create new types in a more complex way. A construction receives a name, a specification and an optional implementation

```
cons TypeName generics specification impl implementation
```

The generics are optional, the specification can be any type and the implementation is also optional an can be used to tag the constructed

```
impl #(tag1, tag2, ...) {
    meth1 ...
    meth2 ...
    ...
}
```

A construction that provides nothing but a specification is called an *alias*, *aliased* types can't be implictly casted from/to the original type, nor they are implicitly compatible, but a default cast is available.

## Values
