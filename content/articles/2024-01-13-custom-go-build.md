```json
{
  "author": "Andreas Linz",
  "title": "How to build against a modified version of Go",
  "description": "How to build your program against a modified build of the Go standard library.",
  "created_at": "2024-01-13"
}
```
# How to build against a modified version of Go

This week I read [an article](https://jazco.dev/2024/01/10/golang-and-epoll/) about the vertical scaling limits of a Go application under heavy I/O load, that linked to [a Go issue](https://github.com/golang/go/issues/65064) in which the `syscall.EpollWait` bottleneck is investigated. One particular [comment](https://github.com/golang/go/issues/65064#issuecomment-1887777889) in this issue thread sparked my interest, it says:

> Note that is possible to simply edit the runtime source in GOROOT and rebuild your program (no special steps required for the runtime, it is treated like any other package).

What this means is, that any import of standard library packages is handled as if they're user packages, just that the standard library packages are resolved with `$GOROOT/src` as the base directory. Also, it's a little bit more complicated than the comment makes it look like, but we'll come to that.
With this knowledge, let's try what is needed to run a Go program with a custom Go build.

The following steps assume that a Go distribution is already available on your system.
Let's create a simple hello-world Go app:

```sh
$ mkdir app && cd app
$ go mod init example.com/hello-app
```

Where `hello.go` contains the following lines:

```go
package main

import "fmt"

func main() {
	fmt.Println("World!")
}
```

Running `go run hello.go` will, obviously, print `World!` to the console.

My contrived example will modify `fmt.Println` in a way that it always puts a `Hello,` before everything it prints. I know, a horrible idea, but nobody says you should ship something like this in production 😛.

We need to clone a source copy of Go into some local folder, in this case I use `_go`, because the [`go` tool](https://pkg.go.dev/cmd/go) ignores folders that are prefixed with either `_` or `.`.

```sh
$ git clone --depth 1 --branch go1.21.6 git@github.com:golang/go _go
```

Now we need to build the `go` tool from the cloned source:

```sh
pushd _go/src/go && ./make.bash; popd
```

If you forget to do this you'll see an error like

```sh
go: no such tool "compile"
```

when trying to compile against your modified Go version.
A slight environment modifications is necessary so that the custom `go` build is picked up:

```sh
export GOROOT=$PWD/_go
export PATH=$GOROOT/bin:$PATH
```

With this done let's modify `$GOROOT/src/fmt/print.go` as shown in this diff:

```diff
diff --git a/src/fmt/print.go b/src/fmt/print.go
index 9c3bd3e..3a83a99 100644
--- a/src/fmt/print.go
+++ b/src/fmt/print.go
@@ -301,6 +301,7 @@ func Append(b []byte, a ...any) []byte {
 // It returns the number of bytes written and any write error encountered.
 func Fprintln(w io.Writer, a ...any) (n int, err error) {
 	p := newPrinter()
+	p.doPrint([]any{"Hello,"})
 	p.doPrintln(a)
 	n, err = w.Write(p.buf)
 	p.free()
```

Another run of `go run hello.go` will now print `Hello,World!`.
Success 🎉!