## Assignability
A value `x` of type `V` is assignable to a variable of type `T` ("x is assignable to T") if one of the following conditions applies:

### `V` and `T` are identical.

```go
func main() {
	var a int
	b := 3
	a = b
	fmt.Printf("a: [%T, %v], b: [%T, %v]", a, a, b, b)
}

// a: [int, 3], b: [int, 3]
```

### V and T have identical underlying types but are not type parameters and at least one of V or T is not a named type.
- Ref: https://stackoverflow.com/questions/29332879/golang-underlying-types
- Ref: https://stackoverflow.com/questions/32983546/named-and-unnamed-types

`var x struct{ I int }` x is **unnamed**

```go
	var x struct{ I int }

	type Foo struct{ I int }
	var y Foo

	fmt.Println(x, y)

	x = y // works
	y = x // works

	var x2 struct{ I int }
	x = x2 // works

	type Bar struct{ I int }
	var z Bar

	y = z // cannot use z (variable of type Bar) as Foo value in assignment
	y = Foo(z) // works
```

Here `x` and `x2` have the same type, while `y` and `z` do not.

```go
type T1 string
type T2 T1
type T3 []T1
type T4 T3

// The underlying type of string, T1, and T2 is string.
// The underlying type of []T1, T3, and T4 is []T1.

func main() {
	var t1 T1 = "ABC"
	fmt.Printf("t1: [%T, %v]\n", t1, t1)
	// t1: [main.T1, ABC]

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

### V and T are channel types with identical element types, V is a bidirectional channel, and at least one of V or T is not a named type.

Channel types can be bi-directional or single-directional. Assume `T` is an arbitrary type,
* `chan T` denotes a bidirectional channel type. Compilers allow both receiving values from and sending values to bidirectional channels.
* `chan<- T` denotes a send-only channel type. Compilers don't allow receiving values from send-only channels.
* `<-chan T` denotes a receive-only channel type. Compilers don't allow sending values to receive-only channels.

Values of bidirectional channel type `chan T` can be implicitly converted to both send-only type `chan<- T` and receive-only type `<-chan T`, but not vice versa (even if explicitly). 

Values of send-only type `chan<- T` can't be converted to receive-only type `<-chan T`, and vice versa. Note that the `<-` signs in channel type literals are modifiers.
  
### T is an interface type, but not a type parameter, and x implements T.

* x is the predeclared identifier nil and T is a pointer, function, slice, map, channel, or interface type, but not a type parameter.
x is an untyped constant representable by a value of type T.
Additionally, if x's type V or T are type parameters, x is assignable to a variable of type T if one of the following conditions applies:

x is the predeclared identifier nil, T is a type parameter, and x is assignable to each type in T's type set.
V is not a named type, T is a type parameter, and x is assignable to each type in T's type set.
V is a type parameter and T is not a named type, and values of each type in V's type set are assignable to T.
