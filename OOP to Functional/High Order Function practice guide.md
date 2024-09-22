[Link](https://samwalpole.com/blog/a-practical-guide-to-higher-order-functions-in-c/index.html)

This article is a two quick high order function concepts to help represent the concept using a function as either a input or an output

Example one

```csharp
List<TOut> Map<TIn, TOut>(List<TIn> list, Func<TIn, TOut> mapper)
{
    var newList = new List<TOut>();
    foreach (var item in list)
    {
        var newItem = mapper(item);
        newList.Add(newItem);
    }

    return newList;
}

// usage
var myList = new List<int> { 1, 2, 3, 4, 5 };

int multiplyBy2 (int num) => num * 2;
var multipliedList = Map(myList, multiplyBy2);
// output { 2, 4, 6, 8, 10 };

bool isEven (int num) => num % 2 == 0;
var isEvenList = Map(myList, isEven);
// output { false, true, false, true, false };
```

the `Map` function is the main focus here its defining several things in detail for a function definition.

`List<TOut> Map<TIn, TOut>(List<TIn> list, Func<TIn, TOut> mapper)`

`List<TOut>`
- the function outputs to a list of a generic type 

`Map<TIn, TOut>` 
- the function  defines the input and output types. notice there are more then 1 argument and doesn't define them specifically in the function definition

`List<TIn>, Func<TIn, TOut> mapper`
* the 2 parameters the function intakes.
* One is a function making this a high order function


the function that is passed through the `Map` function is a LINQ function.

`int multiplyBy2 (int num) => num * 2;`
- the return and input type in `int`   this simple LINQ function multiplies what ever number is passed in by 2.

---
Example 2:

the second example is based on the use case of output functions. its purpose is to demonstrate that the output of a function can be reused based on the filtering of the input.

```csharp
public interface IProductRepository
{
    int Create(Product product);
    bool Update(Product product);
    bool Delete(int id);
    Product GetById(int id);
    IEnumerable<Product> GetAll();
    IEnumerable<Product> GetByCategoryId(int categoryId);
    IEnumerable<Product> GetActive();
    // etc...
}
```

above is a series of interfaces each with "different" outputs but in reality there are 3 with the same output and the only change are the inputs. We can use high order functions to simplify this interface.

```csharp
interface IProductRepository { // create, update, delete omitted 
IEnumerable<Product> Get(Func<Product, bool> filter = null);
}
```

`Get` function now has a declared output type of the 3 interface definitions above it. meaning the input can be generic so long as it meets the criteria of the output such as using the below examples to represent the 3 `IEnumerable<Product>` 

```csharp
var allProducts = _productRepository.Get();
var productsByCategoryId = _productRepository.Get(p => p.CategoryId == 1);
var activeProducts = _productRepository.Get(p => p.Active);
```

With an exception to the first function its assumed that all values get returned from a `Get` function. The others have specific filtering criteria matching the original interface.