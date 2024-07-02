---
title: "Automatically Testing Software"
teaching: 30
exercises: 20
questions:
- "Does the code we develop work the way it should do?"
- "Can we (and others) verify these assertions for themselves?"
- "To what extent are we confident of the accuracy of results that appear in publications?"
objectives:
- "Explain the reasons why testing is important"
- "Describe the three main types of tests and what each are used for"
- "Implement and run unit tests to verify the correct behaviour of program functions"
keypoints:
- "The three main types of automated tests are **unit tests**, **functional tests** and **regression tests**."
- "We can write unit tests to verify that functions generate expected output given a set of specific inputs."
- "It should be easy to add or change tests, understand and run them, and understand their results."
- "We can use a unit testing framework like Pytest to structure and simplify the writing of tests in Python."
- "We should test for expected errors in our code."
- "Testing program behaviour against both valid and invalid inputs is important and is known as **data validation**."
---

## Introduction

Being able to demonstrate that a process generates the right results
is important in any field of research,
whether it's software generating those results or not.
So when writing software we need to ask ourselves some key questions:

- Does the code we develop work the way it should do?
- Can we (and others) verify these assertions for themselves?
- Perhaps most importantly, to what extent are we confident of
  the accuracy of results that software produces?

If we are unable to demonstrate that our software fulfills these criteria,
why would anyone use it?
Having well-defined tests for our software is useful for this,
but manually testing software can prove an expensive process.

Automation can help, and automation where possible is a good thing -
it enables us to define a potentially complex process in a repeatable way
that is far less prone to error than manual approaches.
Once defined, automation can also save us a lot of effort, particularly in the long run.
In this episode we'll look into techniques of automated testing to
improve the predictability of a software change,
make development more productive,
and help us produce code that works as expected and produces desired results.


## What Is Software Testing?

For the sake of argument, if each line we write has a 99% chance of being right,
then a 70-line program will be wrong more than half the time.
We need to do better than that,
which means we need to test our software to catch these mistakes.

We can and should extensively test our software manually,
and manual testing is well-suited to testing aspects such as
graphical user interfaces and reconciling visual outputs against inputs.
However, even with a good test plan,
manual testing is very time consuming and prone to error.
Another style of testing is automated testing,
where we write code that tests the functions of our software.
Since computers are very good and efficient at automating repetitive tasks,
we should take advantage of this wherever possible.

There are three main types of automated tests:

- **Unit tests** are tests for fairly small and specific units of functionality,
  e.g. determining that a particular function returns output as expected given specific inputs.
- **Functional or integration tests** work at a higher level,
  and test functional paths through your code,
  e.g. given some specific inputs,
  a set of interconnected functions across a number of modules
  (or the entire code) produce the expected result.
  These are particularly useful for exposing faults in how functional units interact.
- **Regression tests** make sure that your program's output hasn't changed,
  for example after making changes your code to add new functionality or fix a bug.

For the purposes of this course, we'll focus on unit tests.
But the principles and practices we'll talk about can be built on
and applied to the other types of tests too.

## Set Up a New Feature Branch for Writing Tests

We're going to look at how to run some existing tests and also write some new ones,
so let's ensure we're initially on our `develop` branch we created earlier.
And then, we'll create a new feature branch called `test-suite` off the `develop` branch -
a common term we use to refer to sets of tests - that we'll use for our test writing work:

~~~
$ git checkout develop
$ git branch test-suite
$ git checkout test-suite
~~~
{: .language-bash}

Good practice is to write our tests around the same time we write our code on a feature branch.
But since the code already exists, we're creating a feature branch for just these extra tests.
Git branches are designed to be lightweight, and where necessary, transient,
and use of branches for even small bits of work is encouraged.

Later on, once we've finished writing these tests and are convinced they work properly,
we'll merge our `test-suite` branch back into `develop`.

Don't forget to activate our `venv` environment, launch Jupyter Notebook and let's see 
how we can test our software for light curve analysis. 

## Lightcurve Data Analysis

Let's go back to our [lightcurve analysis software project](/python-intermediate-development/11-software-project/index.html#light-curve-analysis-project).
Recall that it contains a `data` directory, where we have observations of presumably variable stars, namely RR Lyrae candidates, coming
from two sources: the Kepler Space Telescope and LSST Data Preview 0. 

> ## Don't forget about the best practices
>
> Following the best practices from [the corresponding section](/python-intermediate-development/14-jup-best-practices/index.html),
> let's start with creating a new notebook and drafting its structure.
> Using headers in the markdown cells, determine the sections of your notebook.
> 
> > ## Solution
> > We can start with the following sections:
> > 1. Imports
> > 2. Params
> > 3. Data loading
> > 4. Data inspection
> > 5. Selecting light curves for a single object
> > 6. Trying the model.py functions
> > 7. Test development
> > 
> > If we need something else, we can always add it later.
> > 
> {: .solution}
> 
{: .challenge}

Now let's open our data and have a look at it. For this we will use `pandas` package.
Import it, open the `lsst_RRLyr.pkl` catalogue and have a look at the format of this table.
Don't forget to put your code in the sections where it belongs!

~~~
import pandas as pd
~~~
{: .language-python}

~~~
lc_datasets = {}
lc_datasets['lsst'] = pd.read_pickle('data/lsst_RRLyr.pkl')
lc_datasets['lsst'].info()
~~~
{: .language-python}

~~~
lc_datasets['lsst'].head()
~~~
{: .language-python}

We can see that the dataset contains 11177 rows ('entries') and 12 columns.
the `lc_datasets['lsst'].info()` function also informs us about the types of the data in the columns,
as well as about the number of non-null values in each column.
Having a look at the top 5 rows (`lc_datasets['lsst'].head()`) gives us an impression of what kind
of values we have in each column.

For now there are four columns that we'll need:
1. 'objectId' that contains identificators of the observed objects;
2. 'band' that informs us about the band in which the observation is made;
3. 'expMidptMJD' that contains the time stamp of the observation;
4. 'psfMag' that containes measured magnitudes.

Let's assume that we want to know the maximum measured magnitudes of
the light curves in each band for a single
object. Our dataset contains observations in all bands for a number of sources, so we have
to a) pick only one source, and b) separate the observations in different bands from each other.
There are many ways of how to do this, but for the purposes of this episode we
will store the single-source observational data for each band in a dictionary
and then apply the `max_mag` function defined in our `models.py` file.

First, pick an id of the object that we will investigate.
~~~
### Pick an object
obj_id = lc_datasets['lsst']['objectId'].unique()[4]
~~~
{: .language-python}

And then store its observations in each band as items of a dictionary
`lc`.
~~~
### Get all the observations for this obj_id for each band
# Create an empty dict
lc = {}
# Define the bands names
bands = 'ugrizy'
# For each band create a bool array that indicates
# that this observation belongs to a certain object and is made in a
# certain band
for b in bands:
    filt_band_obj = (lc_datasets['lsst']['objectId'] == obj_id) & (
        lc_datasets['lsst']['band'] == b
    )
    # Select the observations and store in the dict 'lc'
    lc[b] = lc_datasets['lsst'][filt_band_obj]
~~~
{: .language-python}

> ## Don't forget about the best practices
>
> Do you see any variables defined in the code above that
> you should move in some other section?
> > ## Solution
> >
> > It seems very likely that we will need the variable `bands`
> > many times in the future. Let's move it to the 'Parameters'
> > section of the notebook.
> > 
> {: .solution}
> 
{: .challenge}

Have a look at the resulting dictionary: you will find that each element
has a key corresponding to the band name, and it's value will contain a Pandas DataFrame with
observations in this band.

Now we need to import the functions from the `models.py` file.
We should do it in the 'Imports' section. 

~~~
import lcanalyzer.models as models
~~~
{: .language-python}

Pick a function from this module, for example, `max_mag`, and apply it to one of the light
curves.

~~~
models.max_mag(lc['g'],'psfMag')
~~~
{: .language-python}

~~~
19.183367224358136
~~~
{: .output}

> ## Don't forget about the best practices
> Do you see anything in the code we just typed that can be put in the 'Parameters'?
> 
> > ## Solution
> > 
> > It is better to put
> > the magnitude column name, 'psfMag', in a variable (let's call it `colname_mag`) and
> > declare it in the 'Parameters' section. Why? Because chances are, in the future
> > we will want to apply our code to another dataset with different column names,
> > and if we continue using 'psfMag' across the notebook, later on we'll have to replace it
> > either manually, or using 'Search' functionality. In a large notebook both actions are likely
> > to produce errors.
> > 
> {: .solution}
>
{: .challenge}

How would you check if our `max_mag` function works correctly?

The answer that just came to your head, in all likelyhood, sounds similar
to this: "I would pass a simple DataFrame to this function and check manually
that the returned maximum value is correct". It makes perfect sense, and, perhaps, may work
with a function as simple as ours:

~~~
test_input = pd.DataFrame(data=[[1, 5, 3], [7, 8, 9], [3, 4, 1]], columns=list("abc"))
test_output = 7
models.max_mag(test_input, "a") == test_output
~~~
{: .language-python}
~~~
True
~~~
{: .output}

But now let's make the task more realistic and recall our original objective: to
get maximum values of the light curves in _all_ bands. We can write a function
for this as well:

~~~
### Get maximum values for all bands
def calc_stat(lc, bands, mag_col):
    # Define an empty dictionary where we will store the results
    stat = {}
    # For each band get the maximum value and store it in the dictionary
    for b in bands:
        stat[b + "_max"] = models.max_mag(lc[b], mag_col)
    return stat
~~~
{: .language-python}

And then construct the test data:
~~~
df1 = pd.DataFrame(data=[[1, 5, 3], [7, 8, 9], [3, 4, 1]], columns=list("abc"))
df2 = pd.DataFrame(data=[[7, 3, 2], [8, 4, 2], [5, 6, 4]], columns=list("abc"))
df3 = pd.DataFrame(data=[[2, 6, 3], [1, 3, 6], [8, 9, 1]], columns=list("abc"))
test_input = {"df1": df1, "df2": df2, "df3": df3}
test_output = {"df1_max": 8, "df12_max": 6, "df3_max": 8}
test_output == calc_stat(test_input, ["df1", "df2", "df3"], "b")
~~~
{: .language-python}

See what kind of output this code produces.

> ## What went wrong?
> If you just copied the code above, you got `False`.
> Try to find out what is wrong with our `calc_stat` function.
>
> > ## Solution
> > 
> > Our `calc_stat` function is fine. Our `test_output` contains two errors.
> > This example highlights an important point:
> > as well as making sure our code is returning correct answers,
> > we also need to ensure the tests themselves are also correct.
> > Otherwise, we may go on to fix our code only to return
> > an incorrect result that *appears* to be correct.
> > So a good rule is to make tests simple enough to understand
> > so we can reason about both the correctness of our tests as well as our code.
> > Otherwise, our tests hold little value.
> {: .solution}
>
{: .challenge}

Our crude test failed and didn't even inform us about the reasons they failed.
Surely there must be a better way to do this.

### Testing Frameworks

The example above shows that manually constructing even a simple test for a fairly simple function
can be tedious, and may produce new errors instead of fixing the old ones. Besides, 
we would like to test many functions in various scenarios, and for a complex
function or a library, a **test suite** - a set of tests - can include 
dozens of tests. Obviously, running them one by one in a notebook is not a good
idea, so we need a tool to automatize this process and to obtain a comprehensive
report on which of the tests were passed and which failed. We'd also prefer
to have something that tells us what exactly went wrong.

A solution for these tasks is called **unit testing frameworks**.
In such a framework we define the tests we want to run as functions,
and the framework automatically runs each of these functions in turn,
summarising the outputs. Since most people don't enjoy writing tests,
the unit testing fraimworks aim to make it simple to:

- Add or change tests,
- Understand the tests that have already been written,
- Run those tests, and
- Understand those tests' results.

Test results must also be reliable.
If a testing tool says that code is working when it's not,
or reports problems when there actually aren't any,
people will lose faith in it and stop using it.

We will use a testing framework called `pytest`. It is a Python
package that can be installed, as usual, using `pip`:

~~~
$ python -m pip install pytest
~~~
{: .language-bash}

> ## Why Use pytest over unittest?
>
> We could alternatively use another Python unit test framework, [unittest](https://docs.python.org/3/library/unittest.html),
> which has the advantage of being installed by default as part of Python. This is certainly a solid and established 
> option, however [pytest has many distinct advantages](https://realpython.com/pytest-python-testing/#what-makes-pytest-so-useful), 
> particularly for learning, including:
>
> - unittest requires additional knowledge of object-oriented frameworks (covered later in the course) 
> to write unit tests, whereas in pytest these are written in simpler functions so is easier to learn
> - Being written using simpler functions, pytest's scripts are more concise and contain less boilerplate, and thus are
> easier to read
> - pytest output, particularly in regard to test failure output, is generally considered more helpful and readable
> - pytest has a vast ecosystem of plugins available if ever you need additional testing functionality
> - unittest-style unit tests can be run from pytest out of the box!
>
> A common challenge, particularly at the intermediate level, is the selection of a suitable tool from many alternatives
> for a given task. Once you've become accustomed to object-oriented programming you may find unittest a better fit
> for a particular project or team, so you may want to revisit it at a later date!
>
{: .callout}

`pytest` requires that we put our tests into a separate `.py` file.
We already have some tests in `tests/test_models.py`:

~~~
"""Tests for statistics functions within the Model layer."""
import pandas as pd

def test_max_mag_integers():
    # Test that max_mag function works for integers
    from lcanalyzer.models import max_mag

    test_input_df = pd.DataFrame(data=[[1, 5, 3], [7, 8, 9], [3, 4, 1]], columns=list("abc"))
    test_input_colname = "a"
    test_output = 7

    assert max_mag(test_input_df, test_input_colname) == test_output
...
~~~
{: .language-python}

The first function represent the same **test case** as the one we tried first in
our notebook. However, it has a different format:
- we import the function we test right inside the test function, for clarity
of testing environment;
- then we specify our test input and output;
- and then we run our testing using **assert** keyword.

We haven't met with `assert` keyword before, however, it is essential for developing,
debugging and testing of robust and reliable code. `assert` keyword is responsible
for checking if some condition
is true. If it is true, nothing happens and the execution of the code continues. However,
if the condition is not fullfilled, an AssertionError occurs. When you write
your own `assert` checks, you can use the following syntax:
~~~
assert condition, message
~~~
{: .language-python}
And testing frameworks already have their own implementations of various assertions,
for example those that can check if two dictionaries are the same (and then inform us
where exactly they differ), if two variables are of the same type and so on. Apart from that,
some other packages, including `numpy` and `pandas`, have `testing` modules that allow you
to compare numpy arrays, DataFrames, Series and so on.

### Running Tests

Now we can run these tests in the command line using `pytest`:

~~~
$ python -m pytest tests/test_models.py
~~~
{: .language-bash}

Here, we use `-m` to invoke the `pytest` installed module,
and specify the `tests/test_models.py` file to run the tests in that file explicitly.

> ## Why Run Pytest Using `python -m` and Not `pytest` ?
>
> Another way to run `pytest` is via its own command,
> so we *could* try to use `pytest tests/test_models.py` on the command line instead,
> but this would lead to a `ModuleNotFoundError: No module named 'lcanalyzer'`.
> This is because using the `python -m pytest` method
> adds the current directory to its list of directories to search for modules,
> whilst using `pytest` does not -
> the `lcanalyzer` subdirectory's contents are not 'seen',
> hence the `ModuleNotFoundError`.
> There are ways to get around this with
> [various methods](https://stackoverflow.com/questions/71297697/modulenotfounderror-when-running-a-simple-pytest),
> but we've used `python -m` for simplicity.
{: .callout}

~~~
============================= test session starts ==============================
platform linux -- Python 3.11.5, pytest-8.0.0, pluggy-1.4.0
rootdir: /home/alex/InterPython_Workshop_Example
plugins: anyio-4.2.0
collected 2 items                                                              

tests/test_models.py ..                                                  [100%]

============================== 2 passed in 0.44s ===============================
~~~
{: .output}

Pytest looks for functions whose names also start with the letters 'test_' and runs each one.
Notice the `..` after our test script:

- If the function completes without an assertion being triggered,
  we count the test as a success (indicated as `.`).
- If an assertion fails, or we encounter an error,
  we count the test as a failure (indicated as `F`).
  The error is included in the output so we can see what went wrong.

So if we have many tests, we essentially get a report indicating which tests succeeded or failed.

> ## Exercise: Write Some Unit Tests
>
> We already have a couple of test cases in `tests/test_models.py`
> that test the `max_mag()` function.
> Looking at `lcanalyzer/models.py`,
> write at least two new test cases that test the `mean_mag()` and `min_mag()` functions,
> adding them to `tests/test_models.py`. Here are some hints:
>
> - You could choose to format your functions very similarly to `max_mag()`,
>   defining test input and expected result arrays followed by the equality assertion.
> - Try to choose cases that are suitably different,
>   and remember that these functions take a DataFrame and return a float
>   corresponding to a chosen column
> - Experiment with the functions in a notebook cell in `test-development.ipynb` to make sure your test result
>   is what you expect the function to return for a given input. Don't forget to put your
>   new test in `tests/test_models.py` once you think it's ready!
>
> Once added, run all the tests again with `python -m pytest tests/test_models.py`,
> and you should also see your new tests pass.
>
> > ## Solution
> >
> > ~~~
> > def test_min_mag_negatives():
> >    # Test that min_mag function works for negatives
> >    from lcanalyzer.models import min_mag
> >
> >    test_input_df = pd.DataFrame(data=[[-7, -7, -3], [-4, -3, -1], [-1, -5, -3]], columns=list("abc"))
> >    test_input_colname = "b"
> >    test_output = -7
> >
> >    assert min_mag(test_input_df, test_input_colname) == test_output
> > ~~~
> > {: .language-python}
> >
> > ~~~
> > def test_mean_mag_integers():
> >    # Test that mean_mag function works for negatives
> >    from lcanalyzer.models import mean_mag
> >
> >    test_input_df = pd.DataFrame(data=[[-7, -7, -3], [-4, -3, -1], [-1, -5, -3]], columns=list("abc"))
> >    test_input_colname = "a"
> >    test_output = -4.
> >
> >    assert mean_mag(test_input_df, test_input_colname) == test_output
> > ~~~
> > {: .language-python}
> {: .solution}
>
{: .challenge}

> ## Optional Exercise: Write a Unit Test for the `calc_stat` function
>
> If you have some time left, extract our `calc_stat` function into the
> `models.py` file and write a test for this function, using the (correct)
> test input and output from our experiments earlier.
>
{: .challenge}

The big advantage is that as our code develops we can update our test cases and commit them back,
ensuring that ourselves (and others) always have a set of tests
to verify our code at each step of development.
This way, when we implement a new feature, we can check
a) that the feature works using a test we write for it, and
b) that the development of the new feature doesn't break any existing functionality.

### What About Testing for Errors?

There are some cases where seeing an error is actually the correct behaviour,
and Python allows us to test for exceptions.
Add this test in `tests/test_models.py`:

~~~
import pytest
def test_max_mag_strings():
    # Test for TypeError when passing a string
    from lcanalyzer.models import max_mag

    test_input_colname = "b"
    with pytest.raises(TypeError):
        error_expected = max_mag('string', test_input_colname)
~~~
{: .language-python}

Note that you need to import the `pytest` library at the top of our `test_models.py` file
with `import pytest` so that we can use `pytest`'s `raises()` function.

Run all your tests as before.

Since we've installed `pytest` to our environment,
we should also regenerate our `requirements.txt`:

~~~
$ pip3 freeze > requirements.txt
~~~
{: .language-bash}

Finally, let's commit our new `test_models.py` file,
`requirements.txt` file,
and test cases to our `test-suite` branch,
and push this new branch and all its commits to GitHub:

~~~
$ git add requirements.txt tests/test_models.py
$ git commit -m "Add initial test cases for mean_mag() and min_mag()"
$ git push -u origin test-suite
~~~
{: .language-bash}


> ## Why Should We Test Invalid Input Data?
>
> Testing the behaviour of inputs, both valid and invalid,
> is a really good idea and is known as *data validation*.
> Even if you are developing command line software
> that cannot be exploited by malicious data entry,
> testing behaviour against invalid inputs prevents generation of erroneous results
> that could lead to serious misinterpretation
> (as well as saving time and compute cycles
> which may be expensive for longer-running applications).
> It is generally best not to assume your user's inputs will always be rational.
>
{: .callout}


> ## What About Unit Testing in Other Languages?
>
> Other unit testing frameworks exist for Python,
> including Nose2 and Unittest,
> and the approach to unit testing can be translated to other languages as well,
> e.g. pFUnit for Fortran,
> JUnit for Java (the original unit testing framework),
> Catch or gtest for C++, etc.
{: .callout}

{% include links.md %}
