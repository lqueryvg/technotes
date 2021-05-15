# Python

## Debugging

```python
import pdb; pdb.set_trace()             # drop into debugger from code
import pudb; pu.db                      # ditto, for pudb
python -m pdb myscript.py arg1 arg2 etc # invoke on startup

import pprint
pprint.pprint(vars(my_object))          # dump object
print my_object.__dict__

pip show {module}                       # show where source code stored
pip install some_module==               # show all available versions
```

### Array Indices

```bash
    0   1   2   3   4
    H   e   l   l   o
   -5  -4  -3  -2  -1

a[start:end] # items start through end-1
a[start:]    # items start through the rest of the array
a[:end]      # items from the beginning through end-1
a[:]         # a copy of the whole array
a.insert(0, 'a')   # insert at start of list

a[start:end:step] # start through not past end, by step
a[-1]             # last item in the array
a[-2:]            # last two items in the array
a[:-2]            # everything except the last two items
a[::-1]           # reverse string
a = "1"; a[:-2]   # empty string
```

## Misc

```python
line[0]                                   # first char of string

price = slice(30,40)                      # named slice, use = items[price]
                                          # has start, stop & step attrs
if s.endswith(":"):   s = s[:-1]          # chop last if is ":"
if s.startswith(":"): s = s[1:]           # chop first if is ":"
",".join(my_list)                         # NB join is method of string, NOT list
len(my_list)                              # no. of elts in list
str = str.rstrip()                        # strip whitespace on right
list.append(elt)
list.split()                  # split on whitespace
list[-1]                      # last elt
list.pop(0)                   # remove first elt & return it
'row {}'.format(row_number)   # formatting (do NOT use % for formatting)
os.path.splitext(filename)[0] # remove file extension

d = OrderedDict()       # remembers order; from collections import OrderedDict

min(zip(prices.values(), prices.keys())) # min tuple based on value
a.keys() & b.keys()                      # keys which are common
a.keys() - b.keys()                      # keys in a that are not in b
a.items() & b.items()                    # common (key,value) pairs
first, *middle, last = grades            # extract middle as a list (star expression)
str.replace(old, new[, count])           # replace old with new in string, count times

("blah blah blah"
 "blah blah blah")      # split long string over multiple lines (but with no newlines)

sorted(rows, key=itemgetter('uid') # return sorted list of dicts by key
                                   # itemgetter() accepts multiple keys
sorted(users, key=attrgetter()

sorted(
  disks,                                # "hdisk20 hdisk5".split()
  cmp=lambda a,b: cmp(int(a), int(b)),  # numeric comparison
  key=lambda s: s.replace("hdisk","")   # extract number
)

  # or, better…

sorted(
  disks,                                    # "hdisk20 hdisk5".split()
  key=lambda s: int(s.replace("hdisk",""))  # extract number as int
)

from collections import Counter
words = "a b c d a d".split()
Counter(words).most_common(2)

# groupby
rows.sort(key=itemgetter('date'))   # must sort first
for date, items in groupby(rows, key=itemgetter('date')):
  # do stuff

[n for n in mylist if n > 0]        # filter
list(filter(is_int, mylist))        # use a custom function

{ key:value for key, value in           # select one dict from another
  prices.items() if value > 200 }

Coord = namedtuple('Coord', ['x', 'y'])  # named tuple, can access with Coord.x

sum(x * x for x in range(1,10))        # generator expression
str.endswith(".txt")
str.startswith("file_")
```

## capture groups

```python
datepat = re.compile(r'(\d+)/(\d+)/(\d+)')      # compile first for performance
m = datepat.match('11/27/2012')
month, day, year = m.groups()
```

Replace with capture groups:

```python
text = 'Today is 11/27/2012. PyCon starts 3/13/2013.'
re.sub(r'(\d+)/(\d+)/(\d+)', r'\3-\1-\2', text)
> 'Today is 2012-11-27. PyCon starts 2013-3-13.'
```

## ChainMap layered lookups

```python
a = {'x': 1, 'z': 3 }
b = {'y': 2, 'z': 4 }
c = ChainMap(a,b)
print(c['y']) # Outputs 2 (from b)
print(c['z']) # Outputs 3 (from a)

```

## files

```python
from __future__ import print_function
f = open('file', 'w')
print('hello', file=f)

with open(fname) as f:
    content = f.readlines()
content = [x.strip() for x in content]
```

## range vs xrange

- use `range` for forward compat (in Python3 `range` = `xrange`)
- unless using a large list:
  - `xrange` generally faster and less memory hungry
  - but `range` creates whole list in memory whereas `xrange` is lazy

```python
for i in range(4)   # for i in [0, 1, 2, 3]
range(2, 10, 2)     # [2, 4, 6, 8]

$ python -m timeit 'for i in range(1000000):' ' pass'
10 loops, best of 3: 23.3 msec per loop

$ python -m timeit 'for i in xrange(1000000):' ' pass'    # faster
100 loops, best of 3: 12.8 msec per loop
```

## argparse

- `optparse` is deprecated

```python
import argparse
parser = argparse.ArgumentParser(description='Do some stuff.')
parser.add_argument('filename', nargs=1)
parser.add_argument('--boxwidth', default=3, help='box width in cells')
parser.add_argument('-v', '--verbose', action='count', default=0)

args = parser.parse_args()
filename = args.filename[0]
if args.verbose == 0:
  logging.getLogger().setLevel(logging.CRITICAL)
elif args.verbose == 1:
  logging.getLogger().setLevel(logging.INFO)
else:
  logging.getLogger().setLevel(logging.DEBUG)
```

## Iterables, Generators & Yield

### Iterable

- can iterate multiple times, because values are stored in memory

```python
mylist = [x*x for x in range(3)]
```

### Generator

- can iterate only once; values are thrown away once used

```python
mygenerator = (x*x for x in range(3))
```

### Yield

- `yield x` returns a generator
- when enclosing function is called the first time, it runs to the yield
- next call continues from after the `yield` to the next `yield`
- generator is exhausted when function returns without further `yield`

## Classes

### @staticmethod

- this is a decorator for a method within a class
- class is not passed implicitly as first argument (unlike a class method)
- i.e. an ordinary method, but logically grouped with the class

### Initialise Superclass

```python
class ThisClass(superClass):
  def __init__(self, my_arg):
    super(superClass, self).__init__(my_arg)
```

## Pydoc

```python
def fred():
  """ some doc """

pydoc fred      # without .py extension
```

## Unittest

```python
import unittest
class TestThing1(unittest.TestCase):
  def one_is_one(self):
    self.assertTrue(1 == 1)

if __name__ == '__main__':
    unittest.main(verbosity=2)
```

## dedent

```python
from textwrap import dedent
str = dedent(
  """
      text to be dedented
  """
)
```

## Compile Python3 on CentOS

Need to install openssl-dev first, otherwise openssl headers not detected and
ssl feature not compiled in.

```sh
./configure --prefix=/home/local \
  --enable-shared LDFLAGS="-Wl,-rpath /home/local/lib"
make
make install
```

## Flask

    #!/usr/bin/env python

    import statsd
    from flask import Flask, request
    from flask_restful import Resource, Api
    from json import dumps

    app = Flask(__name__)
    api = Api(app)

    counter = 0
    c = statsd.StatsClient('localhost', 9125)

    class Get(Resource):
        def get(self):
            return {'counter': counter}

    class Add(Resource):
        def get(self):
            global counter
            counter = counter + 1
            c.incr('counter')
            return {'counter': counter}
    api.add_resource(Add, '/add')
    api.add_resource(Get, '/get')

    if __name__ == '__main__':
        app.run()

## dis

    from dis import dis
    dis(func)    # only funcs allowed

## CLI

    python -i main.py    # drop into interpreter prompt after running

## pytest

```
pytest dir/
pytest file.py
pytest -k my_method_name file.py
```

- path can point to a directory or a python file
- keyword can point to a method

## Pycharm shortcuts

Highlights:

```
Ctrl Shift A            # find action
Ctrl Cmd g              # select all occurances
Tab                     # indent selected lines
Shift Tab               # un-indent selected lines
Cmd Backspace           # delete line
Cmd D                   # duplicate line
Shift Enter             # start new line
Option Shift Up / Down  # move line
Ctrl Alt Shift N        # go to symbol
Ctrl Shift Left / Right # nav back / forward
Ctrl B / Ctrl Click     # go to declaration
Ctrl Shift F            # find in path (project)
Shift F6                # rename
Ctrl P                  # param info within function
```

General:

```text
Ctrl D        # duplicate line
Ctrl /        # Comment / uncomment
Ctrl Y        # delete current line
Ctrl Alt L    # reformat file
Ctrl .        # fold
Alt Down / Up # next / prev method
```


## pyenv

```bash
# first, remove all brew related pythons

brew uninstall python...
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
# see ~/dotfiles/profile.d/python.sh for shell init


pyenv install --list  # list avaiable python version (depends on pyenv version)
pyenv install 3.4.5
pyenv install 2.7.14
pyenv install 3.6.5
pyenv versions        # list all available versions
pyenv version         # show current
pyenv global 3.6.5    # set the global version
pyenv shell 2.7.14    # point this shell to a specific version
pyenv shell --unset   # deactivate
pyenv shell -         # previous
pyenv local           # show 

git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
# see ~/dotfiles/profile.d/python.sh for shell init

pyenv virtualenv 3.6.5 john    # create venv
pyenv virtualenvs             # list
pyenv activate john           # activate venv
pyenv version                   # show current
pyenv virtualenv-delete john
```

## format

From: https://kapeli.com/cheat_sheets/Python_Format_Strings.docset/Contents/Resources/Documents/index

# Width and alignment
'hey {:10}'.format('hello')       Specify width (Aign left, space fill)
'{:010}'.format(2)                 Fill with zeroes,                Output: 0000000002
'{:*^20}'.format('text')           Specify width, align center      Output: '********text********'

# Access methods
'{}, {}, {}'.format(1, 2, 3)                   position (>=2.7),    Output: 1, 2, 3 
'{0}, {1}, {2}'.format(1, 2, 3)                ordinal position,    Output: 1, 2, 3
'{v1}, {v2}, {v2}'.format(v1=1, v2=2, v3=3)   keyword,              Output: 1, 2, 2
'{[1]}'.format(['1st', '2nd', '3rd'])          index,               Output: 2nd
'{.name}'.format(sys.stdin)                    element attribute    Output: <stdin>
'{[name]}'.format({'name': 'thing'})          key                   Output: something 

# Numerics
'{:x}'.format(100)            hex                                   Output: 64
'{:X}'.format(3487)           hex (uppercase)                       Output: D9F
'{:#x}'.format(100)           hex (including the 0x)                Output: 0x64
'{:b}'.format(100)            Binary representation                 Output: 1100100
'{:c}'.format(100)            Character representation              Output: d
'{:d}'.format(100)            Decimal representation (default)      Output: 100
'{:,}'.format(1000000)        With thousands separator              Output: 1,000,000
'{:o}'.format(100)            Octal representation                  Output: 144
'{:n}'.format(100)            Like d, use locale for separators     Output: 100
'{:e}'.format(0.0000000001)   Exponent                              Output: 1.000000e-10
'{:E}'.format(0.0000000001)   Exponent (capital 'E')                Output: 1.000000E-10
'{:f}'.format(3/14.0)         Fixed point                           Output: 0.214286
'{:g}'.format(3/14.0)         General format                        Output: 0.214286
'{:%}'.format(0.66)           Percentage                            Output: 66.000000%
'{:.3}'.format(0.214286)      Precision                             Output: 0.214

# Conversion
'{!r}'.format('string')      Call repr on args                      Output: 'string'
'{!s}'.format(1.53438987)    Call str on args                       Output: 1.53438987
