# Built-in data types

| **Category**       | **Data Type** | **Description**                                                                 |
|---------------------|---------------|---------------------------------------------------------------------------------|
| **Numbers**         | `int`         | Represents integer values (e.g., `42`, `-7`).                                  |
|                     | `double`      | Represents floating-point numbers (e.g., `3.14`, `-0.5`).                      |
|                     | `num`         | A supertype for both `int` and `double`.                                       |
| **Boolean**         | `bool`        | Represents a value that is either `true` or `false`.                           |
| **Strings**         | `String`      | Represents a sequence of characters (e.g., `'Hello'`, `"World"`).              |
| **Collections**     | `List`        | Represents an ordered collection of objects (e.g., `[1, 2, 3]`).               |
|                     | `Set`         | Represents an unordered collection of unique objects (e.g., `{1, 2, 3}`).      |
|                     | `Map`         | Represents a collection of key-value pairs (e.g., `{'key': 'value'}`).         |
| **Null**            | `Null`        | Represents a `null` value (absence of value).                                  |
| **Universal Types** | `Object`      | The base type of all Dart objects (except `null`).                             |
|                     | `dynamic`     | Represents a variable that can hold any type of value.                        |
|                     | `void`        | Used for functions that do not return a value.                                 |

# Numeric data types

| **Data Type** | **Size**     | **Minimum Value**     | **Maximum Value**         |
|---------------|--------------|-----------------------|---------------------------|
| `int`         | Platform-dependent: <br>32-bit on mobile (Dart VM), <br>64-bit on native platforms. | **-2,147,483,648** (32-bit) <br>or **-9,223,372,036,854,775,808** (64-bit) | **2,147,483,647** (32-bit) <br>or **9,223,372,036,854,775,807** (64-bit) |
| `double`      | 64-bit       | **~4.9 × 10⁻³²⁴**     | **~1.8 × 10³⁰⁸**          |
| `num`         | N/A (abstract type) | Can represent any range of `int` or `double` depending on the assigned value. |

### Explanation:
1. **`int`**:
    - Represents whole numbers.
    - The size is **platform-dependent**:
        - On mobile/Dart VM, it's a **32-bit signed integer**.
        - On native Dart platforms, it's a **64-bit signed integer**.

2. **`double`**:
    - Represents floating-point numbers based on IEEE 754 64-bit standard.
    - Handles both extremely large and very small decimal values.

3. **`num`**:
    - Abstract type that can store either an `int` or a `double`.

### Notes:
- Dart does not have unsigned integer types.
- The sizes for `int` depend on the underlying platform, so consider this for performance-sensitive applications. 



