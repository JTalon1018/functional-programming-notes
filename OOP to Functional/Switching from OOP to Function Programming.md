[Article](https://medium.com/@olxc/switching-from-oop-to-functional-programming-4187698d4d3)

Contents:
1. [[#No Classes]]
2. [[#All you need is a function]]
3. [[#No, you cant change a variable]]
4. [[#No, you can't do 'for' loops]]
5. [[#Your code is not a list of instructions anymore]]
6. [[#On `null` and exceptions]]
7. [[#Functors, Monads, Applicative]]

---
## No Classes 

a program is just a collection of functions in FP that have [[some properties to them]] that also need to be [[composed]]. `composition` is a core concept to FP it will come up a lot.
FP is all about a bungle of functions that call each other.

FP has a notion of [[Online Articles#^cff258|Class Type]] but it shouldn't be confused with OOP classes. Their purpose is to create polymorphism.

In OOP a class can hold a value and that value can be changed by interacting with a property or using a function to interact with it. In FP we dont have that we have ADT(Algebraic Data Types) fancy for "record that holds data". They operate as a class that ONLY has a constructor no properties or methods. you CAN NOT make changes in place (overwrite an existing value in a class) this called a mutation you can only make a copy of an object

ADT Has 2 types product and sum.
Product Type:
- A collection of fields. ALL have to be specified to construct the type.
Sum Type:
- optional object class type. Think of an interface or an inherited class maybe
- they are hierarchical as well meaning a Sum can hold a sum or a product
Using these 2 you can create functions with optional outcomes:

>  Product types can be deconstructed and statically checked while sum types can be used for pattern matching

```scala
sealed trait Shape

case class Circle(radius: Int) extends Shape

case class Rectangle(width: Int, height: Int) extends Shape

// 'match' keyword allows us to pattern match on a 
// specific option of the sum type
def area(shape: Shape): Double = shape match {
  case Circle(r) => math.Pi * r * r
  case Rectangle(w, h) => w * h // Note how rectangle's width and height 
                                // are captured in 'w' and 'h'. It's possible
                                // because Rectange is a product type
}
```

---
## All you need is a function

No more getter/setter/constructor/static/etc. a function is a function in FP.  No instantiating because there are no classes you just call the function.

functions should have 3 main properties:
1. Pure:
	- No Side effects it cant do more then what its definition says. if it takes in a `int` and returns an `int` thats it no changing global variables, accessing files, network requests.
	- The input can be transformed and thats it
2.  Total:
	- Returns values on all inputs. Functions have definitions that are strict if im returning an exception the parent function is prepared with a response to that expected failure. any potential input of any kind has an expected output.
3. Deterministic:
	- No mater what always return the same value for the same input. Ex i there is a dependency on time or time zone then that function is not deterministic.

Need to look into how these interact with C# scala and haskell have very different reactions to this information.

These concepts create ["referential transparency"](https://medium.com/@olxc/referential-transparency-93352c2dd713), meaning you can look at a function and now what its going to do no need to worry about because RT guarantees you nothing will break. Unlike OO where you need to run full instruction scenarios with different objects to make sure theres no particular issue.

---
## No, you cant change a variable

Variables can NOT be reassigned to new ones. you can create a copy of that variable with different values. variables may be able to be changes in place however it breaks RT which is the key to FP. this also means no `var` are available or rather its best not to use them because it will hurt RT.

---

## No, you can't do 'for' loops

At first i didnt understand this but its referring to literal `for (var i = 0; i < 10; i++)` for loops. The main way to iterate through things seems to be recursion and maybe `foreach` loops if you do it without manipulating the values inside, however the recommendation is to use LINQ 

Example a program the 'recursively' looks through a list and adds each number:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

class Program
{
    static void Main()
    {
        List<int> numbers = new List<int> { 1, 2, 3, 4, 5 };
        int result = numbers.Aggregate((x, number) => x + number);
        Console.WriteLine("The sum is: " + result);
    }
}
```

same example but with actual functional recursion that can implement guardrails:

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        List<int> numbers = new List<int> { 1, 2, 3, 4, 5 };
        int result = Sum(numbers);
        Console.WriteLine("The sum is: " + result);
    }

    static int Sum(List<int> numbers)
    {
        return SumRecursive(numbers, 0);
    }

    static int SumRecursive(List<int> numbers, int index)
    {
        if (index >= numbers.Count)
        {
            return 0;
        }
        return numbers[index] + SumRecursive(numbers, index + 1);
    }
}
```

a key concept may be `map`  im not sure how that relates to c# but it could be a useful for iterating though an object.

---
## Your code is not a list of instructions anymore

oh boy... so OO offers a list of instructions that usually result in the mutation of a variable of some kind. 

traditional OO/ imperative language:
```csharp
object MyProgram extends App {
  doThis()
  doThat()
  doSomethingElse()
  printResult()
}
```

functional style:
```csharp
object MyProgram extends App {
  unsafeRun(             //       <-- This guy takes your pure program 
                         //           and actually runs it
    printResult(         // --
      doSomethingElse(   //   | 
        doThat(          //   |
          doThis()       //   | This is your 'pure' part, nothing happened yet
        )                //   |
      )                  //   |
    )                    // --
    
  )
}
```

---
## On `null` and exceptions

By following RT the concept of null is unnecessary because the expected response should be pure  a pure program that represents absence but the value should be explicitly unknown. Throwing exceptions should only happen if we have no choice it makes a function partial and not total.

---
## Functors, Monads, Applicative

> People have discovered general patterns and gave them names from category theory. Functors, Monads and Traversables are pretty powerful and common abstractions, you will see them everywhere. It’s probably a topic for an article on its own. But for now— don’t worry about it. You will learn about them eventually (or maybe even re-invent them yourself). Get comfortable with function composition, higher order functions and polymorphic functions. Then read about [type classes](https://medium.com/@olxc/type-classes-explained-a9767f64ed2c). After that Functors and Monads should come naturally. The takeaway here is that there is no magic and there isn’t much more to it than we have already discussed in this article — pure functions and function composition.