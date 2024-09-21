### Switch

Cases are evaluated from
top to bottom, so the first matching one is executed. The optional default case matches if none
of the othercases does; it may be placed anywhere, but common practice to place in the end.

```go
switch coinflip() {
  case "heads":
    heads++
  case "tails":
    tails++
  default:
    fmt.Println("landed on edge!")
}
```


In Go, after a matching case is executed, the control flow automatically exits the switch statement unless explicitly told to "fall through".
This is in contrast to languages like C where cases fall through to the next one unless a break statement is used.


A switch does not need an operand; it can just list the cases, each of which is a boolean
expression:
```go
func Signum(x int) int {
  switch {
  case x > 0:
    return +1
  default:
    return 0
  case x < 0:
    return -1
  }
}
```
