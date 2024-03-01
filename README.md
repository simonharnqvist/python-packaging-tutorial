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
Explain package vs library vs module vs collection of scripts.

## 2. Test driven development: always start by failing

## 3. helloworld.py: a module for saying `hello` to the world

First module: helloworld.py
```{python}
def helloworld():
  """Print a greeting to the whole world."""
  print("Hello, world!")

def main():
  helloworld()

if __name__ == "__main__":
  main()
```

## 4. Let's organise some files
Explain file structure required - different options. Highlight what is important vs not.

## 5. Testing, testing

## 5. Tools for setting up with `setuptools`

## 6. Inventing the `wheel`: building our package

## 7. `pip` installs packages, including yours (from GitHub)

## 8. PyPI and conda: making your package easily available


