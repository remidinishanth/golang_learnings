Refer: https://eli.thegreenplace.net/2020/embedding-in-go-part-1-structs-in-structs/


## Interface embedding

Such a pattern is even used in the Golang sort package https://go.dev/src/sort/sort.go for reverse

```go
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
