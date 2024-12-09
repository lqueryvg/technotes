
## min heap

- properties

  - binary tree: every node has 2 children
  - partially sorted:
    - a node is always less than or equal to immediate children:
      - therefore the smallest value is always at the root
    - no particular ordering among nodes on same level, even siblings
  - complete binary tree
    - it has smallest possible height:
      - nodes are always added to the bottom right (if you imagine the root
        is at the top) until the bottom level is full,
        then the next level is populated

### observations

- height is log(n)
- root to leaf operations are O(log(n))
- any root to leaf path is sorted
- add/remove node needs 1 root to leaf path order to be fixed
  - therefore any add/remove operation is O(log(n))
- common use case is priority queue

### Conjecture ?

- getting the nth smallest child requires popping the root node n times

### array implementation

- assuming array indexes start at 0
- root it at index 0
- the the k'th element
  - left child is at 2*k + 1
  - right child is at 2*k + 2
  - parent is at floor((k-1)/2)

#### insert

- put new element at end of array
- while less than parent, bubble up

### delete min

  - remove from root
  - move last to root
  - bubble down from root:
    - if value is greater than smallest immediate child
      - swap with that child
    - continue bubbling down until no further swaps

