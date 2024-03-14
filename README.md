# Introduction to Python packaging

## Background: What is a package? Why package?
A package is simply a way of bundling code together so that you (or someone else) can reuse it without worrying about paths to scripts (or worse, copy-pasting the actual code). There are additional benefits to packaging beyond reusability, like unit testing, automatic dependency management, versioning - but we'll get to that. 

If you code in Python, you almost certainly use packages all the time; `SciPy`, `NumPy`, and `Pandas` are all Python packages. Each package contains modules, which you can generally think of a single script that contains functions and classes. The package `SciPy` contains a `stats` module, so we can do:
```{python} 
from scipy import stats
```
or just
```{python}
import scipy.stats
```
What this tells Python to do is to go into a directory called `scipy` and find the file called `stats`. **There is no magic here: it's just file names.** Because you have installed `SciPy`, Python knows where to go look for these files (probably in a folder called something like `/conda/[name_of_conda_environment]/scipy/stats`. The code exists on your computer, and Python just uses it just like if you had written the code yourself.


Some terminology:
* A **module** is a collection of functions or classes. Typically it refers to a single script that we can import functions or classes from.
* A **package** is a reuseable bundle of at least one module, typically something that doesn't belong to the *standard library*. (E.g. `math` is a _module_ that ships with Python, so it's not really a _package_)
* A **library** is a collection of code - it may be a single module or a whole package. A package may consist of multiple libraries, and a library can contain multiple packages.



## Test driven development: always start by failing

#### What's a test?
In software development, a test is a piece of code that checks that your other code is working. If we have a function that adds two numbers together:
``` {python}
def add_numbers(num1, num2):
    """Add num1 to num2"""
    return num1 + num2
```

Then we write some tests to check that the function works
```{python}
def test_add_numbers():
    assert add_numbers(1, 1) == 2
```

We might write a few of these. What if one of the numbers is negative?
```{python}
def test_add_numbers_negative():
    assert add_numbers(1, -1) == 0
```

In this case, we would manually have to run each test every time we changed something:
```{python}
test_add_numbers()
test_add_numbers_negative()
```

But fortunately, there are ways of running all tests in one go - and we'll get there.

#### TDD
A popular approach in modern software development is something called *Test-driven development* (just TDD, if you're really cool). The workflow of TDD is simple, but perhaps a bit counter-intuitive:
1. Write a test to check that the function you haven't written yet gives you the output you want (yes - before you write the actual code!)
2. Check that the test fails
3. Write the function
4. Check that the test passes

There are a few benefits of TDD: it ensures that you write a working function, it keeps you focused on the task, and it means you won't have to go back and write a test later. There are some cases when TDD doesn't make sense - this is unfortunately often true of bioinformatics software when you need to write functions step-wise while working on real files. But in our examples today, there is no excuse not to.

## Our first package: HelloTools
We're now going to design a package. If you have a single-file script that contains standalone functions, you can use your own code here.

The most important thing about packages is getting the folder structure right. Otherwise, nothing will import correctly, and that's a real headache. Fortunately, we can keep our file structure pretty simple since we only have one module:
```
HelloTools
├── hellotools
│   ├──__init__.py
│   ├── helloworld.py
├── tests
│   ├── test_helloworld.py
└── pyproject.toml
```

`__init__.py` is an empty files, just acting as a signal to Python that `hellotools` is a package that can be imported from. 

Here, `HelloTools` is the name of the directory (and/or Git repo), and `hellotools` is the name of our package. Some people would call the subdirectory containing the code `src` or something like that, and there are different views on what is best practice. I think it's a good idea to give the subdirectory with the code the same name as our package, but once it's installed it won't matter.

#### Writing a test for hello_world()
We want to design a module `helloworld.py`, with a single function `hello_world()`, which just returns the string "Hello, world!". You might prefer to write something more interesting - perhaps something that takes input.

Now that we know what our module should do, we can write a test in `test_helloworld.py`:
```{python}
import pytest
from hellotools.helloworld import hello_world # remember: from package.module import function

def test_hello_world():
    output = hello_world()
    assert output == "Hello, world!"
```

Before we run the tests, we need to install `pytest`, preferably in a new mamba environment:
```
mamba create -n hellotools
mamba activate hellotools
mamba install pytest
```

Now let's check that it fails. Go into the HelloTools directory and run:
```{shell}
pytest
```

Hopefully, it will throw an error informing you that `hellotools` does not exist.

#### Fixing our first bug: we forgot to write the code in the first place

In `hellotools/hello_world.py`, let's create the function:
```{python}
def hello_world():
  """Print a greeting to the whole world."""
  return "Hello, world!"

def main():
  hello_world()

if __name__ == "__main__":
  main()
```

Now that the code exists, should the test pass?
```{shell}
pytest
```

#### Configuring the package with `setuptools`
Chances are that didn't work, because we haven't actually made the package yet. You might be able to do a local import of `hello_world()` without making a package, but we won't spend time on that when configuring a package is easy.

This is where the dodgy-looking `pyproject.toml` comes in. This is simply a configuration file that tells Python how to _build_ your package. Here's a minimal version:

```
[project]
name = "hellotools"
version = "0.0.1"
```

There are lots [more things]([https://setuptools.pypa.io/en/latest/userguide/declarative_config.html](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/)) we can specify in the config file, but we'll leave that there for now.

**Note: until recently, a `setup.py` was used instead of `pyproject.toml`. This is still a valid approach, and you may see other projects use this instead. However, new code should use `pyproject.toml` and use of `setup.py` will eventually be deprecated.**

## Building `wheel`s
I've been lying to you so far: a package is actually a bit more than just a collection of code. We also want to _build_ our package, i.e. convert it from scripts to something that others can execute. Fortunately, building is easy.

First we need install the Python module `build`:
```
pip install build
```

Then, in the top-level `HelloTools` directory, just run:
```
python -m build
```

This should produce directories called `build` and `dist`. We don't really care what's in them, but you should check that `dist` contains a `wheel` file (ending in `.whl`), which is the built distribution itself. A `wheel` is really nothing more than a compressed archive (like a zip) containing your package, and the benefit is that they making installing your package consistent and fast. Wheels are sometimes platform specific, so ideally we would build them on multiple systems (there are ways to do that automatically on GitHub), but fortunately wheels are made for users when installing your package if the one you have made won't work on their system.

## Installing your package
It may seem odd that you would install your own package, but this is really the main usecase for packaging in my view. Once the package is installed in an environment, you never have to worry about where you keep your scripts relative to your working directory. You can always just `import package` (provided you are in the right environment).

You can install a local package without putting it on any repository using `pip`. `pip` is the native package manager for Python, and its name is a [recursive acronym](https://en.wikipedia.org/wiki/Recursive_acronym) for `pip installs packages`. 

In the top-level directory `HelloTools`, run:
```
pip install .
```

However, if you install it this way, you will have to reinstall your package each time you edit the code. This is good if you are using your package for anything that may go into a publication, or if you want to try something out while keeping an untouched installation in a different environment*. However, it gets annoying if your package is still in development and you want to make frequent edits. For this, we can make an _editable install_ using the `-e` flag:
```
pip install -e .
```

Check that `hellotools` is installed in your conda environment:
```
conda list | grep "hellotools"
```

_* Really you should create a new Git branch for this_

#### Testing again
Let's see if `pytest` can find our package now:
```
pytest
```

What did you get? (This one should actually work...)

If the test passes: **congratulations - you've just made and tested your first package.**

## Sidenote: a build workflow
Professional software engineers set up advanced workflows for their software that automatically builds and tests every time they make changes to their code. You can set this up using [GitHub actions](https://github.com/features/actions) (which we won't cover that today), but there are still major benefits in setting up a simple workflow with what we've just done for each of your projects, just using a simple shell script.

Here's how it would look for `hellotools`:
```{shell}
python -m build
pip install .
pytest
```

Then it's easy to build, install, and test in one go each time you make (substantial) changes to your code, just using:

```{shell}
./build_workflow.sh # need to chmod +x before first use
```

## Make your package available on GitHub
Hopefully you see the benefit of making and installing packages even if you're the only user. But we still want to be able to easily share the package with others (or ourselves on a different computer). Fortunately, it is just as easy to install from GitHub as it is from your local files.

You just need to push your build (i.e. the `dist` and `build` directories) onto GitHub alongside your code (use `git add *` if necessary). Your package should now be installable as:
```
pip install git+https://github.com/[git username]/[repo].git
```

## PyPI and conda
Although putting your code on GitHub is usually sufficient for scientific purposes, you might want to make your package more easily available. There are two primary distribution systems for Python:
* PyPI is the channel that `pip` installs from. PyPI is in [principle very easy to push to once you have a project on GitHub](https://packaging.python.org/en/latest/guides/publishing-package-distribution-releases-using-github-actions-ci-cd-workflows/), but in reality this is a bit fiddly. It's probably only worth doing if you're publishing the package itself as a method.
* Conda is a package manager that uses multiple channels, most notably _conda-forge_ and _bioconda_. Publishing on _bioconda_ requires approval (unlike PyPI, where you can upload anything). Once you have uploaded your package to PyPI, there is a [process to add it to bioconda](https://bioconda.github.io/contributor/index.html).

For most users, this won't be necessary. But the point is: this is entirely possible to do, and you should really do it if you're publishing a tool.


