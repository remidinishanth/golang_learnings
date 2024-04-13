## Assignability
A value `x` of type `V` is assignable to a variable of type `T` ("x is assignable to T") if one of the following conditions applies:

* `V` and `T` are identical.

```go
func main() {
	var a int
	b := 3
	a = b
	fmt.Printf("a: [%T, %v], b: [%T, %v]", a, a, b, b)
}

// a: [int, 3], b: [int, 3]
```

* V and T have identical underlying types but are not type parameters and at least one of V or T is not a named type. Ref: https://stackoverflow.com/questions/29332879/golang-underlying-types

```go
type T1 string
type T2 T1
type T3 []T1
type T4 T3

// The underlying type of string, T1, and T2 is string.
// The underlying type of []T1, T3, and T4 is []T1.

func main() {
	var t3 T3 = []T1{"a", "b"}
	fmt.Printf("t3 = '%+v'\n", t3)

	// var t4 T4 = []string{}
	// cannot use []string literal (type []string) as type T4 in assignment

	var t4 T4 = T4(t3)
	fmt.Printf("t4 = '%+v'\n", t4)

	t4 = []T1{T1("c"), T1("d")}
	fmt.Printf("t4 = '%+v'\n", t4)
}

// t3 = '[a b]'
// t4 = '[a b]'
// t4 = '[c d]'
```

* V and T are channel types with identical element types, V is a bidirectional channel, and at least one of V or T is not a named type.
T is an interface type, but not a type parameter, and x implements T.
x is the predeclared identifier nil and T is a pointer, function, slice, map, channel, or interface type, but not a type parameter.
x is an untyped constant representable by a value of type T.
Additionally, if x's type V or T are type parameters, x is assignable to a variable of type T if one of the following conditions applies:

x is the predeclared identifier nil, T is a type parameter, and x is assignable to each type in T's type set.
V is not a named type, T is a type parameter, and x is assignable to each type in T's type set.
V is a type parameter and T is not a named type, and values of each type in V's type set are assignable to T.
