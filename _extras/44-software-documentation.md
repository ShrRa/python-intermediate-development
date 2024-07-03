---
title: "Software Documentation using Sphinx"
teaching: 15
exercises: 10
questions:
- "What tools can we use to document our software package?"
- "How to make software documentation public?"
---

## What options do we have for software documentation

## Using Sphinx

### Installing Sphinx


[Sphinx installation instructions](https://docs.readthedocs.io/en/stable/intro/getting-started-with-sphinx.html).

Install Sphinx using `pip`:

~~~
$ source venv/bin/activate
$ pip install sphinx
$ pip freeze > requirements.txt
~~~
{: .language-bash}

Sphinx requires a specific folder structure. Create documents folder, go there and launch `sphinx-quickstart` wizaard

~~~
$ mkdir docs
$ cd docs
$ sphinx-quickstart --quiet --project=LCAnalyzer --author=alex -v 0.1
~~~
{: .language-bash}

~~~
Finished: An initial directory structure has been created.

You should now populate your master file /mnt/Data/Work/GitHub/InterPython_Workshop_Example/docs/index.rst and create other documentation
source files. Use the Makefile to build the docs, like so:
   make builder
where "builder" is one of the supported builders, e.g. html, latex or linkcheck.
~~~
{: .output}

Launching `sphinx` this way we are telling it to skip most of the wizard questions (`--quiet` flag; in the most cases 
the default answers are the most suitable ones), and giving it only the bare minimum of information that Sphinx requires: the project name
`--project=LCAnalyzer`, the name of the author (`--author=alex`) and the version of our software (`-v 0.1`).

As we execute this command, Sphinx creates a number of new files in the `docs` folder.
~~~
$ ls -l
~~~
{: .language-bash}

~~~
total 10
drwxrwxrwx 1 root root   0 Jul  3 18:27 _build
-rwxrwxrwx 1 root root 965 Jul  3 18:27 conf.py
-rwxrwxrwx 1 root root 446 Jul  3 18:27 index.rst
-rwxrwxrwx 1 root root 800 Jul  3 18:27 make.bat
-rwxrwxrwx 1 root root 634 Jul  3 18:27 Makefile
drwxrwxrwx 1 root root   0 Jul  3 18:27 _static
drwxrwxrwx 1 root root   0 Jul  3 18:27 _templates
~~~
{: .output}

The files that we are going to edit are `conf.py` and `index.rst`.

### Using Read The Docs theme

You can quickly change the appearance of your documentation site using themes. 
In `conf.py` you will find a line that states `html_theme = 'alabaster'`.
You can change this parameter to any of the Sphinx built-in themes, listed
[here](https://www.sphinx-doc.org/en/master/usage/theming.html#builtin-themes). 

Another common design theme is the one developed by 'Read the Docs', which is
also a popular software documentation hosting. You can install this theme as any other python 
package:
~~~
$ pip install sphinx_rtd_theme
~~~
{: .language-bash}
and then add it to the Sphinx extensions in the `conf.py` file
and setting it as the html theme:
~~~
extensions = [
     ...
    'sphinx_rtd_theme',
]
...
html_theme = "sphinx_rtd_theme"
~~~
{: .language-python}

### Creating documentation

> ## Optional Exercise: Enhancing our Package Metadata
>
> An exercise
{: .challenge}

{% include links.md %}
