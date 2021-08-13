[ToC]


## Intro

* separate language
* not really intended as such, some warts
* very dense, take breaks to practice


## Basic Usage

### Writing Basic Templates

At their core, C++ templates are a way to write generic code parameterized on types.  Without templates, you'd have to write code like

```cpp
struct three_ints
{
    int value1;
    int value2;
    int value3;
};
struct three_doubles
{
    double value1;
    double value2;
    double value3;
};
```

et cetera, or you could be clever and use `void` pointers:

```cpp
struct three
{
    void* value1;
    void* value2;
    void* value3;
};
```

But if you did that, you couldn't automatically free the memory the pointers point to with a destructor, because C++ has no idea what type they are.  You could also forget, and try casting them to something they aren't.

With templates, you can write

```cpp
template< typename T > struct three
{
    T value1;
    T value2;
    T value3;
};
```

Now you can get a `three< int >` or a `three< double >` or basically whatever you want — the template parameter T will be replaced with the type in angle brackets.  Because the member variables are values and not pointers they get cleaned up along with the containing object.  The C++ standard library containers (`vector`, `queue`, etc.) work this way.

You can even have multiple template parameters, such as with [`std::pair<>`](https://cppreference.com/w/cpp/utility/pair):

```cpp
template< typename T1, typename T2 > class pair
{
public:
    using  first_type = T1;
    using second_type = T2;
    T1 first;
    T2 second;
};
```

> **Note:** When you write a template, you can either use `typename` or `class` inside the parameters — they mean the same thing.

> **Note:** Before C++11, you had to write `typedef T1 first_type` (C-style) instead of `using first_type = T1`.  The newer version (`using`) makes it a lot more clear that you're creating an alias for `T1` called `first_type`.

For a variable of type `pair< int, char >` called `p`, `p.first` will be an `int` and `p.second` will be a `char`.

Template parameters can also have defaults; for example, if you wanted `pair` to default to two `int`s for some reason, you can write:

```cpp
template<
    typename T1 = int,
    typename T2 = int
> class pair
{ // etc...
```

This means you can write `pair<>` and mean `pair< int, int >` (note you still need the angle brackets even with an empty parameter list).  Default parameters act similar to function default arguments; writing `pair< float >` means `pair< float, int >`.

Template parameters can also be certain types of constants, such as `int`, which is how [`std::array<>`](https://cppreference.com/w/cpp/container/array) works:

```cpp
template<
    typename T,
    std::size_t N
> class array
{
    T _values[ N ];
    // ...
};
```

> **Note:** `std::size_t` is a useful utility type, defined to be some unsigned type able to store pretty much as high an element count as you'd ever need.  Typically this means "as large as pointers can get on your system."  You should always use `std::size_t` for sizes of containers, or any counts of items, unless you have a good reason not to.

One important thing to notice about `std::array<>` is that its size is now set at compile-time; you can't change it while the program is running.  If you need to do that, use `std::vector<>`; while there are some important performance differences, that's a topic for another time.

### Other Kinds of Templates

#### Functions

All the examples so far have been template classes and structs.  Functions can also be templated:

```cpp
template< typename T > T sum(
    T start,
    const std::vector< T >& elements
)
{
    for( auto& e : elements )
        start += e;
    return start;
}
```

This defines a template function that takes a starting value and a vector of elements of that value's type, then adds all the elements to the starting value.  (Note the use of a C++11 range-based `for` loop.)  Because both `int` and `std::string` have a `+=` operator, the following both work:

```cpp
sum< int >( 0, { 1, 2, 3 } );
sum< std::string >( "Hello", { ", ", "World", "!" } );
```

The result of the first one is the integer `6`, while the result of the second is the string `Hello, World!`.  (This example uses another C++11 feature, initializer lists, for brevity; without it you'd have to create the vectors first and push elements into them.)

One difference ([until C++17](https://en.cppreference.com/w/cpp/language/class_template_argument_deduction)) between class/struct templates and function templates is that function template parameters can be deduced from the function's arguments, making template functions act like overloaded functions.  This doesn't require `<>` unlike default template parameters, making the example above even shorter:

```cpp
sum( 0, { 1, 2, 3 } );
```

One problem though: the string version doesn't compile:

```cpp
sum( "Hello", { ", ", "World", "!" } );
```

This is because the `start` argument is a C string — an array of `char`, which decays to `const char*` following C's array rules.  This means `T` is deduced to `const char*`, then we try to add more `const char*`s to that, which won't work.  If you want to pass string literals to `sum()`, you have to do one of these:

```cpp
sum( std::string{ "Hello" }, { ", ", "World", "!" } );
// We don't need to convert the list of strings to add, as `start` (a
// `std::string`) knows what to do when you try to add a `const char*`
// to it
```
```cpp
// C++14 introduces string literals in the standard library:
using namespace std::literals::string_literals;
sum( "Hello"s, { ", "s, "World"s, "!"s } );
```

Now `T` is deduced properly as `std::string`.

Template functions can be class and struct members, too:

```cpp
class thing
{
private:
    int _value;
public:
    template< typename T > get_value_as()
    {
        return static_cast< T >( _value );
    }
};
```

which can be called as `a_thing.get_value_as< double >()`.  This may not seem particularly useful at first, because you could always just cast the result of a non-template `get_value()`.  However, it can very useful for simplifying some complex template code, and can be used to enforce certain type-based constraints using template enabling/disabling (which will be covered later).

Template classes and structs can also contain template member functions *with different parameters than the class or template's parameters*.  One possible use is converting containers of one contained type to another (note that the C++ standard library templates don't implement this functionality):

```cpp
template< typename T > class my_container
{
    // ...
public:
    template< typename O >
    my_container& operator=( const my_container< O >& o )
    {
        clear();
        for( auto& e : o )
            push_back( static_cast< T >( e ) );
        return *this;
    }
};
```

This would allow you to assign a `my_container< int >` to a `my_container< double >`, as it takes each element of the incoming container, converts it to the type of the target container, and pushes that onto the end.  Note the `static_cast<>()` isn't really necessary for `int` and `double`, but without it only *implicit* conversions from `O` to `T` would be possible.  C++ allows some conversion to be marked [`explicit`](https://cppreference.com/w/cpp/language/explicit) to prevent accidental conversions.  If you want only implicit conversions leave out the `static_cast<>()` — it depends on what you're trying to achieve.

Defining a template member function of a template class is straightforward when you do it inside the class.  However it's good practice to only put member function declarations inside the class definition to prevent clutter.  To implement template member functions of a template class outside the parent, you have to write:

```cpp
template< typename T > class my_container
{
    // ...
public:
    template< typename O >
    my_container& operator=( const my_container< O >& o );
};

template< typename T >
template< typename O >
my_container< T >& my_container< T >::operator=( const my_container< O >& o )
{
    clear();
    for( auto& e : o )
        push_back( static_cast< T >( e ) );
    return *this;
}
```

#### Variable Templates

Before C++14, templated constants needed to be wrapped in a template struct or class:

```cpp
template< typename T > struct math_constants
{
    static T pi = static_cast< T >( 3.1415926535897932385L );
};
```

The `pi` constant can be obtained with `math_constants< double >::pi`; the `math_constants` struct is never instantiated itself.

C++14 introduces variable templates:

```cpp
template< typename T > T pi = static_cast< T >( 3.1415926535897932385L );
template< std::size_t Bits > unsigned long highest_bit = 2 << ( Bits - 1 );
```

This results in a less verbose syntax with identical semantics: `pi< double >`, `highest_bit< 64 >`.  Note you can still wrap `pi` in a `math_constants` namespace, but the constant itself is templated rather than the namespace, making the syntax clearer.

This may seem like a fairly useless feature, but can be quite powerful when combined with other template metaprogramming techniques such as specialization.


#### Template Aliases

More complex templates can be aliased to hide arguments not needed for common usage.  For example, `std::string` in the C++ standard library is actually an alias for `std::basic_string< char >`.  This is accomplished with a simple alias:

```cpp
namespace std
{
    using string = basic_string< char >;
}
```

You can also *partially* alias templates.  If you want to have a type called `dictionary`, that behaves exactly like `std::map` except the keys are always `std::string`, you can write:

```cpp
template< typename Value > using dictionary = std::map< std::string, Value >;
```

That way when you write `dictionary< int >`, it's actually an alias for `std::map< std::string, int >`.


## Where Templates can Exist

Now that you know how to write basic templates, there's one critical thing to remember: templates by themselves don't get compiled with the rest of your code.  Whenever you specialize a template with parameters, it becomes a real class, function, or whatever that can be compiled.  However, the template itself only exists in the C++ source file you wrote it in and other files that include that one.  This means that in general (with certain caveats that will be covered later), both template declarations and definitions have to remain entirely in header files.


## Basic Techniques

### Traits Types

Traits types are commonly used in the C++ standard library to add a kind of "functionality kernel" to templates.


## Intermediate Usage

* full template specialization
    * quote from Bjarne re: regretting not having template overloading
    * definition of declared specializations
* partial template specialization
    * `template<> using`
    * basically [currying](https://en.wikipedia.org/wiki/Currying)
    * note about aliases not working with `std::is_same<>` in most compilers (something about not fully resolving templates before checking same, or creating new template)


## Intermediate Techniques

* dispatch — more general form of traits types

### API Dispatch

*TODO*


### Combinatorial Dispatch

*TODO*


## Advanced Usage

* template template parameters
* variadic templates / parameter pack in C++11
    * recursive accesss
* SFINAE


## Advanced Techniques

### `void_t`

*TODO*


### `enable_if`

*TODO*


### Optimizations

*TODO*
