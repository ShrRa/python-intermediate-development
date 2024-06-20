---
title: "Functional Programming"
teaching: 30
exercises: 30
questions:
- What is functional programming?
- Which situations/problems is functional programming well suited for?
objectives:
- Describe the core concepts that define the functional programming paradigm
- Describe the main characteristics of code that is written in functional programming style
- Learn how to generate and process data collections efficiently using MapReduce and Python's comprehensions
keypoints:
- Functional programming is a programming paradigm where programs are constructed by applying and composing smaller and simple functions into more complex ones (which describe the flow of data within a program as a sequence of data transformations).
- In functional programming, functions tend to be *pure* - they do not exhibit *side-effects* (by not affecting anything other than the value they return or anything outside a function). Functions can also be named, passed as arguments, and returned from other functions, just as any other data type.
- MapReduce is an instance of a data generation and processing approach, in particular suited for functional programming and handling Big Data within parallel and distributed environments.
- Python provides comprehensions for lists, dictionaries, sets and generators - a concise (if not strictly functional) way to generate new data from existing data collections while performing sophisticated mapping, filtering and conditional logic on original dataset's members.
---

## Introduction

Functional programming is a programming paradigm where
programs are constructed by applying and composing/chaining **functions**.
Functional programming is based on the
[mathematical definition of a function](https://en.wikipedia.org/wiki/Function_(mathematics))
`f()`,
which applies a transformation to some input data giving us some other data as a result
(i.e. a mapping from input `x` to output `f(x)`).
Thus, a program written in a functional style becomes a series of transformations on data
which are performed to produce a desired output.
Each function (transformation) taken by itself is simple and straightforward to understand;
complexity is handled by composing functions in various ways.

Often when we use the term function we are referring to
a construct containing a block of code which performs a particular task and can be reused.
We have already seen this in procedural programming -
so how are functions in functional programming different?
The key difference is that functional programming is focussed on
**what** transformations are done to the data,
rather than **how** these transformations are performed
(i.e. a detailed sequence of steps which update the state of the code to reach a desired state).
Let's compare and contrast examples of these two programming paradigms.

## Functional vs Procedural Programming

The following two code examples implement the calculation of a factorial
in procedural and functional styles, respectively.
Recall that the factorial of a number `n` (denoted by `n!`) is calculated as
the product of integer numbers from 1 to `n`.

The first example provides a procedural style factorial function.

~~~
def factorial(n):
    """Calculate the factorial of a given number.

    :param int n: The factorial to calculate
    :return: The resultant factorial
    """
    if n < 0:
        raise ValueError('Only use non-negative integers.')

    factorial = 1
    for i in range(1, n + 1): # iterate from 1 to n
        # save intermediate value to use in the next iteration
        factorial = factorial * i

    return factorial
~~~
{: .language-python}

Functions in procedural programming are *procedures* that describe
a detailed list of instructions to tell the computer what to do step by step
and how to change the state of the program and advance towards the result.
They often use *iteration* to repeat a series of steps.
Functional programming, on the other hand, typically uses *recursion* -
an ability of a function to call/repeat itself until a particular condition is reached.
Let's see how it is used in the functional programming example below
to achieve a similar effect to that of iteration in procedural programming.

~~~
# Functional style factorial function
def factorial(n):
    """Calculate the factorial of a given number.

    :param int n: The factorial to calculate
    :return: The resultant factorial
    """
    if n < 0:
        raise ValueError('Only use non-negative integers.')

    if n == 0 or n == 1:
        return 1 # exit from recursion, prevents infinite loops
    else:
        return  n * factorial(n-1) # recursive call to the same function
~~~
{: .language-python}

Note: You may have noticed that both functions in the above code examples have the same signature
(i.e. they take an integer number as input and return its factorial as output).
You could easily swap these equivalent implementations
without changing the way that the function is invoked.
Remember, a single piece of software may well contain instances of multiple programming paradigms -
including procedural, functional and object-oriented -
it is up to you to decide which one to use and when to switch
based on the problem at hand and your personal coding style.

Functional computations only rely on the values that are provided as inputs to a function
and not on the state of the program that precedes the function call.
They do not modify data that exists outside the current function, including the input data -
this property is referred to as the *immutability of data*.
This means that such functions do not create any *side effects*,
i.e. do not perform any action that affects anything other than the value they return.
For example: printing text,
writing to a file,
modifying the value of an input argument,
or changing the value of a global variable.
Functions without side affects
that return the same data each time the same input arguments are provided
are called *pure functions*.

> ## Exercise: Pure Functions
>
> Which of these functions are pure?
> If you're not sure, explain your reasoning to someone else, do they agree?
>
> ~~~
> def add_one(x):
>     return x + 1
>
> def say_hello(name):
>     print('Hello', name)
>
> def append_item_1(a_list, item):
>     a_list += [item]
>     return a_list
>
> def append_item_2(a_list, item):
>     result = a_list + [item]
>     return result
> ~~~
> {: .language-python}
>
> > ## Solution
> >
> > 1. `add_one` is pure - it has no effects other than to return a value and this value will always be the same when given the same inputs
> > 2. `say_hello` is not pure - printing text counts as a side effect, even though it is the clear purpose of the function
> > 3. `append_item_1` is not pure - the argument `a_list` gets modified as a side effect - try this yourself to prove it
> > 4. `append_item_2` is pure - the result is a new variable, so this time `a_list` does not get modified - again, try this yourself
> {: .solution}
{: .challenge}

## Benefits of Functional Code

There are a few benefits we get when working with pure functions:

- Testability
- Composability
- Parallelisability

**Testability** indicates how easy it is to test the function - usually meaning unit tests.
It is much easier to test a function if we can be certain that
a particular input will always produce the same output.
If a function we are testing might have different results each time it runs
(e.g. a function that generates random numbers drawn from a normal distribution),
we need to come up with a new way to test it.
Similarly, it can be more difficult to test a function with side effects
as it is not always obvious what the side effects will be, or how to measure them.

**Composability** refers to the ability to make a new function from a chain of other functions
by piping the output of one as the input to the next.
If a function does not have side effects or non-deterministic behaviour,
then all of its behaviour is reflected in the value it returns.
As a consequence of this, any chain of combined pure functions is itself pure,
so we keep all these benefits when we are combining functions into a larger program.
As an example of this, we could make a function called `add_two`,
using the `add_one` function we already have.

~~~
def add_two(x):
    return add_one(add_one(x))
~~~
{: .language-python}

**Parallelisability** is the ability for operations to be performed at the same time (independently).
If we know that a function is fully pure and we have got a lot of data,
we can often improve performance by
splitting data and distributing the computation across multiple processors.
The output of a pure function depends only on its input,
so we will get the right result regardless of when or where the code runs.

> ## Everything in Moderation
> Despite the benefits that pure functions can bring,
> we should not be trying to use them everywhere.
> Any software we write needs to interact with the rest of the world somehow,
> which requires side effects.
> With pure functions you cannot read any input, write any output,
> or interact with the rest of the world in any way,
> so we cannot usually write useful software using just pure functions.
> Python programs or libraries written in functional style will usually not be
> as extreme as to completely avoid reading input, writing output,
> updating the state of internal local variables, etc.;
> instead, they will provide a functional-appearing interface
> but may use non-functional features internally.
> An example of this is the [Pandas library](https://pandas.pydata.org/)
> that we already used in previous episodes -
> most of its functions appear pure
> as they return new data objects instead of changing existing ones.
{: .callout}

There are other advantageous properties that can be derived from the functional approach to coding.
In languages which support functional programming,
a function is a *first-class object* like any other object -
not only can you compose/chain functions together,
but functions can be used as inputs to,
passed around or returned as results from other functions
(remember, in functional programming *code is data*).
This is why functional programming is suitable for processing data efficiently -
in particular in the world of Big Data, where code is much smaller than the data,
sending the code to where data is located is cheaper and faster than the other way round.
Let's see how we can do data processing using functional programming.

## MapReduce Data Processing Approach

When working with data you will often find that you need to
apply a transformation to each datapoint of a dataset
and then perform some aggregation across the whole dataset.
One instance of this data processing approach is known as MapReduce. This term
usually arises in application to Big Data distributed processing on a cluster,
e.g. using tools such as [Spark](https://en.wikipedia.org/wiki/Apache_Spark)
or [Hadoop](https://hadoop.apache.org/). However, the name itself comes from 
applying an operation to (mapping) each value in a dataset,
then performing a reduction operation which
collects/aggregates all the individual results together to produce a single result. 
This approach can be modelled even on a single processor and a small dataset.
MapReduce relies heavily on composability and parallelisability of functional programming -
both map and reduce can be done in parallel and on smaller subsets of data,
before aggregating all intermediate results into the final result.

### Mapping
`map(f, C)` is a function takes another function `f()` and a collection `C` of data items as inputs.
Calling `map(f, L)` applies the function `f(x)` to every data item `x` in a collection `C`
and returns the resulting values as a new collection of the same size.

This is a simple mapping that takes a list of names and
returns a list of the lengths of those names using the built-in function `len()`:

~~~
name_lengths = map(len, ["Mary", "Isla", "Sam"])
print(list(name_lengths))
~~~
{: .language-python}
~~~
[4, 4, 3]
~~~
{: .output}

This is a mapping that squares every number in the passed collection using anonymous,
inlined *lambda* expression (a simple one-line mathematical expression representing a function):

~~~
squares = map(lambda x: x * x, [0, 1, 2, 3, 4])
print(list(squares))
~~~
{: .language-python}
~~~
[0, 1, 4, 9, 16]
~~~
{: .output}

> ## Lambda
> Lambda expressions are used to create anonymous functions that can be used to
> write more compact programs by inlining function code.
> A lambda expression takes any number of input parameters and
> creates an anonymous function that returns the value of the expression.
> So, we can use the short, one-line `lambda x, y, z, ...: expression` code
> instead of defining and calling a named function `f()` as follows:
> ~~~
> def f(x, y, z, ...):
>   return expression
> ~~~
> {: .language-python}
> The major distinction between lambda functions and ‘normal’ functions is that
> lambdas do not have names.
> We could give a name to a lambda expression if we really wanted to -
> but at that point we should be using a ‘normal’ Python function instead.
>
> ~~~
> # Don't do this
> add_one = lambda x: x + 1
>
> # Do this instead
> def add_one(x):
>   return x + 1
> ~~~
> {: .language-python}
{: .callout}

In addition to using built-in or inlining anonymous lambda functions,
we can also pass a named function that we have defined ourselves to the `map()` function.

~~~
def add_one(num):
    return num + 1

result = map(add_one, [0, 1, 2])
print(list(result))
~~~
{: .language-python}
~~~
[1, 2, 3]
~~~
{: .output}

> ## Exercise: Use Pandas Map and Apply functions for processing complex data 
> 
> For common tasks, like filtering the table following a certain condition or applying arithmetical operations
> to table columns, with libraries like `numpy` and `pandas` it is always better to use built-in functions, since
> they are often optimized and work much faster than using `map` with a lambda function. However, not all data
> can be processed using already existing solutions.
> Apart from the Python built-in `map` function, `numpy` and `pandas` have their own implementations
> of mapping, optimized for processing
> numpy arrays, Series and DataFrames. One situation in which it can be really handy is
> when you have a table with complex data stored in the columns, e.g. lists, dicts or arrays.
> For example, some surveys use such a format for storing light curves or spectra measurements.
> Let's simulate such situation:
> 
> ~~~
> # Create an empty list where we will be storing our light curves
> lcs = []
> # For each observed object
> for obj_id in lc_datasets["lsst"]["objectId"].unique():
>     # Create an empty dict for the light curves of this object
>     lc = {}
>     lc['objectId'] = obj_id
>     for b in bands:
>         filt_band_obj = (lc_datasets["lsst"]["objectId"] == obj_id) & (
>             lc_datasets["lsst"]["band"] == b
>         )
>         # The observations in each band are converted to lists and stored as dict elements
>         lc[b+'_'+mag_col] = np.array(lc_datasets["lsst"][filt_band_obj][mag_col])
>         lc[b+'_'+time_col] = np.array(lc_datasets["lsst"][filt_band_obj][time_col])
>     lcs.append(lc)
> # Turn the list of dicts into a DataFrame    
> lcs = pd.DataFrame.from_records(lcs)
> ~~~
> {: .language-python}
> 
> In the resulting DataFrame each row will contain an Id of the observed object, six columns for
> magnitude arrays and six corresponding columns with time stamps arrays. How would you deal with NaNs
> in the data stored in this format? For this task, you may find to be useful
> functions `pd.map`, `pd.apply`, `np.where` and `np.isnan`. Try to come up with a solution where you replace
> the NaN values with zeros, and with a solution where you discard them entirely (don't forget to remove
> the corresponding time stamps values too!). 
> 
> > ## Solution
> > Let's consider two possible solutions: the one where we replace the NaN values with zeros,
> > and the one where we discard NaN values entirely.
> > For the first solution we can use `pd.map` function, that act similarly to the classic Python `map`,
> > but works only for `pd.Series`.
> > ~~~
> > b = 'u'
> > lcs[b+'_'+mag_col+'_cleaned'] = lcs[b+'_'+mag_col].map(lambda l: np.where(np.isnan(l),0,l))
> > ~~~
> > {: .language-python}
> >
> > For the second solution, we need to remove not only NaN values from the magnitude arrays,
> > but also corresponding to them time stamps. For this reason, we cannot use `pd.map` as it
> > can be used only with `pd.Series` (e.g. with only one column). However, another Pandas function,
> > `apply`, can accept rows.
> > ~~~
> > b = "u"
> > # Create column names variables for better readability
> > mcol = b + "_" + mag_col
> > tcol = b + "_" + time_col
> > mcol_cl = mcol + "_cleaned"
> > tcol_cl = tcol + "_cleaned"
> > # The new cleaned columns, `mcol_cl` and `tcol_cl`, contain the result of applying
> > # a lambda function to each row (`axis=1` argument). The lambda function returns a tuple
> > # of two numpy arrays, filtered according to the mask that is `False` for the elements that
> > # are NaNs and `True` to all other elements.
> > lcs[[mcol_cl, tcol_cl]] = lcs.apply(
> >     lambda l: (
> >         l[mcol][~np.isnan(l[mcol])],
> >         l[tcol][~np.isnan(l[mcol])],
> >     ),
> >     axis=1,
> >     result_type="expand",
> > )
> > ~~~
> > {: .language-python}
> {: .solution}
> Note: you're better not to store a table with any kinds of collections (lists, tuples, dictionaries or arrays)
> in the columns in a '.csv' file, since reading and parsing it correctly can take a lot of effort.
{: .challenge}

#### Comprehensions for Mapping/Data Generation

Another way you can generate new collections of data from existing collections in Python is
using *comprehensions*,
which are an elegant and concise way of creating data from
[iterable objects](https://www.w3schools.com/python/python_iterators.asp) using *for loops*.
While not a pure functional concept,
comprehensions provide data generation functionality
and can be used to achieve the same effect as the built-in "pure functional" function `map()`.
They are commonly used and actually recommended as a replacement of `map()` in modern Python.
Let's have a look at some examples.

~~~
integers = range(5)
double_ints = [2 * i for i in integers]

print(double_ints)
~~~
{: .language-python}
~~~
[0, 2, 4, 6, 8]
~~~
{: .output}

The above example uses a *list comprehension* to double each number in a sequence.
Notice the similarity between the syntax for a list comprehension and a for loop -
in effect, this is a for loop compressed into a single line.
In this simple case, the code above is equivalent to using a map operation on a sequence,
as shown below:

~~~
integers = range(5)
double_ints = map(lambda i: 2 * i, integers)
print(list(double_ints))
~~~
{: .language-python}
~~~
[0, 2, 4, 6, 8]
~~~
{: .output}

We can also use list comprehensions to filter data, by adding the filter condition to the end:

~~~
double_even_ints = [2 * i for i in integers if i % 2 == 0]
print(double_even_ints)
~~~
{: .language-python}
~~~
[0, 4, 8]
~~~
{: .output}

> ## Set and Dictionary Comprehensions and Generators
> We also have *set comprehensions* and *dictionary comprehensions*,
> which look similar to list comprehensions
> but use the set literal and dictionary literal syntax, respectively.
> ~~~
> double_even_int_set = {2 * i for i in integers if i % 2 == 0}
> print(double_even_int_set)
>
> double_even_int_dict = {i: 2 * i for i in integers if i % 2 == 0}
> print(double_even_int_dict)
> ~~~
> {: .language-python}
> ~~~
> {0, 4, 8}
> {0: 0, 2: 4, 4: 8}
> ~~~
> {: .output}
>
> Finally, there’s one last ‘comprehension’ in Python - a *generator expression* -
> a type of an iterable object which we can take values from and loop over,
> but does not actually compute any of the values until we need them.
> Iterable is the generic term for anything we can loop or iterate over -
> lists, sets and dictionaries are all iterables.
>
>The `range` function is an example of a generator -
> if we created a `range(1000000000)`, but didn’t iterate over it,
> we’d find that it takes almost no time to do.
> Creating a list containing a similar number of values would take much longer,
> and could be at risk of running out of memory.
>
> We can build our own generators using a generator expression.
> These look much like the comprehensions above,
> but act like a generator when we use them.
> Note the syntax difference for generator expressions -
> parenthesis are used in place of square or curly brackets.
>
> ~~~
> doubles_generator = (2 * i for i in integers)
> for x in doubles_generator:
>    print(x)
> ~~~
> {: .language-python}
> ~~~
> 0
> 2
> 4
> 6
> 8
> ~~~
> {: .output}
{: .callout}


Let's now have a look at reducing the elements of a data collection into a single result.

### Reducing

`reduce(f, C, initialiser)` function accepts a function `f()`,
a collection `C` of data items
and an optional `initialiser`,
and returns a single cumulative value which
aggregates (reduces) all the values from the collection into a single result.
The reduction function first applies the function `f()` to the first two values in the collection
(or to the `initialiser`, if present, and the first item from `C`).
Then for each remaining value in the collection,
it takes the result of the previous computation
and the next value from the collection as the new arguments to `f()`
until we have processed all of the data and reduced it to a single value.
For example, if collection `C` has 5 elements, the call `reduce(f, C)` calculates:

~~~
f(f(f(f(C[0], C[1]), C[2]), C[3]), C[4])
~~~

One example of reducing would be to calculate the product of a sequence of numbers.

~~~
from functools import reduce

sequence = [1, 2, 3, 4]

def product(a, b):
    return a * b

print(reduce(product, sequence))

# The same reduction using a lambda function
print(reduce((lambda a, b: a * b), sequence))
~~~
{: .language-python}
~~~
24
24
~~~
{: .output}

Note that `reduce()` is not a built-in function like `map()` -
you need to import it from library `functools`.

> ## Exercise: Calculate the Sum of a Sequence of Numbers Using Reduce
> Using reduce calculate the sum of a sequence of numbers.
> Although in practice we would use the built-in `sum()` function for this - try doing it without it.
>
> > ## Solution
> > ~~~
> > from functools import reduce
> >
> > sequence = [1, 2, 3, 4]
> >
> > def add(a, b):
> >     return a + b
> >
> > print(reduce(add, sequence))
> >
> > # The same reduction using a lambda function
> > print(reduce((lambda a, b: a + b), sequence))
> > ~~~
> > {: .language-python}
> > ~~~
> > 10
> > 10
> > ~~~
> > {: .output}
> {: .solution}
{: .challenge}

### Putting It All Together
Let's now put together what we have learned about map and reduce so far
by writing a function that calculates the sum of the squares of the values in a list
using the MapReduce approach.

~~~
from functools import reduce

def sum_of_squares(sequence):
    squares = [x * x for x in sequence]  # use list comprehension for mapping
    return reduce(lambda a, b: a + b, squares)
~~~
{: .language-python}

We should see the following behaviour when we use it:

~~~
print(sum_of_squares([0]))
print(sum_of_squares([1]))
print(sum_of_squares([1, 2, 3]))
print(sum_of_squares([-1]))
print(sum_of_squares([-1, -2, -3]))
~~~
{: .language-python}
~~~
0
1
14
1
14
~~~
{: .output}

Now let’s assume we’re reading in these numbers from an input file,
so they arrive as a list of strings.
We'll modify the function so that it passes the following tests:

~~~
print(sum_of_squares(['1', '2', '3']))
print(sum_of_squares(['-1', '-2', '-3']))
~~~
{: .language-python}
~~~
14
14
~~~
{: .output}

The code may look like:

~~~
from functools import reduce

def sum_of_squares(sequence):
    integers = [int(x) for x in sequence]
    squares = [x * x for x in integers]
    return reduce(lambda a, b: a + b, squares)
~~~
{: .language-python}

Finally, like comments in Python, we’d like it to be possible for users to
comment out numbers in the input file they give to our program.
We'll finally extend our function so that the following tests pass:

~~~
print(sum_of_squares(['1', '2', '3']))
print(sum_of_squares(['-1', '-2', '-3']))
print(sum_of_squares(['1', '2', '#100', '3']))
~~~
{: .language-python}
~~~
14
14
14
~~~
{: .output}

To do so, we may filter out certain elements and have:

~~~
from functools import reduce

def sum_of_squares(sequence):
    integers = [int(x) for x in sequence if x[0] != '#']
    squares = [x * x for x in integers]
    return reduce(lambda a, b: a + b, squares)
~~~
{: .language-python}

## Decorators

Finally, we will look at one last aspect of Python where functional programming is coming handy.
As we have seen in the
[episode on parametrising our unit tests](../22-scaling-up-unit-testing/index.html#parameterising-our-unit-tests),
a decorator can take a function, modify/decorate it, then return the resulting function.
This is possible because Python treats functions as first-class objects
that can be passed around as normal data.
Here, we discuss decorators in more detail and learn how to write our own.
Let's look at the following code for ways on how to "decorate" functions.

~~~
def with_logging(func):

    """A decorator which adds logging to a function."""
    def inner(*args, **kwargs):
        print("Before function call")
        result = func(*args, **kwargs)
        print("After function call")
        return result

    return inner


def add_one(n):
    print("Adding one")
    return n + 1

# Redefine function add_one by wrapping it within with_logging function
add_one = with_logging(add_one)

# Another way to redefine a function - using a decorator
@with_logging
def add_two(n):
    print("Adding two")
    return n + 2

print(add_one(1))
print(add_two(1))
~~~
{: .language-python}
~~~
Before function call
Adding one
After function call
2
Before function call
Adding two
After function call
3
~~~
{: .output}

In this example, we see a decorator (`with_logging`)
and two different syntaxes for applying the decorator to a function.
The decorator is implemented here as a function which encloses another function.
Because the inner function (`inner()`) calls the function being decorated (`func()`)
and returns its result,
it still behaves like this original function.
Part of this is the use of `*args` and `**kwargs` -
these allow our decorated function to accept any arguments or keyword arguments
and pass them directly to the function being decorated.
Our decorator in this case does not need to modify any of the arguments,
so we do not need to know what they are.
Any additional behaviour we want to add as part of our decorated function,
we can put before or after the call to the original function.
Here we print some text both before and after the decorated function,
to show the order in which events happen.

We also see in this example the two different ways in which a decorator can be applied.
The first of these is to use a normal function call (`with_logging(add_one)`),
where we then assign the resulting function back to a variable -
often using the original name of the function, so replacing it with the decorated version.
The second syntax is the one we have seen previously (`@with_logging`).
This syntax is equivalent to the previous one -
the result is that we have a decorated version of the function,
here with the name `add_two`.
Both of these syntaxes can be useful in different situations:
the `@` syntax is more concise if we never need to use the un-decorated version,
while the function-call syntax gives us more flexibility -
we can continue to use the un-decorated function
if we make sure to give the decorated one a different name,
and can even make multiple decorated versions using different decorators.

> ## Exercise: Measuring Performance Using Decorators
> One small task you might find a useful case for a decorator is
> measuring the time taken to execute a particular function.
> This is an important part of performance profiling.
> While in the Jupyter Lab you can use cell magics for this task,
> in `.py` file a decorator is a suitable replacement.
>
> Write a decorator which you can use to measure the execution time of the decorated function
> using the [time.process_time_ns()](https://docs.python.org/3/library/time.html#time.process_time_ns) function.
> There are several different timing functions each with slightly different use-cases,
> but we won’t worry about that here.
>
> For the function to measure, you may wish to use this as an example:
> ~~~
> def measure_me(n):
>     total = 0
>     for i in range(n):
>         total += i * i
>
>     return total
> ~~~
> {: .language-python}
> > ## Solution
> >
> > ~~~
> > import time
> >
> > def profile(func):
> >     def inner(*args, **kwargs):
> >         start = time.process_time_ns()
> >         result = func(*args, **kwargs)
> >         stop = time.process_time_ns()
> >
> >         print("Took {0} seconds".format((stop - start) / 1e9))
> >         return result
> >
> >     return inner
> >
> > @profile
> > def measure_me(n):
> >     total = 0
> >     for i in range(n):
> >         total += i * i
> >
> >     return total
> >
> > print(measure_me(1000000))
> > ~~~
> > {: .language-python}
> > ~~~
> > Took 0.124199753 seconds
> > 333332833333500000
> > ~~~
> > {: .output}
> {: .solution}
{: .challenge}
