# zsh

## Variables

    array=("first word" "" "third word")
    scalar="only word"


## Parameter Expansion

    ${name:l}     # lowercase
    ${(L)name}    # ditto (using expansion flag)
    ${name:u}     # uppercase
    ${(U)name}    # ditto
    ${array[3]}   # 3rd element

    A=(1 2 3 4 5)
    B=(2 4)
    echo ${A:|B}   # set theory "A not B"

    A=(1 2 3)
    B=(1 3 4 5)
    echo ${A:*B}   # set theory "A intersection B"

    fred=(a c b d)
    echo ${(o)fred}  # sort
    echo ${(O)fred}  # sort descending

## Brace Expansion

    foo{xx,yy,zz}bar   # fooxxbar fooyybar foozzbar
    {3..6}             # 3 4 5 6
    {1..10..4}         # 1 5 9
    {a..e}             # a b c d e

