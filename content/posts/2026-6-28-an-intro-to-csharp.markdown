---
layout: post
title:  "An Intro to C#"
date: 2026-06-28
---
`NOTE: Everything is written in simple and short words — like a student's notes.`

## Declaring variables of different types:
```csharp
// type variablename = value; 
int num;
string s;
bool b;
char ch;
double dub;
float fl;
decimal dc;
// arrays are of fixed length and can be provided a size during initialisation. 
// Compiler infers the size if you do not provide it. 
int[] arr; 
List<int> nums; // List<type> varname
Dictionary<string, int> dict;  // Dictionary<keyType, valueType> varname;
```

## Initialising variables of different types. 
```csharp
// Syntax: 
// Either `var varname = value;` — here type is inferred by the compiler 
// Or `type varname = value;`

var num1 = 1;
int num2 = 2;

var dub1 = 1.99;
double dub2 = 2.99;

var fl1 = 1.99f; // for float 'f' or 'F' suffix must be provided at the end of the value.
float fl2 = 2.99f;

var dc1 = 1.99m;
decimal dc2 = 2.99m; // for decimal 'm' or 'M' suffix must be provided at the end of the value.

var ch1 = 'A';
char ch2 = 'B';

var s1 = "first one";
string s2 = "second one";

var b1 = true;
bool b2 = false;

// Ways to Initialise an array:

// NOTE: Some of the other ways to initialise an array, a list, and a dictionary have not been added here.
// Only standard and recommended ways have been added.

// recommended way for legacy projects
int[] arr1 = { 1, 2, 3 }; // creates an array with values {1, 2, 3}

// recommended for new projects
int[] arr2 = [1, 2, 3]; // use square brackets; creates an array with values [1, 2, 3].

// Ways to initialise a list:

List<int> nums1 = [1, 2, 3]; // modern syntax: c# 12+
// traditional standard: using var keyword
var nums2 = new List<int>() { 1, 2, 3 }; 


// Ways to initialise a dictionary:
// classic way:
var dict1 = new Dictionary<string, int> {
    {"user1", 1},
};

// Recommended way:
var dict2 = new Dictionary<string, int>
{
    ["user1"] = 1,
};
```


## Constants
```csharp
// const type varname = value;
const int num = 5;
```

## OOP
In short words, Object-oriented programming (OOP) is a way of designing software around data, or objects, rather than functions and logic. 

```csharp
using Finance;

namespace Notes; // namespace is like a declaration of name for this package.


class Cheatsheet
{
    // Main method; methods behave similar to functions from other languages. 
    static void Main() // static keyword: this method belongs to the class itself, and not an instance.  
    {

        var c = new Car("toyota", 2022, "Dean", 5000);
        var c2 = new Car("nissan", 2020, "Noah", 4000);

        Console.WriteLine(c.Brand); // toyota
        Console.WriteLine(c.EofySale); // false
                                       // Console.WriteLine(c.EofySale); // you can do this way too, if you have created a property. 

        Console.WriteLine(c.PriceAfterDiscount()); // 4500

        // call static method for Car.
        Console.WriteLine(Car.CarInstances()); // 2

        // call another namespace 
        Console.WriteLine(FinanceOptions.IsAvailable(c2.CustomerName)); // true 
        Console.WriteLine(FinanceOptions.s_customers[0]); // here, internal is accessible because they are in same assembly (project), 
                                                          // even though they have different namespace.

        // c.EofySale = true; // This will throw an error as only get method have been added for this member.
        // Console.WriteLine(c.CarInstances()); // this will fail: static methods can only be invoked on the Class itself.

        // call static method for Car
        Console.WriteLine(Car.CarInstances()); // 3

    }
}


// class and objects.
class Car
{
    public bool EofySale { get; } // syntatic sugar to allow get method only.

    public string Brand; // public keyword (also called an access modifier) makes a field accessible from outside.
    public int Year;
    public int Cost;
    public string CustomerName;
    private decimal _discountPercent = 10.00m; // private: can only be accessed within the same class, not accessible from an instance. 

    static int s_noOfInstances = 0; // fields are private by default, cannot be accessed outside of the class. 

    // Constructor
    public Car(string brand, int year, string customerName, int cost)
    {
        this.Brand = brand; // this keyword refers to the values for this specific instance/object.
        this.Year = year;
        this.CustomerName = customerName;
        this.EofySale = false;
        this.Cost = cost;
        s_noOfInstances++;
    }

    public static int CarInstances() // static keyword: this method belongs to the class itself, and not an instance.  
    {
        return s_noOfInstances;
    }

    public decimal PriceAfterDiscount()
    {
        return this.Cost - ((this._discountPercent / 100) * this.Cost);
    }
}
```


```csharp
// Finance.cs
namespace Finance;


class FinanceOptions
{
    internal static string[] s_customers = { "John", "Aki", "Noah" }; // internal: accessible anywhere within this assembly.

    public static bool IsAvailable(string customerName)
    {
        foreach (string c in s_customers)
        {
            if (c == customerName)
            {
                return true;
            }
        }
        return false;
    }
}

```

## A note on access modifiers:
```
public: code is accessible for all classes
private: code is only accessible within the same class  —  not accessible from an instance.
protected: code is only accessible within the same class, or in a class that inherits this class. 
internal: code is accessible within its own assembly (project). 
	     field with "internal" can be used throughout this project, even though they maybe of different namespaces.  
	     Example: If you have install a package which has internal field, you cannot access that field from your project. 
static: code belongs to the class itself, and not an instance.  

Encapsulation: 
In C#, usually "private" keyword and properties are used to achieve 
encapsulation — encapsulation means hiding sensitive data from the users, 
and protecting the integrity of an object's internal state.  
```

## C# Naming Conventions:
- Public methods, fields, and classes: PascalCase
- Private methods: camelCase
- Private fields: _camelCase
- local variables, parameters: camelCase
- Interface: IPascalCase (Note: Letter 'I' is required at front)
- static fields: s_camelCase
- static methods: camelCase



## Inheritance:
Inheritance is a way to inherit fields and methods from parent class (base class) to child class (derived class). In C#, this is achieved using the single colon ":" symbol. 

```csharp
// UsedCar inherits the parent class "Car"

class UsedCar : Car
{
    public int WarrantyYears { get; } // read-only field for anyone accessing this field outside this class.

    // base passes the arguments for these parameters to the parent class's constructor. 
    public UsedCar(string brand, int year, string customerName, int cost) : base(brand, year, customerName, cost)
    {
        if (cost > 5000)
        {
            this.WarrantyYears = 3;
        }
        else
        {
            this.WarrantyYears = 1;
        }
    }
}


// Use sealed keyword to not allow the class to be inherited. 
sealed class Person
{
}

// This will fail: no class can inherit from Person. 
// class Student: Person {
// }

```


## Polymorphism:
Polymorphism means "many forms". 
In programming, this mainly refers to methods of inherited classes with same name as parent's method, but different behaviours. 


```csharp
// Example without polymorphism: 

namespace Notes;

class Cheatsheet
{
    static void Main()
    {
        Device dv = new Device();

        // Here, variable mp's reference type is "Device". 
        // Even though it's actual object type is "Mobile", 
        // when the method with same name "getInfo" is called, C# uses the parent's method. 
        // This is C#'s hiding behaviour. 
        // Polymorphism is used in the next paragraph to solve this problem.
        Device mp = new Mobile();

        Console.WriteLine(dv.GetInfo()); // I am a device
        Console.WriteLine(mp.GetInfo()); // I am a device 

    }
}

class Device
{
    public string GetInfo()
    {
        return "I am a device";
    }
}

class Mobile : Device
{
    public string GetInfo()
    {
        return "I am a mobile phone";
    }
}
```


```csharp
// Example with polymorphism:
// In C#, polymorphism is achieved using `virtual` keyword on the method inside the base class, 
// and 'override' keyword on the method with same name in the derived class. 

namespace Notes;

class Cheatsheet
{
    static void Main()
    {
        Device dv = new Device();
        Device mp = new Mobile();

        Console.WriteLine(dv.GetInfo()); // I am a device
        Console.WriteLine(mp.GetInfo()); // I am a mobile phone

    }
}

class Device
{
    public virtual string GetInfo() // virtual keyword in the base class's method.
    {
        return "I am a device";
    }
}

class Mobile : Device
{
    public override string GetInfo() // override keyword in the derived class's method. 
    {
        return "I am a mobile phone";
    }
}

```
## Abstraction:
In simple words, abstraction is about hiding complexity from the user.

Example: Let us say "stripe" provides a method charge that charges the amount from user when card and amount are passed as arguments. 
stripe(card, amount)

Here, when we call this method, we don't know `(and don't have to know)` all internal things such as fraud checks, http calls, etc. 
This is an example of abstraction. 

```
Difference between encapsulation and abstraction:Encapsulation is about hiding what's inside, 
while abstraction is about how things are done. 
```

In C#, abstraction can be achieved primarily by two ways: 
- Using "abstract" keyword is used for classes and methods. 
- Using interface.


In C#, an object/instance cannot be created from an abstract class. 

Methods with abstract keyword can only live inside an abstract class. 
Abstract methods cannot have a body, and the derived class that inherits from the abstract class 
overrides the abstract method, and provides a body. 

Example using "abstract" keyword:


```csharp
namespace PaymentFlow;

class Payment
{

    public static void Main()
    {
        PaymentType pp = new PayPal();
        PaymentType ap = new Afterpay();

        Console.WriteLine(pp.Checkout()); // You are now paying via PayPal
        Console.WriteLine(ap.Checkout()); // You are now paying via Afterpay


        // NOTE: Polymorphism is occuring here.
        // The reference type is "PaymentType" and actual object types are "PayPal" and "Afterpay". 
        // The method "Checkout" of the parent class gets overriden by the "Checkout" of the derived class. 
    }
}

abstract class PaymentType
{
    // abstract methods cannot have a body.
    public abstract string Checkout();
}

class PayPal : PaymentType
{
    // Abstract method gets override on the derived class. 
    public override string Checkout()
    {
        return "You are now paying via PayPal";
    }

}

class Afterpay : PaymentType
{
    public override string Checkout()
    {
        return "You are now paying via Afterpay";
    }
}
```

## Interface
Interface is a completely abstract class, whose members are abstract and public by default. 
It can only contain methods and properties with empty body — not fields.

```csharp
namespace OrderingSystem;

class Order
{

    public static void Main()
    {
        var dl = new Delivery();
        var cc = new ClickAndCollect();

        Console.WriteLine(dl.Order());
        Console.WriteLine(cc.Order());
        Console.WriteLine(cc.Address());
    }
}


interface IOrderType // Good practice to add 'I' at the beginning of an interface's name.
{
    string Order(); // methods/properties are abstract and public by default. 
}

interface IAddress
{
    string Address();
}


// Use the : colon to implement an interface (just like inheriting)
class Delivery : IOrderType
{
    public string Order() // No need to add "override" keyword on methods of the class that implements an interface. 
    {
        return "Your order will be delivered within 3 business days.";
    }
}


class ClickAndCollect : IOrderType, IAddress // Multiple interfaces can be implemented by using ','
{
    public string Order()
    {
        return "Please allow up to 4 hours for your Click & Collect order to be processed.";
    }

    public string Address()
    {
        return "Your order will be ready to collect from CS Warehouse Anewol";
    }
}
```

## enums 
```csharp
enum Phone
{
    Used,
    New,
    Refurbished
}
```


## yield
C# has "yield" keyword which can be used to temporarily pause a function, and return a value to the caller. 


```csharp
namespace YielderRunner;

class Yielder
{
    public static void Main()
    {
        foreach (int num in GenSingDigits())
        {
            if (num == 100)
            {
                break;
            }
            Console.WriteLine(num);
        }

    }

    public static IEnumerable<int> GenSingDigits()
    {
        yield return 0;
        yield return 1;
        yield return 2;
        yield return 100;
    }
}
```


## Task, async, await, record

- In C#,`Task` represents an asynchronous operation, similar to promise concept in js. 
- `record`: a shorthand to get a type that holds data, nothing else. Pairs really well with 
JSON deserialisation, returning a record instead of returning multiple values,etc.

```csharp
namespace ExampleAsync;

using System.Net.Http.Json;

class AsyncAwaitExample
{
    public static async Task Main()
    {
        var ip = await MyIp();
        Console.WriteLine(ip);
    }

    public static HttpClient client = new()
    {
        BaseAddress = new Uri("https://api.ipify.org/?format=json"),
    };

    public static async Task<string> MyIp()
    {
        try
        {
            var ip = await client.GetFromJsonAsync<IP>("");
            if (ip != null)
            {
                return ip.ip;
            }
            return "No IP returned";
        }
        catch (HttpRequestException error)
        {
            return error.Message;
        }
    }

}

public record IP(string ip);
```


## Delegates/Action/Func
- A delegate is a type that safely encapsulates a method.
- A delegate can be a variable that holds a method. 
- Once in a while, you might need to send a method as a parameter to another method, and that is 
one of the scenarios where you will need delegates. 

`NOTE: Create/Use a delegate when you want the caller to inject the behaviour from outside. `


An example where a delegate is a variable that holds a method. 

```csharp
public delegate void Log(string s); // use "delegate" keyword.

class Logger
{
    public static void Main()
    {
        Log logHere = Println; // Here the delegate "logHere" is holding a method "println".
        logHere("Hello world"); // And executing the "println" here.  
    }

    public static void Println(string msg)
    {
        Console.WriteLine(msg);
    }
}


// An example where a method is sent as a parameter to another method. 
```

An example where a delegate holds multiple methods. 
public delegate void Remind(string s);

```csharp
class Reminder
{
    public static void Main()
    {
        Remind r = RemindViaMessage; // This delegate holds RemindViaMessage.
        r += RemindViaEmail; // appending a method to this delegate.
        r += RemindViaNotification; // appending another method to this delegate.

        r("Go to Csharp Meetup");
        // Output:
        //Sending a message: Go to Csharp Meetup
        // Sending an email: Go to Csharp Meetup
        // Sending a notification: Go to Csharp Meetup
    }

    public static void RemindViaMessage(string s)
    {
        // simulate sending a reminder via message. 
        Console.WriteLine($"Sending a message: {s}");
    }

    public static void RemindViaEmail(string s)
    {
        // simulate sending a reminder via email. 
        Console.WriteLine($"Sending an email: {s}");
    }

    public static void RemindViaNotification(string s)
    {
        // simulate sending a reminder via email. 
        Console.WriteLine($"Sending a notification: {s}");
    }
}
```

An example where a method is sent as a parameter to another method. 
```csharp
public delegate void PrintFunction<T>(IEnumerable<T> value);

class Printer()
{
    public static void Main()
    {
        var nums = new List<int> { 1, 2, 3 };
        Print(PrintHorizontal, nums);
        Print(PrintVertical, nums);
    }

    public static void Print<T>(PrintFunction<T> fn, IEnumerable<T> value)
    {
        fn(value);
    }

    public static void PrintHorizontal<T>(IEnumerable<T> value)
    {
        foreach (var v in value)
        {
            Console.Write($"{v} ");
        }
    }

    public static void PrintVertical<T>(IEnumerable<T> value)
    {
        foreach (var v in value)
        {
            Console.WriteLine(v);
        }
    }
}
```



- Action: Action is a built-in delegate that takes in 0-16 parameters but doesnot return a value. 
- Func: Funce is a built-in delegate that that takes in 0-16 parametes and returns a value. 

```csharp
namespace Delegation;

class Example
{
    public static void Main()
    {
        Action<int> log = (s) => Console.WriteLine(s);
        log(5);

        // Here, the last int before ">" represents the return type. 
        Func<int, int, int> add = (a, b) => a + b;
        log(add(2, 3));
    }
}
```

## Extension method:
Extension method enables you to add new methods to an existing type without creating a derived type or modifying the original type. 
Three rules to follow when creating an extension method:
1. class using the extension method must be declared as "static" — static classes cannot instantiated or inherited. 
2. The method itself must be declared static. 
3. The data type you want to extend must have "this" keyword before writing that type. 


Simplest example to show an extension to an "int" type.
```csharp
class Program
{
    public static void Main()
    {
        var num = 4;
        Console.WriteLine(num.IsEven()); // true
    }
}

// 1. class must be static.
static class ExtensionMethods
{
    // 2. method must be static.
    public static bool IsEven(this int n) // 3. this keyword infront of the type is necessary.
    {
        return n % 2 == 0;
    }
}
```


## Cleaning up resources such as an opened file, database connection, etc:

C# provides an interface "IDisposable" with a method "Dispose" that can be implemented on 
a class to safely cleanup the resources — if your class manages native resources. 

When using built-in class that manages resources, you should use the `using` statement. 
This will make sure "Dispose" method gets called before this method/block returns. 

Example of `using` statement to manage resource:


```csharp
namespace Reader;

class File
{
    public static void Main()
    {
        try
        {
            // Read a file line by line. 
            using var reader = new StreamReader("input.txt"); // use "using" statement"
            {
                while (true)
                {
                    var line = reader.ReadLine();
                    if (line != null)
                    {
                        Console.WriteLine(line);
                    }
                    else
                    {
                        break;
                    }
                }
            }
        }
        catch (Exception e)
        {
            Console.WriteLine(e.Message);
        }
    }
}
```



## Dependency Injection
Example: 
Instead of creating a new instance of a database for each controller, you inject the shared instance to all the controllers. 

```csharp
namespace DI;

class DInjection
{
    public static void Main()
    {
        var db = new DB();
        db.PrintUsers(); // Luke, Noah

        var uc = new UserController(db);
        uc.AddUser("Abraham");
        db.PrintUsers(); // Luke, Noah, Abraham 
    }
}


class DB
{
    public List<string> Users { get; set; }

    public DB()
    {
        Users = new List<string> { "Luke", "Noah" };
    }

    public void PrintUsers()
    {
        foreach (var user in Users)
        {
            Console.WriteLine(user);
        }
    }
}

class UserController
{
    private DB _db; // underscore added in variable name to identify it as a private member. 

    public UserController(DB db)
    {
        _db = db;
    }

    public void AddUser(string name)
    {
        _db.Users.Add(name);
    }
}
```
