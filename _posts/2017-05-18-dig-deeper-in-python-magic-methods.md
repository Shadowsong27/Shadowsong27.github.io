---
title: Magic Methods
description: dig-deeper-in-python
header: Dig Python
---
### - Magic Method -

1. `len()` and `__len__` instead of OO-styled `.length()`

    Generic APIs at the highest level. Every Class that implements `__len__` would be able to use `len()` - pythonic

    By overriding special methods, common operators can be used for user-defined class.
    e.g. `__getitem` with list indexes

2. List of special methods can be found [here](https://docs.python.org/3/reference/datamodel.html#special-method-names)
