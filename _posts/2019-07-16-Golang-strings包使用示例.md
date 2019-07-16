## 示例

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	str := "Hi, I'm Marc, Hi."
	fmt.Printf("The position of \"Marc\" is: ")
	fmt.Printf("%d\n", strings.Index(str, "Marc"))
	fmt.Printf("The position of \"Hi\" is: ")
	fmt.Printf("%d\n", strings.Index(str, "Hi"))
	fmt.Printf("The last index of \"Hi\" is: ")
	fmt.Printf("%d\n", strings.LastIndex(str, "Hi"))
	fmt.Printf("The index of \"hehe\" is: ")
	fmt.Printf("%d\n", strings.LastIndex(str, "hehe"))
	fmt.Printf("%d\n", strings.Count(str, "Hi"))
	fmt.Printf(strings.Replace(str, "H", "h", -1) + "\n")
	fmt.Printf(strings.Repeat(str, 2) + "\n")
	fmt.Printf(strings.ToLower(str) + "\n")
	fmt.Printf(strings.ToUpper(str) + "\n")
	sl := strings.Fields(str)
	fmt.Printf("%v\n", sl)
	fmt.Printf("%v\n", sl[2])
}
```
