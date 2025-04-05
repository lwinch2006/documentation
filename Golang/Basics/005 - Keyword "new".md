The `new` function in Go is used to allocate memory for a variable of a given type and returns a pointer to the newly allocated zero value of that type.

Here’s an example to demonstrate the usage of the `new` function:

```go
package main

import "fmt"

func main() {
    // Using new to allocate memory for an int
    p := new(int)
    
    // p is a pointer to an int
    fmt.Println("Value of p:", p)         // Output: Value of p: 0xc0000xxxxxx (address may vary)
    
    // *p dereferences the pointer to get the value
    fmt.Println("Value at p:", *p)        // Output: Value at p: 0
    
    // Setting the value at the pointer location
    *p = 42
    fmt.Println("New value at p:", *p)    // Output: New value at p: 42
}
```

Explanation:

1. **`p := new(int)`**: This allocates memory for an `int` variable and returns a pointer to it, which is stored in `p`.
2. **`fmt.Println("Value of p:", p)`**: Prints the address stored in `p`.
3. **`fmt.Println("Value at p:", *p)`**: Dereferences the pointer `p` to access the value it points to, which defaults to zero (`0`) for an `int`.
4. **`*p = 42`**: Sets the value at the memory location pointed to by `p` to `42`.
5. **`fmt.Println("New value at p:", *p)`**: Prints the updated value (`42`).

The `new` function is often used when you need a persistent reference to a variable, especially when passing pointers between functions.

Here’s another example with a custom struct:

```go
package main

import "fmt"

type Person struct {
    Name string
    Age  int
}

func main() {
    // Using new to allocate memory for a Person struct
    p := new(Person)
    
    // p is a pointer to a Person
    fmt.Println("Value of p:", p)         // Output: Value of p: &{ 0}
    
    // Setting fields of the struct via the pointer
    p.Name = "Alice"
    p.Age = 30
    
    // Accessing fields via the pointer
    fmt.Println("Person:", *p)            // Output: Person: {Alice 30}
}
```

In this example:

1. **`p := new(Person)`**: Allocates memory for a `Person` struct and returns a pointer to it.
2. **`p.Name = "Alice"`** and **`p.Age = 30`**: Set the fields of the struct via the pointer `p`.
3. **`fmt.Println("Person:", *p)`**: Dereferences the pointer to access the `Person` struct’s fields.

The use of `new` can help in situations where you need to initialize a variable and use its pointer directly.
