# Golang

* https://gobyexample.com/
* https://astaxie.gitbooks.io/build-web-application-with-golang/content/en/index.html
* https://www.openmymind.net/The-Little-Go-Book/
* https://github.com/joncalhoun/GoBooks

![](images/go_cli.png)

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
