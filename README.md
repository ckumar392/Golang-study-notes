# Go - Golang Quick Intro Study Notes 📝💨

Comprehensive Go study notes for SDE interviews. This guide covers Go fundamentals through advanced topics, with interview tips and common gotchas highlighted throughout.

> 🎯 = Interview Tips | ⚠️ = Common Gotchas/Pitfalls

## Table of Contents
- [🏓 Go Fundamentals](#-go-fundamentals)
  * [Notes](#notes)
  * [Zero Values Table](#zero-values-table)
  * [Variables Declaration](#variables-declaration)
  * [Go Primitive Types](#go-primitive-types)
  * [Visibility](#visibility)
  * [Take Input from console](#take-input-from-console)
  * [Iota](#iota)
  * [Go Pointers](#go-pointers)
  * [Go If-Conditions](#go-if-conditions)
  * [Go For-Loops](#go-for-loops)
  * [Go For-Each loop](#go-for-each-loop)
  * [Go Switch Statement](#go-switch-statement)
- [✨ Go Functions and Return Types](#-go-functions-and-return-types)
  * [Typical Function](#typical-function)
  * [Multiple Returns](#multiple-returns)
  * [Named Returns](#named-returns)
  * [Variadic Functions](#variadic-functions---variable-arguments-list)
  * [Type Function and Returning Functions](#type-function-and-returning-functions-1)
  * [Callbacks](#callbacks---passing-functions-as-argument)
  * [Defer Keyword](#defer-keyword)
  * [Receivers (Value vs Pointer)](#receivers)
  * [Method Shadowing](#method-shadowing-with-embedded-types)
- [🏗 Go Data Structures](#-go-data-structures)
  * [Arrays (Value Types!)](#arrays)
  * [Slices (Internal Structure)](#slices)
  * [Appending to Slice](#appending-to-slice)
  * [Common Slice Functions](#common-slice-functions)
  * [Maps](#maps)
  * [Critical Map Behaviors](#️-critical-map-behaviors-interview-favorites)
- [🏢 Go Structs / OOP](#-go-structs--oop)
  * [Composition (Not Inheritance!)](#composition-not-inheritance)
  * [Polymorphism and Interfaces](#polymorphism-and-interfaces)
  * [Empty Interface and Type Assertions](#empty-interface-and-type-assertions)
  * [The Nil Interface Gotcha](#️-the-nil-interface-gotcha-classic-interview-question)
- [🥂 Go Concurrency](#-go-concurrency)
  * [Goroutines vs OS Threads](#goroutines-vs-os-threads-important-for-interviews)
  * [The sync Package](#the-sync-package)
  * [Go Channels](#go-channels)
  * [Channel Operations Cheat Sheet](#️-channel-operations-cheat-sheet-memorize-this)
  * [Select Statement](#select-statement-critical-for-interviews)
  * [Context Package](#context-package-essential-for-production-code)
  * [Common Concurrency Patterns](#common-concurrency-patterns)
- [🐞 Go Error Handling](#-go-error-handling)
  * [Error Wrapping (Go 1.13+)](#error-wrapping-go-113)
  * [Panic and Recover](#panic-and-recover)
- [🧬 Go Generics (Go 1.18+)](#-go-generics-go-118)
  * [Type Parameters](#type-parameters)
  * [Generic Types](#generic-types)
  * [Constraints](#constraints)
- [🧪 Go Testing](#-go-testing)
  * [Table-Driven Tests](#table-driven-tests-go-idiom)
  * [Benchmarks](#benchmarks)
- [� Go Modules](#-go-modules)
- [🎯 Interview Quick Reference](#-interview-quick-reference)


--------------------------------------------------------------------------


# 🏓Go Fundmentals

## Notes

1. Program Excuetable is at `package main/func main()`
``` Go

        package main        //Excuetables must be of package main
        import "fmt"
        
        func main() {
        	var g string = "Hello golang"
        	println(g)
        }
        
        func function() string {
        	return "Five of Diamonds"
        }
```

2. **Blank Identifier**: Use `_` to replace unused variables.

3. **`new()` vs `make()`** - Two ways to allocate memory, but they're different:

| `new(T)` | `make(T)` |
|----------|-----------|
| Allocates zeroed storage | Initializes the internal data structure |
| Returns `*T` (pointer) | Returns `T` (value) |
| Works with any type | **Only for slice, map, channel** |
| Memory is zeroed but not initialized | Memory is initialized and ready to use |

``` Go
// new - returns pointer to zeroed memory
p := new(int)       // *int, value is 0
s := new([]int)     // *[]int, value is nil slice (unusable!)

// make - initializes and returns value
slice := make([]int, 5)      // []int with len=5, cap=5
m := make(map[string]int)    // initialized empty map (ready to use)
ch := make(chan int, 10)     // buffered channel with capacity 10
```

> 🎯 **Interview Tip:** "Why can't you use `new` for maps?" - Because `new` only zeroes memory, and a zeroed map is `nil`, which panics on write.

4. **⚠️ IMPORTANT: Everything in Go is passed by VALUE.** This is a common misconception. Slices, maps, and channels are NOT passed by reference. They are structs/pointers that are copied by value, but they contain internal pointers to underlying data. When you pass a slice, you copy the slice header (pointer, length, capacity), but both headers point to the same underlying array.

``` Go
// Arrays are VALUE types - fully copied when passed
func modifyArray(arr [3]int) {
    arr[0] = 999  // Modifies the COPY, not the original
}

func main() {
    a := [3]int{1, 2, 3}
    modifyArray(a)
    fmt.Println(a)  // Still [1 2 3] - original unchanged!
}

// Slices contain a pointer internally, so modifications affect original data
func modifySlice(s []int) {
    s[0] = 999  // Modifies underlying array through copied pointer
}

func main() {
    s := []int{1, 2, 3}
    modifySlice(s)
    fmt.Println(s)  // [999 2 3] - data changed!
}
```

5. Unlike in C, it's perfectly OK to return the address of a local variable; the storage associated with the variable survives after the function returns (Go performs escape analysis and allocates to heap when needed).

## Variables Declaration

``` Go

      //Declration
      var g string
      
      //Assignment
      g = "golang"
      
      //Declration & Assignment
      var g = "golang"
      var g string = "golang"
      
      //Shorthand - Declration & Assignmnet
      a := 10
      b := "golang"
```

*Uninitialized variables are given its zero value *(e.g int = 0, string = "", bool = false)* 

### Zero Values Table

| Type | Zero Value |
|------|------------|
| `int`, `float64`, all numeric types | `0` |
| `string` | `""` (empty string) |
| `bool` | `false` |
| `pointer` | `nil` |
| `slice` | `nil` (length 0, capacity 0) |
| `map` | `nil` (unusable until initialized) |
| `channel` | `nil` |
| `interface` | `nil` |
| `function` | `nil` |
| `struct` | All fields set to their zero values |
| `array` | All elements set to their zero values |

> 🎯 **Interview Tip:** A nil slice can be appended to, but a nil map will panic on write!

## Go Primitive Types

The possible values for `bool` are `true` and `false`.

- **uint8** : unsigned 8-bit integers `(0 to 255)`
- **uint16** : unsigned 16-bit integers `(0 to 65535)`
- **uint32** : unsigned 32-bit integers `(0 to 4294967295)`
- **uint64** : unsigned 64-bit integers `(0 to 18446744073709551615)`
- **int8** : signed 8-bit integers (`-128 to 127)`
- **int16** : signed 16-bit integers `(-32768 to 32767)`
- **int32** : signed 32-bit integers `(-2147483648 to 2147483647)`
- **int64** : signed 64-bit integers `(-9223372036854775808 to 9223372036854775807)`
- **int** is either **int64** or **int32** depends on the implementation.
- **float32** : set of `all IEEE-754 32-bit` floating-point numbers
- **float64** : set of `all IEEE-754 64-bit` floating-point numbers
- **complex64** the set of all complex numbers with float32 real and imaginary parts
- **complex128** the set of all complex numbers with float64 real and imaginary parts
- byte alias for `uint8` rune alias for `int32`

## Visibility

If `variables`/`functions` starts with Uppercase character, it is accessible outside the scope of its package, if lowercase then it is only accessible inside its package.

``` Go
package myPkg

var Uppercase = "This is accessible outside the pkg"
var lowercase = "This is not accessible outside the pkg"
func UppercaseFunc() string  {	return "This is accessible outside the pkg"}
func lowercaseFunc() string  {	return "This is accessible outside the pkg"}

// Another file:
package main
import "myPkg"

func main() {
  //Accessible
  println(myPkg.Uppercase)
  println(myPkg.UppercaseFunc())

  //Not Accessible
  println(myPkg.lowercase)
  println(myPkg.lowercaseFunc())
}
```
## Take Input from console
``` Go
//take input like cin >> in c++
var x int = 1337
var y string = "string value"

_, err := fmt.Scan(&x, &y)
fmt.Println("You Entered x:", x, " and y: ", y, " Error: ", err)

//take input like scanf in C
_, err = fmt.Scanf("%d %s", &x, &y)
fmt.Println("You Entered x:", x, " and y: ", y, " Error: ", err)

//take input with white spaces
var z string = "string"

scanner := bufio.NewScanner(os.Stdin)

scanner.Scan()

z = scanner.Text()

fmt.Println("You Entered z:", z)
```
## Iota

`iota` is a special constant generator in Go. Its value starts at 0 within a `const` block and increments by 1 for each constant specification.

``` Go
// Basic usage - iota resets to 0 in each const block
const (
  c0 = iota  // c0 == 0
  c1 = iota  // c1 == 1
  c2 = iota  // c2 == 2
)

// Implicit iota - same as above (more idiomatic)
const (
  a = iota  // 0
  b         // 1 (iota is implicit)
  c         // 2
)

// Skip values with blank identifier
const (
  _ = iota  // 0 (skipped)
  One       // 1
  Two       // 2
)

// Byte sizes pattern (very common in interviews!)
const (
  _  = iota             // 0 (skip)
  KB = 1 << (10 * iota) // 1 << 10 = 1024
  MB                    // 1 << 20 = 1,048,576
  GB                    // 1 << 30 = 1,073,741,824
  TB                    // 1 << 40
)

// Bit flags pattern (common for permissions/options)
const (
  FlagRead    = 1 << iota  // 1 (binary: 001)
  FlagWrite                // 2 (binary: 010)
  FlagExecute              // 4 (binary: 100)
)

// Usage: permissions := FlagRead | FlagWrite  // 3 (binary: 011)
```

> 🎯 **Interview Tip:** Bit flags with iota is a common pattern. Know how to use bitwise OR (`|`) to combine and AND (`&`) to check flags.

## Go Pointers

Pointers syntax is essentially like C/C++
``` Go
var value int = 1000
var pointer *int = &value
println(value)                //1000
println(pointer)              //0xfffffffff
println(*pointer)             //1000
(*pointer)++		  			      //1001
*pointer = *pointer + 10	    //1011
println(*pointer)			        //1011
println(*pointer + *pointer)  //1011 + 1011 = 2022
```

## Go If-Conditions

- Braces must open in the same line of the if/else ( Aghh 😕 )
- in Go's if-statements **parentheses( )** around conditions **are optional**.  but the **braces { } are required** even for oneliners.
``` Go
  value := 10
  if value < 10 {
    println("Less Than 10")
  } else if value > 10 {
    println("Greater Than 10")
  } else {
    println("Equals 10")
  }

  //if conditions with statment
  //note that value is inscope of all if/else's 
  if 	value := 10; value < 10 {
    println(value, "Less Than 10")
  } else if value > 10{
    println(value, "Greater Than 10")
  }else{
    println(value, "Equals 10")
  }
```

Go doesn't have Ternary Operator ( x < 0 ? A : B ) 🤷

## Go For-Loops

There are **3 forms** of for loops, also **there is no a while loop syntax in GO** (instead it is a form of for loops), also there is no do-while at all 
``` Go
  //For loop
  for j := 7; j <= 9; j++ { /*stuff*/ }

  //While like for loop
  i := 1
  for i <= 3 { /*stuff*/ i++ }

  //Infinite Loop : While(true)
  for { /*stuff*/ if (/*stuff*/) break }
```
## Go For-Each loop
``` Go
for i, v := range arr {	//do stuff }
for _, v := range arr {	//do stuff }
for i, _ := range arr {	//do stuff }
```
## Go Switch Statement

Switch statements in GO doesn't require `break`; they will break by default, `fallthrough` keyword used to go to NEXT statement even if condition doesn't match, `fallthrough` is like a break so no code can be after it. however a workaround is to use `labels` and `goto`
```Go
i := 2
fmt.Println("Switch for i = ", i, " goes to: ")
switch i {
  case 1:
    fmt.Println("one")
  case 2:
    fmt.Println("two")
    i = 4
    fallthrough //goes to NEXT case even if doesn't match.
  case 3:
    fmt.Println("three")
  case 4:
    fmt.Println("four")
  case 5,6:
    fmt.Println("five or six")
  default:
    fmt.Println("default")
}
```
## Type Function and Returning Functions

1. Functions can be assigned to variables `func0 := func() int {x++; return x}`
2. Functions that are returned from another functions has its own scope per returned function (yea ikr ? 🤷).
``` Go

package main

var x = 0

func main() {
  //local x
  x := 0

  func0 := func() int {x++; return x}
  func1 := incrementGlobalX //without ()
  func2 := wrapper()
  func3 := wrapper()

  println(func0(), " : func0 (local x)")
  println(func1(), " : func1 (global x)")
  println(func2(), " : func2 (per func scope x1)")
  println(func3(), " : func3 (per func scope x2)")
  println("Second Increment")
  println(func0(), " : func0 (local x)")
  println(func1(), " : func1 (global x)")
  println(func2(), " : func2 (per func scope x1)")
  println(func3(), " : func3 (per func scope x2)")
}

func incrementGlobalX() int  {
  x++
  return x
}

func wrapper() func() int {
  x := 0
  return func() int {
    x++
    return x
  }
}
```  
      
      
--------------------------------------------------------------------------
      
            

# ✨Go functions and return types.

## Notes

- **Everything in Go is passed by value.** Slices, maps, and channels contain internal pointers, so modifications to their contents affect the original data, but the slice/map/channel header itself is copied.
- Unlike in C, it's perfectly OK to return the address of a local variable; the storage associated with the variable survives after the function returns (Go's escape analysis moves it to heap).
- Functions are first-class citizens - they can be assigned to variables, passed as arguments, and returned from other functions.

## Typical Function
``` Go
  // return void
  func add(x int, y int)  {
    fmt.Println("Hello, World!")
  }

  //-------arguments------return------
  func add(x int, y int)  int {
    return x + y
  }

  //-----same type arguments-----------
  func add(x, y int)  int {
    return x + y
  }
```    

## Multiple Returns
```Go
  func swap(x, y string) (string, string) {
    return y, x
  }

  //in main
  a, b := swap("hello", "world")
  fmt.Println(a, b) //prints "world hello"
```
## Named Returns

You can declare return variables and name them at the beginning, they are returned in the end.

*you can override the returns and return whatever you want at the return statement.
```Go
  //Returns x,y at the end.
  func split(sum int) (x, y int) {
    x = sum * 4 / 9
    y = sum - x
    return
    //return a,b  <- u can override default return of x,y.
  }
```
## Variadic Functions / Variable arguments list

The rightmost argument can be a list of variable size (*slice*) of data.
```Go
  // x value here has no use for average. just illustrates the idea of having arguments
  // then a variable number of arguments
  func average(x int, values ...int) float64{
    //print values
    fmt.Println("Single argument value: ", x)
    fmt.Println("Variable argument values: ", values)

    //calculate average
    total := 0
    for _, value := range values {
      total += value
    }

    return float64(total) / float64(len(values))
  }

  func main() {
    avg := average(10,20,30,40,50)
    println("Average:", avg)
  }
```
## Type Function and Returning Functions

1. Functions can be assigned to variables `func0 := func() int {x++; return x}` as anonymous functions or to another declared functions
2. Functions that are returned from another functions has its own scope per returned function ( Closures yea ikr ? 🤷).
``` Go

package main

var x = 0

func main() {
  //local x
  x := 0

  func0 := func() int {x++; return x}
  func1 := incrementGlobalX //without ()
  func2 := wrapper()
  func3 := wrapper()

  println(func0(), " : func0 (local x)")
  println(func1(), " : func1 (global x)")
  println(func2(), " : func2 (per func scope x1)")
  println(func3(), " : func3 (per func scope x2)")
  println("Second Increment")
  println(func0(), " : func0 (local x)")
  println(func1(), " : func1 (global x)")
  println(func2(), " : func2 (per func scope x1)")
  println(func3(), " : func3 (per func scope x2)")
}

func incrementGlobalX() int  {
  x++
  return x
}

func wrapper() func() int {
  x := 0
  return func() int {
    x++
    return x
  }
}
```      
      

## Callbacks - Passing functions as argument
```Go
func visit(numbers []int, callback func(int2 int)){
  for _, n := range numbers {
    callback(n*2)
  }
}
func main() {
  visit([]int{1,2,3,4}, func(n int){
    fmt.Println(n, "- Printed withing the callback function.")
  })
}
```
## Defer Keyword

`defer` schedules a function call to execute when the surrounding function returns. Deferred calls are executed in **LIFO (stack) order**. Common uses: closing files, unlocking mutexes, cleanup operations.

```Go
fmt.Println("One")
defer fmt.Println("Four")
defer fmt.Println("Three")
fmt.Println("Two")

//Prints One Two Three Four
```

### ⚠️ Critical Defer Behaviors (Common Interview Questions!)

```Go
// 1. Arguments are evaluated IMMEDIATELY, not when deferred function runs
func main() {
    x := 10
    defer fmt.Println(x)  // Prints 10, NOT 20
    x = 20
    fmt.Println("x is now", x)
}
// Output: x is now 20
//         10

// 2. Deferred functions CAN modify named return values
func example() (result int) {
    defer func() { result++ }()
    return 10  // Returns 11, not 10!
}

// 3. ⚠️ Defer in loops - MEMORY LEAK potential!
// BAD - all defers stack up until function exits
for i := 0; i < 1000000; i++ {
    f, _ := os.Open(files[i])
    defer f.Close()  // 1 million defers waiting!
}

// GOOD - wrap in anonymous function
for i := 0; i < 1000000; i++ {
    func() {
        f, _ := os.Open(files[i])
        defer f.Close()  // Executes at end of anonymous function
    }()
}

// 4. Defer runs even if function panics
func riskyOperation() {
    defer fmt.Println("This WILL print even if panic occurs")
    panic("something went wrong")
}
```

> 🎯 **Interview Tip:** "What happens if you defer in a loop?" is a classic gotcha. Always explain the memory implications.

## Receivers

Receivers are Go's way of attaching methods to types. They come in two forms: **value receivers** and **pointer receivers**.

``` Go
type rect struct {
    width, height int
}

// Pointer receiver - can modify the receiver
func (r *rect) area() int {
    return r.width * r.height
}

//used as 
r := rect{2,3}
areaX := r.area()
fmt.Println(areaX)
```

### Value vs Pointer Receivers (Critical for Interviews!)

```Go
type Counter struct {
    count int
}

// Value receiver - operates on a COPY, original unchanged
func (c Counter) IncrementValue() {
    c.count++  // Modifies copy only!
}

// Pointer receiver - operates on the ORIGINAL
func (c *Counter) IncrementPointer() {
    c.count++  // Modifies original
}

func main() {
    c := Counter{count: 0}
    
    c.IncrementValue()
    fmt.Println(c.count)  // Still 0!
    
    c.IncrementPointer()
    fmt.Println(c.count)  // Now 1
}
```

### When to Use Pointer Receivers:
1. **When you need to modify the receiver** (most common reason)
2. **When the struct is large** (avoid copying overhead)
3. **For consistency** - if one method needs a pointer receiver, use pointer for ALL methods

> 🎯 **Interview Tip:** Go automatically dereferences/takes address when calling methods. `c.IncrementPointer()` works even though `c` is not a pointer.

## Method Shadowing with Embedded Types

**Note:** Go does NOT have traditional method overriding. What happens with embedded types is called **method shadowing** - the outer type's method shadows the inner type's method, but both still exist.

```Go
 type Person struct {
    First string
    Last string
    Age int
   }

   type Employee struct {
    Person  // Embedded type
    ID string
    Salary int
   }

   func (p Person) FullName() string{
    return p.First + " " + p.Last
   }

   // This SHADOWS Person.FullName, doesn't override it
   func (e Employee) FullName() string{
    return e.ID + " " + e.First + " " + e.Last
   }


   func main() {
    x := Employee{
      Person{
        "Sherif",
        "Abdel-Naby",
        12},
      "0ID12000ID",
      9999,
    }

fmt.Println(x)
fmt.Println(x.Person.FullName()) //Sherif Abdel-Naby
fmt.Println(x.FullName()) 		   //0ID12000ID Sherif Abdel-Naby
```


--------------------------------------------------------------------------



# 🏗Go Data-Structures

Arrays, Slices, Maps, and Structs

## Arrays

- Arrays are of **static size**, size can't be changed in arrays.
- **⚠️ Arrays are VALUE types** - they are fully copied when passed to functions or assigned!
- Arrays elements do not need to be initialized explicitly; the zero value of the type is the initial value.
- The size is part of the type: `[5]int` and `[10]int` are different types!

``` Go
    var x[15] int
    var twoD [2][3] int
    
    // Arrays are copied by value!
    a := [3]int{1, 2, 3}
    b := a        // b is a COPY of a
    b[0] = 999
    fmt.Println(a) // [1 2 3] - unchanged!
    fmt.Println(b) // [999 2 3]
```

## Slices

Slices are dynamic, flexible views into arrays. **Understanding slice internals is critical for interviews.**

### Slice Internal Structure (24 bytes on 64-bit systems)

```
Slice Header:
┌─────────────────────┬──────────────┬──────────────┐
│ Pointer to array    │   Length     │   Capacity   │
│     (8 bytes)       │  (8 bytes)   │  (8 bytes)   │
└──────────┬──────────┴──────────────┴──────────────┘
           │
           ▼
┌───┬───┬───┬───┬───┬───┬───┬───┐
│ 0 │ 1 │ 2 │ 3 │ 4 │ 5 │ 6 │ 7 │  ← Underlying Array
└───┴───┴───┴───┴───┴───┴───┴───┘
```

``` GO
    letters := []string{"a", "b", "c", "d"}
    
    /*  using make -> make([]T, len, cap) */
    var s []byte
    s = make([]byte, 5, 5)
    //OR
    s := make([]byte, 5)
    
    // both equiavlent to: s == []byte{0, 0, 0, 0, 0}
    
    // len() = number of elements accessible
    // cap() = max elements before reallocation needed
    fmt.Println(len(s), cap(s))  // 5, 5
```
- A slice does not store any data, it just describes a section of an underlying array. Changing the elements of a slice modifies the corresponding elements of its underlying array. **Other slices that share the same underlying array will see those changes**.
- Slicing a slice changes pointers of the underlying array, so it is as efficient as manipulating array indices, size and capacity of the new slice are changed too, capacity is equal `old capacity - sliced part from the beginning only`
```gO
    	names := [4]string{"John","Paul","George","Ringo",}
    	fmt.Println(names)   //[John Paul George Ringo]
    	a := names[0:2]
    	b := names[1:3]
    	fmt.Println(a, b)    //[John Paul] [Paul George]
    	b[0] = "XXX"
    	fmt.Println(a, b)    //[John XXX] [XXX George] 
    	fmt.Println(names)   //[John XXX George Ringo]
    
    	//ALSO
    
    	//This is an array literal:
    	[3]bool{true, true, false}
    
    	//And this creates the same array as above, then builds a slice that references it:
    	[]bool{true, true, false}
```
## Iterating over a Slice
``` Go
    for i, v := range arr {	//do stuff }
    for _, v := range arr {	//do stuff }
    for i, _ := range arr {	//do stuff }
```

## Appending to Slice

`append` returns a new slice header (always assign the result!).

``` Go
      var s []int
    	// append works on nil slices.
    	s = append(s, 0)
    	// The slice grows as needed.
    	s = append(s, 1)
```

### Append Behavior and Capacity Growth

- If **capacity is sufficient**: appends to existing array, returns slice pointing to same array
- If **capacity is insufficient**: allocates NEW array, copies data, returns slice pointing to new array

**Capacity Growth Strategy (Go 1.18+):**
- If `cap < 256`: double the capacity
- If `cap >= 256`: grow by ~25% (`cap + cap/4 + 192`)

```Go
// Watch capacity grow
s := make([]int, 0)
for i := 0; i < 10; i++ {
    s = append(s, i)
    fmt.Printf("len=%d cap=%d\n", len(s), cap(s))
}
// cap grows: 1, 2, 4, 8, 16...
```

> 🎯 **Interview Tip:** Pre-allocate when you know the size to avoid reallocations:
```Go
// BAD - many reallocations
var results []int
for i := 0; i < 1000; i++ {
    results = append(results, i)
}

// GOOD - single allocation
results := make([]int, 0, 1000)  // Pre-allocate capacity
for i := 0; i < 1000; i++ {
    results = append(results, i)
}
```

### ⚠️ Append Gotcha (Classic Interview Question)

Append add element at the end of the slice **if there is enough capacity** and r**eturn a reference type**!, if **not enough capacity** it allocate and copy to a new array and **return it as a new value**! and the **old array points to the old data**.

If Append had enough capacity (didn't allocate new array) then ***changing a value in the new returned array changes the value in the old***! but if it allocated a new array to expand capacity, then **changing a value at an index of the newly returned array *DOESN'T* change the old array!**

consider only using append where the left hand side is the same variable in the append first argument **(S = append(S, .....) )** to avoid any unexpected results

``` Go
  //Allocate new capacity  
  var s []int
  s = make([]int, 5, 5)
  x := append(s, 1, 2 ,3)
  x[0] = 1337
  s[0] = 6800
  fmt.Println(s,x) //[6800 0 0 0 0] [1337 0 0 0 0 1 2 3]

  //Doesn't allocat new capacity and return reference
  var s []int
  s = make([]int, 5, 150)
  x := append(s, 1, 2 ,3)
  x[0] = 1337
  s[0] = 6800
  fmt.Println(s,x) //[6800 0 0 0 0] [6800 0 0 0 0 1 2 3]
                   //notice that 1337 is overwritten
```
## Common Slice Functions

**Append Another Slice**
``` Go
  a = append(a, b...)
```
**Copy**

Copy only copy elements of size = min(len(a), len(b)). so, the new slice to which a copy is to be made must have a size == size of the original array to have all elements copied.
```Go
    b = make([]T, len(a))
    copy(b, a)
    // or
    b = append([]T(nil), a...)
```
**Cut**
```Go
    a = append(a[:i], a[j:]...)
```
**Delete**
```Go
    a = append(a[:i], a[i+1:]...)
    // or
    a = a[:i+copy(a[i:], a[i+1:])]
```
## Slices Tricks

[golang/go](https://github.com/golang/go/wiki/SliceTricks)

## Maps

From [https://play.golang.org/p/U67R66Oab8r](https://play.golang.org/p/U67R66Oab8r)
```Go
// To create an empty map, use the builtin `make`:
// `make(map[key-type]val-type)`.
m := make(map[string]int)

// Set key/value pairs using typical `name[key] = val`
// syntax.
m["k1"] = 7
m["k2"] = 13

// Printing a map with e.g. `fmt.Println` will show all of
// its key/value pairs.
fmt.Println("map:", m)

// Get a value for a key with `name[key]`.
v1 := m["k1"]
fmt.Println("v1: ", v1)

// The builtin `len` returns the number of key/value
// pairs when called on a map.
fmt.Println("len:", len(m))

// The builtin `delete` removes key/value pairs from
// a map.
delete(m, "k2")
fmt.Println("map:", m)

// The optional second return value when getting a
// value from a map indicates if the key was present
// in the map. This can be used to disambiguate
// between missing keys and keys with zero values
// like `0` or `""`. Here we didn't need the value
// itself, so we ignored it with the _blank identifier_
// `_`.
_, prs := m["k2"]
fmt.Println("prs:", prs)

//You can use zero value to check if value exist. use second return instead.
fmt.Println("key not found (gets zero value):", m["notFoundKey"])


// You can also declare and initialize a new map in
// the same line with this syntax.
n := map[string]int{"foo": 1, "bar": 2}
fmt.Println("map:", n)
```

### ⚠️ Critical Map Behaviors (Interview Favorites!)

```Go
// 1. Map iteration order is RANDOM (by design, for security)
m := map[string]int{"a": 1, "b": 2, "c": 3}
for k, v := range m {
    fmt.Println(k, v)  // Order varies each run!
}

// 2. Nil map - reading is OK, writing PANICS
var m map[string]int  // nil map
_ = m["key"]          // OK - returns zero value
m["key"] = 1          // PANIC: assignment to nil map

// 3. Always check if key exists (important pattern!)
value, ok := m["key"]
if !ok {
    // key doesn't exist
    fmt.Println("key not found")
}

// 4. Delete is safe even if key doesn't exist
delete(m, "nonexistent")  // No panic, no-op

// 5. Maps are NOT safe for concurrent use!
// Use sync.Map or protect with mutex for concurrent access

// 6. You cannot take the address of a map element
// m["key"] is not addressable
// ptr := &m["key"]  // COMPILE ERROR
```

> 🎯 **Interview Tip:** "Why is map iteration order random?" - For security (to prevent hash collision attacks) and to discourage depending on order.


--------------------------------------------------------------------------



# 🏢Go Structs / OOP

## Notes

- In Go you don't create classes, but create **types**
- **Go does NOT have inheritance** - it uses **composition via embedding** instead. This is a core Go philosophy: "Favor composition over inheritance"
- Embedded types' fields and methods are "promoted" to the outer type, providing a form of code reuse
- Struct fields can have tags (written between backticks after field type), used for reflection, JSON encoding, database mapping, etc.
- Fields and methods starting with **Uppercase** are exported (public), **lowercase** are unexported (package-private)

```Go
type User struct {
    Name     string `json:"name"`           // JSON key will be "name"
    Password string `json:"-"`              // Excluded from JSON
    Age      int    `json:"age,omitempty"`  // Omit if zero value
}
```

## Go supports

### Encapsulation

- State/Fields
- Methods
- Public/Private → Exported/unexported

### Composition (Not Inheritance!)

**⚠️ Important:** Go does NOT have inheritance. What's shown below is **embedding/composition**.

``` Go

package main
import "fmt"

type Parent struct {
  First string
  Last string
  Age int
}

type Child struct {
  Parent       // Embedded type - NOT inheritance!
  First string // Shadows Parent.First
  Middle string
}


func main() {

  x := Child{
    Parent{
      "First",
      "Last",
      12},
    "Child's First",
    "Middle",
  }

  fmt.Println(x)

  fmt.Println(x.First)        // "Child's First" (shadows Parent.First)
  fmt.Println(x.Parent.First) // "First" (explicit access)
  fmt.Println(x.Middle)       // "Middle"

  fmt.Println(x.Last)         // "Last" (promoted from Parent)
  fmt.Println(x.Parent.Last)  // "Last" (explicit access)

  fmt.Println(x.Age)          // 12 (promoted from Parent)
  fmt.Println(x.Parent.Age)   // 12 (explicit access)

}
```

> 🎯 **Interview Tip:** "Why doesn't Go have inheritance?" - Go favors composition for flexibility and to avoid the fragile base class problem. Embedding provides code reuse without the tight coupling of inheritance.
```
Packages might need your type to implement its interface to work, for example the `sort` package requires you to implement `swap`, `less`, `equal` methods in order to work. also `fmt.Println()` requires you to implement `func (t T) String() string`

### Polymorphism and Interfaces

A type implements an interface by implementing its methods. There is no explicit declaration of intent, no "implements" keyword.

``` Go

// Here's a basic interface for geometric shapes.
type geometry interface {
  area() float64
  perim() float64
  //extraFunc() string	//if we uncomment this, so rect and circle won't
              // be implementing the geometry interface
}

// For our example we'll implement this interface on
// `rect` and `circle` types.
type rect struct {
  width, height float64

}
type circle struct {
  radius float64
}

// To implement an interface in Go, we just need to
// implement all the methods in the interface. Here we
// implement `geometry` on `rect`s.
func (r rect) area() float64 {
  return r.width * r.height
}
func (r rect) perim() float64 {
  return 2*r.width + 2*r.height
}

// The implementation for `circle`s.
func (c circle) area() float64 {
  return math.Pi * c.radius * c.radius
}
func (c circle) perim() float64 {
  return 2 * math.Pi * c.radius
}

// If a variable has an interface type, then we can call
// methods that are in the named interface. Here's a
// generic `measure` function taking advantage of this
// to work on any `geometry`.
func measure(g geometry) {
  fmt.Println(g)
  fmt.Println(g.area())
  fmt.Println(g.perim())
}

func main() {
  r := rect{width: 3, height: 4}
  c := circle{radius: 5}

  // The `circle` and `rect` struct types both
  // implement the `geometry` interface so we can use
  // instances of
  // these structs as arguments to `measure`.
  measure(r)
  measure(c)
}
```

### Empty Interface and Type Assertions

The **empty interface** `interface{}` (or `any` in Go 1.18+) can hold values of any type. It's Go's way of achieving generic programming (before generics were added).

```Go
// Empty interface can hold anything
func printAnything(v interface{}) {
    fmt.Println(v)
}

printAnything(42)
printAnything("hello")
printAnything([]int{1, 2, 3})

// Type assertion - extract concrete type from interface
var i interface{} = "hello"

// Unsafe - panics if wrong type
s := i.(string)
fmt.Println(s)  // "hello"

// Safe - returns ok=false if wrong type
s, ok := i.(string)
if ok {
    fmt.Println("It's a string:", s)
}

n, ok := i.(int)  // ok = false, n = 0 (zero value)

// Type switch - check multiple types
func describe(i interface{}) {
    switch v := i.(type) {
    case string:
        fmt.Println("string of length", len(v))
    case int:
        fmt.Println("int, doubled:", v*2)
    case bool:
        fmt.Println("boolean:", v)
    default:
        fmt.Printf("unknown type: %T\n", v)
    }
}
```

### ⚠️ The Nil Interface Gotcha (Classic Interview Question!)

An interface value is `nil` only when BOTH its type and value are nil. This is a major source of bugs!

```Go
type MyError struct{}

func (e *MyError) Error() string { return "error" }

func getError() error {
    var e *MyError = nil  // Typed nil pointer
    return e              // Returns interface containing (type=*MyError, value=nil)
}

func main() {
    err := getError()
    
    // This is FALSE! The interface is NOT nil!
    fmt.Println(err == nil)  // false
    
    // The interface contains:
    // - Type: *MyError
    // - Value: nil
    // An interface is only nil when BOTH are nil
}

// Correct way:
func getErrorCorrect() error {
    var e *MyError = nil
    if e == nil {
        return nil  // Return untyped nil
    }
    return e
}
```

> 🎯 **Interview Tip:** This is one of the most common Go gotchas. Always return explicit `nil` for interfaces, not typed nil pointers.

### Method Shadowing (Not Overriding)

**Note:** Go doesn't have method overriding. Embedded types' methods are **shadowed**, not overridden. Both methods still exist.

``` Go

  type Person struct {
    First string
    Last string
    Age int
  }

  type Employee struct {
    Person
    ID string
    Salary int
  }

  func (p Person) FullName() string{
    return p.First + " " + p.Last
  }

  // This SHADOWS Person.FullName, doesn't override it
  func (e Employee) FullName() string{
    return e.ID + " " + e.First + " " + e.Last
  }


  func main() {

    x := Employee{
      Person{
        "Sherif",
        "Abdel-Naby",
        12},
      "0ID12000ID",
      9999,
    }

    fmt.Println(x)
    fmt.Println(x.Person.FullName()) // Sherif Abdel-Naby (Person's method still accessible!)
    fmt.Println(x.FullName())        // 0ID12000ID Sherif Abdel-Naby (Employee's method)
```


--------------------------------------------------------------------------



# 🥂Go Concurrency

## Intro

Go Concurrency is made available by **goroutines**. When a function is preceded with the `go` keyword, it runs in a goroutine. Goroutines are one of Go's most powerful features.

### Goroutines vs OS Threads (Important for Interviews!)

| Goroutines | OS Threads |
|------------|------------|
| ~2KB initial stack (grows/shrinks dynamically) | ~1-8MB fixed stack |
| Managed by **Go runtime scheduler** | Managed by **OS kernel** |
| **M:N scheduling** (many goroutines on few threads) | 1:1 with OS |
| Very cheap to create (millions possible) | Expensive (hundreds/thousands) |
| Cooperative scheduling with preemption | Preemptive scheduling |
| Context switch: ~tens of nanoseconds | Context switch: ~microseconds |

```Go
// Creating a goroutine - just add 'go'
go myFunction()

go func() {
    fmt.Println("Anonymous goroutine")
}()
```

For data synchronization you can use mutex Locks, WaitGroups, and Atomic operations, however..
**It's recommended** to use Go Channels for data synchronization - "Don't communicate by sharing memory; share memory by communicating."

---

## Notes

1. **GO executable exits when `main()` returns**, even with active goroutines running - they will be terminated abruptly!
2. **Goroutine leaks** are a common bug - a goroutine blocked forever (e.g., on a channel) consumes memory indefinitely.

## The sync Package

### Mutex (Mutual Exclusion)

```Go
import "sync"

var (
    counter int
    mu      sync.Mutex
)

func increment() {
    mu.Lock()
    defer mu.Unlock()  // Always use defer to ensure unlock
    counter++
}
```

### RWMutex (Read-Write Mutex)

Allows **multiple readers OR one writer** - more efficient when reads are frequent.

```Go
var (
    data   map[string]string
    rwmu   sync.RWMutex
)

func read(key string) string {
    rwmu.RLock()         // Multiple goroutines can read simultaneously
    defer rwmu.RUnlock()
    return data[key]
}

func write(key, value string) {
    rwmu.Lock()          // Exclusive access for writing
    defer rwmu.Unlock()
    data[key] = value
}
```

### WaitGroup

```Go
var wg sync.WaitGroup

for i := 0; i < 5; i++ {
    wg.Add(1)  // Increment counter BEFORE starting goroutine
    go func(id int) {
        defer wg.Done()  // Decrement counter when done
        fmt.Println("Worker", id)
    }(i)
}

wg.Wait()  // Block until counter reaches 0
fmt.Println("All workers done")
```

### Once (Singleton Pattern)

```Go
var (
    instance *Database
    once     sync.Once
)

func GetDatabase() *Database {
    once.Do(func() {
        // This runs exactly once, even with concurrent calls
        instance = &Database{}
        instance.Connect()
    })
    return instance
}
```

### Pool (Object Reuse)

```Go
var bufferPool = sync.Pool{
    New: func() interface{} {
        return make([]byte, 1024)
    },
}

func process() {
    buf := bufferPool.Get().([]byte)
    defer bufferPool.Put(buf)  // Return to pool when done
    // Use buf...
}
```

## Go Channels

Channels are typed conduits for communication between goroutines.

```Go
// Unbuffered channel - synchronous, blocks until both sender and receiver are ready
ch := make(chan int)

// Buffered channel - async up to capacity, blocks when full
ch := make(chan int, 10)  // capacity of 10
```

### ⚠️ Channel Operations Cheat Sheet (Memorize This!)

| Operation | Nil Channel | Closed Channel | Open Channel |
|-----------|-------------|----------------|--------------|
| **Send** `ch <- v` | Block forever | **PANIC** | Block until received (unbuffered) or buffer has space |
| **Receive** `<-ch` | Block forever | Zero value, `ok=false` | Block until value available |
| **Close** `close(ch)` | **PANIC** | **PANIC** | Succeeds, future receives get zero values |

```Go
// Check if channel is closed
val, ok := <-ch
if !ok {
    fmt.Println("Channel closed!")
}

// Only sender should close a channel, NEVER the receiver
// Closing is only necessary to signal "no more values coming"
```

### Channel Direction (Function Parameters)

```Go
// Send-only channel
func producer(ch chan<- int) {
    ch <- 42
    // <-ch  // Compile error!
}

// Receive-only channel
func consumer(ch <-chan int) {
    val := <-ch
    // ch <- 1  // Compile error!
}

// Bidirectional converts to directional automatically
ch := make(chan int)
go producer(ch)  // chan int -> chan<- int
go consumer(ch)  // chan int -> <-chan int
```

### Example 1

Note that I am using time.Sleep at the end to wait for the code to execute as the program will instantly close after running the two go routines.
```Go
c := make(chan int)

go func() {
  for i := 0; i < 9; i++ {
    time.Sleep(time.Second)
    c <- i
  }
}()

go func() {
  for{
    fmt.Println( <- c )
  }
}()

time.Sleep(time.Second * 15)
```
### Example 2

Using Range on a channel, it will iterate over values added on the channel until the channel is closed.
No need to use time.sleep as the for-range is a blocking function.
```Go
c := make(chan int)

go func() {
  for i := 0; i <= 10; i++ {
    time.Sleep(time.Second)
    c <- i
  }
  close(c)
}()

for n := range c{
  fmt.Println(n)
}
```
### Example 3

Using wait-group to use more than 1 function to write to the same channel.
Using a `waitGroup` to close the channel once the two writing functions signal `wg.Done()`
```Go
c := make(chan int)

var wg sync.WaitGroup

wg.Add(2)

go func() {
  for i := 0; i <= 10; i++ {
    time.Sleep(time.Millisecond * 350)
    c <- i
  }
  wg.Done()
}()

go func() {
  for i := 1000; i <= 1010; i++ {
    time.Sleep(time.Millisecond * 350)
    c <- i
  }
  wg.Done()
}()

go func() {
  wg.Wait()
  close(c)
}()

for n := range c{
  fmt.Println(n)
}
```
### Example 4

Using dynamic number of function calls.

Also notice passing i inside the go func, this is because the value outside is in a for-loop, hence it is changing, so using it inside the the go-routine will lead to unexpected results.
```Go
c := make(chan string)

var wg sync.WaitGroup

n := 10

wg.Add(n)

for i := 0; i < n; i++  {
  go func(i int) {
    for t := i*10; t < i*10 + 10; t++ {
      c <- "From " + 	strconv.Itoa(i) + " : " + strconv.Itoa(t)
    }
    wg.Done()
  }(i)
}

go func() {
  wg.Wait()
  close(c)
}()

for x := range c{
  fmt.Println(x)
}
```
### Example 5 - Semaphores

Using only channels without waitGroup. 
This is done using a channel that store bool (or anything really), and use a function to receive n-done signals then close the main channel.
```Go
c := make(chan string)
done := make(chan bool)

n := 2


for i := 0; i < n; i++  {
  go func(i int) {
    for t := i*10; t < i*10 + 10; t++ {
      c <- "From " + 	strconv.Itoa(i) + " : " + strconv.Itoa(t)
    }
    done <- true
  }(i)
}

go func() {
  //receive the n-dones from the go-routines
  for i := 0; i < n; i++{
    <- done
  }
  close(c)
}()

for x := range c{
  fmt.Println(x)
}
```

### Example 6 - Using channels as arguments/returns

In this example we sum values from 0 to i. e.g( i = 3 → 0+1+2+3 = 6)

---

This code we create a go routine that feeds the **increment channels values** 1,2,3
another Channel called the **sum channel** will take the **increment channel and processes its values ( so the sum channel will run until the increment channel closes )**, then the sum channel will **put its sum value for the main to pick**. the point here that main can do other stuff while sum channel finish processing. also we can pass any type of channel to sum channel to sum not necessary an incrementing values in a decoupled way.
```Go
func main() {

  i := 10

  //Return A Channel that produces 1, 2, 3...n
  c := incrementer(i)

  // Take a channel that produces 1,2,3...n and sum these numbers
  // returns a channel that have the data in it after summation (so it is not blocking the main itself)
  cSum := puller(c)

  /* DO STUFF WHILE Puller is working (that's why it is returning a channel */

  //Pull from the puller when we want the result (This is blocking now)
  //Result for i := 10 should be : 10 + 9 + 8 + 7 + 6 + 5 + 4 + 3 + 2 + 1 + 0 = 55
  fmt.Println("Final Sum", <- cSum)

}

//returns an ACTIVE go routine that produces 1,2,3..n
func incrementer(n int) chan int  {
  out := make(chan int, 10)

  //no need to pass n as parameter as it is a non-changing variable in this context
  go func() {
    for i := 0; i <= n; i++ {
      fmt.Println("From incrementer: Produced i = ", i )
      out <- i
      //just to illustrate it is blocking in main.
      time.Sleep(time.Millisecond * 100)
    }
    close(out)
  }()
  return out
}

//takes a channel that produces numbers that are to be summed together.
func puller(c chan int) chan int  {
  out := make (chan int)
  go func() {
    var sum int
    for n := range c{
      fmt.Println("From Puller go-routine: Sum + i ->", sum, "+", n, "=", sum + n)
      sum += n
    }
    fmt.Println("Summation Finished -> Outputing SUM")
    out <- sum
    // also we can output each sum stage for whoever uses the channel and close when finish.
    //close(out)
  }()
  return out
}
```
```
Output:

    From incrementer: Produced i =  0
    From Puller go-routine: Sum + i -> 0 + 0 = 0
    From incrementer: Produced i =  1
    From Puller go-routine: Sum + i -> 0 + 1 = 1
    From incrementer: Produced i =  2
    From Puller go-routine: Sum + i -> 1 + 2 = 3
    From incrementer: Produced i =  3
    From Puller go-routine: Sum + i -> 3 + 3 = 6
    From incrementer: Produced i =  4
    From Puller go-routine: Sum + i -> 6 + 4 = 10
    From incrementer: Produced i =  5
    From Puller go-routine: Sum + i -> 10 + 5 = 15
    Summation Finished -> Outputing SUM
    Final Sum 15
```

## Select Statement (Critical for Interviews!)

`select` lets a goroutine wait on multiple channel operations. It's like a `switch` for channels.

```Go
select {
case msg := <-ch1:
    fmt.Println("Received from ch1:", msg)
case ch2 <- value:
    fmt.Println("Sent to ch2")
case <-time.After(time.Second * 5):
    fmt.Println("Timeout after 5 seconds!")
default:
    fmt.Println("No channel ready, don't block")
}
```

### Common Select Patterns

```Go
// 1. Non-blocking receive
select {
case msg := <-ch:
    process(msg)
default:
    // Channel empty, continue without blocking
}

// 2. Non-blocking send
select {
case ch <- msg:
    // Sent successfully
default:
    // Channel full, drop message or handle
}

// 3. Timeout pattern
select {
case result := <-workChan:
    fmt.Println("Got result:", result)
case <-time.After(time.Second * 10):
    fmt.Println("Timed out!")
}

// 4. Cancellation with done channel
done := make(chan struct{})

go func() {
    for {
        select {
        case <-done:
            fmt.Println("Cancelled!")
            return
        case data := <-dataChan:
            process(data)
        }
    }
}()

// To cancel:
close(done)  // All receivers of done will unblock
```

## Context Package (Essential for Production Code!)

The `context` package is the standard way to handle cancellation, timeouts, and request-scoped values.

```Go
import "context"

// Creating contexts
ctx := context.Background()  // Root context, never cancelled

// With cancellation
ctx, cancel := context.WithCancel(context.Background())
defer cancel()  // Always call cancel to release resources!

// With timeout
ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
defer cancel()

// With deadline
deadline := time.Now().Add(10 * time.Second)
ctx, cancel := context.WithDeadline(context.Background(), deadline)
defer cancel()

// With value (use sparingly - for request-scoped data only)
ctx := context.WithValue(parentCtx, "userID", 123)
userID := ctx.Value("userID").(int)
```

### Using Context in Functions

```Go
func doWork(ctx context.Context) error {
    // Check if already cancelled
    select {
    case <-ctx.Done():
        return ctx.Err()  // context.Canceled or context.DeadlineExceeded
    default:
    }
    
    // Do work with periodic cancellation checks
    for i := 0; i < 100; i++ {
        select {
        case <-ctx.Done():
            return ctx.Err()
        default:
            // Do iteration work
        }
    }
    return nil
}

// HTTP handler example
func handler(w http.ResponseWriter, r *http.Request) {
    ctx := r.Context()  // Get context from request
    
    result, err := doSlowOperation(ctx)
    if err != nil {
        if err == context.Canceled {
            // Client disconnected
            return
        }
        http.Error(w, err.Error(), 500)
        return
    }
    
    fmt.Fprint(w, result)
}
```

> 🎯 **Interview Tip:** "How do you handle timeouts in Go?" - Use `context.WithTimeout` and pass the context through your call chain. Always check `ctx.Done()` in long-running operations.

## Common Concurrency Patterns

### Worker Pool

```Go
func workerPool(numWorkers int, jobs <-chan int, results chan<- int) {
    var wg sync.WaitGroup
    
    for i := 0; i < numWorkers; i++ {
        wg.Add(1)
        go func(workerID int) {
            defer wg.Done()
            for job := range jobs {
                results <- process(job)
            }
        }(i)
    }
    
    wg.Wait()
    close(results)
}
```

### Fan-Out, Fan-In

```Go
// Fan-out: distribute work to multiple goroutines
func fanOut(input <-chan int, n int) []<-chan int {
    outputs := make([]<-chan int, n)
    for i := 0; i < n; i++ {
        outputs[i] = worker(input)
    }
    return outputs
}

// Fan-in: merge multiple channels into one
func fanIn(channels ...<-chan int) <-chan int {
    var wg sync.WaitGroup
    out := make(chan int)
    
    for _, ch := range channels {
        wg.Add(1)
        go func(c <-chan int) {
            defer wg.Done()
            for v := range c {
                out <- v
            }
        }(ch)
    }
    
    go func() {
        wg.Wait()
        close(out)
    }()
    
    return out
}
```


--------------------------------------------------------------------------



# 🐞Go Error Handling

## Notes
Go doesn't use try/catch and exceptions to handle errors. Instead, functions return an error as the last return value. The programmer should check this error and handle it explicitly.

**Go's Error Handling Philosophy:**
- Errors are values, not exceptions
- Handle errors where they occur
- Return errors to callers who can handle them appropriately
- Don't ignore errors!

## Example

from "Go by Example"...
```Go
// In Go it's idiomatic to communicate errors via an
// explicit, separate return value. This contrasts with
// the exceptions used in languages like Java and Ruby and
// the overloaded single result / error value sometimes
// used in C. Go's approach makes it easy to see which
// functions return errors and to handle them using the
// same language constructs employed for any other,
// non-error tasks.

package main

import "errors"
import "fmt"

// By convention, errors are the last return value and
// have type `error`, a built-in interface.
func f1(arg int) (int, error) {
  if arg == 42 {

      // `errors.New` constructs a basic `error` value
      // with the given error message.
      return -1, errors.New("can't work with 42")

  }

  // A `nil` value in the error position indicates that
  // there was no error.
  return arg + 3, nil
}

// It's possible to use custom types as `error`s by
// implementing the `Error()` method on them. Here's a
// variant on the example above that uses a custom type
// to explicitly represent an argument error.
type argError struct {
  arg  int
  prob string
}

func (e *argError) Error() string {
  return fmt.Sprintf("%d - %s", e.arg, e.prob)
}

func f2(arg int) (int, error) {
  if arg == 42 {

      // In this case we use `&argError` syntax to build
      // a new struct, supplying values for the two
      // fields `arg` and `prob`.
      return -1, &argError{arg, "can't work with it"}
  }
  return arg + 3, nil
}

func main() {

  // The two loops below test out each of our
  // error-returning functions. Note that the use of an
  // inline error check on the `if` line is a common
  // idiom in Go code.
  for _, i := range []int{7, 42} {
      if r, e := f1(i); e != nil {
          fmt.Println("f1 failed:", e)
      } else {
          fmt.Println("f1 worked:", r)
      }
  }
  for _, i := range []int{7, 42} {
      if r, e := f2(i); e != nil {
          fmt.Println("f2 failed:", e)
      } else {
          fmt.Println("f2 worked:", r)
      }
  }

  // If you want to programmatically use the data in
  // a custom error, you'll need to get the error as an
  // instance of the custom error type via type
  // assertion.
  _, e := f2(42)
  if ae, ok := e.(*argError); ok {
      fmt.Println(ae.arg)
      fmt.Println(ae.prob)
  }
```

## Error Wrapping (Go 1.13+)

Error wrapping lets you add context to errors while preserving the original error for inspection.

```Go
import (
    "errors"
    "fmt"
    "os"
)

// Wrapping errors with %w verb
func readConfig(path string) error {
    _, err := os.Open(path)
    if err != nil {
        // Wrap the error with additional context
        return fmt.Errorf("failed to read config from %s: %w", path, err)
    }
    return nil
}

// Unwrapping and checking errors
var ErrNotFound = errors.New("not found")
var ErrPermission = errors.New("permission denied")

func doSomething() error {
    return fmt.Errorf("operation failed: %w", ErrNotFound)
}

func main() {
    err := doSomething()
    
    // errors.Is - check if error IS or WRAPS a specific error
    if errors.Is(err, ErrNotFound) {
        fmt.Println("Resource not found!")  // This prints
    }
    
    // errors.As - extract specific error type from chain
    var pathErr *os.PathError
    if errors.As(err, &pathErr) {
        fmt.Println("Path error on:", pathErr.Path)
    }
    
    // Unwrap to get the next error in chain
    unwrapped := errors.Unwrap(err)
    fmt.Println(unwrapped)  // "not found"
}
```

> 🎯 **Interview Tip:** Use `errors.Is()` for sentinel errors (like `io.EOF`) and `errors.As()` for error types (like `*os.PathError`). Never use `==` for wrapped errors!

## Panic and Recover

`panic` is for unrecoverable errors. `recover` can catch panics (but should be used sparingly).

```Go
// panic - stops normal execution
func mustParse(s string) int {
    i, err := strconv.Atoi(s)
    if err != nil {
        panic(fmt.Sprintf("invalid number: %s", s))
    }
    return i
}

// recover - catches panics (only works in deferred functions!)
func safeCall() (err error) {
    defer func() {
        if r := recover(); r != nil {
            err = fmt.Errorf("recovered from panic: %v", r)
        }
    }()
    
    riskyOperation()  // Might panic
    return nil
}

// Common pattern: recover at API boundary
func httpHandler(w http.ResponseWriter, r *http.Request) {
    defer func() {
        if err := recover(); err != nil {
            log.Printf("panic recovered: %v\n%s", err, debug.Stack())
            http.Error(w, "Internal Server Error", 500)
        }
    }()
    
    handleRequest(w, r)
}
```

### When to Use Panic vs Error

| Use Panic | Use Error |
|-----------|-----------|
| Programming bugs (nil pointer, index out of range) | Expected failures (file not found, network timeout) |
| Impossible states ("this should never happen") | User input validation |
| Initialization failures (can't start server) | Business logic failures |
| In `init()` functions | Almost everywhere else |

> 🎯 **Interview Tip:** "When should you use panic?" - Only for truly unrecoverable errors or programmer bugs. Use errors for anything that could reasonably happen during normal operation.


--------------------------------------------------------------------------



# 🧬 Go Generics (Go 1.18+)

Generics allow you to write functions and types that work with multiple types.

## Type Parameters

```Go
// Generic function
func Min[T constraints.Ordered](a, b T) T {
    if a < b {
        return a
    }
    return b
}

// Usage
fmt.Println(Min(3, 5))       // 3
fmt.Println(Min(3.14, 2.71)) // 2.71
fmt.Println(Min("a", "b"))   // "a"
```

## Generic Types

```Go
// Generic Stack
type Stack[T any] struct {
    items []T
}

func (s *Stack[T]) Push(item T) {
    s.items = append(s.items, item)
}

func (s *Stack[T]) Pop() (T, bool) {
    if len(s.items) == 0 {
        var zero T
        return zero, false
    }
    item := s.items[len(s.items)-1]
    s.items = s.items[:len(s.items)-1]
    return item, true
}

// Usage
intStack := Stack[int]{}
intStack.Push(1)
intStack.Push(2)
val, _ := intStack.Pop()  // 2

stringStack := Stack[string]{}
stringStack.Push("hello")
```

## Constraints

```Go
import "golang.org/x/exp/constraints"

// Built-in constraints
// any        - any type (alias for interface{})
// comparable - types that support == and !=

// From constraints package
// Ordered    - types that support < > <= >=
// Integer    - all integer types
// Float      - all float types
// Signed     - signed integers
// Unsigned   - unsigned integers

// Custom constraint
type Number interface {
    constraints.Integer | constraints.Float
}

func Sum[T Number](numbers []T) T {
    var sum T
    for _, n := range numbers {
        sum += n
    }
    return sum
}
```


--------------------------------------------------------------------------



# 🧪 Go Testing

## Basic Test

```Go
// math.go
package math

func Add(a, b int) int {
    return a + b
}

// math_test.go
package math

import "testing"

func TestAdd(t *testing.T) {
    result := Add(2, 3)
    if result != 5 {
        t.Errorf("Add(2, 3) = %d; want 5", result)
    }
}
```

Run tests:
```bash
go test              # Run tests in current package
go test ./...        # Run tests in all packages
go test -v           # Verbose output
go test -cover       # Show coverage
go test -race        # Detect race conditions
```

## Table-Driven Tests (Go Idiom!)

```Go
func TestAdd(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        expected int
    }{
        {"positive numbers", 2, 3, 5},
        {"negative numbers", -1, -1, -2},
        {"zero", 0, 0, 0},
        {"mixed", -1, 5, 4},
    }
    
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := Add(tt.a, tt.b)
            if result != tt.expected {
                t.Errorf("Add(%d, %d) = %d; want %d", 
                    tt.a, tt.b, result, tt.expected)
            }
        })
    }
}
```

## Benchmarks

```Go
func BenchmarkAdd(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Add(2, 3)
    }
}
```

Run benchmarks:
```bash
go test -bench=.              # Run all benchmarks
go test -bench=BenchmarkAdd   # Run specific benchmark
go test -bench=. -benchmem    # Include memory allocations
```

## Test Helpers

```Go
// t.Helper() marks function as test helper
// Error messages will show caller's line number
func assertEqual(t *testing.T, got, want int) {
    t.Helper()
    if got != want {
        t.Errorf("got %d; want %d", got, want)
    }
}

// Cleanup for resources
func TestWithTempFile(t *testing.T) {
    f, err := os.CreateTemp("", "test")
    if err != nil {
        t.Fatal(err)
    }
    t.Cleanup(func() {
        os.Remove(f.Name())
    })
    
    // Use f...
}
```


--------------------------------------------------------------------------



# 📦 Go Modules

## Initialize a Module

```bash
go mod init github.com/username/projectname
```

This creates `go.mod`:
```
module github.com/username/projectname

go 1.21
```

## Common Commands

```bash
go mod tidy           # Add missing, remove unused dependencies
go mod download       # Download dependencies to cache
go mod vendor         # Copy dependencies to vendor/
go mod verify         # Verify dependencies haven't changed
go mod graph          # Print dependency graph
go get package@v1.2.3 # Add/update specific version
go get -u ./...       # Update all dependencies
```

## Version Selection

```bash
go get github.com/pkg/errors           # Latest version
go get github.com/pkg/errors@v0.9.1    # Specific version
go get github.com/pkg/errors@latest    # Latest version (explicit)
go get github.com/pkg/errors@master    # Specific branch
```

## go.sum

The `go.sum` file contains cryptographic checksums for dependencies. **Commit both `go.mod` and `go.sum` to version control!**


--------------------------------------------------------------------------



# 🎯 Interview Quick Reference

## Top 10 Go Interview Questions

1. **"Is Go pass-by-value or pass-by-reference?"**
   → Always pass-by-value. Slices/maps/channels contain pointers internally.

2. **"What's the difference between arrays and slices?"**
   → Arrays: fixed size, value type. Slices: dynamic, reference underlying array.

3. **"When would a goroutine leak?"**
   → Blocked forever on channel with no sender/receiver, or waiting on mutex.

4. **"What happens if you send to a closed channel?"**
   → Panic!

5. **"Explain `new` vs `make`"**
   → `new`: allocates zeroed memory, returns pointer. `make`: initializes slice/map/channel, returns value.

6. **"How do you handle errors in Go?"**
   → Return error as last value, check for nil, wrap with context using %w.

7. **"What's the empty interface?"**
   → `interface{}` or `any`. Can hold any type. Use type assertions to extract.

8. **"Explain Go's scheduler"**
   → M:N threading. Goroutines (G) run on OS threads (M) managed by processors (P). GOMAXPROCS sets P count.

9. **"Why doesn't Go have inheritance?"**
   → Favors composition over inheritance. Use embedding for code reuse without tight coupling.

10. **"How do you prevent race conditions?"**
    → Channels (preferred), sync.Mutex, sync.RWMutex, atomic operations. Use `go run -race`.
