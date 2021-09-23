---
date: 2021-07-31
lastmod: ["lastmod"]
title: Python Memo
description: "H"
tags: ["Python", "Memo"]
---

# Python Memo

## Install macOS

### Check the default version of Python on macOS 
```bash
$ python --version
Python 2.7.16
$ which python
/usr/bin/python
```

### Install the latest version using Homebrew
* `brew update` Update Homebrew itself
* `brew install` Install package
```bash
$ brew update
$ brew install python
```

### Add Python's path
```bash
$ vi ~/.bash_profile
export PATH="/usr/local/opt/python/libexec/bin:$PATH"
$ source ~/.bash_profile
```

### Check that the latest version is available
```bash
$ python --version
Python 3.9.7
$ which python
/usr/local/opt/python/libexec/bin/python
```

### If you want to update Python on macOS in the future
* `brew upgrade` Update if there is an update for the installed package
```bash
$ brew update
$ brew upgrade python
```

## Version

### Terminal
```bash
$ python --version
Python 3.9.6
```
Short option of ```--version```
```bash
$ python -V
Python 3.9.6
```

More detailed information
```bash
$ python -VV
Python 3.9.6 (default, Jul 22 2021, 15:16:20) 
[GCC 8.3.0]
```

### Source code

#### ```sys```
```python
import sys

print(sys.version)
# 3.9.6 (default, Jul 22 2021, 15:16:20) 
# [GCC 8.3.0]
print(type(sys.version))
# <class 'str'>

print(sys.version_info)
# sys.version_info(major=3, minor=9, micro=6, releaselevel='final', serial=0)
print(type(sys.version_info))
# <class 'sys.version_info'>

print(sys.version_info[0])
# 3
print(sys.version_info[1])
# 9
print(sys.version_info[2])
# 6

print(sys.version_info.major)
# 3
print(sys.version_info.minor)
# 9
print(sys.version_info.micro)
# 6
```

#### ```platform```

```python
import platform

print(platform.python_version())
# 3.9.6
print(type(platform.python_version()))
# <class 'str'>
print(platform.python_version_tuple())
# ('3', '9', '6')
print(type(platform.python_version_tuple()))
# <class 'tuple'>
```

## Package

```bash
$ pip list
```
|option|option|description|
|---|---|---|
|`-o`|`--outdated`|List outdated packages.|
|`-u`|`--uptodate`|List update packages.|
|`-e`|`--editable`|List editable projects.|
||`--format <list_format>`|Select the output format among: columns (default), freeze, or json.|
||`--not-required`|List packages that are not dependencies of installed packages.|

```bash
$ pip freeze
```

```pip list --format freeze``` equals ```pip freeze --all```

```bash
$ pip show <installed package>
```
e.g.)
```bash
$ pip show numpy
```

## Argument

### ```sys.argv```
```sample.py```
```python
import sys

args = sys.argv

print(args)
print(args[1])
print(args[2])
```
Run
```bash
$ python simple.py aaa 123
['sample.py', 'aaa', '123']
aaa
```

### ```argparse```

#### Simple sample
```sample.py```
```python
import argparse

description = 'A description of this program.'
parser = argparse.ArgumentParser(description=description)
parser.add_argument('foo', type=str, help='First argument.')
parser.add_argument('bar', type=int, help='Second argument.')
args = parser.parse_args()

print(args)
print(args.foo)
print(args.bar)
```

Run
```bash
$ python sample.py aaa 123
Namespace(foo='aaa', bar=123)
aaa
123
```

Check help using  ```-h``` or ```---help```
```bash
$ python sample.py --help
usage: sample.py [-h] foo bar

A description of this program.

positional arguments:
  foo         First argument.
  bar         Second argument.

optional arguments:
  -h, --help  show this help message and exit
```

#### Advanced sample
```sample.py```
```python
import argparse

description = 'A description of this program.'
parser = argparse.ArgumentParser(description=description)

help = 'A required named argument.'
parser.add_argument('--named_arg1', type=str, required=True, help=help)

help = 'An optional named argument.'
parser.add_argument('--named_arg2', type=int, help=help)

help = 'True is specified automatically if you set this.'
parser.add_argument('--flag1', action='store_true', help=help)

help = 'False is specified automatically if you set this.'
parser.add_argument('--flag2', action='store_false', help=help)

args = parser.parse_args()

print(args)

print(args.named_arg1)
print(args.named_arg2)
print(args.flag1)
print(args.flag2)
```
Run
```bash
$ python sample.py --named_arg1 aaa --named_arg2 123 --flag1 --flag2
Namespace(named_arg1='aaa', named_arg2=123, flag1=True, flag2=False)
aaa
123
True
False
```

## File/Directory

### Move to current directory
```python
import os
os.getcwd()
```

### Get an absolute path
```python
import os
absolute_path = os.path.abspath(path)
```

### Check if it is an absolute path
```python
import os
os.path.isabs(path)
```

### Get a relative path
```python
import os
relative_path = os.path.relpath(path)
```

### Get a file's directory

```python
import os
dir_name = os.path.dirname(file_path)
```

### Get a file name
```python
import os
file_name = os.path.basename(file_path)
```

### Get a file's stem
```python
import os
file_stem = os.path.splitext(file_name)[0] # sample.png -> sample
```

### Check if a file or a directory exists

```python
import os
path = "/foo/bar/sample.png"
os.path.exists(path)
```

```python
import os
path = "/foo/bar/sample.png"
os.path.isfile(path)
```

```python
import os
path = "/foo/bar/"
os.path.isdir(path)
```

## References
* https://docs.python.org/ja/3.9/library/argparse.html
* https://qiita.com/kzkadc/items/e4fc7bc9c003de1eb6d0
* https://www.delftstack.com/ja/howto/python/python-get-filename-without-extension-from-path/
* https://note.nkmk.me/python-os-basename-dirname-split-splitext/
* https://note.nkmk.me/python-sys-platform-version-info/
* https://pip.pypa.io/en/stable/cli/pip_list/
