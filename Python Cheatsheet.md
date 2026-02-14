# Cheat sheet for Python interviews

# Strings
Essential methods for `s: str`.
1. `s.find(other, [start, [end]]) -> int` finds the beginning index of the other string (or -1)
1. `s.split([separator]) -> List[str]` splits the string based on the separator:
    - when no separator is given, separates by "spaces"; result contains no empty string tokens.
    - when separator is given, result might contain empty string tokens.
1. `s.join(List[str]) -> str` joins the list of strings by the string `s`.
1. `s.count()`
1. `s[x:y]` substring starting at x and ending at y-1

**Examples**
```python
s = " Hello  world"

assert s.find("Hello") == 1
assert s.split() == ["Hello", "world"]
assert s.split(" ") == ["", "Hello", "", "world"]

# trick to reverse a string
assert "foo"[::-1] == "oof"
```

**Gotchas**
1. Strings in Python are immutable.
1. Behavior of `s.split()` vs `s.split(" ")` is inconsistent.

**Patterns**
1. Use `List[str]` and `"".join(lst)` instead of string buffer.
2. Reverse a string using `s[::-1]` syntax.

# Regular Expressions
1. `import re`
1. Splitting a string `re.split(r"[*/+-]", "term1+term2-term3") -> ["term1", "term2", "term3"]` 
1. Find the next pattern `re.findall(r"[*/+-]", "a + b - c)") -> ["+", "-"]`
1. Escape characters `re.escape("*/+-") -> "\*/\+\-"`

```python
import re
terms = re.split(r"[%s]" % re.escape(" */-+"), "41 + 20 / 4")
terms = list(filter(lambda x: len(x) > 0, terms))
assert terms == ["41", "20", "4"]

terms = re.findall(r"[\d]+", "41 + 20 / 4")
assert terms == ["41", "20", "4"]

ops = re.findall(r"[%s]" % re.escape("*/-+"), "41 + 20 / 4")
assert ops == ["+", "/"]
```

# Heaps

1.  By default Python heaps are **min-heaps**.
1. `import heapq` from the standard library
1. `heapq.heapify(lst)` mutates the underlying list to satisfy heap property
1. `heapq.heappush(lst, value)`
1. `heapq.heappop(lst)` returns the root and removes it from the heap


```python
import heapq

heap = [3, 2, 5, 4]
heapq.heapify(heap)
heapq.heappush(heap, 1)

assert heap[0] == 1

ordered = [heapq.heappop(heap) for _ in range(len(heap))]
assert ordered == [1, 2, 3, 4, 5]
```

**Heap gotchas**
1. All operations on the heap are done in place on the underlying list.
2. There is no peek operation. Just read the first element of the heap.

**Heap patterns**
To sort by arbitrary rules use tuples:
```python
import heapq

max_heap = [(-n, n) for n in [1, 4, 2, 5, 3]]
heapq.heapify(max_heap)

ordered = [heapq.heappop(max_heap)[1] for _ in range(5)]
assert ordered == [5, 4, 3, 2, 1]
```

# Lists
1. Initialize with `[]` or `list()`.
1. `lst.append(value)` appends a list in place and extends the underlying array if needed.
1. `lst.extend(iterable)` appends all the values of iterable.
1. `lst.pop() -> value` deletes the last element and returns it.
1. `lst.reverse()` reverses the list in place.
1. `lst.sort([key: lambda], reverse=False)` 
    - use the key like `lst.sort(key = lambda person: person.age)` to change the sort comparison.
1. `lst.index(value)` similar to `str.find` but it raises `ValueError` on missing element.

**Gotchas**
1. Lists are mutable; append, extend, reverse, etc. methods don't return new lists.

**Patterns**

```python
# initialize a list with repeated elements:
hundred_0s = [0] * 100
assert len(hundred_0s) == 100

# map from another list
lst = [("Alice", 30), ("Bob", 32), ("Cody", 17)]
adult_names = [p[0] for p in lst if p[1] >= 18]
assert adult_names == ["Alice", "Bob"]

# preallocate memory
lst = [None] * 100
lst[:] = [] # modifies the original list
assert len(lst) == 0

# enumerate with index and value
lst = ["Alice", "Bob"]
assert [(index, name) for index, name in enumerate(lst)] == [(0, "Alice"), (1, "Bob")]
```

# Sets
1. Initialize with `set([iterable])` or `{1, 2, 3}`
1. `s.add(value)`
1. `s.remove(value)` - raises error when value is not found.
1. `s.discard(value)` - does not raise error.
1. `s.pop() -> value` - returns and discards an arbitrary element.
1. Set operations:
    - `a - b`
    - `a | b` - union 
    - `a & b` - intersection
    - `a ^ b` - XOR
    - `a == b` - compare sets for equality
    - `a < b` - inclusion

**Patterns**
```python
lst = [1, 2, 1, 2, 1, 3, 2, 4]
dedup = list(set(lst))

assert len(dedup) == 4
assert set(lst) == {3, 2, 1, 4}
```

**Gotchas**
1. Elements in sets must be hashable and cannot be lists, sets or dictionaries.
1. User defined classes can be used but must implement __hash__ and __eq__.
1. Use tuples for quick implementations.

# Deque
1. `from collections import deque`
1. `deque([iterable, [maxlen]])` - initialize from a list or other iterable
1. `q.append(value)` - append an element at the end (right) of the queue
1. `q.extend(iterable)` - append all elements
1. `q.appendleft(value)` and `q.extendleft(iterable)` - prepends value(s)
1. `q.pop()` - stack pop from the end of the queue (LIFO)
1. `q.popleft()` - queue pop from the beginning of the queue (FIFO)

**Patterns**
```python
from collections import deque

# LIFO stack processing
q = deque()
q.append(1)
q.append(2)
q.append(3)

lifo = [q.pop() for _ in range(3)]
assert lifo == [3, 2, 1]

# FIFO queue processing
q = deque()
q.append(1)
q.append(2)
q.append(3)

fifo = [q.popleft() for _ in range(3)]
assert fifo == [1, 2, 3]
```

**Gotchas**
1. Need to use `popleft` to have expected queue FIFO behavior.
2. `left` versions of the methods are with one word, lower case: `popleft`, `appendleft`, etc.

# Dictionaries
1. Initialize with `dict()` or `{}`.
1. `d[key]` - raises error on key not found.
1. `d.get(key, "default")` returns "default" when key is not present.
1. `del d[key]` - deletes specified key; raises error if key not found.
1. `d.pop(key, [default])` - returns and deletes the key; unless default is specified raises error on key not found.
1. `key in d` - test whether a key exists in the dictionary
1. `d.keys()` - a "view" of the dictionary keys; gets dynamically updated.
    -  supported operations: iterate, membership tests, length.
1. `d.values()` - similar to keys but for values.

**Gotchas**
1. the delete syntax is non standard; use pop for more intuitve syntax.
1. keys and values look like sets but they have a different type and need to be converted to sets / lists if needed.


# Bisect
Useful for binary search within a list.

1. `import bisect`
1. `bisect.bisect_left(ordered_lst, value)`
    -  finds the index where value would be inserted in order.
    -  if value already exists in the list, this is the index of the first occurence.
1. `bisect.bisect_right(ordered_lst, value)`

**Examples**
```python
import bisect
ordered = [1, 4, 6, 9, 10]

assert bisect.bisect_left(ordered, 5) == 2
assert bisect.bisect_left(ordered, 4) == 1
assert bisect.bisect_left(ordered, 11) == 5
```

Simple binary search using bisect
```python
import bisect

def binary_search(ordered, value):
    index = bisect.bisect_left(ordered, value)
    if index < len(ordered) and ordered[index] == value:
        return index
    return -1 

ordered = [1, 4, 6, 9, 10]

assert binary_search(ordered, 4) == 1
assert binary_search(ordered, 0) == -1
```

# Random
1. `import random`
1. `random.randomint(first, last) -> value` - a random integer between (and including) first and last
1. `random.uniform(first, last)-> value` - a random float between (and including) first and last
1. `random.random() -> value` - a random float between 0.0 and 1.0
1. `random.shuffle(lst)` - shuffles the list in a random order
1. `random.sample(lst, k) -> List[values]`
    -  returns a list of k unique values form lst
    -  raises error on invalid k
1. `random.choices(lst, k) -> List[values]`
    -  returns a list of k values with duplicates
1. `random.choices(lst, weights=w, k=1)`
    -  returns a list of k values with duplicates weighted by weights

**Examples**
Selecting an element from a list with weights.

```python
import random
random.seed(42)

nums = [1] * 100
nums.append(100)

weights = nums
count_100s = 0
for i in range(1000):
    if random.choices(nums, weights=weights) == [100]:
        count_100s += 1

assert count_100s > 400
assert count_100s < 600
```

# Bytes
Bytes are immutable arrays of byte.

```python
import pytest

b = b"Hello World"

assert b[0] == ord('H')

with pytest.raises(TypeError) as err:
    b[0] = "G"

assert "does not support item assignment" in str(err)
```

# Sorted Containers
`sortedcontainers` is an actively maintained library written in Python.
It is much faster than the similar `sortedcollection` library event thought the latter is written in C.

```python
from sortedcontainers import SortedList, SortedDict, SortedSet

sl = SortedList([3, 1, 2])

assert [x for x in sl] == [1, 2, 3]
assert sl.bisect_left(2) == 1

sd = SortedDict()
sd[2] = 'b'
sd[1] = 'a'
sd[3] = 'c'
assert [x for x in sd.keys()] == [1, 2,3]
```