# Haskell
Notes from *Learn You a Haskell* and *Real World Haskell*.

## Basic Types
```Haskell
2 + 15                      -- 17
5 * -3                      -- error
5 * (-3)                    -- -15
True && False               -- False
not (True && True)          -- False
5 == 5                      -- True
5 /= 5                      -- False
"hello" == "hello"          -- True
5 + "llama"                 -- error
```

## Functions 1
```Haskell
succ 8                      -- 9
min 3.4 3.2                 -- 3.2
max 100 101                 -- 101
succ 9 * 10                 -- 100
succ (9 * 10)               -- 91
div 92 10                   -- 9 (integer division)
92 `div` 10                 -- 9 (using infix notation to make it clearer)
doubleMe x = x + x          -- define function plus body; it takes 1 param
doubleUs x y = x*2 + y*2    -- function which takes 2 params
doubleSmallNumber x = if x > 100 then x else x*2          -- else is mandatory
doubleSmallNumber' x = (if x > 100 then x else x*2) + 1
```
Single quote in function name is not special.
Usually `'` denotes strict (none lazy) functions, or slightly modified versions
of functions.

## Lists
- variable number of elements
- elements must be same type
- can be nested
- a list of lists of chars is a different type from a list of lists of numbers
- can be infinite
```Haskell
['h','e','l','l','o']       -- "hello" (a string is just a list of chars)
[1,2]                       -- actually shorthand for (1:(2:[]))
"hello" ++ " " ++ "world"   -- "hello world"  (string concatenation)
'c'                         -- single character
[1,2,'a',3,'b','c',4]       -- error (can't mix types in a list)
[1,2,3] ++ [4]              -- has to walk through whole list on LHS
'A':" SMALL CAT"            -- "A SMALL CAT"
                            --   cons operator concatenates quickly
                            --   short for construct
5:[1,2,3,4,5]               -- [5,1,2,3,4,5]
1:2:3:[]                    -- [1,2,3]
"Steve Buscemi" !! 6        -- 'B'   (get 6th elt, indices start at 0)
[9.4,33.2,96.2] !! 1        -- 33.2
```
- `[]`, `[[]]` and `[[],[],[]]` are all different things
- lists within lists can be different lengths, but not different types
```Haskell
[3,2,1] > [2,1,0]        -- True (compares two list's contents, one by one)
head [5,4,3,2,1]         -- 5
tail [5,4,3,2,1]         -- [4,3,2,1]
last [5,4,3,2,1]         -- 1
init [5,4,3,2,1]         -- [5,4,3,2]
head []                  -- error
length [5,4,3,2,1]       -- 5
null [1,2,3]             -- False
null []                  -- True
reverse [5,4,3,2,1]      -- [1,2,3,4,5]
take 3 [5,4,3,2,1]       -- [5,4,3]
take 0 [6,6,6]           -- []
drop 3 [5,4,3,2,1]       -- [2,1]
drop 100 [1,2,3,4]       -- []
minimum [8,4,2,1,5,6]    -- 1
maximum [1,9,2,3,4]      -- 9
sum [1,2,3]              -- 6
product [2,3,4]          -- 24
4 `elem` [3,4,5,6]       -- True (tells us if thing is in list)
replicate 3 10           -- [10,10,10]
```

### Ranges
```Haskell
[1..20]           -- [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20]
['a'..'z']        -- "abcdefghijklmnopqrstuvwxyz"
['K'..'Z']        -- "KLMNOPQRSTUVWXYZ"
[2,4..20]         -- [2,4,6,8,10,12,14,16,18,20]
[3,6..20]         -- [3,6,9,12,15,18]
[0.1, 0.3 .. 1]   -- [0.1,0.3,0.5,0.7,0.8999999999999999,1.0999999999999999]
                  -- (watch out for floats)
```

### Infinite Lists
```Haskell
[13,26..24*13] == take 24 [13,26..]     -- True
take 10 (cycle [1,2,3])                 -- [1,2,3,1,2,3,1,2,3,1]
take 12 (cycle "LOL ")                  -- "LOL LOL LOL "
take 10 (repeat 5)                      -- [5,5,5,5,5,5,5,5,5,5]
```

### List Comprehensions
```Haskell
[x*2 | x <- [1..10]]                    -- [2,4,6,8,10,12,14,16,18,20]
[x*2 | x <- [1..10], x*2 >= 12]         -- [12,14,16,18,20]
[ x | x <- [50..100], x `mod` 7 == 3]   -- [52,59,66,73,80,87,94]

boomBangs xs = [ if x < 10 then "BOOM!" else "BANG!" | x <- xs, odd x]
boomBangs [7..13]                       -- ["BOOM!","BOOM!","BANG!","BANG!"]

[ x*y | x <- [2,5,10], y <- [8,10,11], x*y > 50]  -- [55,80,100,110]
                                                  -- all possible products > 50

length' xs = sum [1 | _ <- xs]          -- _ means we don't care about a value

[ c | c <- "IdontLIKEFROGS", c `elem` ['A'..'Z']]     -- "ILIKEFROGS"

let xxs = [[1,3,5,2,3,1,2,4,5],[1,2,3,4,5,6,7,8,9],[1,2,4,2,1,6,3,1,3,2,3,6]]
[ [ x | x <- xs, even x ] | xs <- xxs]  -- [[2,2,4],[2,4,6,8],[2,4,2,6,2,6]]
                                        -- uses nested list comprehension)
```

## Tuples
- fixed number of elements
- can be mixed type
```Haskell
[(1,2),(8,11,5),(4,5)]  -- error, since list elements are not same type;
                        -- (8,11,5) is not same type as (1,2)
fst (8,11)              -- 8 (first)
snd (8,11)              -- 11 (second)

zip [1..] ["the", "big", "chi", "man"]
                    -- [(1,"the"),(2,"big"),(3,"chi"),(4,"man")]
```

Right triangles with sides under or equal to 10, side `b` isn't larger than the
hypotenuse `c` and side `a` isn't larger than side `b`, and the perimeter is
`24`.

``` Haskell
let rightTriangles' = [ (a,b,c) | c <- [1..10], b <- [1..c], a <- [1..b],
                        a^2 + b^2 == c^2, a+b+c == 24]
rightTriangles'         -- [(6,8,10)]
```

## Types
```Haskell
ghci> :t True
True :: Bool

ghci> :t "HELLO!"
"HELLO!" :: [Char]

ghci> :t (True, 'a')
(True, 'a') :: (Bool, Char)

ghci> :t 4 == 5
4 == 5 :: Bool
```

`[Char]` is synonymous with `String`.

- `Int` is a small integer (range dependent on machine architecture).
- `Integer` is unlimited.
- `Float` has single precision.
- `Double` double precision.
- `Bool` is `True` or `False`.
- `Char` single char, surrounded by single quotes.
- *Tuples* depend on their length and element types.
- `Ordering` a type which can be GT, LT or EQ. It is the type returned by
  `compare`.
```
ghci> "Abrakadabra" < "Zebra"
True

ghci> "Abrakadabra" `compare` "Zebra"
LT

ghci> 5 >= 2
True

ghci> 5 `compare` 3
GT
```

**Function signatures** (not compulsory):
```Haskell
addThree :: Int -> Int -> Int -> Int        -- Return type is on the right.
addThree x y z = x + y + z
```

### Type Variables
```Haskell
ghci> :t head
head :: [a] -> a
```

Here, `a` is a **type variable**, so `head` takes a list of any type and
returns single value of that same type.

### Typeclasses & Class Constraints
Examine the type of the `==` operator:
```Haskell
ghci> :t (==)
(==) :: Eq a => a -> a -> Bool
```
- Function names consisting only of special characters are infix by default
- To examine their type, we have to surround them by parens.
- Class constraint is before the `=>`.
  It says that type `a` must be a member of typeclass `Eq` which provides
  an interface for testing equality.

Some typeclasses:
- `Eq`: Types which can be compared for equality with `==` and `/=`.
- `Ord`: Types which can be compared for ordering with `>`, `<`, `>=`, `<=`.
- `Show`: Types which can be represented as strings.
- `Read`: Types which can be converted from strings (with the `read` function).


### Type Annotations
```Haskell
read "4"     -- error, not obvious what type to convert "4" to
```

Type annotation at the end of expression tells compiler what type we want
returned.

```Haskell
read "5" :: Int                 -- 5
read "5" :: Float               -- 5.0
(read "5" :: Float) * 4         -- 20.0
read "[1,2,3,4]" :: [Int]       -- [1,2,3,4]
read "(3, 'a')" :: (Int, Char)  -- (3, 'a')
```

### More Typeclasses
- `Enum`: Types can be enumerated (sequentially ordered) so can be used in
  ranges and each value has a distinct predecessor or successor.
- `Bounded`: Types which have an upper and lower bound.
  Tuples are in Bounded if their elements are also in Bounded.
```Haskell
minBound :: Int         -- -2147483648
maxBound :: Char        -- '\1114111'
maxBound :: Bool        -- True
minBound :: Bool        -- False
```
- `Num`: types which can act like numbers.
```Haskell
20 :: Int        -- 20
20 :: Integer    -- 20
20 :: Float      -- 20.0
20 :: Double     -- 20.0

:t (*)    -- (*) :: (Num a) => a -> a -> a
          -- accepts two Nums and returns a Num

(5 :: Int) * (6 :: Integer)     -- error because * needs two values of same type
5 * (6 :: Integer)              -- ok, because 5 will act like an Integer
```
- `Integral`: only integers, i.e. `Int` and `Integer`
- `Floating`: only floats, i.e. `Float` and `Double`

#### fromIntegral
Attempting to add an `Int` to a `Float` gives error.
```Haskell
length [1,2,3,4] + 3.2                  -- error
fromIntegral (length [1,2,3,4]) + 3.2   -- works, converts in to general Num
```

## Defining Types


```Haskell
data BookInfo = Book Int String [String]
                deriving (Show)
```

- `BookInfo` is the *type* constructor: the **name** of the new type
- `Book` is the *value* constructor: the word used to **create** new values
  of the type.
- Both must **start** with a **capital letter**.
- The type and value constructor names can have the *same* but they are
  still different things. E.g.
```Haskell
data Point = Point Float Float deriving (Show)
```

### Type Synonyms
Introduced with `type` keyword; just another name for an existing type to
make code more readable.
```Haskell
type CustomerID = Int    -- create synonym
type ReviewBody = String -- create synonym

data Review = Review CustomerID ReviewBody
```
### Algebraic data types
Can have more than one value constructor.
```Haskell
data Bool = False | True    -- read the pipe as 'or'
```
Value constructors here are True and False.
Each value constructors can take zero or more arguments

### Polymorphic (generic) types
Introduce type variable into type declaration.
```Haskell
data Maybe a = Just a       -- 'a' is a type variable
             | Nothing
```

- Here, `Maybe` is the *type* constructor which defines a new type.
- `Just` and `Nothing` are *value* constructors meaning that to create a
  value of type `Maybe` you have to use the words `Just` or `Nothing`.
- `Nothing` takes no parameters, so you don't give it a value.
- `Just` takes one parameter: a value of variable type.

```Haskell
ghci> let jim = Just True
ghci> let bob = Just 'a'
ghci> let fred = Nothing
ghci> :t (jim, bob, fred)
(jim, bob, fred) :: (Maybe Bool, Maybe Char, Maybe a)
```

#### Record Syntax

Makes it obvious which field is which, and we don't have to worry about the
**order**.

```Haskell
data Car = Car {company :: String, model :: String, year :: Int}
  deriving (Show)

ghci> Car {model="Mustang", company="Ford", year=1967}
Car {company = "Ford", model = "Mustang", year = 1967}
```

Convert an `Integral` to a `Num`.
`fromIntegral :: (Num b, Integral a) => a -> b`

```Haskell
fromIntegral(length [1,2,3,4]) + 3.2      -- ok
```

## Function Pattern Matching

### Matching goes from top to bottom.
```Haskell
charName :: Char -> String
charName 'a' = "Albert"
charName 'b' = "Broseph"

ghci> charName 'c'      -- "Non-exhaustive" error, since no patterns match
```

### Matching tuples:
```Haskell
second (_, y, _) = y       -- "_" means don't care, as in list comprehensions

addVectors (x1, y1) (x2, y2) = (x1 + x2, y1 + y2)
```
### Matching Lists
- `[]` matches empty list
- `(x:xs)` matches 1 or more elements; `x` is the head.
- `(x:[])` list with 1 elements
- `(x:y:[])` list with 2 elements.

Note that `[1,2]` is shorthand for `1:(2:[])` which is `1:2:[]`, because
`:` is right associative.

```Haskell
sumList (x:xs) = x + sumList xs
sumList []     = 0
```

### Matching in list comprehension:
```Haskell
ghci> [a+b | (a,b) <- [(1,3), (4,3), (2,4)]]
[4,7,6]
```
`x:xs` matches list of length 1 or more, binding head to `x`
and remainder (possibly empty list) to `xs`

`x:y:z:xs` matches lists of length 3 or more.

### *As* Patterns with `@`
Use to avoid repeating yourself. E.g.
```Haskell
capital :: String -> String
capital "" = "Empty string, whoops!"
capital all@(x:xs) = "The first letter of " ++ all ++ " is " ++ [x]
```

### Guards
Like patterns, but check boolean expressions instead of patterns.
```Haskell
bmiTell :: (RealFloat a) => a -> String
bmiTell bmi
    | bmi <= 18.5 = "underweight"
    | bmi <= 25.0 = "normal"
    | bmi <= 30.0 = "fat"
    | otherwise   = "whale"

max' :: (Ord a) => a -> a -> a
max' a b
    | a > b     = a
    | otherwise = b

myCompare :: (Ord a) => a -> a -> Ordering
a `myCompare` b
    | a > b     = GT
    | a == b    = EQ
    | otherwise = LT
```
## Where Bindings
Add `where` binding to avoid repetition:
```Haskell
bmiTell :: (RealFloat a) => a -> a -> String
bmiTell weight height
    | bmi <= skinny = "underweight"
    | bmi <= normal = "normal"
    | bmi <= fat    = "fat"
    | otherwise     = "huge"
    where bmi = weight / height ^ 2
          skinny = 18.5
          normal = 25.0
          fat = 30.0
```
Can use patterns in where bindings
```Haskell
initials :: String -> String -> String
initials firstname lastname = [f] ++ ". " ++ [l] ++ "."
    where (f:_) = firstname
          (l:_) = lastname
```
Can declare a "helper" function in where bindings
```
calcBmis :: (RealFloat a) => [(a, a)] -> [a]
calcBmis xs = [bmi w h | (w, h) <- xs]
    where bmi weight height = weight / height ^ 2
```

## Let
```Haskell
cylinder :: (RealFloat a) => a -> a -> a
cylinder r h =
    let sideArea = 2 * pi * r * h
        topArea = pi * r ^2
    in  sideArea + 2 * topArea
```
Difference between `let` is and `where` (apart from bindings
being opposite way around), is that `let` is also an expression.
```Haskell
ghci> 4 * (let a = 9 in a + 1) + 2
42

ghci> [let square x = x * x in (square 5, square 3, square 2)]
[(25,9,4)]

ghci> (let b = 2; c = 3 in b*c, let f="xx "; b = "yyy" in f ++ b)
(6,"xx yyy")

ghci> (let (a,b,c) = (1,2,3) in a+b+c) * 100
600

calcBmis :: (RealFloat a) => [(a, a)] -> [a]
calcBmis xs = [bmi | (w, h) <- xs, let bmi = w / h ^ 2]
```

## Shadowing
Generally not wise and confusing. Where an inner expression masks
an outer expression or parameter.
```Haskell
quux a = let a = "foo"
         in a ++ "eek!"
```

## Braces

Indentation changes meaning, but we can use braces to
make structure explicit (although indentation is the more popular approach).

E.g. this:

```Haskell
foo = let { a = 1;  b = 2;
        c = 3 }
      in a + b + c
```
Has the same meaning as:
```Haskell
bar = let a = 1
          b = 2
          c = 3
      in a + b + c
```
## Case
```
case expression of pattern -> result
                   pattern -> result
                   pattern -> result
                   ...
```
This:
```Haskell
describeList :: [a] -> String
describeList xs = "The list is " ++ case xs of [] -> "empty."
                                               [x] -> "a singleton list."
                                               xs -> "a longer list."

```
is the same as:
```Haskell
describeList :: [a] -> String
describeList xs = "The list is " ++ what xs
    where what [] = "empty."
          what [x] = "a singleton list."
          what xs = "a longer list."
```

## Recursion
```Haskell
quicksort :: (Ord a) => [a] -> [a]
quicksort [] = []
quicksort (x:xs) =
    let smallerSorted = quicksort [a | a <- xs, a <= x]
        biggerSorted = quicksort [a | a <- xs, a > x]
    in  smallerSorted ++ [x] ++ biggerSorted
```

## Currying

Haskell functions really take **1 parameter**.

A **space** between 2 things is really **function application** and takes
**highest precedence**.

So, `max 4 5` is actually `(max 4) 5`.
And:

    max :: (Ord a) => a -> a -> a

can be written as:

    max :: (Ord a) => a -> (a -> a)

meaning that max is a function which takes an `a` and returns another function
which takes an `a` and returns an `a`.


### Partial application
Calling with too few params returns a **partially applied** function.

```Haskell
multThree :: (Num a) => a -> a -> a -> a
multThree x y z = x * y * z

ghci> let multTwoWithNine = multThree 9
ghci> multTwoWithNine 2 3
54
ghci> let multWithEighteen = multTwoWithNine 2
ghci> multWithEighteen 10
180
```

### Infix functions
Surround infix functions with parentheses and supply a parameter on one side.
```Haskell
divideByTen :: (Floating a) => a -> a
divideByTen = (/10)
```
This has the same effect as `200 / 10` or `(/10) 200`.

### Functions as parameters
```Haskell
applyTwice :: (a -> a) -> a -> a
applyTwice f x = f (f x)

ghci> applyTwice (+3) 10
16
```

Parentheses are needed here. First param is a function (which takes an `a` and
returns an `a`), the second param is an `a` and finally, applyTwice returns an
`a`.

`map (*) [0..]` returns a list of partially applied functions, like
`[(0*), (1*), (2*), (3*),...]`

### Lambdas
Introduced with a backslash `\`.

`map (+3) [1,6,3,2]` and `map (\x -> x + 3) [1,6,3,2]` are the same.

Note: if a pattern match fails in a lambda you get a runtime error.

## Folds
- `foldl (z x) z list` fold from left to right.
- `foldr (x z) z list` fold from right to left.

Both take a binary fold function, a starting accumulator value `z` and
a list to fold.  They walk over the `list` applying the binary function
to each element and the accumulator as they go. They return the
accumulated value.

Note that the order of the arguments are swapped in the fold function for `foldl` and `foldr`:
- for `foldl` the accumulator goes **first**
- for `foldr` the accumulator goes **second**

Also:
- in `foldl` the fold function goes on the **left** of each list item, so the initial accumulator value must go on the left of the first list item.
- in `foldr` the fold function goes on the **right** of each list item, so the initial accumulator value must go on the right of the last list item.

```Haskell
-- Recurse immediately, can only start applying f when the end is reached.
-- Therefore can't work on infinite lists - will never end - and will
-- traverse *all* of a finite list.
foldl f z []     = z                  
foldl f z (x:xs) = foldl f (f z x) xs

-- Apply f to the first element and the result of folding the rest.
-- Can therefore work on infinite lists and stop when the r-values
-- is not requested.
foldr f z []     = z 
foldr f z (x:xs) = f x (foldr f z xs) 
```

```Haskell
foldl (++) "x" ["a","b","c"]    -- "abcx"
foldr (++) "x" ["a","b","c"]    -- "xabc"

foldl f z [1,2,3]   -- f (f (f z 1) 2) 3
foldr g z [1,2,3]   -- g 1 (g 2 (g 3 z))
```

`foldl` can be viewed as an imperative loop:
```Haskell
\f z list ->
  var r = z
  foreach (e in list)
    r = f(r, e)
  return r
```

`foldr` can be viewed as *cons replacement*.
- replace every cons constructor (:) with f
- replace final [] with z (if not an infinite list)

```Haskell
foldr f z list
-- replaces:
A : (B : (C : (D : [])))
-- with
A `f` (B `f` (C `f` (D `f` z)))
```

### Fold Properties

- `foldl'` eliminates tail recursion, so is more space efficient
- `foldl` & `foldl'` recurse in the *first* argument of the fold function so
  they won't work on **infinite** lists; they will continue recursing before
  ever evaluating anything.

- `foldr` recurses in the *second* argument of the fold function, so lazy
  evaluation will stop the recursion if the second argument is not demanded.
  - To halt the traversal in `foldr`, have the fold function return an
    expression which is not based on the second argument (which is the
    accumulator value `z`).

`foldl1` and `foldr1` assume the list starting element to be the initial
value, and so will error if called with an empty list..

Many standard library functions can be implemented with folds:

```Haskell
maximum' :: (Ord a) => [a] -> a
maximum' = foldr1 (\x acc -> if x > acc then x else acc)

reverse' :: [a] -> [a]
reverse' = foldl (\acc x -> x : acc) []

product' :: (Num a) => [a] -> a
product' = foldr1 (*)

filter' :: (a -> Bool) -> [a] -> [a]
filter' p = foldr (\x acc -> if p x then x : acc else acc) []

head' :: [a] -> a
head' = foldr1 (\x _ -> x)

last' :: [a] -> a
last' = foldl1 (\_ x -> x)
```

`scanl` and `scanr` are similar to fold, but report all intermediate
accumulator values too.

## Function application with `$`
Serves two purposes:
- Function application with `$` is right associative.
- Normally function application with space is left associative.
- So we can use `$` to make code more readable by avoiding parens.
E.g.
```Haskell
f a b c     == ((f a) b) c      -- function application is left associative
f $ a $ b c ==   f (a (b c))    -- $ is right associative

sum (map sqrt [1..130])     -- ok
sum $ map sqrt [1..130]     -- more readable
```

- Also, function application can be treated like another function, eg.
```Haskell
ghci> map ($ 3) [(4+), (10*), (^2), sqrt]
[7.0,30.0,9.0,1.7320508075688772]
```
## Function composition with `.`
Often clearer than lambdas. E.g.
```Haskell
ghci> map (\xs -> negate (sum (tail xs))) [[1..5],[3..6],[1..7]]
[-14,-15,-27]
```
becomes:

```Haskell
ghci> map (negate . sum . tail) [[1..5],[3..6],[1..7]]
[-14,-15,-27]
```

And:
```Haskell
fn x = ceiling (negate (tan (cos (max 50 x))))
```
becomes:
```Haskell
fn = ceiling . negate . tan . cos . max 50
```

## Modules
```Haskell
import Data.List
import qualified Data.Map       -- now need to use fully qualify names
import qualified Data.Map as M  -- can now use shortcut M.filter
```
From GHCI:
```Haskell
:module + Data.List
:module + Data.List Data.Map Data.Set
```

## GHCI
```Haskell
:t (+)      -- (= :type) shows type info
:i (+)      -- (= :info) more detailed info than :t
it          -- handy shortcut for the result of the last expression
:m          -- (= :module) load a module
it          -- the last thing to be evaluated
```

## return and `<-`
- `return` is the opposite of `<-`
- `return` wraps a pure value.
- `<-` unwraps an impure value.
