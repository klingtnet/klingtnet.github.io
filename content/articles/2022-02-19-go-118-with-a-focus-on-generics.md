```json
{
  "author": "Andreas Linz",
  "title": "Go 1.18 with a focus on generics",
  "description": "Go 1.18 is the biggest release since Go 1.0 came out.  In this article I will summarize most of the updates and additions and I will also give a more in-depth look into generics.  This article will also be the basis of the talk I will give at the #25 Golang Leipzig meetup.",
  "created_at": "2022-02-19"
}
```

# Go 1.18 with a focus on Generics

Go 1.18[^1] is the biggest release since the Go version 1.0 came out in 2012.  What made this release so important are two major features that got introduced to Go, those are:

- Generics
- and Fuzz testing support.

Before we go into more detail about the last two features, let's look at the general innovations first.

Regardless of Go 1.18 being a larger than normal release, it will be no exception of the rule, meaning the [compatibility promise] still holds and there will be no breaking changes.  This is what makes Go a viable choice for production code.

## `debug/buildinfo`

Binaries compiled with the new version will automatically include version control information.  This data can be accessed at runtime through ` runtime/debug.ReadBuildInfo` or by running `go version -m path/to/binary`.  A common workaround that was used before was to set version information on compile time using a `go build` flag like ` -ldflags "-X main.Version=$VERSION"` which is now obsolete.

```shell
$ go1.18rc1 version -m hello
hello: go1.18rc1
        path    hello:
        mod     hello   (devel)
        build   -compiler=gc
        build   CGO_ENABLED=1
        build   CGO_CFLAGS=
        build   CGO_CPPFLAGS=
        build   CGO_CXXFLAGS=
        build   CGO_LDFLAGS=
        build   GOARCH=arm64
        build   GOOS=darwin
        build   vcs=git
        build   vcs.revision=9b40c4e23a9f695c5f2adb7886d7d45f3e395eb7
        build   vcs.time=2022-02-19T11:59:45Z
        build   vcs.modified=true
```

## `net/netip`

[Tailscales' custom IP address struct][tailscale ip] landed in the standard library.  In contrast to [`net.IPAddr`][net.ipaddr] this type is immutable, takes less memory is comparable making it usable as a map key.  Brad Fitzpatrick did a talk about [Go at Tailscale] in FOSDEM 2021 where he goes into the details why they needed a new IP type.

## Miscellaneous

With `strings.Cut()` you can cut a string around the first instance of a separator, e.g. `strings.Cut("a,b,c", ",")` will result in `"a", "b,c", true`, where the last argument indicates if the separator was found or not.  The `strings.Title()` function for making a string title-case is now deprecated because it doesn't handle unicode properly, use [`x/test/cases.Title`][cases.title] instead.

In range pipelines of `html/template` or `text/template` templates iteration can now be ended with `{{break}}` or the next iterations can be started with `{{continue}}`.

Mutexes from `sync` now have non blocking `TryLock/TryRLock` methods that try to acquire the lock if it's not currently held.

> Note that while correct uses of TryLock do exist, they are rare, and use of TryLock is often a sign of a deeper problem in a particular use of mutexes. 

A small [MaxBytesHandler](https://pkg.go.dev/net/http@master#MaxBytesHandler) middleware was added to the `http` package.  This middleware limits the amount of data read from a request body or written in a response.  Most Go HTTP routers and web frameworks already provide such a middleware, often to prevent denial-of-service attacks.

The `go vet` linting tool was updated to work with generic functions and types.  Did you know that `go vet` is run automatically when you `go test` your application?

Our beloved formatting tool `gofmt` now reads and formats source code files concurrently making it significantly faster.  To be honest, formatting speed was never an issue for any Go code base I worked with.

TLS 1.0 and TLS 1.1 are disabled for by default now for _client_ connections, the server side still allows TLS 1.0.

## Compiler

Go now supports [four microarchitecture levels] for the `amd64` target.  A specific level can be selected using the `GOAMD64=<level>` environment variable.  To compile a binary for a recent x86-64 CPU that supports AVX instructions run `GOAMD64=v3 GOARCH=amd64 go build my/application`.  A higher/newer microarchitecture version does not necessarily result in better performance, which means you need to benchmark.  The actual performance benefit should be negligible for most web servers.

<!-- TODO: Use github.com/golang/benchmarks or a small tool that applies image transformations to benchmark architecture levels  -->

In the [previous release] the calling convention for functions on x86-64 targets switched from stack to register based, resulting in a minor speedup and slightly smaller binaries.  Support for the register based calling convention is now expanded to arm64[^2] and amd64 on all operating systems.

## Workspace Mode

The [module workspace proposal] was accepted and implemented.  Workspace mode is activated if the `go` tool finds a `go.work` file in the current directory or any of its parents.  This file could look like this:

```
go 1.18

use ../foo/bar
use ./baz

replace example.com/foo v1.2.3 => example.com/bar v1.4.5
```

Note that, as for any other `go.*` file, there is a subcommand that must be used to create or edit its contents.  In this case the command is `go work`.  In workspace mode you can easily use development versions of a library for many go modules, without having to write a `replace` directives in each of every `go.mod` files.  In the example above, that was copied from `go help work`, `./baz` could be a fork of `github.com/strechr/testify` or any other dependency you need to modify locally.

## Fuzzing

Regular unit tests use a fixed set of inputs to test a function, this often includes simple success cases and inputs that represent corner cases, e.g. the empty string, `nil` so on and so forth.

Fuzz testing, or fuzzing, on the other hand generates random inputs to test functions with the goal to trigger bugs.  You can imagine that the search space, from which random inputs are taken, gets  huge very quickly.  Hence, fuzz testing implementations often use techniques to reduce the search space, e.g. some form of heuristics like starting with extreme values of an input type, those could be `NaN`, `-/+inf` for a float type or invalid unicode strings.

Anyways, we don't have to care about the implementation details since the Go team did all the hard work of implementing a fuzzing framework for us and we can just use it.  A starting point to learn about fuzz testing in Go is the [fuzzing landing page] that also links to the official tutorial.

```go
// copied from https://pkg.go.dev/testing@master#hdr-Fuzzing
func FuzzHex(f *testing.F) {
  for _, seed := range [][]byte{{}, {0}, {9}, {0xa}, {0xf}, {1, 2, 3, 4}} {
    f.Add(seed)
  }
  f.Fuzz(func(t *testing.T, in []byte) {
    enc := hex.EncodeToString(in)
    out, err := hex.DecodeString(enc)
    if err != nil {
      t.Fatalf("%v: decode: %v", in, err)
    }
    if !bytes.Equal(in, out) {
      t.Fatalf("%v: not equal after round trip: %v", in, out)
    }
  })
}
```

Fuzz tests look very similar to regular unit tests but their name must start with `Fuzz`.  Inputs to the _seed corpus_, a set of default test inputs, are registered using `f.Add()`.  A fuzz test must have only one fuzz target, that is the function passed to `f.Fuzz()` which takes an instance of `*testing.T` as their first argument. All remaining arguments must be in same order and type as those added to the seed corpus with `f.Add()`.  To enable fuzzing pass the `-fuzz=FuzzMyTest` flag to `go test`.  If the flag was not specified fuzz tests are still run, but only with their fixed seed corpus as input.  Only a limited set of types is allowed as fuzz inputs, primitives, `string` and `[]byte` among others.

Of a fuzz tested function only properties can be tests since there is no control over the inputs.  Properties could be that the functions result is still a valid unicode string or that a number is in a certain range.

Be aware that fuzzing can create several gigabytes of data in the fuzz cache and might cause high load on the test machine.  I would therefore advise against enabling fuzz tests in a CI pipeline.

Should fuzzing detect a bug in your code, consider adding it to the [trophy case](https://github.com/golang/go/wiki/Fuzzing-trophy-case).

## Generics

Generics might be the most controversial feature ever added to Go language.  Not having Generics was one of Go's "features" since they make a language certainly more complex.  It took multiple language change proposal until we landed with the [type parameters proposal] that was eventually implemented with Go 1.18.  There was quite some buzz around that proposal and [everyone and their mother wrote blog posts](https://duckduckgo.com/?q=go+generics+blog&t=h_&ia=web) about using generics with Go, [and so did I][previous blog post].

Let's start with a definition:

> Generics allow you to write one, _generic_, function for a set of types without relying on reflection.  The compiler will then implement one version of your generic function for every concrete type it was instantiated with.

This might sound intimidating to some, so here's an example:

```go
func min[T int | uint | int64](x, y T) T {
	if x <= y {
		return x
	}
	return y
}
```

What's different from a normal Go function is that there is a _type parameter_ list in square brackets (`[T int | uint | int64]`) before the function arguments.  Here we can define the set of types the function is allowed to be instantiated with.  Thanks to Go's type inference we don't have to explicitly write out the type when calling the function, instead we can just do `min(1, -5)` and the compiler will be smart enough to infer the type for us.

One last minute change to the release was that the [constraints package] was moved out of the standard library.  This package predefined a set of common interfaces, e.g. one for signed or unsigned integer numbers.  This brings us to the next point, that is we don't need to define the set of allowed types inline, instead we can define them in an interface like:

```go
type Signed interface {
	~int | ~int8 | ~int16 | ~int32 | ~int64
}
```

and then define our `min` function as

```go
func min[T Signed](x, y T) T {
	if x <= y {
		return x
	}
	return y
}
```

A new operator is shown in the example above, the tilde operator `~T` specifies the underlying type of `T`.  Sounds confusing but is actually pretty simple, in Go one often defines type alias, e.g. `type MyEnum string` and if a function only accepts `[T string]` then it could not be instantiated with an argument of type `MyEnum` even though the argument is a string.  But `[T ~string]` on the other hand will happily accept any type whose underlying type is string, making the generic function much more useful.

For some functions there is no need to restrict the set of type at all and in such cases you can use `[T interface{}]`, or even better, use `any` instead of `interface{}`.  An example of such a function with great "general" use is the following one that returns a pointer to any type:

```go
func PointerOf[T any](t T) *T {
	return &t
}
```

Parameter list are by no means limited to a single type.  I hope the following function name leaves no doubt that this is a contrived example without practical use:

```go
func weirdAddYankovic[T1 int | uint, T2 float32 | float64](x T1, y T2) string {
	sum := float64(x) + float64(y)
	return strconv.FormatFloat(sum, 'f', -1, 32)
}
```

Besides functions, type parameters can also be applied to structs and even interface's can take them:

```go
type Container[T any] struct {
    elements []T
}

// c := Container[int]{elements: []int{2,3,4}}
```

For some reason Go was not able to infer `T` in the example above and so it must be instantiated explicitly.

Be aware that using generics comes with downsides, one of them is about 15% slower compile times at the moment, increased binary size since the compiler will generate a variant of the generic function or struct for any type it was instantiated with.  Most importantly, in my opinion, it reduces the readability of your code, so think twice before adding type parameters.

Do not use type parameters when a regular interface would suffice:

```go
// Wrong: func Write[T io.Writer](w T) error {}
func Write(w io.Writer) error {}
```

Also, do **not** use generics if the implementation of a common methods is different for each type.

Generics are no panacea and there are limitations with the current implementation of this feature.  Most notably type parameters cannot be used in methods.  This limitation might be lifted with Go 1.19 and there is also a workaround the [facilitator pattern].

No type parameters will be used in the standard library with Go 1.18.  I think it was a sensible decision of the Go team to first gather some experience with how the feature is used and then in a later release introduce generics to the standard library.  However, there are already some generic utility functions for [slices](https://pkg.go.dev/golang.org/x/exp/slices) and [maps](https://pkg.go.dev/golang.org/x/exp/maps).  What I anticipate is a library that provides Rust iterator like functionality for slices, maps and sets.

I hope I could give you some viable insights into the features and changes that Go 1.18 will bring.  Now _go_ and write some, potentially generic, code!

[compatibility promise]: https://go.dev/doc/go1compat
[tailscale ip]: https://tailscale.com/blog/netaddr-new-ip-type-for-go/
[Go at Tailscale]: https://archive.fosdem.org/2021/schedule/event/gotailscale/
[net.ipaddr]: https://pkg.go.dev/net#IPAddr
[strings.cut]: https://pkg.go.dev/strings@master#Cut
[cases.title]: https://pkg.go.dev/golang.org/x/text/cases#Title
[four microarchitecture levels]: https://github.com/golang/go/wiki/MinimumRequirements#amd64
[previous release]: https://tip.golang.org/doc/go1.17#compiler
[module workspace proposal]: https://go.googlesource.com/proposal/+/master/design/45713-workspace.md
[fuzzing design draft]: https://go.googlesource.com/proposal/+/master/design/draft-fuzzing.md
[fuzzing landing page]: https://go.dev/doc/fuzz/
[type parameters proposal]: https://go.googlesource.com/proposal/+/refs/heads/master/design/43651-type-parameters.md
[facilitator pattern]: https://rakyll.org/generics-facilititators/

[previous blog post]: go-generics-a-quick-overview.html
[constraints package]: https://github.com/golang/exp/tree/6cf2b201936e0673ace5b31d2dffa00dafb3ee58/constraints

[^1]: For this article [Go 1.18 release candidate 1](https://go.dev/dl/#go1.18rc10) was used.  This release can be installed by running `go install golang.org/dl/go1.18rc1@latest && go1.18rc1 download`.  Any other details were mostly found in the [preliminary release notes](https://tip.golang.org/doc/go1.18#library).
[^2]: Also for PowerPC, but I don't know anyone targeting those CPUs.