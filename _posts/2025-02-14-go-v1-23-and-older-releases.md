## Go v1.23 and older releases

I know Go v1.23 is already here with us for several months. However, I decided to go in the past and check
what new feature was added to Go and its ecosystem since I started as a Go developer.

To make it easier, I will mention just those features I am using and those I would like to start using.


---

### Go v1.23

Released on 2024-08-13.


#### Already used features of Go v1.23

Introduction of iterators was a big deal for me. However, before using them, I read the 
[official documentation ot `iter` package](https://pkg.go.dev/iter@go1.23.0) and two articles
before I was able to understand it correctly. I have already used it and I really like it.
It is true that consuming already made iterator is more fun than implementing the brand-new one.
I was curious about adding `Preorder` iterator into `go/ast` package.
Here is an example of iterator I have implemented (map-based y/x position sorting):

```go
func (g guideMap) allSorted() iter.Seq[any] {
	return func(yield func(any) bool) {
		ys := slices.Collect(maps.Keys(g))
		sort.Ints(ys)

		for _, y := range ys {
			row := g[y]
			xs := slices.Collect(maps.Keys(row))
			sort.Ints(xs)

			for _, x := range xs {
				if !yield(row[x]) {
					return
				}
			}
		}
	}
}
```

Touches on `time.Timer` and `time.Ticker` affected my work life. A possibility to garbage-collect these unstopped
objects before they fire bring some good. But usage of unbuffered channel internally brought guarantee that for 
any call to a `Reset` or `Stop` method, no stale values prepared before that call will be sent or received after 
the call. TL;DR: no more optional receiving of ticks after stopping a ticker/timer.


#### Not yet used interesting features of Go v1.23

I did not opt in for Go telemetry (using `go telemetry` command). I felt like possibly exposing something
secret. Better safe than sorry situation for me.

I did not even check generic aliases as it was still an experimental feature.

I did not find any reason to try "canonicalizing" values via [`unique`](https://tip.golang.org/pkg/unique)
package. I know, Lua uses "interning" of strings, so there is always just one instance of each string
in the memory. So using `Make[T]` generic function and working with `Handle[T]` type (two values of that 
generic type  are considered equal iff the values used for making them are equal) would reduce memory
footprint and comparison might be as efficient as simple pointer comparison.

The new `structs` package provided types for struct fields that modify properties of the containing struct type
such as memory layout. Not used yet.

The new `Encode` and `Decode` functions of `encoding/binary` package became byte slice equivalents to `Read` 
and `Write`. Also `Append` allowed marshaling multiple data into the same byte slice.

Etc.

---

### Go v1.22

Released on 2024-02-06.


#### Already used features of Go v1.22

The most importantly, `for` loop creates a new variable for each iteration, so accidental sharing bugs are avoided
(aka trying to save pointers into slice for all iterated values will not produce a slice of pointers all pointing
to the only one variable with value from the very last iteration). Also `go vet` was aligned to this.

I almost immediately, started using `for` loops ranging over integers (e.g. `for range b.N { ... }`) not just
in my new benchmarks.

`go get` could not be used outside a module anymore.

Printing of coverage summaries for packages without own test files on `go test -cover` is something one might
notice (e.g. `mymod/mypack coverage: 0.0% of statements`).


#### Not yet used interesting features of Go v1.22

Enhanced routing patterns used by [`net/http.ServeMux`](https://tip.golang.org/pkg/net/http#ServeMux):
- registering `"POST /items/create"` to restrict invocation method
  (mind that `"GET"` method also registers for `"HEAD"` method)
- Wildcards in patterns, like `/items/{id}`, match segments of the URL path.
  The actual segment value may be accessed by calling the `Request.PathValue` method.
- A wildcard ending in `...`, like `/files/{path...}`, must occur at the end of a pattern and matches all the 
  remaining segments.
- A pattern that ends in `/` matches all paths that have it as a prefix, as always.
- To match the exact pattern including the trailing slash, end it with `{$}`, as in `/exact/match/{$}`.
- If two patterns overlap in the requests that they match, then the more specific pattern takes precedence. 
  If neither is more specific, the patterns conflict. This rule generalizes the original precedence rules
  and maintains the property that the order in which patterns are registered does not matter.

Vendor directory for workspaces was not used as I am not usually using Go workspaces.

I did not notice that `vet` complained on `slice = append(slice)`, nor `defer log.Println(time.Since(t))`
(equivalent of `tmp := time.Since(t); defer log.Println(tmp)`), nor warnings for mismatched key-value pairs 
in `log/slog` calls.

I did not use `math/rand/v2` even though it brought many changes. Perhaps, one might change this...

Also, I did not face a brand-new package of `go/version`.

The `Null[T]` generic type of `database/sql` provided a way to scan nullable columns for any column types.

Etc.


---

### Go v1.21

Released on 2023-08-08.


#### Already used features of Go v1.21


#### Not yet used interesting features of Go v1.21


---

### Go v1.20

Released on 2023-02-01.


#### Already used features of Go v1.20


#### Not yet used interesting features of Go v1.20


---

### Go v1.19

Released on 2022-08-02.


#### Already used features of Go v1.19


#### Not yet used interesting features of Go v1.19


