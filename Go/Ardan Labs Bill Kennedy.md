
### Ultimate Go

- https://github.com/nathanjmorton/gotraining

There are 2 Rules for types and their memory assignments in terms of the mechanical sympathy: 

1. the type size has to fall in an alignment that divides it evenly (a 4 byte type cannot fall into a 5 byte space, it must fall into a 4Byte space padded by one Byte before it)

key: 1 Byte = 8 bits
```Go
type example struct {
	counter int16
	pi float32
	flag bool
	bignumba int64
}
```
___________
|  int16  |  float32 | bool | padding | int64 |   
0          2           6        7               8         16
- mechanical sympathy
	- 2 byte vals (eg int16) need to fall in a 2 byte alignment (can start at add 0 or 2)
			- if it starts at an odd number it will cross a machine word boundary and create 2 operations, which is inefficient
	- if it is a 1 byte val (eg bool), it can get padded with a space
	- an 8 byte val (eg int64) needs to start at 0 or 8

key: 1 Byte = 8 bits
int32 must fall on a 4Byte alignment
```Go
type example struct {
	flag bool
	[3]Byte padding
	counter int32
	pi float32
}
```

key: 1 Byte = 8 bits
int64 must fall on an 8Byte alignment
```Go
type example struct {
	flag bool
	[7]Byte padding
	counter int64
	pi float32
	[4]Byte padding
}
```

2. the entire struct has to fall on an alignment equivalent to the largest type (if there is an int64, which is 8Bytes, then a float32, which is 4Bytes, must be padded by 4 to make 8Bytes for each space)
- the derivative rule is that each value's type must consume the same amount of space in the memory as the struct's largest type
	- lesser types must be padded
- in order to maximize efficiency and minimize padding, sort your type declarations by size desc (largest first) so only the last space will require padding
```Go
type example struct {
	counter int64 // 8B
	pi float32 // 4B
	flag bool // 1B
	// [3]B padding
}
```

- Bill just said he doesn't want to see you doing this manually, let a linter do it ... not sure what's the best path then but it's good info anyway

```Go
func main(){
	// Declare a variable of type example
	// set its value to zero (var)
	var e1 example 
	// Display the value
	fmt.Printf("%+v\n", e1)
	// Declare a variable of type example
	// init using a struct literal
	e2 := example {
		flag: true,
		counter: 10,
		pi: 3.141592,
	}
	// Display the field values
	fmt.Println("Flag", e2.flag)
	fmt.Println("Counter", e2.counter)
	fmt.Println("Pi", e2.pi)
}
```

- now let's declare a struct inline
```Go
// Declare a variable of an anonymous type
// set to its zero value
var e1 struct {
	flag bool
	counter int16
	pi float32
}

// Display the value
fmt.Printf("%+v\n", e1)

// Declare a variable of an anonymous type
// and init using a struct literal
e2 := struct {
	flag bool
	counter int16
	pi float32
} {
	flag: true,
	counter: 10,
	pi: 3.14159,
}

// Display the values
fmt.Printf("%+v\n", e2)
fmt.Printf("Flag", e2.flag)
fmt.Println("Counter", e2.counter)
fmt.Println("Pi", e2.pi)
```

- let's show how we cannot assign a variable of one type to the value of another type without an explicit cast
```Go
type bill struct {
	flag bool
	counter int16
	pi float32
}
type nancy struct {
	flag bool
	counter int16
	pi float32
}
var b bill
var n nancy
b = n
fmt.Println(b,n) // can't use n (type nancy) as type bill in assignment 

```

- let's try again with a cast of type 
```Go
type bill struct {
	flag bool
	counter int16
	pi float32
}
type nancy struct {
	flag bool
	counter int16
	pi float32
}
var b bill
var n nancy
b = bill(n) // cast nancy into a bill type
fmt.Println(b,n) 

```

- functions are first class citizens

### Pointers 
- 1 goroutine per hardward thread (1 hardware thread allows 1 kernel thread)
	- Bill's machine is an i7 which has 4 cores, but each core can do 2 threads, so he can run 8 goroutines in parallel
- process attached to machine hardware whose os will will schedule a thread for it to run
- goroutine is an app thread which leverages machine to process instructions sequentially
	- machine then leverages hardware to process the executions
- process > goroutine > machine > os > thread execution
- Every goroutine gets its own contiguous block of memory called a stack (2k default)
	- os thread also gets its own stack, which differs depending on the os type
- main() entrypoint
- when a goroutine executes a function, we consider it crossing a program boundary
	- goroutine executes in frame / sandbox with an allocation of memory; that memory can only be written by that goroutine when it is operating inside that functions program boundary
	- every function is a data transformation; every transformation is input > manipulation > output

- count refers the the value, &count refers to the address
	- combined, it is referred to as the data
```Go
func main(){
	count := 10
	println("count:\tValue Of[", count, "]\tAddr Of[", &count "]")
}
```

- sharing data (the value that the pointer points to not the pointer itself; essentially another pointer to the same value, or a copy of the value which overwrites the previous)
```Go
func main() {
	count := 10
	println("count:\tValue Of[", count, "]\tAddr Of[", &count "]")
	// Pass the "address of" count
	increment(&count)
	println("count:\tValue Of[", count, "]\tAddr Of[", &count "]")
}
// *inc refers to the address of the arg
func increment(int *inc) {
	*inc++
	println("inc:\tValue Of[", inc, "]\tAddr Of[", &inc "]\tValue Points To[", *inc, "]")
}
```

| var   | val | addr | pointer |
| ----- | --- | ---- | ------- |
| count | 10  | 7ac  |         |
| inc   | 7ac | 7a0  | 11      |
| count | 11  | 7ac  |         |

### Escape Analysis
```Go
type user struct {
	name string
	email string
}
func main(){
	u1 := createUserV1()
	u2 := createUserV2()
	println("u1", &u1, "u2", u2)
}
func createUserV1() user {
	u := user{
		name: "Bill",
		email: "bill@ardanlabs.com",
	}
	println("V1", &u)
	return u
}
func createUserV2() *user {
	u := user{
		name: "Bill",
		email: "bill@ardanlabs.com",
	}
	println("V2", &u)
	return &u
}
```
- there are no constructors in Go, instead we have factory functions which construct and init a val then return it back to caller

| function | value |
| -------- | ----- |
| main     |       |
| V1       | Bill      |
- v1 uses value semantics
- v2 uses pointer semantics
	- caller gets shared access to the value this function will be constructing, not it's own copy

| function | value |
| -------- | ----- |
| main     |       |
| V2       | Bill  |
|          |       |
- go does static code analysis called escape analysis that determines where your values should go in memory either on stack or heap
- the analysis is how the memory should be shared
- The compiler recognizes that if a value is being shared down, it will be on the stack; if it gets shared up, it will be on the heap as a global
- values only get constructed once; if it gets shared up, it will be escaped from the stack and constructed on the heap
- Garbage Collector manages heap; stack is self-cleaning
- go build -gcflags -m=2
	- gcflags go compiler flags with m2 option
		- compiler will produce escape analysis report (it will not build)
		- we will use this when we are profiling (not when writing code)
		- a profiler can show us what is allocating (on the heap aka 'escaping' the stack), not why; the escape analysis report will tell us why; hint: reason is data sharing up the stack from a main function to another function then back
- we are optimizing for correctness - implying: integrity, readability and simplicity - over performance

### Stack Growth
- when the stack grows, it creates a new stack frame of contiguous memory address blocks (rather than appending blocks to the current frame) & moves the values over (basically a copy operation or lift and shift to a bigger, more roomy and luxurious address space)
- no goroutine can share a value with another goroutine on its own stack
	- any pointers in a goroutine are internal
	- if 2 goroutines need to share a value, then lift state up to the global aka heap (which will be checked by the garbage collector loop & removed once there are no more live pointers to it in any running programs)
- Value semantics give us isolation & safe mutations with data locality, but it is inefficient (making copies of stuff), which requires some pointer semantics to keep the complexity down and efficiency up
	- pointer semantics (a reference to a heap variable shared by 2 goroutines) keeps efficiency up and complexity down

```Go
const size = 10
func main(){
	s := "HELLO"
	stackCopy(&s, 0, [size]int{})
}
func stackCopy(s *string, c int, a [size]int){
	println(c, *s, s)
	c++
	if c == 10 {
		return
	}
	stackCopy(s, c, a)
}
```
- "& (ampersand)" and "* (asterisk)" mean heap variable (pointer / reference)
	- & is how you pass in a variable by its address to a function and * is how you accept the variable as an argument in the function definition

- chatGPT
```bash
Q: 
explain this code: func stackCopy(s *string, c int, a [size]int){ println(c, s, *s) c++ if c == 10 { return } stackCopy(s, c, a) }

A: 
This code defines a function named `stackCopy` that takes in three parameters:

-   a pointer to a string
-   an integer value
-   an array of integers with a fixed size

Inside the function, it prints out the values of `c`, `s`, and the string pointed to by `s`. It then increments the value of `c` by 1.

If `c` is equal to 10, the function returns without doing anything else.

If `c` is not equal to 10, the function calls itself recursively, passing in the same values for the three parameters. This recursive call will continue until `c` becomes equal to 10, at which point the function will return and the recursion will end.
----
Q:
what will be the output of this code? const size = 10 func main(){ s := "HELLO" stackCopy(&s, 0, [size]int{}) } func stackCopy(s *string, c int, a [size]int){ println(c, *s, s) c++ if c == 10 { return } stackCopy(s, c, a) }

A:
The code will print out the following 10 lines:

0 HELLO 0x81729c 1 HELLO 0x81729c 2 HELLO 0x81729c 3 HELLO 0x81729c 4 HELLO 0x81729c 5 HELLO 0x81729c 6 HELLO 0x81729c 7 HELLO 0x81729c 8 HELLO 0x81729c 9 HELLO 0x81729c

On each line, the first value is the current value of `c`, the second value is the string pointed to by `s`, and the third value is the memory address where the string is stored.

The recursion will continue until `c` becomes equal to 10, at which point the function will return and the program will terminate.
```

- now let's increase the size of the array from 10 to 1024

```Go
const size = 1024
func main(){
	s := "HELLO"
	stackCopy(&s, 0, [size]int{})
}
func stackCopy(s *string, c int, a [size]int){
	println(c, *s, s)
	c++
	if c == 10 {
		return
	}
	stackCopy(s, c, a)
}
```

- the output will be:
```Go
0 HELLO 0x81729c 
1 HELLO 0x81729c 
2 HELLO 0x81739c 
3 HELLO 0x81739c 
4 HELLO 0x81739c 
5 HELLO 0x81739c 
6 HELLO 0x81749c 
7 HELLO 0x81749c 
8 HELLO 0x81749c 
9 HELLO 0x81749c
```
- we can see the address changes twice (once from 1 to 2 then from 5 to 6) as the stack frame changes to expand to a greater contiguous memory block

### Garbage Collection
- gc sweeps loose references concurrent to goroutine tasks
- 