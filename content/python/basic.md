---
date: 2021-07-31
lastmod: ["lastmod"]
title: Python Basic
LinkTitle: Basic
description: Python Basic
tags: ["Python", "Basic"]
---

# Python Basic

## Install macOS

### Check the default version of Python on macOS 
```shell
$ python --version
Python 2.7.16
$ which python
/usr/bin/python
```

### Install the latest version using Homebrew
* `brew update` Update Homebrew itself
* `brew install` Install package
```shell
$ brew update
$ brew install python
```

### Add Python's path
```shell
$ vi ~/.bash_profile
export PATH="/usr/local/opt/python/libexec/bin:$PATH"
$ source ~/.bash_profile
```

### Check that the latest version is available
```shell
$ python --version
Python 3.9.13
$ which python
/usr/local/opt/python/libexec/bin/python
```

### If you want to update Python on macOS in the future
* `brew upgrade` Update if there is an update for the installed package
```shell
$ brew update
$ brew upgrade python
```

## Version

### Terminal
```shell
$ python --version
Python 3.9.13
```
Short option of ```--version```
```shell
$ python -V
Python 3.9.13
```

More detailed information
```shell
$ python -VV
Python 3.9.13 (main, May 24 2022, 21:28:31)
[Clang 13.1.6 (clang-1316.0.21.2)]
```

```shell
$ python
>>> print("Hi", "Mike")
Hi Mike
>>> print("Hi", "Mike", sep=", ", end=".\n")
Hi, Mike.
>>> 2 + 2
4
>>> type(1)
<class 'int'>
>>> type(1.0)
<class 'float'>
>>> .1
0.1
>>> 17 / 2
8.5
>>> 17 / 5
3.4
>>> 19 / 3
6.333333333333333
>>> 19 // 3
6
>>> 4 * 4 * 4
64
>>> 4 ** 3
64
>>> pie = 3.14159265359
>>> pie
3.14159265359
>>> round(pie, 2)
3.14
>>> import math
>>> math.sqrt(25)
5.0
>>> help(math)
```

```shell
$ python
>>> print('c:\name')
c:
ame
>>> print('c:\\name')
c:\name
>>> print(r'c:\name')
c:\name
>>> print("""
... aaa
... bbb
... ccc
... """)

aaa
bbb
ccc

>>>
>>> print('Hello, ' * 2 + 'Mark')
Hello, Hello, Mark
>>> str = ('aaaaaa''bbbbbb')
>>> str
'aaaaaabbbbbb'
>>> str = ('aaaaaa'
... 'bbbbbb')
>>> str
'aaaaaabbbbbb'
>>> 
>>> str = 'python'
>>> str[0]
'p'
>>> str[1]
'y'
>>> str[2]
't'
>>> str[-1]
'n'
>>> str[0:2]
'py'
>>> str[:2]
'py'
>>> str[2:]
'thon'
>>> 'J' + str[1:]
'Jython'
>>> str[:]
'Jython'
>>> n = len(str)
>>> n
6
>>> 
>>> s = 'My name is Alice. Hi, Alice.'
>>> s
'My name is Alice. Hi, Alice.'
>>> 
>>> is_start = s.startswith('My')
>>> is_start
True
>>> 
>>> s.find('Alice')
11
>>> 
>>> s.rfind('Alice')
22
>>> 
>>> s.count('Alice')
2
>>> 
>>> s.capitalize()
'My name is alice. hi, alice.'
>>> 
>>> s.title()
'My Name Is Alice. Hi, Alice.'
>>> 
>>> s.upper()
'MY NAME IS ALICE. HI, ALICE.'
>>> 
>>> s.lower()
'my name is alice. hi, alice.'
>>> 
>>> s.replace('Alice', 'Bob')
'My name is Bob. Hi, Bob.'
>>> 
>>> 'My name is {}.'.format('Chris')
'My name is Chris.'
>>> 
>>> "My name is {}. I'm a {}.".format('Chris', 'student')
"My name is Chris. I'm a student."
>>>
>>> "My family name is {1}, my first name is {0}.".format('John', 'Smith')
'My family name is Smith, my first name is John.'
>>> 
>>> "My family name is {family_name}, my first name is {first_name}.".format(first_name='John', family_name='Smith')
'My family name is Smith, my first name is John.'
>>>
>>> x = str(1)
>>> type(x)
<class 'str'>
>>> 
>>> first_name = 'John'
>>> family_name = 'Smith'
>>> print(f'My name is {first_name} {family_name}.')
My name is John Smith.
>>> 
```

### Source code

#### ```sys```
```python
import sys

print(sys.version)
# Python 3.9.13 (main, May 24 2022, 21:28:31)
# [Clang 13.1.6 (clang-1316.0.21.2)]
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
# 13

print(sys.version_info.major)
# 3
print(sys.version_info.minor)
# 9
print(sys.version_info.micro)
# 13
```

#### ```platform```

```python
import platform

print(platform.python_version())
# 3.9.13
print(type(platform.python_version()))
# <class 'str'>
print(platform.python_version_tuple())
# ('3', '9', '13')
print(type(platform.python_version_tuple()))
# <class 'tuple'>
```

## Package

```shell
$ pip list
```
|option|option|description|
|---|---|---|
|`-o`|`--outdated`|List outdated packages.|
|`-u`|`--uptodate`|List update packages.|
|`-e`|`--editable`|List editable projects.|
||`--format <list_format>`|Select the output format among: columns (default), freeze, or json.|
||`--not-required`|List packages that are not dependencies of installed packages.|

```shell
$ pip freeze
```

```pip list --format freeze``` equals ```pip freeze --all```

```shell
$ pip show <installed package>
```
e.g.)
```shell
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
```shell
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
```shell
$ python sample.py aaa 123
Namespace(foo='aaa', bar=123)
aaa
123
```

Check help using  ```-h``` or ```---help```
```shell
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
```shell
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
