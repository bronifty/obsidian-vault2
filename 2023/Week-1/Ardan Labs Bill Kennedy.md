
### Ultimate Go: Web Services 3.0

- [ardanlabs/service github repo](https://github.com/ardanlabs/service.git)

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

