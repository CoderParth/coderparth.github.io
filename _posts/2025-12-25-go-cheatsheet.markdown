---
layout: post
title:  "Go Cheatsheet"
date:   2025-12-25 
---

Below you will find a quick, and short cheatsheet, written with simple examples, and easy to understand words.  

### Declaring variables of different types:  
{% highlight go %}
var i int
var s string
var b byte
var bo bool         // Boolean: true or false
var ch rune         // Go's equivalent of a single character.
var sSlice []string // This creates a slice of strings.
var sArr [3]string  // This creates an array with a length of 3 strings.
var mp map[string]int // Creates a map with string type as key, and int type as value.
{% endhighlight %}

**Note:** For the map declared above, when doing write operations, Go panics: `panic: assignment to entry in nil map`. So an initialization is required before the first write.

Either `mp = map[string]int{}` or `mp = make(map[string]int)` will work.

#### Constants  
{% highlight go %}
const n = 20 // Go supports untyped constants.
const num int = 5
const str string = "const"
const st = "this is a string"
{% endhighlight %}

#### Declaration/Initialisations that are only valid inside a function.  

{% highlight go %}
func declare() { // All functions start with "func" keyword.
	str := "" // This way of initialization is only valid inside a function.
	num := 3
	isTrue := true
	c := 'a'                    // creates a rune
	s := []string{}             // creates a slice of string type.
	hashMap := map[string]int{} // Creates a map of string as key, and int as value.
}
{% endhighlight %}

**Note:**  In Golang, all declared variables must be used, else compiler will throw an error.  
Example of the error: `declared and not used: str`

### Functions  
In Golang, functions are declared using the `func` keyword. Below `a` and `b` are parameters with an `int` type.
{% highlight go %}
func add(a int, b int) int { // int indicates the type of the return value.
	// function parameters of the same type can also be written like this:
	// func add(a , b int) int {
	return a + b
}

func nums() (int, int) { // A way to indicate multiple return values/types.
	return 3, 4
}
{% endhighlight %}

**NOTE:** Function names that start with lowercase (example: func add()) act like private methods of a package. And function names that start with an uppercase (example: func Add()) act like public methods, and are automatically exported.

### Variadic functions  
Variadic functions can accept a variable number of arguments.
In Go, three dots(...) followed by a type, is used as a parameter to signify a variadic function.

{% highlight go %}
func variadic(nums ...int) {
	sum := 0
	for _, n := range nums {
		sum += n
	}
	fmt.Println(sum)
}

variadic(3, 4, 5)
variadic(1,2)
{% endhighlight %}

### Defer  
An important concept/keyword to understand when learning functions in golang is the concept of "defer".
`defer` works quite similar to what it means: it puts off an event to a later time.
`defer` runs just before the function returns.

{% highlight go %}
func deferExample() {
	defer fmt.Println("print this later") // prints this later, when the function is about to return.  
	fmt.Println("print this now")         // prints this first.
}
{% endhighlight %}

### Slice methods  

{% highlight go %}
func sliceMethods() {
 	arr := []int{1, 2, 3, 4}
 	arr = append(arr, 5) // appending to a slice
 	val := arr[0]        // getting the value at 0th index
 	arr[2] = 4           // setting the value as 4 at 2nd index of the slice.

 	// Create a new slice from a specific range of the original slice.
 	newSlice := arr[0:2]    // Here, 0 is start index, 2 is end index. Value at 2nd index is not included.
 	length := len(newSlice) // len() gives the length of the slice.
}
{% endhighlight %}

### Map methods  

{% highlight go %}
func mapMethods() {
 	mp := map[string]int{}
 	mp["a"] = 1               // set a key/value
 	if v, ok := mp["a"]; ok { // checks if the key exists.
 		// do something with the value.
 	}
 	delete(mp, "a")   // deletes "a" from the map "mp"
 	clear(mp)         // deletes all entries from the map.
 	length := len(mp) // Total number of entries in the map.
}
{% endhighlight %}

### Loops  
In Golang, loops are done using the `for` and `range` keyword
{% highlight go %}
func loops() {
	for i := 0; i < 5; i++ {
		// do something
	}

	n := 5
	for range n { // range lets you loop over things easily.
		// do something
	}

	// An infinite loop, similar to "while" from other languages.
	for {
		if n > 5 {
			break
		}
	}

	arr := []int{1, 2, 3, 4}
	for idx, val := range arr {
		// here idx is an index of the current item in arr.
		// val is the value at that index.
	}

	mp := map[string]int{
		"a": 0,
		"b": 1,
	}

	for k, v := range mp {
		// k: key, v: value
	}
}
{% endhighlight %}

### Conditionals: 
#### if/else statements

{% highlight go %}
func conditionals() {
	a := 0
	b := 1
	if a < b {
		// do something
	} else if b > a {
		// do something
	} else {
		// do something
	}
}
{% endhighlight %}

#### Switch/case  

{% highlight go %}
func cases() {
	a := 5
	switch a {
	case 6:
	// do something if a == 6
	case 7:
	// do something if a == 7
	default:
	// by default do this, if other cases do not match.
	}
}
{% endhighlight %}

### Pointers and references

{% highlight go %}
func pointer() {
	num := 4
	p := &num
	fmt.Println(p) // prints the memory address of num.
}

func changeValue() {
	num := 4
	passWithoutPointerAndChange(num)
	fmt.Println(num)               // still prints 4
	passWithPointerAndChange(&num) // This changes the value to 5.
	// When passed with the pointer, it modifies the value at that address.
	fmt.Println(num) // now prints 5

	// Same applies to other data types.
	arr := []int{}
	tryChangingArrWithoutPointer(arr)
	fmt.Println(arr) // prints an empty slice [].
	tryChangingArrWithPointer(&arr)
	fmt.Println(arr) // prints [3].
}

func passWithoutPointerAndChange(num int) {
	num = 5 // change is made to the copy, not the original
}

func passWithPointerAndChange(num *int) {
	// * is used infront of num to dereference the pointer.
	// In simple words, go to the memory address stored in num,
	// and give the value that lives there. In this case, 4. 
	*num = 5 // modifies the value at that address.
}

func tryChangingArrWithoutPointer(arr []int) {
	arr = append(arr, 3) // append is used this way in golang.
}

func tryChangingArrWithPointer(arr *[]int) {
	*arr = append(*arr, 3) // append is used this way in golang.
}
{% endhighlight %}

### Struct  
Struct, in easy words, is a way to create your own type with multiple fields.

{% highlight go %}

type Card struct {
	Name   string
	Number string
	CCV    int
}

// Initialize Card.
c := &Card{}

// This is a way to create a method for a struct in Golang.
// Go does not have classes.
func (c *Card) Add() {
	c.Name = "Rob Kim" // struct fields are accessed using a dot
	c.Number = "123456890"
	c.CCV = 532
}

// This can be called like this:
c.Add() // Calls Add method on the card created above.
{% endhighlight %}

### Interface
In simple words, an interface is a contract that says: any type that has these methods automatically satifies this interface type.
In golang, there is no explicit declaration for interfaces.

Below is a simple "Animal" example to showcase interface:

{% highlight go %}
type Animal interface {
	Speak()
}

type Cat struct {
	Name string
}

func (c *Cat) Speak() {
	fmt.Printf("Meow says %s\n", c.Name)
}

type Dog struct {
	Name string
}

func (d *Dog) Speak() {
	fmt.Printf("Woof says %s\n", d.Name)
}

func main() {
	c := &Cat{
		Name: "Zing",
	}

	d := &Dog{
		Name: "Max",
	}
	animals := []Animal{c, d}   // Here, both Cat and Dog have the same method Speak(), which is a method of the Animal interface.
	for _, a := range animals { // The types Cat and Dog satisfy the Animal interface type.
		a.Speak()
	}
}
{% endhighlight %}

### Generics  
Generics allows you to write functions or data structures that work with any data type. 

Simple example:
{% highlight go %}
func printSlice[T any](s []T) {
	for _, v := range s {
		fmt.Println(v)
	}
}

// Now you can call this generic function printSlice with any data type. 
nums := []int{1, 2, 3, 4}
printSlice(nums)

s := []string{"a", "b", "c"}
printSlice(s)
{% endhighlight %}


### Concurrency/Goroutines:
`Goroutines` are a way to handle concurrent tasks in Go. It allows multiple tasks to be executed at the same time without blocking each other. 

{% highlight go %}
func add() {
	time.Sleep(2 * time.Second)
	fmt.Println("Adding completed")
}

func subs() {
	time.Sleep(2 * time.Second)
	fmt.Println("Subtracting completed")
}

go add()
go subs()
fmt.Println("I could be doing something else while my goroutines are executing concurrently")
time.Sleep(3 * time.Second) // This is to not let the main function return immediately. 
{% endhighlight %}
**NOTE:** In a real case, if the above code did not have `time.Sleep(3 * time.Second)` on the last line, the main function would return immediately after printing the last line, 
instead of waiting for all the goroutines to finish. To deal with this, either channels, or a sync package is generally used in real case scenarios. 

#### Channels  
In easy to understand words, a channel is like a pipe through which multiple goroutines can share data, without worrying about mutex locks, or deadlocks. 

{% highlight go %}
func main() {
	ch := make(chan int, 2) // Makes a channel of buffer length 2, that can hold data of type int.
	// ch := make(chan int) // You could write this too. This would create a channel without any specific length/size.  
	go add(5, 4, ch)
	go add(2, 5, ch)

	res1 := <-ch // receive the value from the channel, and assign it to res1. The program waits here, until it receives the value.
	res2 := <-ch // receive the value from the channel

	fmt.Println(res1)
	fmt.Println(res2)
}

func add(a, b int, ch chan int) {
	time.Sleep(2 * time.Second)
	ch <- a + b // send the value to the channel
}
{% endhighlight %}

### Mutex  

Mutex is a mutual exclusion lock. 

Example use case: Mutex is generally used to update a shared slice across multiple goroutines. 

Mutex, and other items from the sync package are generally used together. 
These include: WaitGroup and its methods. 
This is a way in Go to avoid deadlocks, and race conditions. 

A simple example is shown below:

{% highlight go %}
import (
	"fmt"
	"sync"
	"time"
)

type Res struct {
	values []int
	mu     sync.Mutex // To ensure only one goroutine access this resource at a time.
}

func NewRes() *Res {
	return &Res{
		values: []int{},
	}
}

func main() {
	var wg sync.WaitGroup // A way to wait for all goroutines to finish their tasks.

	r := NewRes()
	wg.Add(2) // Add goroutines to the wait group.
	go add(5, 4, r, &wg)
	go add(1, 3, r, &wg)
	wg.Wait() // Wait for all the goroutines to finish their tasks.
	for _, v := range r.values {
		fmt.Println(v)
	}
}

func add(a, b int, r *Res, wg *sync.WaitGroup) {
	defer wg.Done() // Mark this goroutine as done, when this function is about to returns.
	time.Sleep(2 * time.Second)
	sum := a + b
	r.mu.Lock() // Lock this resource.
	r.values = append(r.values, sum)
	r.mu.Unlock() // Unlock this resource.
}
{% endhighlight %}
