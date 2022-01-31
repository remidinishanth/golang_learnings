# Golang

* https://gobyexample.com/
* https://astaxie.gitbooks.io/build-web-application-with-golang/content/en/index.html
* https://www.openmymind.net/The-Little-Go-Book/
* https://github.com/joncalhoun/GoBooks

![](images/go_cli.png)

There is also `go doc` for creating documentation and `go list` to list all the installed packages

### Go's 21st Century Characteristics
* Concurrency
* Distributed Systems
* Garbage Collection
* Memory Locality
* Readability

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

* `var x [5]int = [5]{1, 2, 3, 4, 5}` Array pre-defined with values
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
