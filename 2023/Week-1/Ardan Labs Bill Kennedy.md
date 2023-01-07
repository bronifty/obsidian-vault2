
### Ultimate Go: Web Services 3.0

- [ardanlabs/service github repo](https://github.com/ardanlabs/service.git)

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
	[3]byte padding
	counter int32
	pi float32
}
```

key: 1 Byte = 8 bits
int64 must fall on an 8Byte alignment
```Go
type example struct {
	flag bool
	[7]byte padding
	counter int64
	pi float32
}
```

2. the entire struct has to fall on an alignment equivalent to the largest type (if there is an int64, which is 8Bytes, then a float32, which is 4Bytes, must be padded by 4 to make 8Bytes for each space)
- the derivative rule is that each value's type must consume the same amount of space in the memory as the struct's largest type
	- lesser types must be padded

