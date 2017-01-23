---
layout: post
keywords: import, from ... import ...
description: Explain how import and from ... import works in Python
title: "Import and from ... import in Python"
categories: [python]
tags: [import]
group: archive
icon: compass
---
{% include mathsyouth/setup %}


### Import introduction

A module can contain executable statements as well as function definitions. These statements are intended to initialize the module. They are executed only the first time the module name is encountered in an `import` statement.

Each module has its own private symbol table, which is used as the global symbol table by all functions defined in the module. Thus, the author of a module can use global variables in the module without worrying about accidental clashes with a user’s global variables. On the other hand, if you know what you are doing you can touch a module’s global variables with the same notation used to refer to its functions, `modname.itemname`.

Modules can import other modules. The imported module names are placed in the importing module’s global symbol table.


### [The *import* statement][import]

The basic `import` statement (no `from` clause) is executed in two steps:

1. find a module, loading and initializing it if necessary.
1. define a name or names in the local namespace for the scope where the `import` statement occurs.

If the requested module is retrieved successfully, it will be made available in the local namespace in one of three ways:

1. If the module name is followed by `as`, then the name following `as` is   bound directly to the imported module. For example, `import foo.bar.baz as fbb` means `foo.bar.baz` imported and bound as `fbb`.
1. If no other name is specified, and the module being imported is a top level module, the module’s name is bound in the local namespace as a reference to the imported module. For example, `import foo` looks for a `foo` model, loads it into memory and creates a module object called `foo`. It does not give you direct access to any of the names inside `foo` itself. If there is a `bar` object (which could be anything from a function to a submodule) it can be accessed like a member: `foo.bar`
1. If the module being imported is not a top level module, then the name of the top level package that contains the module is bound in the local namespace as a reference to the top level package. For example, `import foo.bar` makes `foo.bar` available without importing other stuff from `foo`. The difference from `import foo` is that if `foo` also had a `baz` member, it won’t be accessible.

### [The *from ... import ...* statement][import]

The `from` form uses a slightly more complex process:

1. find the module specified in the `from` clause, loading and initializing it if necessary;
1. for each of the identifiers specified in the `import` clauses:
   1. check if the imported module has an attribute by that name.
   1. if not, attempt to import a submodule with that name and then check the imported module again for that attribute.
   1. if the attribute is not found, ImportError is raised.
   1. otherwise, a reference to that value is stored in the local namespace, using the name in the `as` clause if it is present, otherwise using the attribute name.

#### Example

1. `from foo import bar` imports `bar` which could be anything that is declared in the module. It could be a function definition, a class (albeit a not-conventionally-named class) or even a submodule (which makes `foo` a package). Notice that if `bar` is a submodule of `foo`, this statement acts as if we simply imported `bar`. This means that a `bar` object is created, and its type is `module`. No `foo` object is created in this statement.
1. `from foo import bar as fizz` acts like `from foo import bar`, except instead of creating a `bar` object, it creates a `fizz` module with the same meaning. There are two main reasons to use this kind of statement: the first is when you’re importing two similarly named objects from two different modules. You then use `import as` to differentiate them. The other reason is when you import a lone-named function (or class) and use it extensively throughout your code and want to shorten its name.
1. `from foo import *` does NOT bring the name “foo” into your module, instead it brings all of the names inside `foo` (like `function` for example) except those beginning with an underscore (`_`) into your module. It is considered a bad practice. The reason is that you are in fact “contaminating” your global namespace. If you’re importing too much stuff from a certain package, you can either just suck it up or just import the package itself (`import foo`) and use the `foo` qualifier for every use. In this case, you can access those names without a prefix, like this: `function()`.

Note that when using `from package import item`, the `item` can be either a submodule (or subpackage) of the package, or some other name defined in the package, like a function, class or variable. The `import` statement first tests whether the item is defined in the package; if not, it assumes it is a module and attempts to load it.

Contrarily, when using syntax like `import item.subitem.subsubitem`, each item except for the last must be a package; the last item can be a module or a package but can’t be a class or function or variable defined in the previous item.

The `import` statement uses the following convention: if a package’s `__init__.py` code defines a list named `__all__`, it is taken to be the list of module names that should be imported when `from package import *` is encountered. Package authors may decide not to support it, if they don’t see a use for importing `*` from their package.

If `__all__` is not defined, the statement `from package import *` does not import all submodules from the `package` into the current namespace; it only ensures that the `package` has been imported (possibly running any initialization code in `__init__.py`) and then imports whatever names are defined in the package. This includes any names defined (and submodules explicitly loaded) by` __init__.py`. It also includes any submodules of the package that were explicitly loaded by previous `import` statements.

The public names defined by a module are determined by checking the module’s namespace for a variable named `__all__`; if defined, it must be a sequence of strings which are names defined or imported by that module. The names given in `__all__` are all considered public and are required to exist. If `__all__` is not defined, the set of public names includes all names found in the module’s namespace which do not begin with an underscore character (`_`). `__all__` should contain the entire public API. It is intended to avoid accidentally exporting items that are not part of the API (such as library modules which were imported and used within the module).


### The module search path

When a module named `spam` is imported, the interpreter first searches for a built-in module with that name. If not found, it then searches for a file named `spam.py` in a list of directories given by the variable `sys.path`:

```python
import sys
print sys.path
```

`sys.path` is initialized from these locations:

* the directory containing the input script (or the current directory).
* `PYTHONPATH` (a list of directory names, with the same syntax as the shell variable `PATH`).
* the installation-dependent default (e.g., `/usr/lib/python`).

After initialization, Python programs can modify `sys.path`. The directory containing the script being run is placed at the beginning of the search path, ahead of the standard library path. This means that scripts in that directory will be loaded instead of modules of the same name in the library directory. This is an error unless the replacement is intended.

Modules must be located in the search path, therefore you can:

* write your own modules within directories already defined in the search path.
  You may use symbolic links (on Linux) to keep the code somewhere else.
* modify the environment variable `PYTHONPATH` to include the directories
  containing the user-defined modules. On Linux/Unix, add the following line to
  a file read by the shell at startup (e.g. /etc/profile, .profile) <br>

  ```shell
  export PYTHONPATH=$PYTHONPATH:/.../user_defined_modules
  ```
* modify the sys.path variable itself within a Python script: <br>

  ```python
  import sys
  new_path = '/.../user_defined_modules'
  if new_path not in sys.path:
      sys.path.append(new_path)
  ```
  This method is not very robust, however, because you have to add the directory to your `sys.path` each time you want to import from a module in this directory.


### The relative import

By using leading dots in the specified module or package after `from` you can specify how high to traverse up the current package hierarchy without specifying exact names. One leading dot means the current package where the module making the import exists. Two dots means up one package level. Three dots is up two levels, etc. So if you execute `from . import mod` from a module in the `pkg` package then you will end up importing `pkg.mod`. If you execute `from ..subpkg2 import mod` from within `pkg.subpkg1` you will import `pkg.subpkg2.mod`. The specification for relative imports is contained within PEP 328.

`from .foo import bar` uses a modified search path for modules. Namely, instead of searching the entire `PYTHONPATH`, it searches in the directory where the importing file lives. So if you have two files called `fizz.py` and `foo.py`, you can use this `import` in `fizz`, and it will import the correct file, even if you have another `foo` module in your `PYTHONPATH`. What is this good for? Well, sometime you create modules with generic names like `common`, but you might also have a `common` package in the base of your project. Instead of giving different names, you can explicitly import the one closest to you.


### Reload  a module

`reload(foo)` reloads the `foo` module. It’s pretty useful when you have a console open playing with a bit of code you’re tweaking and want to continue without resetting your interpreter. If you used `from foo import bar`, it’s not enough to reload foo for bar to update. You need to both reload foo and call `from foo import bar` again.

Note: The instruction `import mymodule` only reads the module once, even if that module has changed. So each time something is changed in the module, quitting and restarting the interpreter is the best way to reload the module. Any sort of live reloading or no-caching strategy will not work seamlessly because objects from no-longer-existing modules can exist and because modules sometimes store state (see [reference][ref]).


### Import a module dynamically

[importlib.import_module()](https://docs.python.org/3/library/importlib.html#importlib.import_module) is provided to support applications that determine dynamically the modules to be loaded. The `import_module()` function acts as a simplifying wrapper around `importlib.__import__()`. The most important difference between these two functions is that `import_module()` returns the specified package or module (e.g. `pkg.mod`), while `__import__()` returns the top-level package or module (e.g. `pkg`).


### Import loops

An import loop would occur in Python if you import two or more modules in a cycle. For example, if in `foo.py` you would `from bar import Bar` and in `bar.py` you `from foo import Foo`, you will get an import loop. When this happens to you, the solution is usually to move the common objects from `foo.py` and `bar.py` to a different file (say `common.py`). However, sometimes there's actually a real loop of dependencies. For example, a method in `Bar` needs to create a `Foo` instance and vice versa. When the dependency is in a limited scope, you should remember that you can use the `import` command wherever you want. Putting imports at the top of the file is the common convention, but sometimes you can solve import loops by importing in a smaller scope, like in a method definition.


### Easter eggs

```python
import antigravity
import this
import __hello__
```


### Reference

- [Modules](https://docs.python.org/2/tutorial/modules.html)
- [The import statement][import]
- http://amir.rachum.com/blog/2013/10/10/python-importing/
- https://docs.python.org/3/reference/import.html#importsystem


[ref]: http://stackoverflow.com/questions/2918898/prevent-python-from-caching-the-imported-modules
[import]: https://docs.python.org/3/reference/simple_stmts.html#import