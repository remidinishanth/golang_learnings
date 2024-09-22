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


Let’s try to implement a function `Sprint`

```go
func Sprint(x interface{}) string {
    type stringer interface {
        String() string
    }
    switch x := x.(type) {
    case stringer:
        return x.String()
    case string:
        return x
    case int:
        return strconv.Itoa(x)
        // ...similar cases for int16, uint32, and so on...
    case bool:
        if x {
            return "true"
        }
        return "false"
    default:
        // array, chan, func, map, pointer, slice, struct
        return "???"
    }
}
```

Without a way to inspect the representation of values of unknown types, we quickly get stuck. What we need is reflection.

## reflect package

Package reflect implements run-time reflection, allowing a program to manipulate objects with arbitrary types.

It defines two important types: `Type` and `Value`.

The typical use is to take a value with static type `interface{}` and extract its dynamic type information by calling `TypeOf`, which returns a `Type`.

A call to `ValueOf` returns a `Value` representing the run-time data. `Zero` takes a `Type` and returns a `Value` representing a zero value for that type. 


### reflect.Type

* A `Type` represents a Go type.
* It is an interface with many methods for discriminating among types and inspecting their components, like the fields of a struct or the parameters of a function.
* The sole implementation of `reflect.Type` is the type descriptor, the same entity that identifies the dynamic type of an interface value.

Conceptually, a value of an interface type, or interface value, has two components: a concrete type and a value of that type. These are called the interface’s dynamic type and dynamic value.

![image](https://github.com/user-attachments/assets/aae7a52b-f9bd-4c50-892e-021929eef0c2)

An interface value is described as nil or non-nil based on its dynamic type, so this is a nil interface value. You can test whether an interface value is nil using w == nil or w != nil.

![image](https://github.com/user-attachments/assets/f7c8017c-a4ff-4483-b6b8-0007ac0d784e)

```go
const debug = true

func main() {
    var buf *bytes.Buffer
    if debug {
        buf = new(bytes.Buffer) // enable collection of output
    }
    f(buf) // NOTE: subtly incorrect!
    if debug {
        // ...use buf...
    }
}

// If out is non-nil, output will be written to it.
func f(out io.Writer) {
    // ...do something...
    if out != nil {
        out.Write([]byte("done!\n"))
    }
}
```

When debug is false, the program would panic because `buf` is `nil`

![image](https://github.com/user-attachments/assets/cef4f70c-7614-4321-9f13-cd4c2130d56f)



The `reflect.TypeOf` function accepts any `interface{}` and returns its dynamic type as a `reflect.Type`:

```go
t := reflect.TypeOf(3) // a reflect.Type
fmt.Println(t.String()) // "int"
fmt.Println(t) // "int"
```

* The `TypeOf(3)` call assigns the value `3` to the `interface{}` parameter.
* An assignment from a concrete value to an interface type performs an implicit interface conversion, which creates an interface value consisting of two components:
  - its dynamic type is the operand’s type (int), and
  - its dynamic value is the operand’s value (3).

Because `reflect.TypeOf` returns an interface value’s dynamic type, it always returns a concrete type.

The code below prints `*os.File`, not `io.Writer`. Later, we will see that `reflect.Type` is capable of representing interface types too.

```go
var w io.Writer = os.Stdout
fmt.Println(reflect.TypeOf(w)) // "*os.File"
```

### reflect.Value

* The other important type in the reflect package is `Value`. A `reflect.Value` can hold a value of any type.
* The `reflect.ValueOf` function accepts any `interface{}` and returns a `reflect.Value` containing the interface’s dynamic value.
* As with reflect.TypeOf, the results of `reflect.ValueOf` are always concrete, but a reflect.Value can hold interface values too.

```go
v := reflect.ValueOf(3) // a reflect.Value
fmt.Println(v)          // "3"
fmt.Printf("%v\n", v)  // "3"
fmt.Println(v.String()) // NOTE: "<int Value>"
```

Calling the `Type` method on a `Value` returns its type as a `reflect.Type`:

```go
t := v.Type() // a reflect.Type
fmt.Println(t.String()) // "int"
```

The inverse operation to `reflect.ValueOf` is the `reflect.Value.Interface` method. It returns an `interface{}` holding the same concrete value as the `reflect.Value`:

```go
v := reflect.ValueOf(3) // a reflect.Value
x := v.Interface()      // an interface{}
i := x.(int)           // an int
fmt.Printf("%d\n", i)  // "3"
```

## APIs of Reflection

Reflection acts on three important reflection properties that every Golang object has: `Type`, `Kind`, and `Value`. ‘Kind’ can be one of `struct`, `int`, `string`, `slice`, `map`, or another Golang primitives.

The reflect package provides the following functions to get these properties:

```go
// Get the reflection value of an object.
rValue := reflect.ValueOf(input)

// Get the reflection kind of an object.
rKind := rValue.Kind()

// Get the reflection type of an object (two options).
rType := reflect.TypeOf(input)
rType := rValue.Type()

// Get the underlying object of a pointer object.
underlyingValue := rValue.Elem()

// Traverse through all the fields of a struct.
if rType.Kind() == reflect.Struct {
	for i := 0; i < rType.NumField(); i++ {
		fieldValue := rValue.Field(i)
	}
}
```

Ref: https://www.nutanix.dev/2022/04/22/golang-the-art-of-reflection/


## Setting values using reflection

* Recall that some Go expressions, like `x`, `x.f[1]`, and `*p`, denote variables, while others, like `x + 1` and `f(2)`, do not. 
* A variable is an addressable storage location that contains a value, and its value may be updated through that address.

A similar distinction applies to `reflect.Values`. Some are addressable; others are not. Consider the following declarations:

```go
x := 2                   // value    type    variable?
a := reflect.ValueOf(2)  // 2        int     (no)
b := reflect.ValueOf(x)  // 2        int     (no)
c := reflect.ValueOf(&x) // &x       *int    (no)
d := c.Elem()            // 2        int     (yes, x)
```


* The value within a is not addressable. It is merely a copy of the integer `2`.
* The same is true of `b`.
* The value within `c` is also non-addressable, being a copy of the pointer value `&x`. In fact, no `reflect.Value` returned by `reflect.ValueOf(x)` is addressable.
* But `d`, derived from `c` by dereferencing the pointer within it, refers to a variable and is thus addressable.

We can use this approach, calling `reflect.ValueOf(&x).Elem()`, to obtain an addressable Value for any variable `x`.

We can ask a `reflect.Value` whether it is addressable through its `CanAddr` method:

```go
fmt.Println(a.CanAddr()) // "false"
fmt.Println(b.CanAddr()) // "false"
fmt.Println(c.CanAddr()) // "false"
fmt.Println(d.CanAddr()) // "true"
```


To recover the variable from an addressable `reflect.Value` requires three steps. 
* First, we call `Addr()`, which returns a Value holding a pointer to the variable.
* Next, we call `Interface()` on this Value, which returns an interface{} value containing the pointer.
* Finally, if we know the type of the variable, we can use a type assertion to retrieve the contents of the interface as an ordinary pointer. We can then update the variable through the pointer:

```go
x := 2
d := reflect.ValueOf(&x).Elem() // d refers to the variable x
px := d.Addr().Interface().(*int) // px := &x
*px = 3 // x = 3
fmt.Println(x) // "3"
```

Alternatively, we can update the variable referred to by an addressable reflect.Value directly, without using a pointer, by calling the reflect.Value.Set method:

```go
d.Set(reflect.ValueOf(4))
fmt.Println(x) // "4"

d.Set(reflect.ValueOf(int64(5))) // panic: int64 is not assignable to int


x := 2
b := reflect.ValueOf(x)
b.Set(reflect.ValueOf(3)) // panic: Set using unaddressable value
```


More details

```
x := 1
rx := reflect.ValueOf(&x).Elem()
rx.SetInt(2) // OK, x = 2
rx.Set(reflect.ValueOf(3)) // OK, x = 3
rx.SetString("hello") // panic: string is not assignable to int
rx.Set(reflect.ValueOf("hello")) // panic: string is not assignable to int


var y interface{}
ry := reflect.ValueOf(&y).Elem()
ry.SetInt(2) // panic: SetInt called on interface Value
ry.Set(reflect.ValueOf(3)) // OK, y = int(3)
ry.SetString("hello") // panic: SetString called on interface Value
ry.Set(reflect.ValueOf("hello")) // OK, y = "hello"
```


## Interface and Reflection

* Interfaces are one of the fundamental tools for abstraction in Go.
* Interfaces store type information when assigned a value.
* Reflection is a method of examining type and value information at runtime.

An interface encodes three things: a value, a method set, and the type of the stored value.

![image](https://github.com/user-attachments/assets/642d1ec5-51a9-469a-b3a3-d9d1cd6429d2)

We can clearly see the three parts of the interface in that diagram: the `_type` is type information, `*data` is a pointer to the actual value, and the `itab` encodes the method set.

When a function accepts an interface as a parameter, passing a value to that function packs the value, method set, and type into the interface.

https://blog.gopheracademy.com/advent-2018/interfaces-and-reflect/
