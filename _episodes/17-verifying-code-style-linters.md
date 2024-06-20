---
title: "Verifying Code Style Using Linters"
start: false
teaching: 15
exercises: 10
questions:
- "What tools can help with maintaining a consistent code style?"
- "How can we automate code style checking?"
objectives:
- "Use code linting tools to verify a program's adherence to a Python coding style convention."
keypoints:
- "Use linting tools in the IDE or on the command line (or via continuous integration) to automatically check your code style."
---

## Verifying Code Style Using Linters

Knowing the rules of code formatting helps us avoid mistakes 
during development, so it is always a good idea to dedicate
some time to learn how to write PEP8-consistent code from the beginning.
However, we also have tools that help us with formatting
the already existing code. These tools are called 
[**code linters**](https://en.wikipedia.org/wiki/Lint_%28software%29),
and their main function is to identify consistency issues in a report-style.
Linters analyse source code to identify and report on stylistic and even programming errors.
For Jupyter Lab, a number of linters (as well as other tools for improving the quality of
your code) are available as part of a package called [`nbQA`](https://github.com/nbQA-dev/nbQA).
Let's look at a very well-used one of these called `pylint`.

First, let's ensure we are on the `style-fixes` branch once again.

~~~
$ git checkout style-fixes
~~~
{: .language-bash}

Make sure that you have activated your `venv` environment, and install the `nbQA` 
package together with the supported tools:
~~~
$ python -m pip install -U nbqa
$ python -m pip install -U "nbqa[toolchain]"
~~~
{: .language-bash}

We should also update our `requirements.txt` with this new addition:

~~~
$ pip3 freeze > requirements.txt
~~~
{: .language-bash}

## Using Pylint on the Notebooks
Now we can use Pylint to check the quality of our code.
Pylint is a command-line tool that can help our code in many ways:

- **Check PEP8 compliance:**
  Pylint will provide a full list of places where your code does not
  comply with PEP8 
- **Perform basic error detection:** Pylint can look for certain Python type errors
- **Check variable naming conventions**:
  Pylint often goes beyond PEP8 to include other common conventions,
  such as naming variables outside of functions in upper case
- **Customisation**:
  you can specify which errors and conventions you wish to check for, and those you wish to ignore

Pylint can also identify **code smells**.

> ## How Does Code Smell?
>
> There are many ways that code can exhibit bad design
> whilst not breaking any rules and working correctly.
> A *code smell* is a characteristic that indicates
> that there is an underlying problem with source code, e.g.
> large classes or methods,
> methods with too many parameters,
> duplicated statements in both if and else blocks of conditionals, etc.
> They aren't functional errors in the code,
> but rather are certain structures that violate principles of good design
> and impact design quality.
> They can also indicate that code is in need of maintenance and refactoring.
>
> The phrase has its origins in Chapter 3 "Bad smells in code"
> by Kent Beck and Martin Fowler in
> [Fowler, Martin (1999). Refactoring. Improving the Design of Existing Code. Addison-Wesley. ISBN 0-201-48567-2](https://www.amazon.com/Refactoring-Improving-Design-Existing-Code/dp/0201485672/).
>
{: .callout}

Pylint recommendations are given as warnings or errors,
and Pylint also scores the code with an overall mark.
We can look at a specific file (e.g. `light-curve-analysis.ipynb`),
or a package (e.g. `lcanalyzer`).
First, let's look at our notebook:
~~~
$ nbqa pylint light-curve-analysis.ipynb --disable=C0114
~~~
{: .language-bash}

The output will look somewhat similar to this:
~~~
************* Module light-curve-analysis
light-curve-analysis.ipynb:cell_7:3:0: C0301: Line too long (115/100) (line-too-long)
light-curve-analysis.ipynb:cell_1:0:0: C0103: Module name "light-curve-analysis" doesn't conform to snake_case naming style (invalid-name)
light-curve-analysis.ipynb:cell_6:1:0: W0104: Statement seems to have no effect (pointless-statement)
light-curve-analysis.ipynb:cell_1:3:0: W0611: Unused numpy imported as np (unused-import)

-----------------------------------
Your code has been rated at 6.92/10
~~~
{: .output}

Your own outputs of the above commands may vary depending on
how you have implemented and fixed the code in previous exercises
and the coding style you have used.

The five digit codes, such as `C0103`, are unique identifiers for warnings,
with the first character indicating the type of warning.
There are five different types of warnings that Pylint looks for,
and you can get a summary of them by doing:

~~~
$ pylint --long-help
~~~
{: .language-bash}

Near the end you'll see:

~~~
  Output:
    Using the default text output, the message format is :
    MESSAGE_TYPE: LINE_NUM:[OBJECT:] MESSAGE
    There are 5 kind of message types :
    * (C) convention, for programming standard violation
    * (R) refactor, for bad code smell
    * (W) warning, for python specific problems
    * (E) error, for probable bugs in the code
    * (F) fatal, if an error occurred which prevented pylint from doing
    further processing.
~~~
{: .output}

So for an example of a Pylint Python-specific `warning`,
see the "W0611: Unused numpy imported as np (unused-import)" warning.

Now we can use Pylint for checking our `.py` files. We can do it in one go, 
checking the `lcanalyzer` package at once.

From the project root do:
~~~
$ pylint lcanalyzer
~~~
{: .language-bash}

Note that this time we use `pylint` as a standalone, without `nbqa`, since 
we are analysing ordinary Python files, not notebooks.

You should see an output similar to the following:
~~~
************* Module lcanalyzer
lcanalyzer/__init__.py:1:0: C0304: Final newline missing (missing-final-newline)
************* Module lcanalyzer.models
lcanalyzer/models.py:6:0: C0301: Line too long (107/100) (line-too-long)
lcanalyzer/models.py:41:0: W0105: String statement has no effect (pointless-string-statement)
lcanalyzer/models.py:12:0: W0611: Unused LombScargle imported from astropy.timeseries (unused-import)
************* Module lcanalyzer.views
lcanalyzer/views.py:5:0: C0303: Trailing whitespace (trailing-whitespace)
lcanalyzer/views.py:15:38: C0303: Trailing whitespace (trailing-whitespace)
lcanalyzer/views.py:21:0: C0304: Final newline missing (missing-final-newline)
lcanalyzer/views.py:6:0: C0103: Function name "plotUnfolded" doesn't conform to snake_case naming style (invalid-name)
lcanalyzer/views.py:4:0: W0611: Unused pandas imported as pd (unused-import)

------------------------------------------------------------------
Your code has been rated at 6.09/10 (previous run: 6.09/10, +0.00)
~~~
{: .output}

It is important to note that while tools such as Pylint are great at giving you
a starting point to consider how to improve your code,
they won't find everything that may be wrong with it.

> ## How Does Pylint Calculate the Score?
>
> The Python formula used is
> (with the variables representing numbers of each type of infraction
> and `statement` indicating the total number of statements):
>
> ~~~
> 10.0 - ((float(5 * error + warning + refactor + convention) / statement) * 10)
> ~~~
> {: .language-bash}
>
> Note whilst there is a maximum score of 10, given the formula,
> there is no minimum score - it's quite possible to get a negative score!
{: .callout}

> ## Exercise: Further Improve Code Style of Our Project
> Select and fix a few of the issues with our code that Pylint detected.
> Make sure you do not break the rest of the code in the process and that the code still runs.
> After making any changes, run Pylint again to verify you've resolved these issues.
{: .challenge}

Make sure you commit and push `requirements.txt`
and any file with further code style improvements you did
and merge onto your development and main branches.

~~~
$ git add requirements.txt
$ git commit -m "Added Pylint library"
$ git push origin style-fixes
$ git checkout develop
$ git merge style-fixes
$ git push origin develop
$ git checkout main
$ git merge develop
$ git push origin main
~~~
{: .language-bash}

## Auto-Formatters for the Notebooks
While Pylint provides us with a full report of all kinds of style inconsistencies,
most of which have to be fixed manually, some style mistakes can be fixed automatically. 
For this, we can use
[`black`](https://black.readthedocs.io/en/stable/) package, also integrated in the
`nbQA`. 
Save and close your notebook, and then go back to the command line.
After running the following command:
~~~
$ nbqa black light-curve-analysis.ipynb
~~~
{: .language-bash}
Open the notebook again, you will see that `black` forced line wrap at a certain length of the
line, fixed duplicated or missing spaces around parenthesis or commas, aligned elements 
in the definitions of lists and dictionaries and so on. Using `black`, you can enforce the same
style all over your code and make it much more readable.

> ## Another Way to Use Auto-Formatter
> You can use `black` not only from the command line but from within Jupyter Lab too.
> For this you will need to install additional extensions, for example,
> [Code Formatter extension](https://github.com/ryantam626/jupyterlab_code_formatter).
> The installation, as usual, can be done using `pip`:
> ~~~
> $ python -m pip install jupyterlab-code-formatter
> ~~~
>{: .language-bash}
> After that you need to refresh your Jupyter Lab page. In notebook tabs, a new button will appear
> at the end of the top panel. By clicking this button, you will execute the `black` formatter over the
> notebook.
{: .callout}

> ## Optional Exercise: Improve Code Style of Your Other Python Projects
> If you have a Python project you are working on or you worked on in the past,
> run it past Pylint to see what issues with your code are detected, if any.
{: .challenge}

It is possible to automate these kind of code checks
with GitHub's Continuous Integration service GitHub Actions -
we will come back to automated linting in the episode on
["Diagnosing Issues and Improving Robustness"](../24-diagnosing-issues-improving-robustness/index.html).

{% include links.md %}
