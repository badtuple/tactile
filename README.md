Tactile
=======

A repl and serializable syntax for playing with your Rust code.

The idea is that Tactile will give you a simple frame from which you can quickly build custom tools for your project.

Tactile is _not_ a programming language. It is a serialization format along with tools that allow you to edit/work with the format. If you choose to use it to serialize into an AST then you can process/evaluate it as you see fit. If you write an evalaution engine using it then you can use the provided hooks to incorporate it into the existing tools.

It comes with:
1. The ability to derive Serialization and Deserialization on arbitrary types.
2. A simple and well-defined syntax that allows you to specify instances of your types.
3. A nice repl with hooks that allow you to provide your own evaluation engine if needed.
4. Various commandline tools, linters, and syntax highlighters to make it usable.

Some examples of tools that you could build:
1. An attachable console for a running service.
2. A repl that you can use for adhoc scripting with your system.
3. Specialized debuggers and visualizers tailored to your project.
4. Prototypes of a new programming language or DSL with Tactile as a temporary frontend.
5. Custom configuration format and accompanying editor/linter.

### Syntax

The syntax of various constructs are meant to be as simple as possible while retaining the Rust type names/type syntax. The first element after an open paren is the Type that is to be constructed. Values used in compound datatypes are then specified directly after and are mapped to the order of the fields in the struct.

|Type | Example |
|--|--|
| Structs | `(MyStruct "first value corresponds to the first field" 45 "third value")` |
| Enum | `(MyEnum::VariantA)`|
| String/&str/Char | `"yolo"` |
| Integers | `1` `-1` `0xFF` `0b111_000` |
| Vec | `(Vec<usize> 1 2 3 4)` |
| Tuple | `((String, i64) ("yolo" -2))` |


If an underscore is placed where a field value is expected then we will use the impl of the Default trait of the type for that field. If a Type is followed by a lone period, then the Default impl of the overall type will be used.

```
(MyStruct "1st field" _ "3rd field") => (MyStruct "1st field" "default 2nd field" "3rd field")
(MyStruct .) => (MyStruct "default 1st field" "default 2nd field" "default 3rd field")
```

An larger example of some encoded data:

```
(EventRecord 1 UserLogin (SessionInfo 1 Option::Some(4) (chrono::DateTime "2012-12-23 00:00:00")))
(EventRecord 2 UserEdit (
  EditInfo 1 (chrono::DateTime "2012-12-23 00:01:23") UserField::Password
))
```

### Why?

I'm working on a program that procedurally generates animated cartoons and it got too complex really fast. Tactile lets me quickly build out tools for specific modules and systems without constantly rewriting parsing and repls and things for different tools I need in different parts of the codebase. Since there are many multi-step passes, I serialize the state of various Data Structures into Tactile and dump them to disk. That way if I'm surprised by the result I can either inspect what happened manually or load it up in a repl to play with it.

I assume others have similar use cases.

### But why can't you use one of the Lisp crates?

The tools I'm building require some pretty specialized evaluation/handling of input. You could abuse Lisp to get there but I'd rather just write the semantics directly. Plus many Lisps have tons of extra syntax to accommodate various implementations, and I don't want that cruft.

Lisps also give too much power by default. If I want to use Tactile as a configuration language or I want to accept it from users, then I want to define exactly how much power it has. I should have to explicitly add evaluation, loops/recursion, Turing Completeness, or anything else that I might need instead of trying to find ways to prove it safe or safeguard against it.
