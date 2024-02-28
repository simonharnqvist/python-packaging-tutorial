# Introduction to Python packaging

## 1. Background: What is a package? Why package?
Explain package vs library vs module vs collection of scripts.

## 2. helloworld.py: a module for saying `hello` to the world

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

## 3. Let's organise some files
Explain file structure required - different options. Highlight what is important vs not.

## 4. Tools for setting up with `setuptools`

## 5. Inventing the `wheel`: building our package

## 6. `pip` installs packages, including yours (from GitHub)

## 7. PyPI and conda: making your package easily available


