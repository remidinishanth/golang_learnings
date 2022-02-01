# Golang

* https://gobyexample.com/
* https://astaxie.gitbooks.io/build-web-application-with-golang/content/en/index.html
* https://www.openmymind.net/The-Little-Go-Book/
* https://github.com/joncalhoun/GoBooks
* https://github.com/uber-go/guide/blob/master/style.md

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

#### Arrays

* `var x [5]int = [5]int{1, 2, 3, 4, 5}` Array pre-defined with values
  * Length of literal must be length of array
  * `...` for size can also be used `x := [...]int{1, 2, 3, 4, 5}`

Iterating

```go
x := [3]int{1, 2, 3}

for i, v range x {
    fmt.Printf("index %d, val %d", i, v)
}
```

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

#### Byte Slices and Strings

```go
bs := []byte{71, 111}
fmt.Printf("%s", bs) // Output: Go
```

`%s` converts the bute slice to a string

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
```
