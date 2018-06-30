# Datadog Checks Dev

[![Latest PyPI version](https://img.shields.io/pypi/v/datadog-checks-dev.svg)](https://pypi.org/project/datadog-checks-dev)
[![Supported Python versions](https://img.shields.io/pypi/pyversions/datadog-checks-dev.svg)](https://pypi.org/project/datadog-checks-dev)
[![License](https://img.shields.io/pypi/l/datadog-checks-dev.svg)](https://choosealicense.com/licenses)

-----

This is the developer toolkit designed for use by any [Agent-based][1] check or
integration repository. There are 2 layers: one for purely development, and the
other for high level tasks such as releasing, dependency management, etc.

`datadog-checks-dev` is distributed on [PyPI](https://pypi.org) as a universal wheel
and is available on Linux, macOS, and Windows, and supports Python 2.7/3.5+ and PyPy.

**Table of Contents**

- [Development](#development)
  * [Installation](#installation)
    + [Usage](#usage)

## Development

This is the layer intended to be used directly for testing.

### Installation

```console
$ pip install datadog-checks-dev
```

This comes with `pytest`, `mock`, and other core requirements for testing.
Most likely you will not install this manually, but rather list it as the
only dependency in a check's `requirements-dev.txt`.

### Usage

Aside from being a source for test dependencies, this also provides many useful
utilities and fixtures (soon!) to avoid re-inventing the wheel. They can all be
found in [this directory][2].

Some examples:

```python
>>> import os
>>> from datadog_checks.dev import run_command, temp_chdir
>>>
>>> # Command can be a list or string
>>> result = run_command('python -c "import sys;print(sys.version)"', capture='out')
>>> result.stdout
'3.6.1 | packaged by conda-forge | (default, May 23 2017, 14:21:39) [MSC v.1900 64 bit (AMD64)]\r\n'
>>>
>>> os.getcwd()
'C:\\Users\\Ofek\\Desktop'
>>> with temp_chdir() as d:
...     d
...     os.getcwd()
...
'C:\\Users\\Ofek\\AppData\\Local\\Temp\\tmp5ef7c2ub'
'C:\\Users\\Ofek\\AppData\\Local\\Temp\\tmp5ef7c2ub'
>>> os.getcwd()
'C:\\Users\\Ofek\\Desktop'
```

In addition to being able to import `mock` itself, you also get a convenient `mocker`
fixture provided by [pytest-mock](https://github.com/pytest-dev/pytest-mock). It's a
thin-wrapper around `mock`'s patching API with the benefit of not having to worry
about undoing patches at the end of a test or stacking `with` statements.

Example from the docs:

```python
import os

class UnixFS:

    @staticmethod
    def rm(filename):
        os.remove(filename)

def test_unix_fs(mocker):
    mocker.patch('os.remove')
    UnixFS.rm('file')
    os.remove.assert_called_once_with('file')
```


[1]: https://github.com/DataDog/datadog-agent
[2]: https://github.com/DataDog/integrations-core/tree/master/datadog_checks_dev/datadog_checks/dev
