# Go

## Type System

### In go, you can even add methods to preliminary types.

```go
package example

type Integer int

func (a *Integer)AddOne() {
	*a = *a+1
}
```

### Inheritance is done by adding a type as a field.

```go
package example

type Base struct {
	Name string
}

func (base *Base) Get() string {
	return base.Name
}

type Sub struct {
	*Base
}

func NewSub() *Sub {
	sub := new(Sub)
	sub.Base = new(Base)
	sub.Name = "Hello"
	return sub
}
```

### Check if a variable is of some interface (or class).

```go
package example

import (
	"fmt"
)

type Reader interface {
	Read() string
}

type ReaderWriter interface {
	Read() string
	Write(s string)
}

type MyReader struct {
	content string
}

func (reader *MyReader) Read() string {
	return reader.content
}

func (reader *MyReader) Write(s string) {
	reader.content = s
}

func main() {
	sub := NewSub()
	fmt.Println(sub.Get())
}
```

## Concurrency

### This is how go conducts concurrency.

```go
package example

import (
	"fmt"
)

func Task(id int) {
	for i := 0; i < 10; i++ {
		fmt.Printf("%d: %d\n",id,i)
	}
}

func main() {
	go Task(1)
	go Task(2)
}
```

## RPC

### Server side.

```go
package main

import (
	"log"
	"net"
	"net/rpc"
	"net/http"
	"encoding/base64"
)

type Base64 int

func (b *Base64) Encode(args []byte, reply *string) error {
	*reply = base64.StdEncoding.EncodeToString(args)
	return nil
}

func (b *Base64) Decode(args string, reply *[]byte) error {
	var err error
	*reply,err = base64.StdEncoding.DecodeString(args)
	return err
}

func main() {
	b := new(Base64)
	rpc.Register(b)
	rpc.HandleHTTP()
	l, e := net.Listen("tcp",":1234")
	if e != nil {
		log.Fatal("listen error",e)
	}
	http.Serve(l,nil)
}
```

### Client side.

```go
package main

import (
	"os"
	"fmt"
	"log"
	"net/rpc"
)

func main() {
	if os.Args[1] == "e" {
		args := []byte(os.Args[2])
		client,err := rpc.DialHTTP("tcp","localhost:1234")
		if err != nil {
			log.Fatal("dial error",err)
		}
		var reply string
		err = client.Call("Base64.Encode",args,&reply)
		if err != nil {
			log.Fatal("encode error",err)
		}
		fmt.Printf("base64: %s\n",reply)
	} else {
		args := os.Args[2]
		client,err := rpc.DialHTTP("tcp","localhost:1234")
		if err != nil {
			log.Fatal("dial error",err)
		}
		var reply []byte
		err = client.Call("Base64.Decode",args,&reply)
		if err != nil {
			log.Fatal("encode error",err)
		}
		fmt.Printf("decode: %q\n",reply)
	}
}
```

