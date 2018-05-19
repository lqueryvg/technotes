# Python

## Debugging

```
import pdb; pdb.set_trace()             # drop into debugger from code
import pudb; pu.db                      # ditto, for pudb
python -m pdb myscript.py arg1 arg2 etc # invoke on startup

import pprint
pprint.pprint(vars(my_object))          # dump object
print my_object.__dict__

pip show {module}                       # show where source code stored
```

### Array Indices
```
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
```
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
'row {}'.format(row_number)   # formatting (don't use % for formatting)
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

# capture groups
```
datepat = re.compile(r'(\d+)/(\d+)/(\d+)')      # compile first for performance
m = datepat.match('11/27/2012')
month, day, year = m.groups()
```
Replace with capture groups:
```
text = 'Today is 11/27/2012. PyCon starts 3/13/2013.'
re.sub(r'(\d+)/(\d+)/(\d+)', r'\3-\1-\2', text)
> 'Today is 2012-11-27. PyCon starts 2013-3-13.'
```

## ChainMap layered lookups
```
a = {'x': 1, 'z': 3 }
b = {'y': 2, 'z': 4 }
c = ChainMap(a,b)
print(c['y']) # Outputs 2 (from b)
print(c['z']) # Outputs 3 (from a)

```

## files
```
from __future__ import print_function
f = open('file', 'w')
print('hello', file=f)
```
```
with open(fname) as f:
    content = f.readlines()
content = [x.strip() for x in content] 
```


## range vs xrange 
- use `range` for forward compat (in Python3 `range` = `xrange`)
- unless using a large list:
  - `xrange` generally faster and less memory hungry
  - but `range` creates whole list in memory whereas `xrange` is lazy
```
for i in range(4)   # for i in [0, 1, 2, 3]
range(2, 10, 2)     # [2, 4, 6, 8]

$ python -m timeit 'for i in range(1000000):' ' pass'
10 loops, best of 3: 23.3 msec per loop

$ python -m timeit 'for i in xrange(1000000):' ' pass'    # faster
100 loops, best of 3: 12.8 msec per loop
```

# argparse
- `optparse` is deprecated
```
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
```
mylist = [x*x for x in range(3)]
```

### Generator
- can iterate only once; values are thrown away once used
```
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
```
class ThisClass(superClass):
  def __init__(self, my_arg):
    super(superClass, self).__init__(my_arg)
```

## Pydoc
```
def fred():
  """ some doc """

pydoc fred      # without .py extension
```

## Unittest
```
import unittest
class TestThing1(unittest.TestCase):
  def one_is_one(self):
    self.assertTrue(1 == 1)

if __name__ == '__main__':
    unittest.main(verbosity=2)
```

# dedent
```
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

# Flask

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

# dis

    from dis import dis
    dis(func)    # only funcs allowed

# CLI

    python -i main.py    # drop into interpreter prompt after running

# pytest
- path can point to a directory or a python file
- keyword can point to a method


# Pycharm shortcuts
```
Ctrl Shift A                # find action

Tab                         # indent 
Ctrl /                      # Comment / uncomment
Shift Enter                 # start new line
Ctrl Y                      # delete current line
Shift F6                    # rename
Ctrl Alt L                  # reformat file

Ctrl B                      # go to declaration
Ctrl Shift Left / Right     # go back / forward

Ctrl .                      # fold
Alt Down / Up               # next / prev method

Ctrl Alt Shift N    # goto symbol
Ctrl Shift F        # find in path (project)

Ctrl P              # param info (within method params)
```
