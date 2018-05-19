# go

```
# Commands
go doc builtin.append     # builtin's are in `builtin` package
godoc -http :6060
go list all   # list installed
~/homebrew/Cellar/go/1.8.3/libexec/src/  # source

for DIR in GOPATH:
- example package source in DIR/src/foo/bar 
- import as "foo/bar"
- compiled to DIR/pkg/GOOS_GOARCH/foo/bar.a
- executable named after folder containing source in main package
- new packages downloaded to first dir in GOPATH

x := 1            # declare and initialise with inferred type
var x = 1         # equivalent

package main      # entrypoint is in main package
func blah() { }   # function


for i := 0; i < 10; i++ { sum += i } # full `for` syntax includes init and post
for ; sum < 1000; { sum += sum }     # init and post are optional
for sum < 1000 { sum += sum }        # same as above (equiv of C `while`)
for { }                              # loop forever

if x < 0 { return 2 }          # parens around condition are optional
if x := -1; x <0 { return x }  # if can include init statement before condition
                               #   scope is local to if/else statement
switch v := 5; a { ... }  # switch can have init statement
switch { ... }            # or no value at all, used like an if/else-if statement
switch true { ... }       # same as above

defer ...         # defer a statement until surround block ends
                  # multiple defers are stacked and executed in reverse order

nil               # zero value for pointer & map

primes := [3]int{2, 3, 5}   # array, fixed size
var s []int = primes[1:4]   # slice, dynamic size
                            # low:high, selects first but excludes last
                            # changing elements of slice changes underlying array
                            # other slices referring to same array will see changes
                            # omit either low or high to select defaults
  
### slice len vs capacity
# internally slice is:
# - pointer - to element in array where slice starts
# - length - how many elements in the slice
# - cap - (capacity) distance to end of underlying array
# append() function allocates a *new* array if underlying capacity not big enough

# Range
var pow = []int{1, 2, 4}
for i, v := range pow { ... }  # iterates with index and value
for i := range pow { ... }     # only want index
for _, v := range pow { ... }  # only want value

new
make(T, args)       # 
```
