---
slug: golang-challenges
title: golang挑戰題 - defer & panic
tags: [golang]
---

### 題目
```go
package main

import "fmt"

func main() {
	defer_call()
}

func defer_call() {
	defer func() { fmt.Println("第一個defer") }()
	defer func() { fmt.Println("第二個defer") }()
	defer func() { fmt.Println("第三個defer") }()

	panic("異常")
}
```

```md title="執行結果"
第三個defer
第二個defer
第一個defer
panic: 異常

goroutine 1 [running]:
main.defer_call()
	test.go:14 +0x78
main.main()
	test.go:6 +0x1c
exit status 2
```

若想知道執行結果為何是這樣的順序，要先了解defer與panic的特性。

### [defer]

一個function內若有多個deferred function，它們會依序被推到一個stack，
最後執行順序會是後進先出(LIFO)

因此解釋了以下的順序:

	第三個defer
	第二個defer
	第一個defer


### [panic]

function defer_call呼叫panic時，會依序做以下動作:
1. function defer_call停止
2. 執行此function的deferred function
3. 返回caller function
4. 對於caller function來說，defer_call的行為可以視為跟呼叫panic一樣
5. 重複以上動作，直到返回程式最上層
6. 程式崩潰

這解釋了為何`panic: 異常`會在`第一個defer`之後
