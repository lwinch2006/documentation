# Built-in datatypes

| **Category**       | **Data Types**                                                                                      | **Description**                                                                                         |
|---------------------|----------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| **Boolean**         | `bool`                                                                                            | Represents a boolean value: `true` or `false`.                                                         |
| **Numeric (Integer)** | `int`, `int8`, `int16`, `int32`, `int64` <br> `uint`, `uint8`, `uint16`, `uint32`, `uint64`, `uintptr` | Signed and unsigned integers of varying sizes. `uintptr` is an unsigned integer large enough to store the uninterpreted bits of a pointer. |
| **Numeric (Floating)** | `float32`, `float64`                                                                            | Floating-point numbers with 32-bit (`float32`) or 64-bit (`float64`) precision.                         |
| **Complex**         | `complex64`, `complex128`                                                                         | Complex numbers with 32-bit or 64-bit floating-point real and imaginary parts.                          |
| **Text**            | `string`                                                                                          | A sequence of characters (UTF-8 encoded text).                                                          |
| **Byte and Rune**   | `byte`, `rune`                                                                                    | `byte`: Represents a single unsigned 8-bit value. <br> `rune`: Represents a Unicode code point (int32). |
| **Alias Types**     | `byte` (alias for `uint8`), `rune` (alias for `int32`)                                            | Aliases used to clarify intent (e.g., `byte` for raw data, `rune` for characters).                      |
| **Error**           | `error`                                                                                           | A built-in interface for handling error messages.                                                       |

# Numeric data types

| **Type**       | **Size (bits)** | **Range**                                                                                   |
|-----------------|-----------------|---------------------------------------------------------------------------------------------|
| **int**        | Platform-dependent (32 or 64 bits) | **32-bit:** −2,147,483,648 to 2,147,483,647 <br> **64-bit:** −9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 |
| **int8**       | 8               | −128 to 127                                                                                 |
| **int16**      | 16              | −32,768 to 32,767                                                                           |
| **int32**      | 32              | −2,147,483,648 to 2,147,483,647                                                             |
| **int64**      | 64              | −9,223,372,036,854,775,808 to 9,223,372,036,854,775,807                                     |
| **uint**       | Platform-dependent (32 or 64 bits) | **32-bit:** 0 to 4,294,967,295 <br> **64-bit:** 0 to 18,446,744,073,709,551,615                              |
| **uint8**      | 8               | 0 to 255                                                                                   |
| **uint16**     | 16              | 0 to 65,535                                                                                |
| **uint32**     | 32              | 0 to 4,294,967,295                                                                         |
| **uint64**     | 64              | 0 to 18,446,744,073,709,551,615                                                            |
| **uintptr**    | Platform-dependent (32 or 64 bits) | Sufficient to hold the bit pattern of any memory address                                      |
| **float32**    | 32              | Approx. ±1.18 × 10⁻³⁸ to ±3.4 × 10³⁸                                                        |
| **float64**    | 64              | Approx. ±2.23 × 10⁻³⁰⁸ to ±1.8 × 10³⁰⁸                                                     |
| **complex64**  | 64              | Real and imaginary parts as `float32` (each approx. ±1.18 × 10⁻³⁸ to ±3.4 × 10³⁸)           |
| **complex128** | 128             | Real and imaginary parts as `float64` (each approx. ±2.23 × 10⁻³⁰⁸ to ±1.8 × 10³⁰⁸)         |
| **byte**       | 8               | Alias for `uint8`: 0 to 255                                                                 |
| **rune**       | 32              | Alias for `int32`: −2,147,483,648 to 2,147,483,647                                          |

### Notes:
1. The **size of `int` and `uint`** depends on the platform architecture:
    - **32 bits** on 32-bit systems.
    - **64 bits** on 64-bit systems.
2. **Floating-point ranges (`float32` and `float64`)** are approximate, as they depend on IEEE-754 standards for floating-point arithmetic.
3. `complex64` and `complex128` store complex numbers with floating-point representation for their real and imaginary components.
