The `builtin` package in Go provides several essential functions, constants, and types that are always available in your Go programs without the need for explicit import. Here's a list of functions provided by the `builtin` package:

1. `append(slice []T, elems ...T) []T`
2. `cap(v Type) int`
3. `close(c chan<- Type)`
4. `complex(r, i FloatType) ComplexType`
5. `copy(dst, src []Type) int`
6. `delete(m map[Type]Type, key Type)`
7. `imag(c ComplexType) FloatType`
8. `len(v Type) int`
9. `make(`type `, size … IntegerType) Type`
10. `new(Type) *Type`
11. `panic(v any)`
12. `print(args ...Type)`
13. `println(args ...Type)`
14. `real(c ComplexType) FloatType`
15. `recover() any`

Here's a brief description of each:

1. **`append(slice []T, elems ...T) []T`**: Adds elements to the end of a slice.
2. **`cap(v Type) int`**: Returns the capacity of various types, such as slices, arrays, and channels.
3. **`close(c chan<- Type)`**: Closes a channel, indicating that no more values will be sent on it.
4. **`complex(r, i FloatType) ComplexType`**: Constructs a complex number from two float values.
5. **`copy(dst, src []Type) int`**: Copies elements from a source slice to a destination slice.
6. **`delete(m map[Type]Type, key Type)`**: Deletes a key-value pair from a map.
7. **`imag(c ComplexType) FloatType`**: Returns the imaginary part of a complex number.
8. **`len(v Type) int`**: Returns the length of various types, such as slices, arrays, maps, strings, and channels.
9. **`make(type, size ... IntegerType) Type`**: Allocates and initializes slices, maps, and channels.
10. **`new(Type) *Type`**: Allocates memory for a variable of a specified type and returns a pointer to it.
11. **`panic(v any)`**: Stops the ordinary flow of control and begins panicking.
12. **`print(args ...Type)`**: Prints the arguments (mainly for debugging).
13. **`println(args ...Type)`**: Prints the arguments followed by a newline (mainly for debugging).
14. **`real(c ComplexType) FloatType`**: Returns the real part of a complex number.
15. **`recover() any`**: Allows a program to manage behavior when a panic occurs.








