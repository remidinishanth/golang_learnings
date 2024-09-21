# Golang

Go is a general-purpose language designed with systems programming in mind.

* https://gobyexample.com/
* https://astaxie.gitbooks.io/build-web-application-with-golang/content/en/index.html
* https://www.openmymind.net/The-Little-Go-Book/
* https://github.com/joncalhoun/GoBooks
* https://github.com/uber-go/guide/blob/master/style.md
* https://www.miek.nl/go/

Also refer https://yourbasic.org/golang/ and https://www.programming-books.io/essential/go/ and https://www.golang-book.com/books/intro/9

![](images/go_cli.png)

There is also `go doc` for creating documentation and `go list` to list all the installed packages

### Go's 21st Century Characteristics
* Concurrency
* Distributed Systems
* Garbage Collection
* Memory Locality
* Readability

Three things that make Go fast, fun, and productive: **interfaces**, **reflection**, and **concurrency**.

#### Concurrency
* **Concurrent**:
Go makes it easy to “fire off” functions to be run as very lightweight threads. These threads are called **goroutines** in Go.
* **Channels**: Communication with these goroutines is done, either via shared state or via channels.
* Select enables task synchronization

#### Objects in Go
* Go uses **structs** with associated methods.
* Simplified implementation of classes
  * No inheritance
  * No constructors
  * No generics

### Workspaces

Three subdirectories
* `src` - contains source code files
* `pkg` - contains packages(libraries)
* `bin` - contains executables

```
    ├──src/
    |  ├──main.go
    |  ├──say/
    |  |  ├──say.go
    |  |  ├──say_test.go
    ├──bin/
    |  ├──say
    └──pkg/
       └──linux_amd64/
          └──say.a
```

Also refer https://github.com/golang-standards/project-layout

### Basics

In Go, a name is **exported** if it begins with a capital letter. For example, `Pizza` is an exported name, as is `Pi`, which is exported from the math package.

#### Arrays

* The type `[n]T` is an array of `n` values of type `T`.
* `var x [5]int = [5]int{1, 2, 3, 4, 5}` Array pre-defined with values
  * Length of literal must be length of array
  * `...` for size can also be used `x := [...]int{1, 2, 3, 4, 5}`, when using `[...]` size will be deduced from `{ ... }`

Iterating

```go
x := [3]int{1, 2, 3}

for i, v := range x {
    fmt.Printf("index %d, val %d", i, v)
}
```

`range` allows to iterate through array, pointer to array, slice, string, map or values received on a channel. Read more at https://medium.com/golangspec/for-statement-and-its-all-faces-in-golang-abcbdc011f81

#### Slice

* A "window" on an **underlying array**
* Variable size, up to the whole array
* Slice has 3 properties
  * **Pointer** indicates the start of the slice
  * **Length** `len()` is the number of elements in the slice
  * **Capacity** `cap()` is maximum number of elements - From start of slice to end of array 

```go
  arr := [...]int{1, 2, 3, 4, 5}

  sli := arr[1:3]
  fmt.Println(cap(sli), len(sli)) // 4 2
```

* Writing to slice changes underlying array
* Overlapping slices refer to the same array elements

Slice literals

* A slice literal is like an array literal without the length.
* This is an array literal: `[3]bool{true, true, false}`
* And this creates the same array as above, then builds a slice that references it:

```go
[]bool{true, true, false}
```

#### Variable Slices

* Create a slice(and array) using `make()`
* `sli = make([]int, 10)` 2 arguments: type and length/capacity
* `b := make([]int, 0, 5) // len(b)=0, cap(b)=5`

```go
slice := make([]int, 0, 5)
// append element to end of slice
slice = append(slice, 5)
// append multiple elements to end
slice = append(slice, 3, 4)

// appending slice to slice
a := []string{"!"}
a2 := []string{"Hello", "world"}
a = append(a, a2...)
```

A good rule of thumb when declaring variables is to use the key- word var when declaring variables that will be initialized to their zero value, and to use the short variable declaration operator when you’re providing extra initialization or making a function call. Ref: Go in Action

#### Byte Slices and Strings

```go
byte // alias for uint8

rune // alias for int32
     // represents a Unicode code point
```

In Go, a string is in effect a **read-only** slice of bytes. Ref: https://go.dev/blog/strings

```go
bs := []byte{71, 111}
fmt.Printf("%s", bs) // Output: Go
```

The following example of `echo` function is a quadratic process that could be costly if the number of arguments is large, this is because strings are read-only.
```go
func main() {
  s, sep := "", ""
  for _, arg := range os.Args[1:] {
    s += sep + arg
    sep = " "
  }
  fmt.Println(s)
}
```

> Each time around the loop, the string s gets completely new contents.
> The += statement makes a new string by concatenating the old string , a space character, and the next argument, then assigns the new string to s.
> The old contents of s are no longer in use, so they will be garbage-collected in due course.

`%s` converts the byte slice to a string

```go
s := "Wow look at me"
bs := []byte(s)
fmt.Printf("%s", bs) // Output: Wow look at me
fmt.Printf("%d", bs) // Output: [87 111 119 32 108 111 111 107 32 97 116 32 109 101]
```

If we use unicode characters in a string

```go
bs := []byte("◺")
fmt.Println(bs) // Output: [226 151 186]
s := string(bs)
fmt.Println(len(s)) // Output: 3

fmt.Println(utf8.RuneCountInString(s)) // Output: 1

fmt.Printf("%+q\n", s) // "\u25fa"
fmt.Printf("%x\n", s) // e297ba
fmt.Printf("% x\n", s) // e2 97 ba

// Lower left triangle unicode (U+25FA)
// UTF-8 (hex)	0xE2 0x97 0xBA (e297ba)
// UTF-8 (binary)	11100010:10010111:10111010
// UTF-16 (hex)	0x25FA (25fa)
```

Also check: https://stackoverflow.com/questions/44565859/how-does-utf-8-encoding-identify-single-byte-and-double-byte-characters

A string contains characters as unicode points, not bytes. `len(string)` returns the number of bytes in this string, but not the number of characters. 
Therefore, we are converting the string to `rune` array and then finding the array length.

```go
    var str = "ab£"
    var length = len([]rune(str))
    println("Length of the string is : ", length) // 3
    println("Output of len(str) is : ", len(str)) // 4
```

#### map

* A map holds a set of key/value pairs and provides constant-time operations to store, retrieve,
or test for an item in the set. 
* The key may be of any type whose values can compared with `==`,
strings being the most common example; the value may be of any type at all.
* The order of map iteration is not specified, but in practice it is random, varying from one
run to another. This design is intentional since it prevents programs from relying on any particular ordering where none is guaranteed.

#### Type

A type determines a set of values together with operations and methods specific to those values.

The method set of an interface type is its interface. The method set of any other type `T` consists of all methods declared with receiver type `T`. The method set of the corresponding pointer type `*T` is the set of all methods declared with receiver `*T` or `T` (that is, it also contains the method set of `T`). 

Ref: https://go.dev/ref/spec#Method_sets

A method call `x.m()` is valid if the method set of (the type of) `x` contains `m` and the argument list can be assigned to the parameter list of m. If `x` is addressable and `&x`'s method set contains `m`, `x.m()` is shorthand for `(&x).m()`.

Ref: https://go.dev/ref/spec#Calls

Also checkout https://go.dev/tour/methods/1

#### Struct

A struct is a sequence of named elements, called fields, each of which has a name and a type.

Struct fields can be accessed through a struct pointer.

To access the field X of a struct when we have the struct pointer p we could write `(*p).X`. However, that notation is cumbersome, so the language permits us instead to write just `p.X`, without the explicit dereference.

```go
type Vertex struct {
    X int
    Y int
}

func main() {
    v := Vertex{1, 2}
    p := &v
    p.X = 1e9
    fmt.Println(v)
}
```

We can list just a subset of fields by using the `Name:` syntax. (And the order of named fields is irrelevant.)

```go
var (
    v1 = Vertex{1, 2}  // has type Vertex
    v2 = Vertex{X: 1}  // Y:0 is implicit
    v3 = Vertex{}      // X:0 and Y:0
    p  = &Vertex{1, 2} // has type *Vertex
)
```

#### Methods

Refer https://go.dev/tour/methods/1

Go does not have classes. However, you can define methods on types.

A method is a function with a special **receiver argument**.

The receiver appears in its own argument list between the func keyword and the method name.

```go
type Person struct {
    name string
    age int
    city,phone string
}

// A person method
func (p Person ) SayHello() {
    fmt.Printf("Hi, I am %s, from %s\n", p.name, p.city)
}

// A person method
func (p Person) GetDetails() {
    fmt.Printf("[Name: %s, Age: %d, City: %s, Phone: %s]\n", p.name,p.age, p.city, p.phone)
}

func main() {
    p := Person{name:"Nishanth", age:24, city:"Warangal"}
    p.SayHello() // Hi, I am Nishanth, from Warangal
}
```

If you want to modify the data of a receiver from the method, the **receiver must be a pointer**. Here go **interprets** `p.SayHello()` as `(&p).SayHello()` since the method has a pointer receiver. This makes our life easier, We could also use `p1 := &Person{name:"Nishanth", city:"Warangal"}` and call `p1.SayHello()`.


A method call `x.m()` is valid if the method set of (the type of) `x` contains `m` and the argument list can be assigned to the parameter list of `m`. If `x` is addressable and `&x`’s method set contains `m`, `x.m()` is shorthand for `(&x).m()`


In Go, you can define methods using both pointer and non-pointer method receivers. The former looks like `func (t *Type)` and the latter looks like `func (t Type)`.

```go
func (p *Person ) SayHello() {
    //Implementations here 
}
```

So what’s the difference between pointer and non-pointer method receivers?

Simply stated: you can treat the receiver as if it was an argument being passed to the method. All the same reasons why you might want to pass by value or pass by reference apply.

Reasons why you would want to pass by reference as opposed to by value:
* You want to actually modify the receiver (“read/write” as opposed to just “read”)
* The struct is very large and a deep copy is expensive
* Consistency: if some of the methods on the struct have pointer receivers, the rest should too. This allows predictability of behavior.

```go
package main

import "fmt"

type Mutatable struct {
    a int
    b int
}

func (m Mutatable) StayTheSame() {
    m.a = 5
    m.b = 7
}

func (m *Mutatable) Mutate() {
    m.a = 5
    m.b = 7
}

func main() {
    m := &Mutatable{0, 0}
    fmt.Println(m)
    m.StayTheSame()
    fmt.Println(m)
    m.Mutate()
    fmt.Println(m)
}

// Output:
// &{0 0}
// &{0 0}
// &{5 7}
```

There are two reasons to use a pointer receiver.
* The first is so that the method can modify the value that its receiver points to.
* The second is to avoid copying the value on each method call. This can be more efficient if the receiver is a large struct, for example.

#### Method on non-struct type

You can declare a method on non-struct types, too.

In this example we see a numeric type MyFloat with an Abs method.

You can only declare a method with a receiver **whose type is defined in the same package as the method**. You cannot declare a method with a receiver whose type is defined in another package (which includes the built-in types such as int).

```go
type MyFloat float64

func (f MyFloat) Abs() float64 {
    if f < 0 {
        return float64(-f)
    }
    return float64(f)
}

func main() {
    f := MyFloat(-math.Sqrt2)
    fmt.Println(f.Abs())
}
```

#### defer

The deferred **call's arguments are evaluated immediately**, but the function call is not executed until the surrounding function returns.

```go
func main() {
    i := 1
    defer fmt.Println(i+1)
    i++
    fmt.Print("value of i:: ")
}
```

### Go Project Structure

Go programs are organized into packages. A package is a collection of source files in the same directory that are compiled together. Functions, types, variables, and constants defined in one source file are visible to all other source files within the same package.

Ref: https://go.dev/doc/code

By convention, one-method interfaces are named by the method name plus an `-er` suffix or similar modification to construct an agent noun: Reader, Writer, Formatter, CloseNotifier etc.

### new

`new` takes a type as an argument, allocates enough memory to fit a value of that type and returns a pointer to it.

```go
func one(xPtr *int) {
  *xPtr = 1
}
func main() {
  xPtr := new(int)
  one(xPtr)
  fmt.Println(*xPtr) // x is 1
}
```

`new` with struct

```go
type Circle struct {
	x, y, r float64
}

func main() {
	var x Circle
	fmt.Println(x)
	c := new(Circle)
	fmt.Println(c)

}

// Output:
// {0 0 0}
// &{0 0 0}
```

### Struct tags in go

https://www.digitalocean.com/community/tutorials/how-to-use-struct-tags-in-go


### Sharing memory by communicating

Don't communicate by sharing memory; share memory by communicating

In idiomatic Go programs you won't see a lot of mutexes, condition variables and critical areas protecting shared data. In fact, you probably won't see much locking at all. This is because Go encourages programmers to use channels instead, and channels are built into the language, with awesome features like select, and so on. Proper use of channels removes the need for more explicit locking, is easier to write correctly, tune for performance, and debug.

Ref: https://eli.thegreenplace.net/2018/go-hits-the-concurrency-nail-right-on-the-head/


### The Go plugin
* A Go plugin is package compiled with the `-buildmode=plugin` which creates a shared object (.so) library file instead of the standard archive (.a) library file. 
* Using the standard library's plugin package, Go can dynamically load the shared object file at runtime to access exported elements such as functions an variables.

Ref: https://medium.com/learning-the-go-programming-language/writing-modular-go-programs-with-plugins-ec46381ee1a9

### Interface embedding

Such a pattern is even used in Golang sort package https://go.dev/src/sort/sort.go for reverse

```go
// You can edit this code!
// Click here and start typing.
package main

import "fmt"

type RandomInterface interface {
	func1(int) int
	func2(int) int
}

type B struct {
	RandomInterface
}

func (v B) func1(x int) int {
	return x - 1
}

func main() {
	var interfaceVariable RandomInterface
	interfaceVariable = B{}
	fmt.Println(interfaceVariable.func1(2))
}
```
