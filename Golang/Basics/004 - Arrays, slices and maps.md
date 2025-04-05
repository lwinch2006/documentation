### Array

An array in Go is a fixed-size collection of elements of the same type.

```go
package main

import "fmt"

func main() {
    // Creating an array of integers with a fixed size of 5
    var arr [5]int
    
    // Initializing array with values
    arr = [5]int{1, 2, 3, 4, 5}
    
    // Accessing elements of the array
    fmt.Println("Array:", arr)            // Output: Array: [1 2 3 4 5]
    fmt.Println("First element:", arr[0]) // Output: First element: 1
    fmt.Println("Length of array:", len(arr)) // Output: Length of array: 5
}
```

### Slice

A slice is a dynamically-sized, flexible view into the elements of an array. Slices have a length and a capacity.

Example 1
```go
package main

import "fmt"

func main() {
    // Creating a slice of integers
    var s []int
    
    // Initializing slice with values
    s = []int{10, 20, 30}
    
    // Appending values to the slice
    s = append(s, 40, 50)
    
    // Accessing elements of the slice
    fmt.Println("Slice:", s)                // Output: Slice: [10 20 30 40 50]
    fmt.Println("First element:", s[0])     // Output: First element: 10
    fmt.Println("Length of slice:", len(s)) // Output: Length of slice: 5
    fmt.Println("Capacity of slice:", cap(s)) // Output: Capacity of slice: 6 (may vary)
}
```

Example 2
```go
package main

import "fmt"

func main() {
    // Create a slice using the make function
    var numbers []int = make([]int, 5) // Length 5, capacity 5
    numbers[0] = 1
    numbers[1] = 2
    numbers[2] = 3
    numbers[3] = 4
    numbers[4] = 5
    fmt.Println("Slice using make:", numbers)

    // Create a slice using shorthand
    fruits := []string{"apple", "banana", "cherry"}
    fmt.Println("Fruits Slice:", fruits)

    // Append elements to a slice
    fruits = append(fruits, "date")
    fmt.Println("Fruits Slice after append:", fruits)

    // Slice an array
    colors := [3]string{"red", "green", "blue"}
    colorSlice := colors[0:2] // Slices from index 0 to 2 (not inclusive)
    fmt.Println("Color Slice:", colorSlice)
}
```

### Map

A map is a collection of key-value pairs, where each key is unique.

Example 1
```go
package main

import "fmt"

func main() {
    // Creating a map with string keys and integer values
    var m map[string]int
    
    // Initializing the map using a map literal
    m = map[string]int{
        "age":  30,
        "year": 2022,
    }
    
    // Adding key-value pairs
    m["score"] = 100
    
    // Accessing values by keys~~~~~~~~
    fmt.Println("Map:", m)                 // Output: Map: map[age:30 score:100 year:2022]
    fmt.Println("Value for 'age':", m["age"]) // Output: Value for 'age': 30
    
    // Checking if a key exists in the map
    value, exists := m["score"]
    if exists {
        fmt.Println("Score exists:", value) // Output: Score exists: 100
    } else {
        fmt.Println("Score does not exist")
    }
    
    // Deleting a key-value pair
    delete(m, "year")
    fmt.Println("Map after deletion:", m) // Output: Map after deletion: map[age:30 score:100]
}
```

Example 2
```go
package main

import "fmt"

func main() {
    // Create a map using the make function
    var ages map[string]int = make(map[string]int)
    ages["Alice"] = 30
    ages["Bob"] = 25
    fmt.Println("Ages Map using make:", ages)

    // Create a map using map literal
    fruits := map[string]int{
        "apple":  5,
        "banana": 3,
        "cherry": 8,
    }
    fmt.Println("Fruits Map:", fruits)

    // Add a new entry to the map
    fruits["date"] = 4
    fmt.Println("Fruits Map after adding 'date':", fruits)

    // Access a value in the map
    fmt.Println("Number of apples:", fruits["apple"])

    // Delete an entry from the map
    delete(fruits, "banana")
    fmt.Println("Fruits Map after deleting 'banana':", fruits)
}
```

### Summary

- **Array**: Fixed size, element type is the same.
- **Slice**: Flexible and dynamically-sized view into an array, can be appended to.
- **Map**: Collection of key-value pairs, supports dynamic growth, and keys must be unique.
