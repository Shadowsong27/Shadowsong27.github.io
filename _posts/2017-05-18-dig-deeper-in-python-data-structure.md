---
title: Data Structure
description: dig-deeper-in-python
header: Dig Python
---
### - Classification of Data Structure -

Container vs Flat - homogeneous element type in the same data structure

Mutable vs Immutable - (as I understand it) length of data structure can be modified based on the difference in special Methods.

### - List Comprehensions (listcomps) -
The goal of a listcomp is to build a new list, but it could be used as a short-handed loop, like:

```python
[print(x) for x in range(10)]
```

But this is very __unpythonic__, because

* reduce the readability as compared to a normal for loop by trying to squeeze a complicate function in a listcomp

* produce a potentially large list that is useless - waste space

So the rule should be:
1. if the goal is not producing a list, do not use this syntax
2. the entire statement should be kept short and readable
3. use linebreak to make compound listcomps more readable

*[Reference on StackOverflow](http://stackoverflow.com/questions/5753597/is-it-pythonic-to-use-list-comprehensions-for-just-side-effects)*

### - List Comprehensions vs Generator Expressions -
In terms iteration effect they are similar, the main difference lies:
* listcomp will create the list first while genex will create the items on the go

So ...
* listcomp is good for creating reusable list, using list methods
* genex is good for very large and infinite sequences

*[Reference on StackOverflow](http://stackoverflow.com/questions/47789/generator-expressions-vs-list-comprehension)*

### - Tuple Unpacking -

`_` should be used for ignoring unwanted elements during unpacking

```python
>>> a, b, _ = range(3)  # does not care about the value stored in _
>>> a
0
>>> b
1
```

Unpacking as arguments (extended unpacking)

```python
>>> a, b, *_ = range(10)
```

Unpacking to capture

```python
>>> a, b, *rest = range(5)
>>> a, b, rest
(0, 1, [2, 3, 4])
>>> a, b, *rest = range(3)
>>> a, b, rest
(0, 1, [2])
>>> a, b, *rest = range(2)
>>> a, b, rest
(0, 1, [])
```

(examples are taken from Fluent Python by Luciano Ramalho)
