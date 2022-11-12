correct clear concise fast
code allocates reads from and writes to memory
- accurate consistent and efficient reads and writes
type system ensures micro level integrity
if a piece of software goes rogue and starts corrupting things you gotta shut it down
at the macro level every problem is a data transformation problem
golang as a data-oriented design lang
- if you don't know the data you don't know the problem and if you don't know the problem you can't solve it with code
- writing less code and error handling is critical to macro integrity
- every function is a data transformation

- Making things easy to do is a false economy. Focus on making things easy to understand and the rest will follow. - Peter Bourgon
- Simplicity is hiding complexity without losing readability. 

1) Get something to work
2) Code readability reviews 
3) Cleanup comprehension of code
4) Cleanup mental models of codebase
5) Ask how can we hide complexity without losing readability
6) Create levels of decoupling

Priorities:
1) Integrity (even at expense of perf)
2) Readability (comprehensible by outsider; not hiding cost)
3) Refactoring toward simplicity to hide complexity

Variables
1) Built-in 
	1) numeric
		1) float64
		2) int
			1) int32 (precision-based)
	2) string
	3) bool
2) Reference
3) Struct

- precision-based integer is useful for multithreaded code in go
- every line of code is allocating reading or writing integer-based memory and we want to be sympathetic with the hardware 
	- the compiler will choose the best int for the hardware
	- on a 64-bit arch addresses will be 64 bits aka 8-bytes
- variable declaration will either be initialized with a value or the compiler will set it to a zero-value construction

#### BEST PRACTICE
- use var to init zero-value construction
```go
func main() {
	// Declare variables that are set to their zero value
	var a int
	var b string
	var c float64
	var d bool

}
```

- an empty string (what Bill is caling a word) can have either a 4 or 8 byte allocation depending on the arch and it points to an address in the RAM address space;
	- an empty string will have a nil pointer (no address space reference) and 0 bytes allocated


#### BEST PRACTICE
- Declare variables and initialize using short variable declaration operator
	- short variable declaration operators are for assigning a value and address space
```go
aa := 10
bb := "hello"
cc := 3.14159
dd := true
```
- bb variable set to 'hello' has 5 bytes - one for each letter - and its address space points to the beginning of the 5 bytes
```
| h | e | l | l | o | 
address reference points to: 'h'
addressable memory allocation: 5 bytes
```

#### BEST PRACTICE
- Specify a type and perform a conversion
- conversion over casting
	- make a copy of the value and allocate it to another variable
```go
aaa := int32(10)
```

- type is life
	- type represents: 
		- size (amount in memory we will be reading and writing)
		- representation (string int etc)
			- compiler protection (by knowing our type we can ensure every read and write has integrity)
- accurate, consistent, efficient
- 