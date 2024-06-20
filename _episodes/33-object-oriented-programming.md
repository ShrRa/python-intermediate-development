---
title: "Object Oriented Programming"
teaching: 30
exercises: 40
questions:
- "How can we use code to describe the structure of data?"
- "How should the relationships between structures be described?"
objectives:
- "Describe the core concepts that define the object oriented paradigm"
- "Use classes to encapsulate data within a more complex program"
- "Structure concepts within a program in terms of sets of behaviour"
- "Identify different types of relationship between concepts within a program"
- "Structure data within a program using these relationships"
keypoints:
- "Object oriented programming is a programming paradigm based on the concept of classes, which encapsulate data and code."
- "Classes allow us to organise data into distinct concepts."
- "By breaking down our data into classes, we can reason about the behaviour of parts of our data."
- "Relationships between concepts can be described using inheritance (*is a*) and composition (*has a*)."
---

## Introduction

Object oriented programming is a programming paradigm based on the concept of objects,
which are data structures that contain (encapsulate) data and code.
Data is encapsulated in the form of fields (attributes) of objects,
while code is encapsulated in the form of procedures (methods)
that manipulate objects' attributes and define "behaviour" of objects.
So, in object oriented programming,
we first think about the data and the things that we’re modelling -
and represent these by objects -
rather than define the logic of the program,
and code becomes a series of interactions between objects.

## Structuring Data

One of the main difficulties we encounter when building more complex software is
how to structure our data.
So far, we've been processing data from a single source and with a simple tabular structure,
but it would be useful to be able to combine data from a range of different sources
and with more data than just an array of numbers.

~~~
data = pd.DataFrame(data = [[1., 2., 3.],
                            [4., 5., 6.]],
                    columns = list('abc'))
~~~
{: .language-python}

Using this data structure has the advantage of
being able to use Pandas operations to process the data
and Matplotlib to plot it,
but often we need to have more structure than this.
For example, we may need to attach more information about the object which light
curve we analyse, such as cutout image of this source or its spectra.

In a way, we already encountered this problem when we needed to store light curves
of a single object, but obtained in different bands. Our solution then was to use a dictionary
where keys corresponded to the bands names, and values were the DataFrames with the measurements.
Generally speaking, we can expand this solution by adding new elements with values of other
data types. For example, we could write something like this:
~~~
lc = {}
...
lc['spectra'] = np.array([4,5,6]) 
~~~
{: .language-python}
Since Python distionaries can store elements of different types, there is nothing
that would stop us from this. We can also make nested dictionaries, creating really complex data
structures.

Then we can get lost in them.

## Classes in Python

Using nested dictionaries and lists should work for some of the simpler cases
where we need to handle structured data,
but they get quite difficult to manage once the structure becomes a bit more complex.
For this reason, in the object oriented paradigm,
we use **classes** to help with managing this data
and the operations we would want to perform on it.
A class is a **template** (blueprint) for a structured piece of data,
so when we create some data using a class,
we can be certain that it has the same structure each time.

With our dictionaries we had in the examples befpre,
we have no real guarantee that each dictionary has the same structure
unless we check it manually. 
With a class, if an object is an **instance** of that class
(i.e. it was made using that template),
we know it will have the structure defined by that class.
Different programming languages make slightly different guarantees
about how strictly the structure will match,
but in object oriented programming this is one of the core ideas -
all objects derived from the same class must follow the same behaviour.

You may not have realised, but you should already be familiar with
some of the classes that come bundled as part of Python, for example:

~~~
my_list = [1, 2, 3]
my_dict = {1: '1', 2: '2', 3: '3'}
my_set = {1, 2, 3}

print(type(my_list))
print(type(my_dict))
print(type(my_set))
~~~
{: .language-python}

~~~
<class 'list'>
<class 'dict'>
<class 'set'>
~~~
{: .output}

Lists, dictionaries and sets are a slightly special type of class,
but they behave in much the same way as a class we might define ourselves:

- They each hold some data (**attributes** or **state**).
- They also provide some methods describing the behaviours of the data -
  what can the data do and what can we do to the data?

The behaviours we may have seen previously include:

- Lists can be appended to
- Lists can be indexed
- Lists can be sliced
- Key-value pairs can be added to dictionaries
- The value at a key can be looked up in a dictionary
- The union of two sets can be found (the set of values present in any of the sets)
- The intersection of two sets can be found (the set of values present in all of the sets)

## Encapsulating Data

Let's start with a minimal example of a class representing a variable object.

~~~
class Variable:
    def __init__(self, obj_id):
        self.obj_id = obj_id
        self.lc = {
                   'mjd': np.array([]),
                   'mag': np.array([])
                  }

star_obs = Variable(obj_id)
print(star_obs.obj_id)
~~~
{: .language-python}

~~~
1405624461041897445
~~~
{: .output}

Here we've defined a class with one method: `__init__`.
This method is the **initialiser** method,
which is responsible for setting up the initial values and structure of the data
inside a new instance of the class -
this is very similar to **constructors** in other languages,
so the term is often used in Python too.
The `__init__` method is called every time we create a new instance of the class,
as in `Variable(obj_id)`.
The argument `self` refers to the instance on which we are calling the method
and gets filled in automatically by Python -
we do not need to provide a value for this when we call the method.

Data encapsulated within our Variable class includes
the object's id, and a light curve dictionary, 
that contains a numpy array with timestamps and a numpy array with magnitude measurements.
In the initialiser method,
we set an object's id  to the value provided,
and create the numpy arrays for observations (initially empty).
Such data is also referred to as the attributes of a class
and holds the current state of an instance of the class.
Attributes are typically hidden (encapsulated) internal object details
ensuring that access to data is protected from unintended changes.
They are manipulated internally by the class,
which, in addition, can expose certain functionality as public behavior of the class
to allow other objects to interact with this class' instances.

## Encapsulating Behaviour

In addition to representing a piece of structured data
(e.g. an object that has an id and the lists with timestamps and magnitude observations),
a class can also provide a set of functions, or **methods**,
which describe the **behaviours** of the data encapsulated in the instances of that class.
To define the behaviour of a class we add functions which operate on the data the class contains.
These functions are the member functions or methods.

Methods on classes are the same as normal functions,
except that they live inside a class and have an extra first parameter `self`.
Using the name `self` is not strictly necessary, but is a very strong convention -
it is extremely rare to see any other name chosen.
When we call a method on an object,
the value of `self` is automatically set to this object - hence the name.
As we saw with the `__init__` method previously,
we do not need to explicitly provide a value for the `self` argument,
this is done for us by Python.

Let's add another method on our Variable class that adds observations to a Variable instance.

~~~
class Variable:
    """A Variable class"""
    def __init__(self, obj_id):
        self.obj_id = obj_id
        self.lc = {
                   'mjd': np.array([]),
                   'mag': np.array([])
                  }

    def add_observations(self, mjds, mags, mag_errs=None):
        """
        Adds observations to the light curve.
    
        Args:
          mjds: A vector of Modified Julian Dates (x values).
          mags: A vector of luminosities (y values).
        """
        self.lc['mjd'] = np.array(mjds)
        self.lc['mag'] = np.array(mags)
        if mag_errs is not None:
          self.lc['mag_errs'] = np.array(mag_errs)

        return


obj_id = lc_datasets['lsst']['objectId'].unique()[7]
b = 'g'
filt_band_obj = (lc_datasets['lsst']['objectId'] == obj_id) & (
        lc_datasets['lsst']['band'] == b
    )
obj_obs = lc_datasets['lsst'][filt_band_obj]
star = Variable(obj_id)
star.add_observations(obj_obs[time_col],obj_obs[mag_col])
print(star)
print(star.lc)
~~~
{: .language-python}

~~~
<__main__.Variable object at 0x7fc0fb40a750>
{'mjd': array([60559.2973682, 59791.3473572, 60559.2978172, 61017.0665232,
       60281.1630512, 59840.2103322, 60560.2654012
...
~~~
{: .output}

Note also how we used `mag_errs=None` in the parameter list of the `add_observations` method,
then initialise it if the value is not `None`, i.e. if the user passed magnitude errors.
This is one of the common ways to handle an optional argument in Python,
so we'll see this pattern quite a lot in real projects.

> ## Class and Static Methods
>
> Sometimes, the function we're writing doesn't need access to
> any data belonging to a particular object.
> For these situations, we can instead use a **class method** or a **static method**.
> Class methods have access to the class that they're a part of,
> and can access data on that class -
> but do not belong to a specific instance of that class,
> whereas static methods have access to neither the class nor its instances.
>
> By convention, class methods use `cls` as their first argument instead of `self` -
> this is how we access the class and its data,
> just like `self` allows us to access the instance and its data.
> Static methods have neither `self` nor `cls`
> so the arguments look like a typical free function.
> These are the only common exceptions to using `self` for a method's first argument.
>
> Both of these method types are created using **decorators** -
> for more information see
> the [classmethod](https://docs.python.org/3/library/functions.html#classmethod)
> and [staticmethod](https://docs.python.org/3/library/functions.html#staticmethod)
> decorator sections of the Python documentation.
{: .callout}

### Dunder Methods

Why is the `__init__` method not called `init`?
There are a few special method names that we can use
which Python will use to provide a few common behaviours,
each of which begins and ends with a **d**ouble-**under**score,
hence the name **dunder method**.

When writing your own Python classes,
you'll almost always want to write an `__init__` method,
but there are a few other common ones you might need sometimes.
You may have noticed in the code above that the method `print(star)`
returned `<__main__.Patient object at 0x7fd7e61b73d0>`,
which is the string representation of the `star` object.
We may want the print statement to display the object's id instead.
We can achieve this by overriding the `__str__` method of our class.

~~~
...
    def __str__(self):
      return str(self.obj_id)


star = Variable(obj_id')
print(star)
~~~
{: .language-python}

~~~
1405624461041897445
~~~
{: .output}

These dunder methods are not usually called directly,
but rather provide the implementation of some functionality we can use -
we didn't call `star.__str__()`,
but it was called for us when we did `print(star)`.
Some we see quite commonly are:

- `__str__` - converts an object into its string representation, used when you call `str(object)` or `print(object)`.
  Pay attention that we had to convert our `self.obj_id` to string in order for this method to work
- `__getitem__` - Accesses an object by key, this is how `list[x]` and `dict[x]` are implemented
- `__len__` - gets the length of an object when we use `len(object)` - usually the number of items it contains

There are many more described in the Python documentation,
but it’s also worth experimenting with built in Python objects to
see which methods provide which behaviour.
For a more complete list of these special methods,
see the [Special Method Names](https://docs.python.org/3/reference/datamodel.html#special-method-names)
section of the Python documentation.

> ## Exercise: Useful Methods for the Variable Class
>
> Add several methods to our class, that would provide the following functionality:
> - return the length of the lightcurve as the length of the `Variable` instance;
> - check that we are passing the suitable type of the data to our `add_observations` method, convert it into `np.array`
>   and check that the length of all observational arrays is the same.
>   
> A hint: you may want to write _several_ methods for the second task.
>
> > ## Solution
> > For the first task we can write our own `__len__` dunder method:
> >
> > ~~~
> > class Variable:
> > ...
> > def __len__(self):
> >     return len(self.lc["mjd"])
> > ~~~
> > {: .language-python}
> > 
> > For the second task we may want to break it into several features and write a function for
> > each of them:
> > ~~~
> > class Variable:
> > ...
> >
> >   def convert_to_array(self,data):
> >        # Check if the data is iterable and convert it into np.array, otherwise raise an exception
> >        if not isinstance(data, np.ndarray):
> >            if isinstance(data, (list,tuple,pd.Series)):
> >                data = np.array(data)
> >            elif isinstance(data, (int, float)):
> >                data = np.array([data])
> >            else:
> >                raise ValueError("The data type of the input is incorrect!")
> >        return data
> >
> >    def compare_len(self,arrs):
> >        # Check that all arrays are of the same length
> >        lens = [len(arr) for arr in arrs]
> >        if len(set(lens)) > 1: # set() turns an iterable into a set of unique values.
> >        # If the values are all the same, the set will contain only one element
> >            raise ValueError("Passed timestamps and mags or mag_errs arrays have different lengths!")
> >        return
> >        
> >   def add_observations(self, mjds, mags, mag_errs=None):
> >        """
> >        Adds observations to the light curve.
> >
> >        Args:
> >          mjds: A vector of Modified Julian Dates (x values).
> >          mags: A vector of luminosities (y values).
> >          mag_errs: A vector of magnitude errors.
> >        """
> >        self.lc["mjd"] = self.convert_to_array(mjds)
> >        self.lc["mag"] = self.convert_to_array(mags)
> >        if mag_errs is not None:
> >            self.lc["mag_errs"] = self.convert_to_array(mag_errs)
> >        self.compare_len(self.lc.values())
> >        return        
> > ~~~
> > {: .language-python}
> > 
> {: .solution}
> 
{: .challenge}

### Properties

The final special type of method we will introduce is a **property**.
Properties are methods which behave like data -
when we want to access them, we do not need to use brackets to call the method manually.

~~~
class Variable:
    ...

    @property
    def mean_mag(self):
        return np.mean(self.lc['mags'])
...
star = Variable(obj_id)
...
mean_mag = star.mean_mag
print(mean_mag)
~~~
{: .language-python}

~~~
18.03180312045771
~~~
{: .output}

You may recognise the `@` syntax from episodes on
parameterising unit tests and functional programming -
`property` is another example of a **decorator**.
In this case the `property` decorator is taking the `last_observation` function
and modifying its behaviour,
so it can be accessed as if it were a normal attribute.
It is also possible to make your own decorators, but we won't cover it here.

## Relationships Between Classes

We now have a language construct for grouping data and behaviour
related to a single conceptual object.
The next step we need to take is to describe the relationships between the concepts in our code.

There are two fundamental types of relationship between objects
which we need to be able to describe:

1. Ownership - x **has a** y - this is **composition**
2. Identity - x **is a** y - this is **inheritance**

### Composition

In object oriented programming, we can make things components of other things.

We often use **composition** where we can say 'x *has a* y' -
for example in our `lcanalyzer` project,
we might want to say that a star _has_ a multiband lightcurve, 
or that a lightcurve _has_ a single-band lightcurve.

In the case of our example, we're already saying that our variable star has a lightcurve,
so we're already using composition here. 
We're currently implementing a single-band lightcurve as a dictionary with a known set of keys though,
and in the previous examples we used a dictionary to store DataFrames with single-band observations
to represent multi-band data. Nothing stops us from turning these dictionaries into
proper classes. In fact, this is exactly what we should do. For our current class example,
it will look like this:

~~~
class Lightcurve:
    """Class Lightcurve"""
    def __init__(self, mjds=None, mags=None, mag_errs = None):
        self.lc = {}
        if mjds is not None:
            self.lc = self.add_observations(mjds, mags, mag_errs)

    def add_observations(self, mjds, mags, mag_errs = None):
        self.lc["mjds"] = self.convert_to_array(mjds)
        self.lc["mags"] = self.convert_to_array(mags)
        if mag_errs is not None:
            self.lc["mag_errs"] = self.convert_to_array(mag_errs)
        self.compare_len(self.lc.values())
        return self.lc
    
    def convert_to_array(self,data):
...

class Variable:
    """A Variable class"""

    def __init__(self, obj_id):
        self.obj_id = obj_id
        self.mband_lc = {}
    
    def add_lc(self,band,mjds,mags,mag_errs=None):
        self.mband_lc[band] = Lightcurve(mjds,mags,mag_errs)
        return self.mband_lc
        
    def __str__(self):
        return str(self.obj_id)


star = Variable(obj_id)
star.add_lc(band = b,mjds = obj_obs[time_col], mags = obj_obs[mag_col])
star.mband_lc['g'].mean_mag

~~~
{: .language-python}

~~~
18.03180312045771
~~~
{: .output} 

Now we're using a composition of two custom classes to
describe the relationship between two types of entity in the system that we're modelling. The benefit of this 
approach is that we can create a new class called e.g. `Asteroid`, and it will require implementing its own analysis methods that will
differ from those of the class `Variable`. The implementation of `Asteroid` class will be different, but it can
still have the `Lightcurve`. Note that this is only one possible implementation; for example, we are still storing 
our light curve observations in a dictionary (`self.lc = {}`) to avoid rewriting our already existing functions,
but in reality it is likely will be more practical to turn `mjds` and `mags` into separate variables. 

### Inheritance

The other type of relationship used in object oriented programming is **inheritance**.
Inheritance is about data and behaviour shared by classes,
because they have some shared identity - 'x *is a* y'.
If class `X` inherits from (*is a*) class `Y`,
we say that `Y` is the **superclass** or **parent class** of `X`,
or `X` is a **subclass** of `Y`.

Extending the previous example, we can recall that there are different types
of variables. For example, we can have periodic variables, such as RR Lyrae, or transient ones,
such as supernovae (or SNe for short). Periodic variables will need a method for 
determining their periods, while transient ones will benefit from implementing
an algorithm for SNe classification. Instead of writing these
two classes completely independently, we can make them both the subclasses of the class `Variable`.

To write our class in Python,
we used the `class` keyword, the name of the class,
and then a block of the functions that belong to it.
If the class **inherits** from another class,
we include the parent class name in brackets.

~~~
class RRLyrae(Variable):
    """A class for RR Lyrae stars."""
    def __init__(self, obj_id):
        super().__init__(obj_id)
        self.period = None

    def period_determination(self, period_range=(0.1,3)):
        """A function to determine the period"""
        self.period = 0.3
        return

rr_lyrae = RRLyrae(obj_id)
rr_lyrae.period_determination()
print(rr_lyrae.mband_lc)
print(rr_lyrae.period)
~~~
{: .language-python}

~~~
{}
0.3
~~~
{: .output}

In this example, `Variable` is a **parent class** (or **superclass**),
and `RRLyrae` is a **subclass**.

There's something else we need to add as well -
Python doesn't automatically call the `__init__` method on the parent class
if we provide a new `__init__` for our subclass,
so we'll need to call it ourselves.
This makes sure that everything that needs to be initialised on the parent class has been,
before we need to use it.
If we don't define a new `__init__` method for our subclass,
Python will look for one on the parent class and use it automatically.
This is true of all methods -
if we call a method which doesn't exist directly on our class,
Python will search for it among the parent classes.
The order in which it does this search is known as the **method resolution order**.

The line `super().__init__(obj_id)` gets the parent class,
then calls the `__init__` method,
providing the `obj_id` variable that `Variable.__init__` requires.
This is quite a common pattern, particularly for `__init__` methods,
where we need to make sure an object is initialised as a valid `X`,
before we can initialise it as a valid `Y` -
e.g. a valid `Variable` must have an `obj_id`,
before we can properly initialise a `RRLyrae` model with their data.


> ## Composition vs Inheritance
>
> When deciding how to implement a model of a particular system,
> you often have a choice of either composition or inheritance,
> where there is no obviously correct choice.
> For example, it's not obvious whether a multi-messenger event (i.e. the one that has
> information coming with different carriers, such as electromagnetic waves and gravitational
> waves)
> *is a* light curve and *is a* [chirp](https://www.ligo.org/science/GW-Inspiral.php),
> or *has a* light curve and *has a* chirp.
>
> ~~~
> class Observation:
>     pass
>
> class Lightcurve(Observation):
>     pass
>
> class Chirp(Observation):
>     pass
>
> class MultiMessengerEvent(Lightcurve, Chirp):
>     # Multi-messenger event `is a` Lightcurve and `is a` Chirp
>     pass
> ~~~
> {: .language-python}
>
> ~~~
> class Observation:
>     pass
>
> class Lightcurve(Observation):
>     pass
>
> class Chirp(Observation):
>     pass
>
> class MultiMessengerEvent(Observation):
>     def __init__(self):
>         # Multi-messenger event `has a` Lightcurve and `has a` Chirp
>         self.lc = Lightcurve()
>         self.chirp = Chirp()
> ~~~
> {: .language-python}
>
> Both of these would be perfectly valid models and would work for most purposes.
> However, unless there's something about how you need to use the model
> which would benefit from using a model based on inheritance,
> it's usually recommended to opt for **composition over inheritance**.
> This is a common design principle in the object oriented paradigm and is worth remembering,
> as it's very common for people to overuse inheritance once they've been introduced to it.
>
> For much more detail on this see the
> [Python Design Patterns guide](https://python-patterns.guide/gang-of-four/composition-over-inheritance/).
{: .callout}

> ## Multiple Inheritance
>
> **Multiple Inheritance** is when a class inherits from more than one direct parent class.
> It exists in Python, but is often not present in other Object Oriented languages.
> Although this might seem useful, like in our inheritance-based model of the Multi-messenger event above,
> it's best to avoid it unless you're sure it's the right thing to do,
> due to the complexity of the inheritance heirarchy.
> Often using multiple inheritance is a sign you should instead be using composition -
> again like the Multi-messenger event model above.
{: .callout}

> ## Exercise: A Survey Class
>
> Use what we've learned to develop a class for processing the survey dataset itself.
> You already have most of the functionality implemented, now you have to think about
> how to re-implement it within an OOP paradigm. At the early stages of a software project
> it is pretty common to spend some time drafting out separate features before getting a
> cleare picture of what your software will be as a whole, especially if you work alone.
> This is called ['bottom-up design'](https://en.wikipedia.org/wiki/Bottom%E2%80%93up_and_top%E2%80%93down_design#Software_development)
> (as opposed to the 'top-down design', in which you start with drafting the overall architecture).
> Create a new branck for this work (you can call it `dev-oop` or something similar).
>
> You can start with defining the requirements. In the [software requirements
> episode](../31-software-requirements/index.html#exercise-new-solution-requirements) we already
> had a couple of solution requirements chiseled out:
> - **SR1.1.1** (from UR1.1): the package can read the data in different formats, such as .csv and .pkl;
> - **SR1.1.2** (from UR1.1): the package can filter out the rows with NaN entries, where NaNs can be filled with different values (e.g. -99.9).
>
> A couple more that you should include:
> - **SR1.1.3** (from UR1.1): the package can return the list of unique object IDs;
> - **SR1.1.4** (from UR1.1): the package can return a dict with the light curve of a user specified object in a user specified band.
> Think of a few more, and write them on the top of you new notebook (or `.py` file if that's more convenient for you)
> before starting your work.
>
> Try using Test Driven Development for any features you add:
> write the tests first, then add the feature. Don't forget to put the code you finished
> in the `.py` files!
>
> > ## Solution
> >
> > Let's assume that in addition to the requirements above we also added two more functional requirements:
> > - **SR1.1.5** (from UR1.1): the package can return a DataFrame with all the observations
>   of a given object in a given band;
> > - **SR1.1.6** (from UR1.1): the package can plot an unfolded light curve.
> > 
> > We can start the implementation with this:
> > - in the `lcanalyzer` directory we can create two new files for the
> >   models level of our architecture: `lightcurve.py` which will contain the `Lightcurve`
> >   class, and `survey.py` that will containe the `Survey` class. Also we will create a file `plots.py`
> >   for the views level of the architecture.
> > - in the `tests` directory we will create files `test_lightcurve.py`, `test_survey.py` and `test_plots.py`.
> > - in the root directory we will create a new `.ipynb` file where we will initially develop the code.
> >
> > The minimal implementation of the requirements above would look like this:
> > In `lcanalyzer/lightcurve.py`
> > ~~~
> > import pandas as pd
> > import numpy as np
> > 
> > class Lightcurve:
> >     """Class Lightcurve"""
> > 
> >     def __init__(self, mjds=None, mags=None, mag_errs=None):
> >         self.lc = {}
> >         if mjds is not None:
> >             self.add_observations(mjds, mags, mag_errs)
> > 
> >     def add_observations(self, mjds, mags, mag_errs=None):
> >         self.lc["mjds"] = self.convert_to_array(mjds)
> >         self.lc["mags"] = self.convert_to_array(mags)
> >         if mag_errs is not None:
> >             self.lc["mag_errs"] = self.convert_to_array(mag_errs)
> >         self.compare_len(self.lc.values())
> >         return self.lc
> > 
> >     def convert_to_array(self, data):
> >         if not isinstance(data, np.ndarray):
> >             if isinstance(data, (list, tuple, pd.Series)):
> >                 data = np.array(data)
> >             elif isinstance(data, (int, float)):
> >                 data = np.array([data])
> >             else:
> >                 raise ValueError("The data type of the input is incorrect!")
> >         return data
> > 
> >     def compare_len(self, arrs):
> >         lens = [len(arr) for arr in arrs]
> >         if len(set(lens)) > 1:
> >             raise ValueError(
> >                 "Passed timestamps and mags or mag_errs arrays have different lengths!"
> >             )
> >         return
> > 
> >     @property
> >     def mean_mag(self):
> >         return np.mean(self.lc["mags"])
> > 
> >     def __len__(self):
> >         return len(self.lc["mjds"])
> > ~~~
> > {: .language-python}
> > 
> > In `lcanalyzer/survey.py`:
> > ~~~
> > from lcanalyzer.lightcurve import *
> > import pandas as pd
> > 
> > class Survey:
> >     def __init__(
> >         self,
> >         filename,
> >         clean_nans = True,
> >         id_col="objectId",
> >         band_col="band",
> >         time_col="expMidptMJD",
> >         mag_col="psfMag",
> >     ):
> >         self.id_col = id_col
> >         self.band_col = band_col
> >         self.time_col = time_col
> >         self.mag_col = mag_col
> >         self.data = self.load_table(filename, clean_nans)
> >         self.unique_objects = self.data[self.id_col].unique()
> > 
> >     def load_table(self, filename, clean_nans = True):
> >         """Load a table from CSV file.
> > 
> >         :param filename: The name of the .csv file to load
> >         :returns: pd.DataFrame with the data from the file.
> >         """
> >         if filename.endswith(".csv"):
> >             df = pd.read_csv(filename)
> >         elif filename.endswith(".pkl"):
> >             df = pd.read_pickle(filename)
> >         if clean_nans == True:
> >            df = self.clean_table(df)
> >         return df
> >
> >     def clean_table(self,df,nan_val='nan'):
> >         if nan_val == 'nan':
> >             filt_nan = ~((df[self.mag_col] == nan_val) | (
> >                 df[self.mag_col].isnull())
> >             )
> >         return df[filt_nan]
> > 
> >     def get_obj_band_df(self, obj_id, band):
> >         filt_band_obj = (self.data[self.id_col] == obj_id) & (
> >             self.data[self.band_col] == band
> >         )
> >         return self.data[filt_band_obj]
> > 
> >     def get_lc(self, obj_id, band):
> >         df = self.get_obj_band_df(obj_id, band)
> >         lc = Lightcurve(mjds=df[self.time_col], mags=df[self.mag_col])
> >         return lc.lc
> > ~~~
> > {: .language-python}
> > 
> > In `lcanalyzer/plots.py`:
> > ~~~
> > """Module containing code for plotting a lightcurve."""
> > 
> > from matplotlib import pyplot as plt
> >     
> > def plotUnfolded(mjds,mags,mjd_label='Mjd (days)',mag_label='Mag',color='blue',marker='o'):
> >     fig = plt.figure(figsize=(7,5))
> >     ax = fig.add_subplot(1,1,1)
> >     ax.scatter(
> >         mjds,
> >         mags,
> >         color=color,
> >         marker=marker
> >     )
> >     ax.minorticks_on()
> >     ax.set_xlabel(mjd_label)
> >     ax.set_ylabel(mag_label)
> >     fig.tight_layout()
> >     plt.show()
> > ~~~
> > {: .language-python}
> >
>  {: .solution}
> 
{: .challenge}

> ## Optional Exercise: A Supernovae Class
>
> Formulate and implement a [few new solution requirements](../31-software-requirements/index.html#exercise-new-solution-requirements)
> for the business requirement BR2: "Functionality for analyzing transient light curves,
> such as observations of Supernovae Ia" and the subsequent user requirement
> UR2.2: "a possibility to automatically classify SNe types". Use what you've learned
> and developed in this episode to minimize code duplication. Try using Test Driven Development for any features you add:
> write the tests first, then add the feature.
>
> You can implement two new classes: a `TransientLC`, that would inherit after the `Lightcurve`,
> and `Supernovae`, that would inherit after the `Variable` and had an attribute of
> the class `TransientLightCurve`. Add a few new attributes to each of
> these classes and write some new methods (you can always use 'pass' in place of the functionality
> that you don't have time to implement right now) to understand what would be the best architecture
> for this implementation.
>
{: .challenge}

{% include links.md %}
