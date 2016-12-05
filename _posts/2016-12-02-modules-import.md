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


When the `import mymodule` statement is executed, the module `mymodule` is searched in a given list of directories. This list includes
a list of installation-dependent default path (e.g., `/usr/lib/python`) as well as the list of directories specified by the environment
variable `PYTHONPATH`. The list of directories searched by Python is given by the `sys.path` variable:

```Python
import sys
print sys.path
```

Modules must be located in the search path, therefore you can:

* write your own modules within directories already defined in the search path. You may use symbolic links (on Linux) to keep the code
somewhere else.
* modify the environment variable `PYTHONPATH` to include the directories containing the user-defined modules. On Linux/Unix,
add the following line to a file read by the shell at startup (e.g. /etc/profile, .profile)

```shell
export PYTHONPATH=$PYTHONPATH:/home/emma/user_defined_modules
```

* modify the sys.path variable itself within a Python script:

```Python
import sys
new_path = '/home/emma/user_defined_modules'
if new_path not in sys.path:
    sys.path.append(new_path)
```

This method is not very robust, however, because it makes the code less portable (user-dependent path) and because you have to add
the directory to your `sys.path` each time you want to import from a module in this directory.
