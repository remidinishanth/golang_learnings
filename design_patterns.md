Ref: https://mauricio.github.io/2022/02/07/gof-patterns-in-golang.html

## Builder pattern

> It can be done by using variadic functions with option functions.

![image](https://github.com/remidinishanth/golang_learnings/assets/19663316/a0d7a82f-9bd9-45e5-b3f5-408440c16678)

Ref: https://dave.cheney.net/2014/10/17/functional-options-for-friendly-apis

Source:  Self referential functions and design by Rob Pike http://commandcenter.blogspot.com.au/2014/01/self-referential-functions-and-design.html

## Singleton

https://stackoverflow.com/questions/1823286/singleton-in-go

Two approaches
* `sync.Once`
* `init()`

```go
 package singleton

 import "sync"

 type singleton struct {
 }

 var instance *singleton
 var once sync.Once

 func GetInstance() *singleton {
     once.Do(func() {
         instance = &singleton{}
     })
     return instance
 }
```

Ref: http://marcio.io/2015/07/singleton-pattern-in-go/

If we just do, then it might not be thread safe

```
package singleton

type singleton struct {
}

var instance *singleton

func GetInstance() *singleton {
	if instance == nil {
		instance = &singleton{}   // <--- NOT THREAD SAFE
	}
	return instance
}
```

## Func implementing interface

From go-cloud, health.go

```go
// Checker wraps the CheckHealth method.
//
// CheckHealth returns nil if the resource is healthy, or a non-nil
// error if the resource is not healthy.  CheckHealth must be safe to
// call from multiple goroutines.
type Checker interface {
	CheckHealth() error
}

// CheckerFunc is an adapter type to allow the use of ordinary functions as
// health checks. If f is a function with the appropriate signature,
// CheckerFunc(f) is a Checker that calls f.
type CheckerFunc func() error

// CheckHealth calls f().
func (f CheckerFunc) CheckHealth() error {
	return f()
}
```

Now we can use functions in

```go
// Handler is an HTTP handler that reports on the success of an
// aggregate of Checkers.  The zero value is always healthy.
type Handler struct {
	checkers []Checker
}

// Add adds a new check to the handler.
func (h *Handler) Add(c Checker) {
	h.checkers = append(h.checkers, c)
}
```

Now we can do 

```
healthHandler.Add(health.CheckerFunc(func() error {
  // ...
}))
```

Inspired from `HandlerFunc` from https://pkg.go.dev/net/http?utm_source=godoc#ServeMux.HandleFunc

## Golang SQL Package 

Package sql provides a generic interface around SQL (or SQL-like) databases.

The sql package must be used in conjunction with a database driver. See https://golang.org/s/sqldrivers for a list of drivers.

This function is in the database/sql package for registering database drivers when you use third-party database drivers. 
All of these should call the `Register(name string, driver driver.Driver)` function in `init()` in order to register themselves.

```
// init is automatically invoked when the driver is imported
func init() {
	sql.Register("godynamo", &Driver{})
}
```

Ref:
* https://vyskocil.org/blog/implement-sql-database-driver-in-100-lines-of-go/
* https://astaxie.gitbooks.io/build-web-application-with-golang/content/en/05.1.html
