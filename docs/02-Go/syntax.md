---
id: golang-basic-syntax
title: 基本語法
tags:
  - golang
---

## 常用命令
* go mod
    ```bash
    [初始化專案]
    $ mkdir my-project
    $ cd my-project
    $ go mod init PACKAGE_NAME
    
    [更新go.mod]
    $ go mod tidy (依據程式引用的模組，更新go.mod，讓go.mod變得整齊正確)
    ```

* 安裝套件
    ```bash
    $ go get -u github.com/gofiber/fiber/v2

    note:
    * 模組會下載在 GOMODCACHE="/Users/myuser/go/pkg/mod"
    * -u: update, 會安裝或更新現有套件
    ```

* 安裝執行檔 (不是用來裝套件，使用情況較少)
    ```bash
    $ go install github.com/github/hub (安裝執行檔) (安裝到GOPATH/bin)
    $ go install PACKAGE_NAME (編譯完後安裝執行檔到GOPATH/bin)
    $ go install main.go (編譯完後安裝執行檔到GOPATH/bin)
    ```


* 移除package
    ```
    1. 手動rm
    2. 跑go mod tidy，以更新go.mod

    ref:
    https://stackoverflow.com/questions/53048977/how-to-remove-a-go-package-downloaded-with-go-get-command
    ```

* go env
    ```bash
    $ go env
    $ go env VARIABLE
    $ go env GOROOT
    $ go env GOPATH
    ```


## 匯入模組
匯入模組時，會匯入該目錄底下所有檔案

```go
import (
	"myproject/controller/user"

	"github.com/gofiber/fiber/v2"
)
```

## 宣告變數

```go
* 宣告
var student1 string = "John" (最完整)
var s = "initial"
a := 10 (常用)

* 多變數宣告
var a, b, c, d int = 1, 3, 5, 7

c, d := 7, "World!"

var (
    a int
    b int = 1
    c string = "hello"
)
```

## 型別列表
    bool / string / int / float

    * int (預設是int64) (常用)
        int8
        int16
        int32
        int64

    * uint (預設是uint64) (常用)
        uint8
        uint16
        uint32
        uint64

    * float32 / float64
        建議使用float64，除非有記憶體問題才用float32，float32精密度只有小數後6,7位精度

        float32 : 使用4bytes
        float64 : 使用8bytes

        var value float32 = 1.1

    * array
        a := [3]int{1, 2, 3}

    * slice
        slice := []int{1, 2, 3}
        slice := make([]int, 5)

    * map
        n := map[string]int{"foo": 1, "bar": 2}

        m := make(map[string]int)
        m["k1"] = 7
        m["k2"] = 13

## debug
```go
* 查看型別
fmt.Printf("var1 = %T\n", var1)

* 使用reflect查看型別
fmt.Println(reflect.TypeOf(a))

* dump 
fmt.Printf("%#v\n", variable)

* 使用go-spew (https://github.com/davecgh/go-spew)
spew.Dump(myVar1)

```

## type assertion

```go
* 此段程式碼可以將原本的 interface{} 斷定為字串。
  不過要注意，若該變數的型別不是字串，此段程式碼會直接panic

var i interface{} = "hello"
s := i.(string)
fmt.Println(s)

* golang 提供了一個機制，型別斷定會回傳兩個數字，一個是轉換後的數字，另外一個是這個斷定是否正確。
  有兩個地方需要注意：
  如果沒有第二個回傳值，除非你很確定型別，不然程式有可能因為斷定失敗而導致 panic
  加入第二個回傳值後，不會造成 panic

args := []interface{}{
    1,
    "1",
    "2",
}

for _, arg := args {
  if val, ok := arg.(int); ok {
    fmt.Println(val)    
  } else {
    fmt.Println("not int.")
  }
}

ref:
https://blog.kalan.dev/posts/golang-type-assertion
```

## array
```go
* 由於array的長度是固定的，無法改變，故較常用slice，而不是array

// 一維陣列
1.  
var a [5]int
a[4] = 100

2.
var a = [3]int{1, 2, 3}

3. (常用)
b := [5]int{1, 2, 3, 4, 5}


// 多維陣列
1.
array := [4][2]int{{10, 11}, {20, 21}, {30, 31}, {40, 41}}

2.
array := [4][2]int{1: {20, 21}, 3: {40, 41}}
```


## slice
```go
1.
var num = []int{1, 2, 3, 4, 5} (跟array的差別在於沒有指定長度)

2.
even_num := []int{2, 4, 8, 16, 32, 64}
strs := []string{"la", "gi"}

3.
s := make([]string, 3) // 長度為3的slice
s := make([]string, 0) // 空slice，後續可用append陸續加入元素
```


## map
```go
* 關聯陣列(hash or dict)

1.
m := make(map[string]int)
m["k1"] = 7
m["k2"] = 13

delete(m, "k2")

_, prs := m["k3"]

2.
n := map[string]int{"foo": 1, "bar": 2}


3.
var translations = map[string]map[string]string{
    "en": {
        "required": "{0} is a required field.",
        "email":    "{0} is invalid.",
        "gte":      "{0} must be {1} or greater.",
        "lte":      "{0} must be {1} or smaller.",
    },
    "vi": {
        "required": "{0} là trường bắt buộc.",
        "email":    "{0} không hợp lệ.",
        "gte":      "{0} phải bằng hoặc lớn hơn {1}.",
        "lte":      "{0} phải bằng hoặc nhỏ hơn {1}.",
    },
}
```

## const
```golang
可出現在var可出現的地方
全域 or function內都行

const s string = "constant"
```


## if
```go
* 可在條件式中宣告變數，變數只能在if/else區塊中使用

if num := 9; num < 0 {
    fmt.Println(num, "is negative")
} else if num < 10 {
    fmt.Println(num, "has 1 digit")
} else {
    fmt.Println(num, "has multiple digits")
}
```

## for loop
```go
* 一般迴圈

    for i := 1; i <= 10; i++ {
        fmt.Println(i)
    }

* while loop

    n := 1
    for n < 5 {
        n *= 2
    }

* infinite loop

    for {
        fmt.Println("hello")
    }

* range

    nums := []int{2, 3, 4}
    for i, num := range nums {
    }

    kvs := map[string]string{"a": "apple", "b": "banana"}
    for k, v := range kvs {
        fmt.Printf("%s -> %s\n", k, v)
    }
```

## function
```go
func plus(a int, b int) int {
}

// Multiple Return Values
func vals() (int, int) {
    return 3, 7
}
```

## closure
```go
func intSeq() func() int {
    i := 0
    return func() int {
        i++
        return i
    }
}
```

## pointer
```go
func zeroptr(iptr *int) {
    *iptr = 0
}

i := 1
zeroptr(&i)
```


## receiver
```go
type Employee struct {
    Id int
    Name string
    Age int
}

// (e Employee)是receiver
func (e Employee) String() string {
    return "Employee{" +
                "Id=" + strconv.Itoa(e.Id) +
                ", Name=" + e.Name +
                ", Age=" + strconv.Itoa(e.Age) +
                "}"
}
```

## struct

ref:
https://pjchender.dev/golang/structs/

## 模擬class
```go
type Rect struct {
    length int
    width int
}

func newRect(l int, w int) *Rect {
    r := Rect{length: l, width: w}

    return &r
}

func (r *rect) area() int { // receiver type of *rect
    return r.width * r.height
}

func (r rect) perim() int { // receiver type of rect, pointer/value receiver都可以
    return 2*r.width + 2*r.height
}

alex := Rect{length: 11, width:  10}
alex.area()

r := newRect(1, 2)

fmt.Println(Rect{10, 20})
fmt.Println(Rect{"length": 10, "width": 20})
```

## interface
```go
type geometry interface {
    area() float64
    perim() float64
}

func measure(g geometry) {
    fmt.Println(g)
    fmt.Println(g.area())
    fmt.Println(g.perim())
}

ref:
https://gobyexample.com/interfaces
```

## regular expression
```go
* 抓出特定的子字串

rgx := regexp.MustCompile(`json: cannot unmarshal string into Go struct field .([a-zA-z]+)`)
matches := rgx.FindStringSubmatch(value.Error())
field := matches[1]

ref:
https://stackoverflow.com/questions/39908955/golang-regex-get-value-inside-parentheses
```


## struct embeded

ref: 
https://gobyexample.com/struct-embedding

## generics

ref:
https://gobyexample.com/generics

## make / new
```go
總結底下 make 跟 new 的區別

make 通常只用於在宣告三個地方，分別是 slice, map 及 channel
make 能夠分配並且初始化所需要的記憶體空間跟結構，而 new 只能回傳指標位置
make 可以初始化上述三種格式的長度跟容量以便提供效率跟減少開銷

func main() {
  var p *map[string]string

  // new
  p = new(map[string]string)
  *p = map[string]string{
    "bar": "foo",
  }
  people := *p
  people["foo"] = "bar"

  fmt.Println(people)
  fmt.Println(p)

  // make
  foobar := make(map[string]string)
  foobar["foo"] = "bar"
  foobar["bar"] = "foo"
  fmt.Println(foobar)
}

ref:
https://blog.wu-boy.com/2021/06/what-is-different-between-new-and-make-in-golang/
```

## defer
A defer statement pushes a function call onto a list. 
The list of saved calls is executed **after** the surrounding function returns. 
Defer is commonly used to simplify functions that perform various clean-up actions.


ref:
https://go.dev/blog/defer-panic-and-recover


## panic
Panic is a built-in function that stops the ordinary flow of control and begins panicking. When the function F calls panic, 
1. execution of F stops, 
2. any deferred functions in F are executed normally, 
3. and then F returns to its caller. 
4. To the caller, F then behaves like a call to panic. 
5. The process continues up the stack until all functions in the current goroutine have returned, at which point the program crashes. Panics can be initiated by invoking panic directly. They can also be caused by runtime errors, such as out-of-bounds array accesses.

ref:
https://go.dev/blog/defer-panic-and-recover


## goroutine
```go
func printName(name string) {
    for i := 1; i <= 10; i++ {
        fmt.Println(name)
    }
}

func main() {
    go printName("lala")
    go printName("gigi")

	time.Sleep(time.Second * 5)
	fmt.Println("done")
}

一般來說如果有goroutine 
最後需使用time.Sleep()或WaitGroup 
以避免程式結束但goroutine還沒跑完
```

## sync package
```go
* WaitGroup
一般來說如果有goroutine 
最後需使用time.Sleep()或WaitGroup 
以避免程式結束但goroutine還沒跑完

func main() {
    wg := sync.WaitGroup{}
    wg.Add(100)
    for i := 0; i < 100; i++ {
        go func(i int) {
            fmt.Println(i)
            wg.Done()
        }(i)
    }
    wg.Wait()
}

有三個方法
Add(), Done(), Wait()

WaitGroup 對象內部有一個計數器，最初從0開始
有三個方法：Add(), Done(), Wait() 用來控制計數器的數量
Add(n) 把計數器設置為n
Done() 每次把計數器-1
Wait() 會阻塞代碼的運行，直到計數器地值減為0

ref:
https://easonwang.gitbook.io/golang/he-xin-mo-zu/sync
```


## Mutex

ref:
* https://ithelp.ithome.com.tw/articles/10226885
* https://go.dev/tour/concurrency/9


## RWMutex

ref:
https://clouding.city/go/mutex-rwmutex/


# channel
```go
1.
var queue chan string

2.
func main() {
    message := make(chan string)
    go func() {
        message <- "ping"
    }()

    msg := <- message
    fmt.Println(msg)
}

ref:
https://gobyexample.com/channels
```

## channel buffering
```go
* 預設是沒buffer的，若receiver沒取資料，是不能塞資料到channel的

ref: 
https://gobyexample.com/channel-buffering
```


## channel blocking
```go
若channel資料沒取出來，會block住，直到取出來，但goroutine receiver除外


ref:
https://gobyexample.com/channel-synchronization
```

## 參考資料
* [golang by examples](https://gobyexample.com)
* [Go 语言原本(程式碼剖析)](https://golang.design/under-the-hood)
* [The Go programming language線上版](https://books.studygolang.com/gopl-zh)
* [gopl練習題解答](https://github.com/linehk/gopl)
* [Go 的並發：Goroutine 與 Channel 介紹](https://ithelp.ithome.com.tw/articles/10212068)
* [slice詳解](https://codeburst.io/a-comprehensive-guide-to-slices-in-golang-bacebfe46669)
* [Go Slices: usage and internals](https://go.dev/blog/slices-intro)
* [有效地去 - Effective go 正體中文翻譯](https://ronmi.github.io/post/go/effectivego)
* [Common Mistakes](https://github.com/golang/go/wiki/CommonMistakes)
* [Defer, Panic, and Recover](https://go.dev/blog/defer-panic-and-recover)
* [pjchender - Defer, Panic 和 Recovery](https://pjchender.dev/golang/defer-panic-recovery)
* [shichao - go programming language book筆記](https://notes.shichao.io/gopl/)
* [Golang 學習筆記](https://medium.com/@john.lin/golang-%E5%AD%B8%E7%BF%92%E7%AD%86%E8%A8%98-582cad359738)