---
title: "From Modules to Packages"
teaching: 20
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- What is a 'package' in Python?
- Why should we organise our code into packages?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Explain how to group multiple modules into a package.
- Understand the purpose of an `__init__.py` file.
- Understand how `__all__` works.
- Understand the purpose of a `__main__.py` file.

::::::::::::::::::::::::::::::::::::::::::::::::

## Packages

In the previous episode, we showed how to convert a simple Python script into a reusable
module by bundling up different parts of the script into functions. However, as our
project grows, we may find it beneficial to spread the functionality of our project
over multiple files. Separating logically distinct units of code into their own files
will help others to understand how our project works, and it allows us to control which
features of our code are exposed to users at the top level.

The primary tool for organising a project spread across multiple files is the _package_.
Packages a very similar to modules, and they are defined by the directory structure we
use to organise our files.

As an example of how to use packages, we'll return to the SIR model introduced in the
previous lesson. After some more time spent developing this project, we may have added 
additional epidemiology models, such as the SEIR model which introduces a new population
category of those who are Exposed to a pathogen, but not yet infectious themselves (this
models diseases such as COVID-19 fairly well). We could also add a SIS model, which is
similar to the SIR model, but Recovered individuals do not gain immunity to the
pathogen, and instead return to the Susceptible population (this can apply to the common
cold and some types of flu). A good way to organise our code might be the following
directory structure:

<!-- Package unicode &#128230; -->
<!-- Folder unicode &#128193; -->
<!-- File unicode &#128220; -->

<code>
&#128230; epi_models<br>
|<br>
|\_\_\_\_&#128220; \_\_init\_\_.py<br>
|\_\_\_\_&#128220; SIR.py<br>
|\_\_\_\_&#128220; SEIR.py<br>
|\_\_\_\_&#128220; SIS.py<br>
</code>

Each model has its own file under the directory `epi_models`, and we've also added a
new file `__init__.py`. The presence of an `__init__.py` file marks the directory as
a package, and the contents of this file are run when the package or any modules within
are imported. For now, this file can be left empty -- we'll explain how to use this file
to set up and control our package in the next section.

:::::::::::::::::::::::::::::: discussion

### Namespace Packages

Since Python 3.3, if you omit `__init__.py`, you may find that the following code
snippets continue to work. This is because directories without `__init__.py` act as
_namespace packages_, which can be used to build a package using multiple 'portions'
spread across your system. The specification for namespace packages can be found in
[PEP 420][PEP 420].

Unless you're intending to create a namespace package, it is good practice to include
`__init__.py`, even if you choose to leave it empty, as this makes your intentions
for the package clearer.

::::::::::::::::::::::::::::::::::::::

To import our functions, we can now enter the directory that contains the package
`epi_models`, and call the following in an interactive session:

```python
>>> import epi_models.SIR
>>> S, I, R = epi_models.SIR.SIR_model(
        pop_size=8000000, beta=0.5, gamma=0.1, days=150, I_0=10
    )
```

Having to type the package name and the module name every time can be inconvenient, so
it's a good idea to use the alternative `import`-ing styles we discussed in the previous
lesson:

```python
>>> # Assign an alias to the import
>>> import epi_models.SEIR as SEIR
>>> S, E, I, R = SEIR.SEIR_model(*args)
>>> # Use 'from ... import ...' to get the function directly
>>> from epi_models.SEIR import SEIR_model
>>> S, E, I, R = SEIR_model(*args)
>>> # Import everything into the current namespace (not recommended!)
>>> from epi_models.SIS import *
>>> S, E, I, R = SEIR_model(*args)
```

As our project develops, we may decide that we need a further level of organisation.
For example, if our modelling tools and our plotting tools become sufficently complex,
we may decide to move these into their own directories. To do this, we can define
_sub-packages_ within our top-level package:

<code>
&#128230; epi\_models<br>
|<br>
|\_\_\_\_&#128220; \_\_init\_\_.py<br>
|<br>
|\_\_\_\_&#128193; models<br>
|\ \ \ \ |<br>
|\ \ \ \ |\_\_\_\_&#128220; \_\_init\_\_.py<br>
|\ \ \ \ |\_\_\_\_&#128220; SIR.py<br>
|\ \ \ \ |\_\_\_\_&#128220; SEIR.py<br>
|\ \ \ \ |\_\_\_\_&#128220; SIS.py<br>
|<br>
|\_\_\_\_&#128193; plotting<br>
\ \ \ \ \ |<br>
\ \ \ \ \ |\_\_\_\_&#128220; \_\_init\_\_.py<br>
\ \ \ \ \ |\_\_\_\_&#128220; plot\_SIR.py<br>
\ \ \ \ \ |\_\_\_\_&#128220; plot\_SEIR.py<br>
\ \ \ \ \ |\_\_\_\_&#128220; plot\_SIS.py<br>
</code>

Note that each sub-directory, and hence sub-package, requires its own `__init__.py`.
With our project organised in this way, we can now import each function using:

```python
>>> import epi_models.models.SIR as SIR
>>> # Alternatively, get a single function instead of a whole module:
>>> from epi_models.plotting.plot_SEIR import plot_SEIR_model
```

## Relative Imports

One advantage of organising our code into packages and sub-packages is that it allows us
to import other modules using _relative imports_. To show how these work, we'll add
an extra file `utils.py` inside the `models` subpackage.

<code>
&#128230; epi\_models<br>
|<br>
|\_\_\_\_&#128220; \_\_init\_\_.py<br>
|<br>
|\_\_\_\_&#128193; models<br>
|\ \ \ \ |<br>
|\ \ \ \ |\_\_\_\_&#128220; \_\_init\_\_.py<br>
|\ \ \ \ |\_\_\_\_&#128220; SIR.py<br>
|\ \ \ \ |\_\_\_\_&#128220; SEIR.py<br>
|\ \ \ \ |\_\_\_\_&#128220; SIS.py<br>
|\ \ \ \ |\_\_\_\_&#128220; utils.py<br>
|<br>
|\_\_\_\_&#128193; plotting<br>
\ \ \ \ \ |<br>
\ \ \ \ \ |\_\_\_\_&#128220; \_\_init\_\_.py<br>
\ \ \ \ \ |\_\_\_\_&#128220; plot\_SIR.py<br>
\ \ \ \ \ |\_\_\_\_&#128220; plot\_SEIR.py<br>
\ \ \ \ \ |\_\_\_\_&#128220; plot\_SIS.py<br>
</code>

This could contain utility functions that may be useful within `SIR.py`, `SEIR.py`,
and `SIS.py`. In each of these files, we could import the function `some_func` from
`utils.py` using the following syntax:

```python
from .utils import some_func
```

The dot preceding the module name instructs Python to search inside the current
(sub-)package to find a module with a matching name.

We may also find it useful for our plotting tools to have access to the modelling
functions. In that case, we can add the following to `plot_SIR.py`:

```python
from ..models.SIR import SIR_model
```

Here, the double dots indicate that we should look one package higher than the current
sub-package.

## The `__init__.py` file

`__init__.py` files do not need to contain any code to mark a directory as a
Python package, but if we wish, we can use them to control what is or isn't visible at
package-level scope, or to perform any additional setup steps.

Consider the `__init__.py` file in the `models` directory. Let's add the following
lines:

```python
# file: epi_models/models/__init__.py
from .SIR import SIR_model
from .SEIR import SEIR_model
from .SIS import SIS_model
```

When `SIR_model`, `SEIR_model` and `SIS_model` are brought into the local namespace in
`models/__init__.py`, they are brought into the namespace of `epi_models.models` too:

```python
>>> from epi_models.models import SIR_model
>>> # Equivalent to:
>>> from epi_models.models.SIR import SIR_model
```

If we wish to expose these functions to the user at the top level package, we can also
add the following to `epi_models/__init__.py`

```python
# file: epi_models/__init__.py
from .models import SIR_model, SEIR_model, SIS_model
```

Note that we can import these names directly from the `models` sub-package instead of
going to each of the modules in turn. It is now much easier for users to access these
functions:

```python
>>> from epi_models import SIR_model
```

When writing `__init__.py` files, it is important to consider what we _don't_ import.
Above, we did not import any functions from `utils.py`, as these are only intended
for use within the `models` sub-package, and there's no need to expose these
implementation details to the user. A well crafted `__init__.py` allows us to define a
public API while keeping the internals private, which makes it much easier to develop
our project further without breaking things for the end-user. The following sub-section
introduces the `__all__` variable, which allows us to more rigorously define a public
API.

As the contents of `__init__.py` is run whenever the package or any sub-packages/modules
are imported, we can also use `__init__.py` to perform any additional package-level
setup. For instance, it might be used to set up connection to a database used throughout
the package. In this way, `__init__.py` performs a similar role for a package as an
`__init__` function does for a class.


## Using `__all__` to control `from module import *`

`__all__` is an optional variable that we may set in our modules and packages (i.e. in
`__init__.py`). It should be a list of strings matching the names of all objects --
including functions, classes, constants and variables -- that we wish to be considered
'public' features that the user may wish to use.

`__all__` also changes the behaviour of `from module import *`. By default, this would
import all objects within the namespace of `module`, and bring them into the current
namespace. This may not be desirable, as this may bring utility
functions/classes/variables into the current scope, including any objects we explicitly
mark as private using a preceeding underscore, and even anything we've imported.
If we set `__all__`, only those objects with names matching the strings contained
within `__all__` will be loaded. For example, if we wrote the following:

```python
# file: epi_models/__init__.py
from .models import SIR_model, SEIR_model, SIS_model
__all__ = ["SIR_model", "SEIR_model"] # Note SIS_model is missing!
```

Calling the following in an interactive session would work just fine:

```python
>>> from epi_models import *
>>> SIR_model
```
```output
<function SIR_model at 0x7f0290dc9150>
```

However, the following would fail:

```python
>>> SIS_model
```
```output
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'SIS_model' is not defined
```

:::::::::::::::::::::: discussion

### 'Private' variables in Python

It is common for Python programmers to mark objects as private by prefixing their
names with an underscore, i.e. `_private_variable`, `_PrivateClass`, etc. These are
still accessible to the end-user, so it should be considered more a warning than a hard
rule. For an added layer of protection, variables set in class instances with two
underscores, i.e. `self.__x`, will have their names mangled when viewed from outside
the class, but they will still be locatable and modifiable to a determined individual.

Python programmers will also sometimes use a _trailing_ underscore, and this is commonly
used to avoid a name clash with a built-in object, e.g.:

```python
>>> lambda = 10 # Fails! Raises SyntaxError
>>> lambda_ = 10 # Works just fine
```
::::::::::::::::::::::::::::::

## Script-like Packages and the `__main__.py` file

In the previous lesson, we introduced the `if __name__ == "__main__"` idiom, and
discussed how to use this to maintain script-like behaviour in our reusable modules.
The good news is that this is still possible when we upgrade our modules to packages.
For instance, let's say that the file `epi_models/plotting/plot_SIR` contains the
following:

```python
import matplotlib.pyplot as plt
from ..models.SIR import SIR_model

def plot_SIR_model(S, I, R):
    """
    Plot the results of a SIR simulation.

    Parameters
    ----------
    S: List[float]
        Number of susceptible people on each day.
    I: List[float]
        Number of infected people on each day.
    R: List[float]
        Number of recovered people on each day.

    Returns
    -------
    None
    """
    plt.plot(range(len(S)), S, label="S")
    plt.plot(range(len(I)), I, label="I")
    plt.plot(range(len(R)), R, label="R")
    plt.xlabel("Time /days")
    plt.ylabel("Population")
    plt.legend()
    plt.show()


if __name__ == "__main__":
    S, I, R = SIR_model(
        pop_size=8000000, beta=0.5, gamma=0.1, days=150, I_0=10
    )
    plot_SIR_model(S, I, R)
```

If we jump into the directory `epi_models/plotting`, and call the following, we
get an error:

```bash
$ python3 plot_SIR.py
```
```output
Traceback (most recent call last):
  File "plot_SIR.py", line 2, in <module>
    from ..models.SIR import SIR_model
ImportError: attempted relative import with no known parent package
```

When running a single file as if its a script, Python will not consider it to be part
of a wider package, and hence relative imports will fail. To solve this, we should
run our script from outside the package using the `-m` flag:

```bash
$ python3 -m epi_models.plotting.plot_SIR
```

Note that we use dots rather than slashes to separate the names of each
sub-package/module, and we don't include `.py` at the end.

We can also add script-like behaviour to packages by adding a `__main__.py` file:

<code>
&#128230; epi\_models<br>
|<br>
|\_\_\_\_&#128220; \_\_init\_\_.py<br>
|\_\_\_\_&#128220; \_\_main\_\_.py<br>
|<br>
|\_\_\_\_&#128193; models<br>
|\ \ \ \ |<br>
|\ \ \ \ |\_\_\_\_&#128220; \_\_init\_\_.py<br>
|\ \ \ \ |\_\_\_\_&#128220; SIR.py<br>
|\ \ \ \ |\_\_\_\_&#128220; SEIR.py<br>
|\ \ \ \ |\_\_\_\_&#128220; SIS.py<br>
|\ \ \ \ |\_\_\_\_&#128220; utils.py<br>
|<br>
|\_\_\_\_&#128193; plotting<br>
\ \ \ \ \ |<br>
\ \ \ \ \ |\_\_\_\_&#128220; \_\_init\_\_.py<br>
\ \ \ \ \ |\_\_\_\_&#128220; plot\_SIR.py<br>
\ \ \ \ \ |\_\_\_\_&#128220; plot\_SEIR.py<br>
\ \ \ \ \ |\_\_\_\_&#128220; plot\_SIS.py<br>
</code>

As the module name of this file is already `__main__`, there's no need to use the
`if __name__ == "__main__"` idiom, and we may write this file as if it were a simple
script. Consider the following example `__main__.py` file, which uses `argparse` to
read arguments from the command line, and uses the library [PyYAML][PyYAML] to read
YAML files to determine inputs (it isn't necessary to understand how these tools work
at this stage -- this is simply an example of what a more advanced scripting interface
might look like):

```python
from argparse import ArgumentParser
import yaml
from .models import SIR_model, SEIR_model, SIS_model
from .plotting import plot_SIR_model, plot_SEIR_model, plot_SIS_model

# Create ArgumentParser, which can read inputs from the command line
parser = ArgumentParser(
    prog="epi_models",
    description="Tool for solving epidemiology models",
)

# Add arguments that the user must supply
parser.add_argument("model")
parser.add_argument("input_file")

# Read command line args
args = parser.parse_args()

# Get data from input file
with open(args.input_file, "r") as f:
    data = yaml.load(f, yaml.Loader)

# Run models and plot
# Note: In real code, you should validate the input data and
# raise helpful errors if something goes wrong!
if args.model == "SIR":
    S, I, R = SIR_model(**data)
    plot_SIR_model(S, I, R)
elif args.model == "SEIR":
    S, E, I, R = SEIR_model(**data)
    plot_SEIR_model(S, E, I, R)
elif args.model == "SIS":
    S, E, I, R = SIS_model(**data)
    plot_SIS_model(S, E, I, R)
else:
    raise ValueError(f"The model '{args.model}' is not recognised.")
```

If our input file `input.yaml` contains the following:

```yaml
pop_size: 8000000
beta: 0.5
gamma: 0.1
days: 100
I_0: 10
```

we could then run the SIR model and plot the results by calling:

```bash
$ python3 -m epi_models SIR input.yaml
```

Many Python tools make use of `__main__.py` in their top-level package to define a
command line interface. For example, when using pip to install new packages:

```bash
$ python3 -m pip install some_package
```

::::::::::::::::::::::::::::: keypoints

- Packages can be used to better organise our code as it becomes more complex
- Packages are defined by a directory structure and the presence of `__init__.py`
- By controlling what is or isn't imported in `__init__.py`, we can define a public
  API for our project.
- A package can be run as a script using the `-m` flag, provided it contains a
  file `__main__.py`.
:::::::::::::::::::::::::::::::::::::::
