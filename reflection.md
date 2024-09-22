## Reflection
* Go provides a mechanism to update variables, inspect their values at runtime, call their methods,
and apply the operations intrinsic to their representation without knowing their types at compile time. 
* This mechanism is called reflection.
* Reflection also lets us treat types themselves as first-class values.


Reflection in computing is the ability of a program to examine its own structure, particularly through types; it’s a form of metaprogramming.


Reflection increases the expressiveness of the language. They are crucial to implementing two important APIs: 
* string formatting provided by `fmt`, and
* protocol encoding provided by packages like `encoding/json` and `encoding/xml`.

Reflection is also essential to the template mechanism provided by the `text/template` and `html/template` packages.
