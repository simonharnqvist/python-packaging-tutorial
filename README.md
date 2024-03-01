# Introduction to Python packaging

Order:
1. Background: packages, modules, libraries
2. File organisation of a package
3. Fail first: test-driven development
4. Configuring a package with setuptools (incl dependencies)
5. Building wheels
6. Installing packages
7. Making packages available to the world: GitHub, PyPI and conda

## 1. Background: What is a package? Why package?
A package is simply a way of bundling code together so that you (or someone else) can reuse it without copy-pasting the actual code. There are additional benefits to packaging beyond reusability, like unit testing, automatic dependency management, versioning - but we'll get to that. 

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
* A **package** is a reuseable bundle of at least one module, typically something that doesn't belong to the *standard library*. (E.g. `math` is a module that ships with Python, so it's not really a package)
* A **library** is a collection of code - it may be a single module or a whole package A package may consist of multiple libraries, and a library can contain multiple packages. It's a very vague term in Python...



## 2. Test driven development: always start by failing

#### What's a test?
In software development, a test is a piece of code that checks that your other code is working. If we have a function that adds two numbers together:
```{python}
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
A key principle of modern software development is something called *Test-driven development* (just TDD, if you're really cool). The workflow of TDD is simple, but perhaps a bit counter-intuitive:
1. Write a test to check that the function you haven't written yet gives you the output you want (yes - before you write the actual code!)
2. Check that the test fails
3. Write the function
4. Check that the test passes

There are a few benefits of TDD: it ensures that you write a working function, it keeps you focused on the task, and it means you won't have to go back and write a test later. There are some cases when TDD doesn't make sense - this is unfortunately often true of bioinformatics software when you need to write functions step-wise while working on real files. But in our examples today, there is no excuse not to.

## 3. Our first package: HelloTools
We're now going to design a package. If you have a single-file script that contains standalone functions, you can use your own code here.

The most important thing about packages is getting the folder structure right. Otherwise, nothing will import correctly, and that's a real headache. Fortunately, we can keep our file structure pretty simple since we only have one module:
```
HelloTools
├── hellotools
│   ├──__init__.py
│   ├── helloworld.py
├── tests
│   ├── test_helloworld.py
└── setup.cfg
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

Let's check that it fails. In a **terminal** window, go into the HelloTools directory and run:
```{shell}
pytest
```

Hopefully, it will throw an error informing you that neither `hellotools.helloworld` nor `hello_world()` exist.

#### Fixing our first bug: we forgot to write the code in the first place

In `hellotools/hello_world.py`, let's create the function:
```{python}
def helloworld():
  """Print a greeting to the whole world."""
  print("Hello, world!")

def main():
  helloworld()

if __name__ == "__main__":
  main()
```

Now that the code exists, should the test pass?
```{shell}
pytest
```


#### Configuring the package with `setuptools`
Chances are, that didn't work, because we haven't actually made the package yet. You might be able to do a local import of `hello_world()` without making a package, but we won't spend time on that when configuring a package is easy.

This is where the dodgy-looking `setup.cfg` comes in. This is a config




## 4. Let's organise some files
Explain file structure required - different options. Highlight what is important vs not.

## 5. Testing, testing

## 5. Tools for setting up with `setuptools`

## 6. Inventing the `wheel`: building our package

## 7. `pip` installs packages, including yours (from GitHub)

## 8. PyPI and conda: making your package easily available


