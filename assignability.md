## Assignability
A value x of type V is assignable to a variable of type T ("x is assignable to T") if one of the following conditions applies:

* V and T are identical.

```go
func main() {
	var a int
	b := 3
	a = b
	fmt.Printf("a: [%T, %v], b: [%T, %v]", a, a, b, b)
}

// a: [int, 3], b: [int, 3]
```

V and T have identical underlying types but are not type parameters and at least one of V or T is not a named type.
V and T are channel types with identical element types, V is a bidirectional channel, and at least one of V or T is not a named type.
T is an interface type, but not a type parameter, and x implements T.
x is the predeclared identifier nil and T is a pointer, function, slice, map, channel, or interface type, but not a type parameter.
x is an untyped constant representable by a value of type T.
Additionally, if x's type V or T are type parameters, x is assignable to a variable of type T if one of the following conditions applies:

x is the predeclared identifier nil, T is a type parameter, and x is assignable to each type in T's type set.
V is not a named type, T is a type parameter, and x is assignable to each type in T's type set.
V is a type parameter and T is not a named type, and values of each type in V's type set are assignable to T.
