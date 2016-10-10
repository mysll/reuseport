# GO_REUSEPORT

[![Go Report Card](https://goreportcard.com/badge/github.com/funny/reuseport)](https://goreportcard.com/report/github.com/funny/reuseport)
[![Build Status](https://travis-ci.org/funny/reuseport.png?branch=master)](https://travis-ci.org/funny/reuseport)
[![codecov](https://codecov.io/gh/funny/reuseport/branch/master/graph/badge.svg)](https://codecov.io/gh/funny/reuseport)
[![GoDoc](https://godoc.org/github.com/funny/reuseport?status.png)](https://godoc.org/github.com/funny/reuseport)

**GO_REUSEPORT** is a little expirement to create a `net.Listener` that supports [SO_REUSEPORT](http://lwn.net/Articles/542629/) socket option.

For now, Darwin and Linux (from 3.9) systems are supported. I'll be pleased if you'll test other systems and tell me the results.
 documentation on [godoc.org](http://godoc.org/github.com/funny/reuseport "go_reuseport documentation").

## Example ##

```go
package main

import (
  "fmt"
  "html"
  "net/http"
  "os"
  "runtime"
  "github.com/funny/reuseport"
)

func main() {
  runtime.GOMAXPROCS(runtime.NumCPU())

  listener, err := reuseport.NewReusablePortListener("tcp4", "localhost:8881")
  if err != nil {
    panic(err)
  }
  defer listener.Close()

  server := &http.Server{}
  http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
    fmt.Println(os.Getgid())
    fmt.Fprintf(w, "Hello, %q\n", html.EscapeString(r.URL.Path))
  })

  panic(server.Serve(listener))
}
```

Now you can run several instances of this tiny server without `Address already in use` errors.

## Thanks

Inspired by [Artur Siekielski](https://github.com/aartur) [post](http://freeprogrammersblog.vhex.net/post/linux-39-introdued-new-way-of-writing-socket-servers/2) about `SO_REUSEPORT`.

