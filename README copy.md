# Interview Questions & Answers: Python, Django, PostgreSQL, Django REST Framework

Interview questions and answers covering Python, PostgreSQL, Django, and Django REST Framework for backend developers.

---

## Python

### 6. What are Python generators and why would you use them instead of regular lists?

Generators are a special type of iterator in Python that generate values on-demand, or lazily, rather than storing them all in memory at once.

There are two ways to create generators: generator expressions, which are similar to list comprehensions but use parentheses, and generator functions, which use `yield` instead of `return`.

The key difference between `return` and `yield` is that `return` stops the function execution and returns a value, while `yield` pauses the function, remembers its state, and continues from that point when the next value is requested using `next()` or in a loop.

I use generators instead of lists for memory efficiency. Generators create values one at a time, only when needed, while lists store all values in memory at once. This makes generators perfect for working with large datasets, processing data streams like reading large files, creating infinite sequences, or when I only need to iterate once.

Generators cannot be indexed because they do not store all values in memory at once. They generate values one at a time as we request them, so there is no predetermined collection to index into. To access a specific element, we would need to iterate through all previous elements first.

I use lists when I need to access elements multiple times, access elements by index, modify the collection, or when the dataset is small and fits easily in memory.

```python
# List comprehension - all values stored in memory
numbers_list = [x for x in range(1000000)]  # Uses ~8 MB of memory

# Generator expression - values created on demand
numbers_gen = (x for x in range(1000000))   # Uses only a few bytes

# Generator function with yield
def count_up_to(n):
    count = 1
    while count <= n:
        yield count  # Pauses here, remembers state
        count += 1

# Using the generator
gen = count_up_to(5)
print(next(gen))  # Output: 1 - function pauses after first yield
print(next(gen))  # Output: 2 - function resumes and pauses again
print(next(gen))  # Output: 3

# Generator in a loop
for num in count_up_to(5):
    print(num)
# Output: 1 2 3 4 5 (each on new line)

# Generator exhaustion
gen_expr = (x**2 for x in range(1, 6))
print(list(gen_expr))  # Output: [1, 4, 9, 16, 25]
print(list(gen_expr))  # Output: [] - Generator exhausted!

# Generators cannot be indexed
gen = (x for x in range(5))
# print(gen[0])  # TypeError: 'generator' object is not subscriptable

# Reading large files with generator
def read_file_generator(filename):
    with open(filename) as f:
        for line in f:
            yield line.strip()  # Yields one line at a time

# Infinite generator
def infinite_numbers():
    num = 0
    while True:
        yield num
        num += 1

gen = infinite_numbers()
print(next(gen))  # Output: 0
print(next(gen))  # Output: 1
print(next(gen))  # Output: 2
```
[Back to Table of Contents](#table-of-contents)


### 10. What is the difference between a stack and a queue?

With a stack, we add elements to the end and remove from the end. This follows a Last In, First Out principle, or LIFO. A classic example is the call stack. When a function calls another function, the new one gets added to the top of the stack. When that function finishes, it gets removed from the top, and the program returns to the previous function. This way the program knows where to return after each function completes.

With a queue, we add elements to the end and remove from the beginning. This follows a First In, First Out principle, or FIFO. A good example is an event queue where user events are processed. If one user clicks a button and then another user clicks a button, the first user's event gets processed first. The events are handled in the order they arrived. This is fair because whoever does something first gets their action handled first.

```python
# Stack example - LIFO (Last In, First Out)
stack = []

# Add elements
stack.append(1)
stack.append(2)
stack.append(3)
print(f"Stack: {stack}")  # Output: [1, 2, 3]

# Remove from end
print(stack.pop())  # Output: 3 (last in, first out)
print(stack.pop())  # Output: 2
print(stack.pop())  # Output: 1

print("\n" + "="*40 + "\n")

# Queue example - FIFO (First In, First Out)
from collections import deque

queue = deque()

# Add elements
queue.append(1)
queue.append(2)
queue.append(3)
print(f"Queue: {queue}")  # Output: deque([1, 2, 3])

# Remove from beginning
print(queue.popleft())  # Output: 1 (first in, first out)
print(queue.popleft())  # Output: 2
print(queue.popleft())  # Output: 3
```
[Back to Table of Contents](#table-of-contents)

### 11. Can you explain what pointers are and how they are used in a doubly-linked list like a deque in Python?

A pointer is an address in memory that tells us where to find an element. In a doubly-linked list like a deque, elements are scattered in different memory locations, and pointers connect them together to form a sequence.

Each element in a deque stores three things: the actual value or data, a pointer to the previous element, or None if it is the first element, and a pointer to the next element, or None if it is the last element.

When we add or remove elements from a deque, Python automatically updates these pointers. For example, when we add a new element to the end, Python finds a free spot in memory for it, updates the old last element's pointer to point to this new element, and sets the new element's pointer to point back to the old last element. This maintains the connection between all elements in the list.

The advantage of using pointers in a doubly-linked list is that we can traverse the list in both directions. We can move forward through the pointers to the next element, or we can move backward through the pointers to the previous element.

```python
# Doubly-linked list example in Python
class Node:
    def __init__(self, value):
        self.value = value
        self.next = None      # Pointer to next element
        self.previous = None  # Pointer to previous element

class DoublyLinkedList:
    def __init__(self):
        self.head = None  # Pointer to first element

    def add_to_end(self, value):
        new_node = Node(value)

        if self.head is None:
            self.head = new_node
        else:
            current = self.head
            while current.next is not None:
                current = current.next
            current.next = new_node      # Update old last's pointer to new element
            new_node.previous = current  # New element points back to old last

    def display_forward(self):
        current = self.head
        while current is not None:
            print(current.value, end=" <-> ")
            current = current.next
        print("None")

    def display_backward(self):
        current = self.head
        if current is None:
            return
        while current.next is not None:
            current = current.next
        while current is not None:
            print(current.value, end=" <-> ")
            current = current.previous
        print("None")

# Using the doubly-linked list
dll = DoublyLinkedList()
dll.add_to_end(10)
dll.add_to_end(20)
dll.add_to_end(30)

print("Forward:")
dll.display_forward()  # Output: 10 <-> 20 <-> 30 <-> None

print("Backward:")
dll.display_backward()  # Output: 30 <-> 20 <-> 10 <-> None
```
[Back to Table of Contents](#table-of-contents)

### 12. How are elements stored in memory in a queue (deque) versus a list?

In a deque, which is a doubly-linked list, elements are scattered in different locations in memory. Each element is like a small package that contains the actual value plus pointers, which are addresses that point to the previous and next elements. This is how we can find and navigate between elements even though they are not next to each other in memory. The pointers connect them together.

In a list, elements are stored sequentially in memory, right next to each other like boxes in a row. All elements are arranged in consecutive memory locations. This means that if we know where the first element is, we automatically know where all the other elements are because they follow one after another.

This difference in storage affects performance. In a list, accessing an element by index is very fast because Python can calculate exactly where it is in memory. However, inserting or removing an element from the beginning of a list is slow because Python has to shift all the remaining elements to fill the gap or make space for the new element.

In a deque, inserting or removing elements from either the beginning or the end is fast because we only need to update the pointers. We do not need to move other elements around. However, accessing an element by index in a deque is slower because we have to follow the pointers one by one to find the element we want.

```python
from collections import deque

# List - elements stored sequentially in memory
my_list = [10, 20, 30, 40, 50]
# Memory layout: [10][20][30][40][50] (consecutive locations)

# Fast index access
print(my_list[2])  # Output: 30 (direct calculation)

# Slow insertion at beginning (requires shifting all elements)
my_list.insert(0, 5)  # Shifts: [5][10][20][30][40][50]

print("\n" + "="*40 + "\n")

# Deque - elements scattered in memory with pointers
my_deque = deque([10, 20, 30, 40, 50])
# Memory layout: scattered with pointers connecting them
# 10 -> pointer to 20, 20 -> pointer to 30, etc.

# Slower index access (must follow pointers)
print(my_deque[2])  # Output: 30 (must follow pointers)

# Fast insertion at beginning (only updates pointers)
my_deque.appendleft(5)  # Output: deque([5, 10, 20, 30, 40, 50])

# Fast removal from beginning
my_deque.popleft()  # Output: 5
```
[Back to Table of Contents](#table-of-contents)

### 13. What is the time complexity of removing elements from the beginning of a deque versus a list?

Removing an element from the beginning of a Python deque has O of one time complexity. Since a deque is a doubly-linked list, when we remove the first element, the element that was second just starts pointing to None on its left side instead of pointing to the removed element. No other elements need to move or change.

However, removing from the beginning of a Python list has O of n time complexity, where n is the number of elements in the list. Lists store elements in sequential memory slots, like boxes in a row. When we remove the first element, all the other elements need to shift one position to the left to fill the gap. The element at index one moves to index zero, the element at index two moves to index one, and so on. The more elements we have, the more shifting operations are needed.

This difference matters for server resources. With a list, the higher the n, the more instructions the processor has to execute. This takes more time to complete and consumes more electricity. So if we have a list with a million elements and we remove from the beginning, the processor must shift all one million elements. With a deque, removing from the beginning takes the same time regardless of how many elements are in it.

```python
from collections import deque
import time

# Deque - O(1) removal from beginning
my_deque = deque(range(100000))
start = time.time()
my_deque.popleft()
end = time.time()
print(f"Deque popleft time: {end - start:.6f} seconds")

# List - O(n) removal from beginning
my_list = list(range(100000))
start = time.time()
my_list.pop(0)
end = time.time()
print(f"List pop(0) time: {end - start:.6f} seconds")

# The list operation is much slower because it shifts all elements
# Deque time is essentially the same regardless of list size
# List time increases dramatically as the list gets bigger
```
[Back to Table of Contents](#table-of-contents)

### 14. What are lambda functions in Python? When would you use them instead of regular functions?

Lambda functions are anonymous functions - they do not have a name. We use lambdas when we do not plan to reuse the code, since without a name we cannot call it from another place.

Lambdas are perfect for short, simple operations. A great example is a calculator where a dictionary contains lambda functions for each mathematical operation. This is very convenient when relying on user input - we can dynamically access the operation from the dictionary no matter what the user chooses, whether it is addition, subtraction, multiplication, and so on.

Lambda functions are also commonly used with built-in functions like `map()`, `filter()`, and `sorted()` for quick transformations or filtering. For example, sorting a list of dictionaries by a specific key, or filtering a list based on a simple condition.

The main limitation is that lambdas can only contain a single expression - they cannot have multiple statements or complex logic. If you need more than one line or complex logic, it's better to use a regular function with a proper name, which also makes the code more readable.

```python
# Calculator using lambdas in a dictionary
operations = {
    'add': lambda x, y: x + y,
    'subtract': lambda x, y: x - y,
    'multiply': lambda x, y: x * y
}

user_input = 'add'
result = operations[user_input](5, 3)
print(result) # Output: 8

# Using lambda with sorted
students = [{'name': 'Alice', 'grade': 85}, {'name': 'Bob', 'grade': 92}]
sorted_students = sorted(students, key=lambda student: -student['grade'])
print(sorted_students) # Output: [{'name': 'Bob', 'grade': 92}, {'name': 'Alice', 'grade': 85}]
```
[Back to Table of Contents](#table-of-contents)

### 15. What is the difference between `map()`, `filter()`, and `reduce()` functions in Python?

`map()`, `filter()`, and `reduce()` are functions that work with collections.

`map()` applies a function to every element in a collection and returns a map object, which is a generator. For example, if we need to square every number in a list, we would use `map()`.

`filter()` applies a function to every element and keeps only the elements where the function returns `True`. It also returns a generator. For example, filtering out only even numbers from a list.

`reduce()` takes a collection and reduces it to a single value by repeatedly applying a function. For example, you could use it to sum all numbers in a list or find the maximum. Unlike `map()` and `filter()`, `reduce()` needs to be imported from the `functools` module.

The key difference is what they return: `map()` transforms each element, `filter()` selects certain elements, and `reduce()` combines all elements into one final result.

```python
from functools import reduce

numbers = [1, 2, 3, 4, 5]

# map() - applies function to every element, returns transformed collection
squared = map(lambda x: x ** 2, numbers)
print(f"map() result: {list(squared)}")
# Output: map() result: [1, 4, 9, 16, 25]

# filter() - keeps only elements where function returns True
evens = filter(lambda x: x % 2 == 0, numbers)
print(f"filter() result: {list(evens)}")
# Output: filter() result: [2, 4]

# reduce() - reduces collection to single value
total = reduce(lambda acc, x: acc + x, numbers)
print(f"reduce() result (sum): {total}")
# Output: reduce() result (sum): 15
```
[Back to Table of Contents](#table-of-contents)

### 16. What is the difference between `enumerate()` and `zip()` functions in Python?

`enumerate()` and `zip()` are both functions that help when iterating through collections, but they serve different purposes.

`enumerate()` gives us a way to iterate through a collection while having access to both the elements and their index. It returns pairs of index and value. By default it starts counting from zero, but you can specify a different starting number with the `start` parameter.

`zip()` combines multiple iterables by pairing elements at the same position. It takes elements from the first position of each iterable and creates a tuple, then elements from the second position, and so on. A common use case is creating a dictionary from two lists - one for keys and one for values. However, `zip()` is more general and can work with any number of iterables, not just two. An important detail is that if the iterables have different lengths, `zip()` stops when the shortest one is exhausted.

Both functions return iterators, so we often need to convert them to a list or dictionary to see the results. The key difference is that `enumerate()` works with a single collection and adds indices, while `zip()` combines multiple collections by pairing their elements.

```python
# enumerate() - adds index to elements of a single iterable
fruits = ['apple', 'banana', 'cherry']

for index, fruit in enumerate(fruits):
    print(f"Index {index}: {fruit}")
# Output: Index 0: apple
#         Index 1: banana
#         Index 2: cherry

# enumerate() with custom start
enumerated = list(enumerate(fruits, start=1))
print(f"enumerate(start=1): {enumerated}")
# Output: enumerate(start=1): [(1, 'apple'), (2, 'banana'), (3, 'cherry')]

# zip() - combines multiple iterables by pairing elements
names = ['Alice', 'Bob', 'Charlie']
ages = [25, 30, 35]

# Common use case: creating dictionary from two lists
person_dict = dict(zip(names, ages))
print(f"Dictionary from zip(): {person_dict}")
# Output: Dictionary from zip(): {'Alice': 25, 'Bob': 30, 'Charlie': 35}
```
[Back to Table of Contents](#table-of-contents)


### 18. What's the difference between a shallow copy and a deep copy in Python? When would you use each one?

Shallow copy creates a new object, but the nested objects inside still reference the same memory locations as the original. Deep copy creates a new object and recursively copies all nested objects, so everything gets new memory references.

The key difference is that with shallow copy, only the outer object is copied and nested objects share the same memory. With deep copy, everything is copied recursively and all objects get new memory, making them completely independent. Shallow copy is faster and uses less memory, but changes to nested objects affect the original. Deep copy is slower and uses more memory, but creates a completely independent copy.

I would use shallow copy when there are no nested objects, like simple lists of numbers or strings, when I have nested objects but will not modify them, or when performance matters. I would use deep copy when I have nested mutable objects like lists, dictionaries, or custom objects, and I need to modify nested objects without affecting the original.

```python
import copy

# Shallow copy problem with nested objects
original = [[1, 2, 3], [4, 5, 6]]
shallow = original.copy()

shallow[0][0] = 999
print(original)  # Output: [[999, 2, 3], [4, 5, 6]] - Original changed!
print(shallow)   # Output: [[999, 2, 3], [4, 5, 6]]

# Deep copy solution
original = [[1, 2, 3], [4, 5, 6]]
deep = copy.deepcopy(original)

deep[0][0] = 999
print(original)  # Output: [[1, 2, 3], [4, 5, 6]] - Original unchanged!
print(deep)      # Output: [[999, 2, 3], [4, 5, 6]]

# Shallow copy is fine for simple objects
original = [1, 2, 3, 4, 5]
shallow = original.copy()

shallow[0] = 999
print(original)  # Output: [1, 2, 3, 4, 5] - Original unchanged!
print(shallow)   # Output: [999, 2, 3, 4, 5]
```
[Back to Table of Contents](#table-of-contents)

### 19. Question: What is the difference between `==` and `is` in Python? Give me an example of when you would use each one.

`==` checks if two objects have the same value. `is` checks if two variables point to the exact same object in memory.

I use `is` when checking for `None`, `True`, or `False` because they are singletons in Python—only one instance exists in the entire program. Using `is` is faster because it just compares memory addresses, which is one simple operation. Using `==` calls the `__eq__()` method behind the scenes to compare values, which takes multiple operations. It is like checking if two ID card numbers match versus comparing people's entire appearance. Since singletons have only one instance, memory address comparison works perfectly and is more efficient.

I use `==` when comparing actual values like strings, numbers, or list contents—basically anytime I care about what the data is, not where it lives in memory.

```python
# Equality vs Identity
list1 = [1, 2, 3]
list2 = [1, 2, 3]
print(list1 == list2)  # True - same values
print(list1 is list2)  # False - different objects in memory

list3 = list1
print(list1 is list3)  # True - same object

# When to use 'is' - checking singletons
x = None
if x is None:  # Correct - fast and Pythonic
    print("x is None")  # Output: x is None

# When to use '==' - comparing values
password = "secret123"
user_input = "secret123"
if password == user_input:  # Correct - comparing values
    print("Access granted")  # Output: Access granted
```
[Back to Table of Contents](#table-of-contents)

### 20. What are `args` and `kwargs` in Python? Can you explain when and why you would use them?

`*args` and `**kwargs` allow a function to accept a variable number of arguments, making functions more flexible.

`*args` collects positional arguments into a tuple, and `**kwargs` collects keyword arguments into a dictionary. The `*` operator packs arguments when defining a function and unpacks them when calling a function.

The order in function definitions must be required parameters, then default parameters, then `*args`, then `**kwargs`.

I use them when I do not know how many arguments will be passed, or when I want to accept any arguments and pass them to another function.

```python
# *args example - variable positional arguments
def sum_all(*args):  # Packing: collects arguments into tuple
    return sum(args)

print(sum_all(1, 2, 3))        # Output: 6
print(sum_all(1, 2, 3, 4, 5))  # Output: 15
# args becomes tuple: (1, 2, 3, 4, 5)

# **kwargs example - variable keyword arguments
def print_info(**kwargs):  # Packing: collects keyword arguments into dict
    for key, value in kwargs.items():
        print(f"{key}: {value}")

print_info(name="John", age=25, city="Sofia")
# Output:
# name: John
# age: 25
# city: Sofia
# kwargs becomes dict: {'name': 'John', 'age': 25, 'city': 'Sofia'}

# Packing and unpacking example
def greet(name, age, city):
    print(f"Hello {name}, {age} years old, from {city}")

# Packing arguments
def wrapper(*args, **kwargs):
    print(f"Packed args: {args}")
    print(f"Packed kwargs: {kwargs}")
    greet(*args, **kwargs)  # Unpacking: spreads them back as individual arguments

wrapper("John", 25, city="Sofia")
# Output:
# Packed args: ('John', 25)
# Packed kwargs: {'city': 'Sofia'}
# Hello John, 25 years old, from Sofia

# Order example
def complete_example(required, default_param="default", *args, **kwargs):
    print(f"Required: {required}")
    print(f"Default: {default_param}")
    print(f"Args: {args}")
    print(f"Kwargs: {kwargs}")

complete_example("hello", "custom", 1, 2, 3, name="John")
# Output:
# Required: hello
# Default: custom
# Args: (1, 2, 3)
# Kwargs: {'name': 'John'}
```
[Back to Table of Contents](#table-of-contents)

### 22. Can you explain what the unpacking operators are used for when calling a function, not just when defining one?

When calling a function, `*` and `**` are used to unpack collections into individual arguments. `*` unpacks lists or tuples into positional arguments, and `**` unpacks dictionaries into keyword arguments.

This is useful when I have arguments stored in a collection and want to pass them to a function. Without unpacking, the function receives the entire collection as one argument, which usually causes an error. With unpacking, the collection is spread out into individual arguments that the function expects.

The `*` and `**` operators work both ways: in function definitions they pack arguments into a tuple or dictionary, and when calling functions they unpack collections into individual arguments.

```python
# Unpacking a list with *
def add(a, b, c):
    return a + b + c

numbers = [1, 2, 3]
result = add(*numbers)  # Same as: add(1, 2, 3)
print(result)  # Output: 6

# Unpacking a dictionary with **
def greet(name, age, city):
    print(f"Hi, I'm {name}, {age} years old from {city}")

person = {"name": "John", "age": 25, "city": "Sofia"}
greet(**person)  # Same as: greet(name="John", age=25, city="Sofia")
# Output: Hi, I'm John, 25 years old from Sofia

# Common use case in decorators
def decorator(func):
    def wrapper(*args, **kwargs):
        print("Before")
        result = func(*args, **kwargs)  # Must unpack here!
        print("After")
        return result
    return wrapper

@decorator
def calculate(a, b):
    return a + b

calculate(5, 3)
# Output:
# Before
# After
```
[Back to Table of Contents](#table-of-contents)


### 21. What is a decorator in Python? Can you give an example of when you might use one?

A decorator is a function that wraps another function to add extra functionality without modifying the original function's code. It follows the DRY principle by allowing us to reuse the same functionality in multiple places.

A decorator accepts a function as a parameter, defines a wrapper function inside that calls the original function, and the wrapper adds extra behavior before or after calling the original function. The decorator returns the wrapper function.

Without decorators, we would have to manually wrap our function by calling the decorator and assigning the result back to a variable. The `@decorator_name` syntax is syntactic sugar that does this for us automatically—Python applies the decorator and reassigns the function name for us.

The wrapper function typically uses `*args` and `**kwargs` to accept any arguments and pass them to the original function, making the decorator flexible and reusable.

I would use decorators for things like logging function execution time or checking authentication before allowing access.

```python
import time

# Define a timing decorator
def timing_decorator(func):
    def wrapper(*args, **kwargs):
        start_time = time.time()
        result = func(*args, **kwargs)  # Call original function with any arguments
        end_time = time.time()
        print(f"{func.__name__} took {end_time - start_time:.4f} seconds")
        return result
    return wrapper

# WITHOUT decorator syntax - manual wrapping
def calculate_sum(a, b, operation="add"):
    time.sleep(1)
    if operation == "add":
        return a + b
    return a * b

# We have to manually wrap and reassign
calculate_sum = timing_decorator(calculate_sum)
print(calculate_sum(5, 3, operation="add"))
# Output:
# calculate_sum took 1.0001 seconds
# 8


# WITH decorator syntax - Python does it for us
@timing_decorator
def calculate_product(a, b, operation="multiply"):
    time.sleep(1)
    if operation == "multiply":
        return a * b
    return a + b

# Python automatically does: calculate_product = timing_decorator(calculate_product)
print(calculate_product(5, 3, operation="multiply"))
# Output:
# calculate_product took 1.0001 seconds
# 15
```
[Back to Table of Contents](#table-of-contents)


### 23. What is the difference between `__str__()` and `__repr__()` methods in Python classes?

`__str__()` and `__repr__()` are both special methods that return string representations of an object, but they serve different purposes.

`__str__()` returns a human-readable string for end users that is informal and nice-looking. `__repr__()` returns an unambiguous string for developers, which is formal and used for debugging.

`print()` and `str()` call `__str__()`, or fall back to `__repr__()` if `__str__()` is not defined. `repr()` and typing the object name in the interpreter call `__repr__()`.

The goal of `__repr__()` is to be precise and unambiguous, ideally showing us exactly how to recreate the object. This is extremely useful for debugging. When we see `Person(name='John', age=25)` in a debugger, we can immediately copy and paste that code to recreate the exact same object and reproduce an issue. This makes troubleshooting much faster.

I always define `__repr__()` because it is used in debugging. I define `__str__()` only when I need different user-friendly output. If I only define `__repr__()`, both `str()` and `repr()` will use it, which is usually fine.

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __str__(self):
        return f"{self.name}, {self.age} years old"

    def __repr__(self):
        return f"Person(name='{self.name}', age={self.age})"

person = Person("John", 25)

print(person)           # Output: John, 25 years old (__str__)
print(str(person))      # Output: John, 25 years old (__str__)
print(repr(person))     # Output: Person(name='John', age=25) (__repr__)

# Only __repr__ defined - it serves both purposes
class Product:
    def __init__(self, name, price):
        self.name = name
        self.price = price

    def __repr__(self):
        return f"Product(name='{self.name}', price={self.price})"

product = Product("Laptop", 1200)
print(product)         # Output: Product(name='Laptop', price=1200)
print(str(product))    # Output: Product(name='Laptop', price=1200) - falls back
print(repr(product))   # Output: Product(name='Laptop', price=1200)

# Why repr() showing recreation is useful for debugging
# When we see this in logs: Person(name='John', age=25)
# We can copy-paste it to recreate the exact object
```
[Back to Table of Contents](#table-of-contents)

### 24. What is the difference between `@staticmethod`, `@classmethod`, and instance methods in Python? When to use each?

These three types of methods differ in what they have access to and how they are called.

Instance methods are the most common type. They receive self as the first parameter and have access to the instance attributes and other instance methods. They can access and modify the specific object's data. We use instance methods when we need to work with a specific object's data or perform operations on that particular instance. For example, if we have an Article class with a views counter, an increment_views method would be an instance method because it works with that specific article's view count.

Class methods use the classmethod decorator and receive cls as the first parameter, which is the class itself, not an instance. They have access to class attributes and can call other class methods, but cannot access instance attributes. They are commonly used as alternative constructors or factory methods to create instances in specific ways. We use class methods when we need to create instances with special logic, or work with class-level data rather than instance data. For example, if we have a Person class, we might create a class method called from_birth_year that takes a birth year and calculates the age automatically when creating a new Person instance.

Static methods use the staticmethod decorator and do not receive self or cls automatically. They have no access to instance or class attributes. Any parameters must be passed explicitly from outside. They are essentially regular functions but kept inside the class for logical grouping. They could be moved outside the class, but stay there because they are conceptually related to the class. We use static methods for utility functions that are related to the class conceptually but do not need access to instance or class data. For example, if we have a Person class, we might have a static method called is_adult that checks if an age is eighteen or over. This does not need any instance or class data, just the age value passed to it.

Here is a quick decision guide. If we need the specific object's data, we use an instance method. If we are creating instances or working with class-level data, we use a class method. If we have a utility function related to the class but independent, we use a static method.

**Code Example:**

```python
from datetime import datetime

class Person:
    species = "Homo sapiens"  # Class attribute

    def __init__(self, name, age):
        self.name = name  # Instance attribute
        self.age = age

    # Instance method - receives self, accesses instance data
    def introduce(self):
        return f"Hello, I am {self.name} and I am {self.age} years old"

    def celebrate_birthday(self):
        self.age += 1
        return f"{self.name} is now {self.age} years old"

    # Class method - receives cls, used as alternative constructor
    @classmethod
    def from_birth_year(cls, name, birth_year):
        current_year = datetime.now().year
        age = current_year - birth_year
        return cls(name, age)

    @classmethod
    def get_species(cls):
        return f"All persons are {cls.species}"

    # Static method - no self or cls, utility function
    @staticmethod
    def is_adult(age):
        return age >= 18

    @staticmethod
    def calculate_age(birth_year):
        return datetime.now().year - birth_year

# Using instance methods
person1 = Person("Alice", 30)
print(person1.introduce())
# Output: Hello, I am Alice and I am 30 years old

print(person1.celebrate_birthday())
# Output: Alice is now 31 years old

# Using class method as alternative constructor
person2 = Person.from_birth_year("Bob", 1990)
print(person2.introduce())
# Output: Hello, I am Bob and I am 35 years old

print(Person.get_species())
# Output: All persons are Homo sapiens

# Using static methods - no instance needed
print(Person.is_adult(25))
# Output: True

print(Person.is_adult(16))
# Output: False

print(Person.calculate_age(2000))
# Output: 25

# Static method can be called from instance too, but doesn't use instance data
print(person1.is_adult(20))
# Output: True
```
[Back to Table of Contents](#table-of-contents)

### 25. What is the difference between `@property` and regular attributes in Python classes? Why would you use `@property`?

`@property` is a decorator that we apply to methods in a class to make them behave like attributes. The main difference is that a property executes logic when accessed, while a regular attribute just returns stored data. Both are accessed using dot notation without parentheses.

We use `@property` for two main reasons: to compute values on the fly, or to add validation and control through getters and setters.

For computed values, if we have a Person class with first name and last name attributes, we can create a full name property that concatenates them. We access it like `person.full_name` just like a regular attribute, but behind the scenes it runs code to combine the names.

For encapsulation, properties let us control how attributes are accessed and modified using getters and setters. The getter validates or controls access to a value based on certain conditions. For example, we might have a getter that only returns sensitive admin data if the user has the admin role, otherwise it raises an exception. The setter validates or processes the value before storing it. For example, we can ensure age is never negative by adding validation in the setter. This gives us the flexibility of methods while maintaining the clean syntax of direct attribute access.

```python
class User:
    def __init__(self, name, role, admin_data):
        self.name = name
        self._role = role
        self._admin_data = admin_data

    # Getter with permission check - controls access based on user role
    @property
    def admin_data(self):
        if self._role == "admin":
            return self._admin_data
        raise PermissionError("Only admins can access this data")

    # Setter with validation - validates data before storing
    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, value):
        if value < 0:
            raise ValueError("Age cannot be negative")
        self._age = value

# Using the class
admin_user = User("Alice", "admin", {"users": 1500, "revenue": 50000})
print(admin_user.admin_data)  # Output: {'users': 1500, 'revenue': 50000}

regular_user = User("Bob", "user", {"users": 1500, "revenue": 50000})
try:
    print(regular_user.admin_data)  # Raises PermissionError
except PermissionError as e:
    print(e)  # Output: Only admins can access this data
```
[Back to Table of Contents](#table-of-contents)

### 26. What is the difference between `open()` with different modes in Python? Explain the difference between 'r', 'w', 'a', 'r+', 'w+', and 'a+' modes.

When we open a file, the mode determines how we can interact with it and what happens to existing content. Mode 'r' opens the file for reading only and raises an error if the file does not exist. Mode 'w' opens for writing and creates the file if it does not exist, but importantly, it truncates the file, meaning it deletes all existing content. Mode 'a' opens for appending, which adds content to the end of the file without deleting existing content.

The plus modes allow both reading and writing. Mode 'r+' opens for reading and writing, but the file must already exist. Mode 'w+' opens for both reading and writing, but it truncates the file first, deleting existing content. Mode 'a+' opens for both reading and appending. The write cursor starts at the end for appending new content, but we can move it to read from anywhere in the file.

A key difference between 'r+' and 'w+' is that 'r+' preserves existing content while 'w+' deletes it. The difference between 'a' and 'a+' is that 'a+' allows us to read the file as well as append to it.

```python
# Mode 'r' - Read only
with open('file.txt', 'r') as f:
    content = f.read()
    print(content)

# Mode 'w' - Write (truncates existing content)
with open('file.txt', 'w') as f:
    f.write("New content")  # Deletes old content first

# Mode 'a' - Append only
with open('file.txt', 'a') as f:
    f.write("\nAppended line")  # Adds to the end

# Mode 'r+' - Read and write (preserves existing content)
with open('file.txt', 'r+') as f:
    content = f.read()
    f.write("\nNew line")  # Adds to the end without deleting

# Mode 'w+' - Write and read (truncates existing content)
with open('file.txt', 'w+') as f:
    f.write("New content")
    f.seek(0)  # Move cursor to the beginning
    content = f.read()
    print(content)

# Mode 'a+' - Append and read
with open('file.txt', 'a+') as f:
    f.write("\nAppended line")
    f.seek(0)  # Move cursor to the beginning to read
    content = f.read()
    print(content)
```
[Back to Table of Contents](#table-of-contents)

### 27. What is the difference between `import module` and `from module import something`? What about `from module import *`?

The main difference is how we access the imported items and what gets added to our namespace. With `import module`, we import the entire module and access its contents using dot notation like `module.function()`. This keeps our namespace clean because everything is under the module name.

With `from module import something`, we import specific items like classes, functions, or variables directly into our namespace, so we can use them without the module prefix. For example, `from math import sqrt` lets us use `sqrt()` directly instead of `math.sqrt()`.

Using `from module import *` imports everything from the module directly into our namespace. This is generally discouraged because it can cause naming conflicts and makes code harder to read. When we see a function being used, we do not know which module it came from, and we might accidentally overwrite variables or functions without realizing it.

```python
# import module - access with dot notation
import math
result = math.sqrt(16)  # Output: 4.0
print(math.pi)          # Output: 3.14159...

# from module import something - direct access
from math import sqrt, pi
result = sqrt(16)       # Output: 4.0
print(pi)               # Output: 3.14159...

# from module import * - imports everything (discouraged)
from math import *
result = sqrt(16)       # Output: 4.0
print(pi)               # Output: 3.14159...

# Example showing namespace pollution with import *
# If we have a function named 'sqrt' defined elsewhere
def sqrt(x):
    return x ** 2

# And we do this:
from math import *
# Now 'sqrt' is overwritten by the math module's sqrt
result = sqrt(16)  # Uses math.sqrt, not our function

# With 'from module import something', we control what we import
from math import sqrt as math_sqrt
def sqrt(x):
    return x ** 2

result = sqrt(16)        # Output: 256 (uses our function)
result2 = math_sqrt(16)  # Output: 4.0 (uses math's sqrt)
```
[Back to Table of Contents](#table-of-contents)

### 28. What is the difference between `__init__` and `__new__` methods in Python classes?

The difference between `__init__` and `__new__` is that `__new__` creates the instance as an empty object, while `__init__` initializes it by setting up the attributes. `__new__` is called first and actually creates and returns the instance, then `__init__` is called to initialize that instance.

`__new__` is a special method that receives the class as its first parameter, which we call `cls`, and it returns the newly created instance. This allows it to control the actual creation of the object before `__init__` runs. `__init__` is an instance method that receives the instance as `self` and returns nothing. It runs after the instance already exists.

In most Python code, we never need to override `__new__` because Python handles object creation automatically. We usually only override `__new__` for special cases like implementing the singleton pattern, where we want to ensure only one instance of a class exists.

```python
# Basic example showing __new__ and __init__ order
class Person:
    def __new__(cls, name):
        print(f"__new__ called: Creating instance of {cls.__name__}")
        instance = super().__new__(cls)
        return instance

    def __init__(self, name):
        print(f"__init__ called: Initializing {name}")
        self.name = name

person = Person("Alice")
# Output:
# __new__ called: Creating instance of Person
# __init__ called: Initializing Alice

# Singleton pattern - override __new__ to create only one instance
class Singleton:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            print("Creating new Singleton instance")
            cls._instance = super().__new__(cls)
        else:
            print("Returning existing Singleton instance")
        return cls._instance

    def __init__(self):
        self.value = "I am a singleton"

singleton1 = Singleton()  # Output: Creating new Singleton instance
singleton2 = Singleton()  # Output: Returning existing Singleton instance
print(singleton1 is singleton2)  # Output: True (same object)
```
[Back to Table of Contents](#table-of-contents)

### 29. What are context managers in Python? How do you use them?

Context managers in Python are a feature that we use with the `with` statement to handle setup and cleanup automatically. Setup refers to the preparation work needed before using a resource, like opening a file. Cleanup refers to releasing that resource, like closing the file.

The most common example is `with open()` when working with files. When we use `with open("file.txt") as file:`, the context manager automatically opens the file when entering the `with` block, and automatically closes it when exiting the block.

The main advantage is that we do not have to worry about closing the file manually. The context manager handles it automatically when we are done, even if an error occurs in our code. This prevents resource leaks, which happen when resources like files stay open and consume system memory unnecessarily. Context managers make our code cleaner and safer by guaranteeing that resources are always properly released.

**Code example:**

```python
# WITHOUT context manager - manual setup and cleanup (not recommended)
file = open("example.txt", "w")  # Manual setup
file.write("Hello World")
file.close()  # Manual cleanup - must remember!

# If an error occurs before close(), the file stays open (resource leak)
file = open("example.txt", "w")  # Setup
file.write("Hello World")
# Some error happens here...
file.close()  # This line never executes! File stays open.

# Context manager handles CLEANUP, not the error
with open("example.txt", "r") as file:
    content = file.read()
    raise ValueError("Something went wrong!")
    # Program crashes here, but file is closed before crashing

# The try-except is only for catching/handling the error
# (so the program doesn't crash)
try:
    with open("example.txt", "r") as file:
        content = file.read()
        raise ValueError("Something went wrong!")
except ValueError as e:
    print(e)  # We handle the error so program continues
# File is still closed (context manager did that)
```
[Back to Table of Contents](#table-of-contents)

### 30. Can you explain the difference between SyntaxError, NameError, TypeError, and ValueError in Python? When does each one occur and can you give me an example of each?

SyntaxError occurs when Python cannot parse the code before execution. Examples include missing colons after if, for, while, def statements, unmatched parentheses or brackets, or incorrect indentation.

NameError occurs when we try to use a variable or function that has not been defined or is not in the current scope.

TypeError occurs when an operation is performed on incompatible types, like trying to add a string to an integer, or calling a non-callable object.

ValueError occurs when a function receives an argument of the correct type but with an invalid value, like trying to convert the string 'abc' to an integer with int().

```python
# SyntaxError - missing colon after if statement
# if True
#     print("Hello")  # SyntaxError: invalid syntax

# SyntaxError - wrong indentation
# def greet():
# print("Hello")  # IndentationError: expected an indented block

# NameError - variable not defined
try:
    print(undefined_variable)
except NameError as e:
    print(f"NameError: {e}")  # Output: NameError: name 'undefined_variable' is not defined

# TypeError - incompatible types
try:
    result = "hello" + 5
except TypeError as e:
    print(f"TypeError: {e}")  # Output: TypeError: can only concatenate str (not "int") to str

# ValueError - invalid value for conversion
try:
    number = int("abc")
except ValueError as e:
    print(f"ValueError: {e}")  # Output: ValueError: invalid literal for int() with base 10: 'abc'

# ValueError - correct type but invalid value
def set_age(age):
    if age < 0 or age > 150:
        raise ValueError("Age must be between 0 and 150")
    return age

# Invalid value - raises ValueError
try:
    print(set_age(-5))
except ValueError as e:
    print(f"ValueError: {e}")  # Output: ValueError: Age must be between 0 and 150
```
[Back to Table of Contents](#table-of-contents)

### 31. What other Python exceptions do you know?

We may receive an IndexError when we try to access an index in a collection that does not exist. If we are working with a list that has 3 elements and we try to access index 3, we would receive IndexError since the greatest index that the list has is 2.

We may experience KeyError if we try to access a key in a dictionary that does not exist. If we have a dictionary that has only a 'name' key and we try to get the key 'age' from that dictionary, this would result in KeyError.

ZeroDivisionError we would receive if we try to divide any number by zero.

```python
# IndexError - index out of range
my_list = [10, 20, 30]
try:
    print(my_list[3])  # List has indices 0, 1, 2 only
except IndexError as e:
    print(f"IndexError: {e}")  # Output: IndexError: list index out of range

# KeyError - key doesn't exist in dictionary
person = {"name": "Alice"}
try:
    print(person["age"])
except KeyError as e:
    print(f"KeyError: {e}")  # Output: KeyError: 'age'

# ZeroDivisionError - division by zero
try:
    result = 10 / 0
except ZeroDivisionError as e:
    print(f"ZeroDivisionError: {e}")  # Output: ZeroDivisionError: division by zero
```
[Back to Table of Contents](#table-of-contents)

### 32. What is the difference between `try-except`, `try-except-else`, and `try-except-else-finally` blocks? When would you use each?

When we use `try-except`, we try to execute the code in the try block, and if it raises an error, we execute the except block.

With `try-except-else`, if the code in the try block does not raise an error, we enter the else block. If it does raise an error, we enter the except block. The else block is useful for separating the risky code that might fail from the code that should only run if everything succeeded.

With `try-except-else-finally`, it works the same way, but the finally block will always execute no matter what happens, whether there is an error or not, and even if there is a return statement in the try, except, or else blocks. We use finally when we need to perform cleanup actions that must happen regardless of the outcome, like closing files or releasing resources.

```python
# Example 1: try-except
def divide_basic(a, b):
    try:
        result = a / b
        print(f"Result: {result}")
    except ZeroDivisionError:
        print("Cannot divide by zero!")

divide_basic(10, 2)  # Output: Result: 5.0
divide_basic(10, 0)  # Output: Cannot divide by zero!

# Example 2: try-except-else
def divide_with_else(a, b):
    try:
        result = a / b  # Risky operation
    except ZeroDivisionError:
        print("Cannot divide by zero!")
    else:
        # Only runs if no error occurred
        print(f"Division successful: {result}")
        print("Continuing with other operations...")

divide_with_else(10, 2)
# Output:
# Division successful: 5.0
# Continuing with other operations...

divide_with_else(10, 0)
# Output: Cannot divide by zero!

# Example 3: try-except-else-finally
def process_file(filename):
    file = None
    try:
        file = open(filename, 'r')
        content = file.read()
        result = int(content)  # Might raise ValueError
    except FileNotFoundError:
        print(f"File {filename} not found")
    except ValueError:
        print("File content is not a valid number")
    else:
        print(f"Successfully read number: {result}")
    finally:
        # Always executes - cleanup happens here
        if file:
            file.close()
            print("File closed")
```
[Back to Table of Contents](#table-of-contents)

### 33. What is Inheritance In OOP?

Inheritance allows us to reuse code by creating child classes that inherit attributes and methods from parent classes. This helps share common functionality.

```python
# Parent class with shared functionality
class Animal:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def eat(self):
        return f"{self.name} is eating"

    def sleep(self):
        return f"{self.name} is sleeping"

# Child classes inherit from Animal
class Dog(Animal):
    def __init__(self, name, age, breed):
        super().__init__(name, age)  # Call parent constructor
        self.breed = breed

    def bark(self):
        return f"{self.name} says: Woof!"

class Cat(Animal):
    def __init__(self, name, age, color):
        super().__init__(name, age)
        self.color = color

    def meow(self):
        return f"{self.name} says: Meow!"

# Usage - child classes inherit parent methods
dog = Dog("Buddy", 3, "Golden Retriever")
cat = Cat("Whiskers", 2, "Orange")

# Inherited methods from Animal
print(dog.eat())  # Output: Buddy is eating
print(cat.sleep())  # Output: Whiskers is sleeping

# Child-specific methods
print(dog.bark())  # Output: Buddy says: Woof!
print(cat.meow())  # Output: Whiskers says: Meow!

# Child-specific attributes
print(f"{dog.name} is a {dog.breed}")  # Output: Buddy is a Golden Retriever
print(f"{cat.name} is {cat.color}")  # Output: Whiskers is Orange
```
[Back to Table of Contents](#table-of-contents)

### 34. What is encapsulation in Python? How do you implement private and protected attributes?

Encapsulation is another of the four pillars of OOP. It allows us to protect sensitive data by restricting access and adding validation before setting values.

In Python, we don't have true encapsulation - it's just a convention. We implement protected attributes with a single underscore prefix (`_attribute`) and private attributes with a double underscore prefix (`__attribute`). Protected attributes are meant to be accessed within the class and its subclasses, while private attributes are meant to be accessed only within the class itself.

However, this is just a convention. Protected attributes can be accessed from anywhere. Private attributes undergo name mangling - Python transforms `__attribute` to `_ClassName__attribute` - but they can still be accessed if you know this pattern.

We achieve proper encapsulation using the `@property` decorator for getters and setters. The pattern works like this: we create a getter method decorated with `@property` that returns an internal variable `self._name`. Then we create a setter decorated with `@name.setter` where we can validate or process the value before storing it as `self._name`.

The underscore in the internal variable (`_name`) is crucial to avoid infinite recursion - if the property and stored variable had the same name, accessing it would trigger the property infinitely. When we write `self.name = value` anywhere in our code (including `__init__`), Python automatically calls the setter, which stores it as `self._name`. This pattern gives us validation and controlled access while maintaining a clean, intuitive interface.

```python
class Person:
    def __init__(self, name, is_authenticated=False):
        self.name = name  # Calls @name.setter
        self._is_authenticated = is_authenticated

    # Getter for name with access validation
    @property
    def name(self):
        if not self._is_authenticated:
            raise PermissionError("Authentication required to access name")
        return self._name

    # Setter for name with validation
    @name.setter
    def name(self, value):
        if not value or not isinstance(value, str):
            raise ValueError("Name must be a non-empty string")
        self._name = value.strip()

    def authenticate(self):
        self._is_authenticated = True

# Creating person without authentication
person = Person("Alice")

# Getter validation restricts access
try:
    print(f"Name: {person.name}")
except PermissionError as e:
    print(f"Error: {e}")
# Output: Error: Authentication required to access name

# Authenticate and access granted
person.authenticate()
print(f"Name: {person.name}")
# Output: Name: Alice

# Setter validation works during assignment
person.name = "Bob"
print(f"Updated name: {person.name}")
# Output: Updated name: Bob

try:
    person.name = ""
except ValueError as e:
    print(f"Error: {e}")
# Output: Error: Name must be a non-empty string
```
[Back to Table of Contents](#table-of-contents)

### 35. What is polymorphism in Python? Can you explain with an example?

Polymorphism is one of the four main pillars of OOP. It allows us to use the same interface on different objects.

Let us imagine that we are building a Django e-commerce application with multiple product categories. Let us say we have watches, rings, and earrings. All categories have common attributes like name and price. However, they also have their own specific attributes - a watch has a type of movement, a ring has finger circumference, while earrings do not have any size.

We can define a BaseProduct abstract class with the common attributes and have each product category as a separate class that inherits from it, where we define their unique attributes. This is where polymorphism appears - while we have separate models for different categories that inherit from the base class, they can all share the same interface, like the `__str__` or `save()` methods.

The beauty of polymorphism is that we don't need `if` checks to decide which method to use. Python automatically calls the right method for each object type. Each child class can implement these methods differently, but they all use the same method names and can be treated uniformly. This makes our code cleaner and more maintainable.

```python
from abc import ABC, abstractmethod

# Abstract base class with common interface
class BaseProduct(ABC):
    def __init__(self, name, price):
        self.name = name
        self.price = price

    @abstractmethod
    def get_details(self):
        pass

    def __str__(self):
        return f"{self.name} - ${self.price}"

# Specific product classes implementing the same interface differently
class Watch(BaseProduct):
    def __init__(self, name, price, movement_type):
        super().__init__(name, price)
        self.movement_type = movement_type

    def get_details(self):
        return f"Watch: {self.name}, Movement: {self.movement_type}"

class Ring(BaseProduct):
    def __init__(self, name, price, circumference):
        super().__init__(name, price)
        self.circumference = circumference

    def get_details(self):
        return f"Ring: {self.name}, Size: {self.circumference}mm"

class Earrings(BaseProduct):
    def __init__(self, name, price):
        super().__init__(name, price)

    def get_details(self):
        return f"Earrings: {self.name}"

# Polymorphism in action - same interface, different implementations
products = [
    Watch("Rolex", 5000, "Automatic"),
    Ring("Gold Band", 800, 18),
    Earrings("Diamond Studs", 1200)
]

# No if-checks needed - Python calls the right method automatically
for product in products:
    print(product)  # Calls __str__() from BaseProduct
    print(product.get_details())  # Calls specific get_details()
    print()
# Output: Rolex - $5000
#         Watch: Rolex, Movement: Automatic
#
#         Gold Band - $800
#         Ring: Gold Band, Size: 18mm
#
#         Diamond Studs - $1200
#         Earrings: Diamond Studs
```
[Back to Table of Contents](#table-of-contents)

### 36. What is Abstraction in OOP?

Abstraction means hiding complex implementation details and showing only essential features. In Python, we create abstract classes using the ABC module and the `@abstractmethod` decorator. Abstract classes serve as blueprints - they cannot be instantiated directly. Child classes must implement all abstract methods with their own custom logic. This ensures all descendants share a common interface while having different implementations.

```python
from abc import ABC, abstractmethod

# Abstract class - cannot be instantiated
class PaymentProcessor(ABC):
    @abstractmethod
    def process_payment(self, amount):
        pass

    @abstractmethod
    def refund(self, transaction_id):
        pass

    # Concrete method - shared by all children
    def log_transaction(self, message):
        return f"LOG: {message}"

# Concrete implementations
class CreditCardProcessor(PaymentProcessor):
    def process_payment(self, amount):
        return f"Processing ${amount} via Credit Card"

    def refund(self, transaction_id):
        return f"Refunding Credit Card transaction {transaction_id}"

class PayPalProcessor(PaymentProcessor):
    def process_payment(self, amount):
        return f"Processing ${amount} via PayPal"

    def refund(self, transaction_id):
        return f"Refunding PayPal transaction {transaction_id}"

# Usage
# payment = PaymentProcessor()  # ERROR: Cannot instantiate abstract class

credit_card = CreditCardProcessor()
paypal = PayPalProcessor()

# Different implementations, same interface
print(credit_card.process_payment(100))  # Output: Processing $100 via Credit Card
print(paypal.process_payment(50))  # Output: Processing $50 via PayPal

print(credit_card.refund("CC123"))  # Output: Refunding Credit Card transaction CC123
print(paypal.refund("PP456"))  # Output: Refunding PayPal transaction PP456

# Shared concrete method
print(credit_card.log_transaction("Payment completed"))  # Output: LOG: Payment completed
```
[Back to Table of Contents](#table-of-contents)

### 37. What is composition vs inheritance in Python? When would you choose composition over inheritance?

Composition and inheritance are two ways to build relationships between classes. Inheritance represents an "is-a" relationship, while composition represents a "has-a" relationship.

With inheritance, a child class inherits from a parent class. For example, Dog is-a Animal. With composition, a class contains instances of other classes as attributes. For example, a Car has-an Engine, has-a Steering Wheel, and has-a Transmission.

We choose composition over inheritance when we want flexibility and when the relationship is more about "having" components rather than "being" a type of something. Composition is generally preferred because it's more flexible - you can change the components at runtime and avoid tight coupling between classes.

A practical example: Instead of having PaymentService inherit from UserCheckout, we'd have UserCheckout contain instances of PaymentService and OrderService as attributes. The UserCheckout class would call methods on these service objects, but it doesn't inherit from them. This way, we can easily swap out payment methods or order creation logic without changing the UserCheckout class structure.

The principle "favor composition over inheritance" suggests using composition when you need to reuse code but don't have a clear "is-a" relationship. Inheritance should be used when there's a true hierarchical relationship.

```python
# Inheritance - "is-a" relationship
class Person:
    def __init__(self, name):
        self.name = name

class Teacher(Person):  # Teacher IS-A Person
    def teach(self):
        return f"{self.name} is teaching"

# Composition - "has-a" relationship with Dependency Injection
class PaymentService:
    def process_payment(self, amount):
        return f"Processing ${amount} payment"

class OrderService:
    def create_order(self, items):
        return f"Order created with {items} items"

class UserCheckout:  # HAS-A PaymentService and OrderService (injected)
    def __init__(self, payment_service, order_service):
        self.payment = payment_service
        self.order = order_service

    def checkout(self, items, amount):
        order_result = self.order.create_order(items)
        payment_result = self.payment.process_payment(amount)
        return f"{order_result} | {payment_result}"

# Usage
teacher = Teacher("Emma")
print(teacher.teach())  # Output: Emma is teaching

payment = PaymentService()
order = OrderService()
checkout = UserCheckout(payment, order)  # Dependencies injected
print(checkout.checkout(3, 99.99))
# Output: Order created with 3 items | Processing $99.99 payment
```
[Back to Table of Contents](#table-of-contents)

### 38. What types of inheritance do you know in Python?

There are five types of inheritance in Python.

Single inheritance is when a child class inherits from only one parent class. This is the simplest form of inheritance.

Multiple inheritance is when a child class inherits from more than one parent class. Python supports this, unlike some other languages like Java or C sharp. Python handles multiple inheritance using MRO, which stands for Method Resolution Order. MRO checks for attributes first in the current class, then in each parent class in the order they are inherited. This is useful when inheriting multiple mixins to follow the DRY principle, which means we do not repeat code.

Multilevel inheritance is a chain of inheritance, like Grandmother inheriting to Mother, and Mother inheriting to Daughter. Each class inherits from the one above it in the hierarchy.

Hierarchical inheritance is when multiple child classes inherit from the same parent class. For example, one Animal class can have multiple children like Dog, Cat, and Bird. Each child class inherits from the same Animal parent.

Hybrid inheritance is a combination of two or more types of inheritance together. For example, we can combine multilevel and multiple inheritance in the same program.

```python
# Single inheritance
class Animal:
    def eat(self):
        print("Eating")

class Dog(Animal):
    def bark(self):
        print("Woof")

# Multiple inheritance
class Flyer:
    def fly(self):
        print("Flying")

class Swimmer:
    def swim(self):
        print("Swimming")

class Duck(Flyer, Swimmer):
    pass

# Multilevel inheritance
class Grandmother:
    def cook(self):
        print("Cooking")

class Mother(Grandmother):
    def teach(self):
        print("Teaching")

class Daughter(Mother):
    def study(self):
        print("Studying")

# Hierarchical inheritance
class Animal:
    def eat(self):
        print("Eating")

class Dog(Animal):
    def bark(self):
        print("Woof")

class Cat(Animal):
    def meow(self):
        print("Meow")

# Hybrid inheritance
class Vehicle:
    def move(self):
        print("Moving")

class LandVehicle(Vehicle):
    def drive(self):
        print("Driving")

class Car(LandVehicle):
    def honk(self):
        print("Honk")

class Truck(LandVehicle):
    def haul(self):
        print("Hauling")
```
[Back to Table of Contents](#table-of-contents)

### 39. What is the difference between abstract classes and interfaces in Python? How do you create an abstract class?

To be an abstract class in Python, a class must inherit from the built-in ABC module and define at least one abstract method decorated with the `@abstractmethod` decorator. An abstract class cannot be instantiated - it's only a blueprint for its descendants. The abstract methods it defines must not have implementation, and descendants are responsible for writing their own implementation.

Python doesn't have a separate interface keyword like TypeScript or Java. Instead, we use abstract classes to create interfaces. The difference is that an abstract class can have both abstract methods without implementation and concrete methods with implementation that all descendants will share. An interface pattern in Python would be an abstract class where ALL methods are abstract with no implementation - essentially defining only the structure that descendants must follow.

So in Python, an abstract class is more flexible - it can have some methods with implementation for shared behavior and some abstract methods that descendants must implement. When you want a pure interface with no implementation at all, you simply make all methods abstract. This is why Python doesn't need a separate interface keyword - abstract classes cover both use cases.

```python
from abc import ABC, abstractmethod

# Abstract class with both abstract and concrete methods
class Animal(ABC):
    @abstractmethod
    def make_sound(self):
        """Abstract method - must be implemented by subclasses"""
        pass

    def sleep(self):
        """Concrete method - shared by all subclasses"""
        return "Zzz..."

# Pure interface pattern - all methods are abstract
class Flyable(ABC):
    @abstractmethod
    def fly(self):
        pass

    @abstractmethod
    def land(self):
        pass

# Concrete implementation
class Dog(Animal):
    def make_sound(self):
        return "Woof!"

class Bird(Animal, Flyable):
    def make_sound(self):
        return "Tweet!"

    def fly(self):
        return "Flying high!"

    def land(self):
        return "Landing safely"

# Usage
# animal = Animal()  # TypeError: Can't instantiate abstract class

dog = Dog()
print(dog.make_sound())  # Output: Woof!
print(dog.sleep())       # Output: Zzz...

bird = Bird()
print(bird.make_sound())  # Output: Tweet!
print(bird.fly())         # Output: Flying high!
print(bird.sleep())       # Output: Zzz...
```
[Back to Table of Contents](#table-of-contents)

### 40. What is duck typing in Python? Can you explain the concept?

Duck typing is a Python concept that says "if it walks like a duck and quacks like a duck, then it's a duck." We don't care what type an object is - we only care if it has the methods we need.

For example, if we have two classes like AirPlane and Bird, and both have a `fly()` method, we can call `fly()` on either object without checking what type it is. Python doesn't care if it's an airplane or a bird - if it has the `fly()` method, it will work.

We can write a function that calls `fly()` on any object passed to it, and it will work for both AirPlane and Bird instances, even though they're completely unrelated classes with no inheritance or interfaces between them. Python simply tries to call the method at runtime, and if the object has it, it works. If not, Python raises an error.

This is different from statically typed languages where you must declare types explicitly and check them. Duck typing makes Python code flexible and reusable - we write functions based on what objects can do, not what they are.

```python
# Two unrelated classes with the same method name
class Airplane:
    def fly(self):
        return "Airplane flying at 30,000 feet"

class Bird:
    def fly(self):
        return "Bird soaring through the sky"

class Car:
    def drive(self):
        return "Car driving on the road"

# Function that accepts any object with a fly() method
def make_it_fly(flying_object):
    return flying_object.fly()  # No type checking - just call the method

# Usage - works with any object that has fly()
airplane = Airplane()
bird = Bird()
car = Car()

print(make_it_fly(airplane))  # Output: Airplane flying at 30,000 feet
print(make_it_fly(bird))      # Output: Bird soaring through the sky

# This will fail because Car doesn't have fly() method
try:
    print(make_it_fly(car))
except AttributeError as e:
    print(f"Error: {e}")  # Output: Error: 'Car' object has no attribute 'fly'
```
[Back to Table of Contents](#table-of-contents)

### 41. What are the SOLID principles in software design? Can you explain what each letter stands for and give a brief explanation of each principle?

S stands for Single Responsibility—each class should have only one reason to exist and solve only one problem. O stands for Open/Closed Principle—a functionality should be written to be open for extension but closed for modification. Currently we have only products that share the same characteristics, so we define a Product class and add an attribute category to differentiate between different product categories. But what if in the future we add a product that does not share the already defined characteristics—it misses some or has more that are not valid for the existing product categories? In this case our functionality is closed for modification and open for modification. We would have to modify the existing logic by adding if-else checks or rewriting the models. We would make it open for extension and closed for modification from the very beginning if we created a BaseProduct class that defines only attributes valid for all products like image URL and description, and inherit that class in other classes for different product categories. Then we are free to add as many unique characteristics to each category as we need and extend the functionality by adding new classes—new categories. L stands for Liskov Substitution—in the previous example we would have ruined Liskov Substitution because we would have made classes depend on attributes that are not valid for themselves. Liskov Substitution says that we should not inherit from a parent class unless all its attributes are valid for the descendant. I stands for Interface Segregation—classes use common interfaces and implement the same methods differently. We can have a mixin named FlyingMixin, then inherit it into the classes Airplane and Bird. Both can fly but fly differently. D stands for Dependency Inversion—we go against that principle if we instantiate classes in another class. If we need an instance of a class in another class it is correct to inject it—to pass it from outside or use the factory design pattern.

**S - Single Responsibility Principle**
Each class has one reason to change - one responsibility.

```python
# BAD: Multiple responsibilities in one class
class UserManager:
    def create_user(self, name, email):
        pass

    def send_welcome_email(self, email):
        pass

    def generate_report(self, user_id):
        pass

# GOOD: Each class has single responsibility
class User:
    def __init__(self, name, email):
        self.name = name
        self.email = email

class EmailService:
    def send_welcome_email(self, email):
        print(f"Welcome email sent to {email}")

class ReportGenerator:
    def generate_report(self, user):
        print(f"Report generated for {user.name}")

# Usage
user = User("John", "john@example.com")
email_service = EmailService()
email_service.send_welcome_email(user.email)  # Output: Welcome email sent to john@example.com
```
[Back to Table of Contents](#table-of-contents)

**O - Open/Closed Principle**
Open for extension, closed for modification.

```python
# GOOD: Open for extension, closed for modification
class BaseProduct:
    def __init__(self, image_url, description):
        self.image_url = image_url
        self.description = description

class ElectronicsProduct(BaseProduct):
    def __init__(self, image_url, description, warranty_years, voltage):
        super().__init__(image_url, description)
        self.warranty_years = warranty_years
        self.voltage = voltage

class ClothingProduct(BaseProduct):
    def __init__(self, image_url, description, size, material):
        super().__init__(image_url, description)
        self.size = size
        self.material = material

# Usage - extending without modifying existing code
laptop = ElectronicsProduct("laptop.jpg", "Gaming laptop", 2, "220V")
shirt = ClothingProduct("shirt.jpg", "Cotton shirt", "L", "100% cotton")
print(f"Laptop warranty: {laptop.warranty_years} years")  # Output: Laptop warranty: 2 years
print(f"Shirt size: {shirt.size}")  # Output: Shirt size: L
```
[Back to Table of Contents](#table-of-contents)

**L - Liskov Substitution Principle**
Subclasses should be substitutable for their base classes.

```python
# BAD: Violates LSP - Square has to override area calculation awkwardly
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height

# GOOD: Respects LSP
class Shape:
    def area(self):
        raise NotImplementedError

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height

class Square(Shape):
    def __init__(self, side):
        self.side = side

    def area(self):
        return self.side * self.side

# Usage - both can be treated as Shape
shapes = [Rectangle(5, 10), Square(5)]
for shape in shapes:
    print(f"Area: {shape.area()}")
# Output: Area: 50
# Output: Area: 25
```
[Back to Table of Contents](#table-of-contents)

**I - Interface Segregation Principle**
Classes should not be forced to implement interfaces they don't use.

```python
from abc import ABC, abstractmethod

# GOOD: Segregated interfaces
class FlyingMixin(ABC):
    @abstractmethod
    def fly(self):
        pass

class SwimmingMixin(ABC):
    @abstractmethod
    def swim(self):
        pass

class Airplane(FlyingMixin):
    def fly(self):
        return "Flying with jet engines"

class Bird(FlyingMixin, SwimmingMixin):
    def fly(self):
        return "Flying with wings"

    def swim(self):
        return "Swimming with webbed feet"

class Fish(SwimmingMixin):
    def swim(self):
        return "Swimming with fins"

# Usage
airplane = Airplane()
duck = Bird()
salmon = Fish()

print(airplane.fly())  # Output: Flying with jet engines
print(duck.fly())  # Output: Flying with wings
print(duck.swim())  # Output: Swimming with webbed feet
print(salmon.swim())  # Output: Swimming with fins
```
[Back to Table of Contents](#table-of-contents)

**D - Dependency Inversion Principle**
Depend on abstractions, not concretions. Inject dependencies instead of instantiating them.

```python
# BAD: High-level module depends on low-level module
class EmailSender:
    def send(self, message):
        print(f"Email sent: {message}")

class NotificationService:
    def __init__(self):
        self.sender = EmailSender()  # Direct instantiation - tight coupling

    def notify(self, message):
        self.sender.send(message)

# GOOD: Dependency injection
class MessageSender(ABC):
    @abstractmethod
    def send(self, message):
        pass

class EmailSender(MessageSender):
    def send(self, message):
        return f"Email sent: {message}"

class SMSSender(MessageSender):
    def send(self, message):
        return f"SMS sent: {message}"

class NotificationService:
    def __init__(self, sender: MessageSender):
        self.sender = sender  # Dependency injected

    def notify(self, message):
        return self.sender.send(message)

# Usage - flexibility through injection
email_notifier = NotificationService(EmailSender())
sms_notifier = NotificationService(SMSSender())

print(email_notifier.notify("Hello!"))  # Output: Email sent: Hello!
print(sms_notifier.notify("Hello!"))  # Output: SMS sent: Hello!
```
[Back to Table of Contents](#table-of-contents)

### 42. What are magic methods (dunder methods) in Python? Can you name and explain a few important ones besides `__init__`?

Magic methods, or dunder methods, get their name from their specific naming convention - they start and end with double underscores. Python calls these methods automatically when we use certain operations or syntax.

A great example is the `__gt__` method, which defines the greater-than comparison. If we want to compare two instances of a Person class, Python wouldn't know by default if one person is greater than another. We can override `__gt__` by accepting `self` and `other` as parameters and writing custom logic. For example, we could write `return self.age > other.age` to compare by age. Then when we write `first_person > second_person`, Python automatically passes `first_person` as `self` and `second_person` as `other`, returning True or False.

Other similar comparison dunder methods are `__ge__` for greater-or-equal, `__lt__` for less-than, `__le__` for less-or-equal, and `__eq__` for equality. The `__str__` method is also a dunder method that returns the string representation we define instead of the default "Object at memory address" format.

There are many other useful dunder methods like `__len__` for defining length, `__getitem__` for indexing support, `__add__` for the addition operator, and `__repr__` for developer-friendly representation. These methods let us make our custom objects behave like built-in Python types.

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __gt__(self, other):  # Greater than: >
        return self.age > other.age

    def __eq__(self, other):  # Equality: ==
        return self.age == other.age

    def __str__(self):  # String representation for users
        return f"{self.name}, {self.age} years old"

    def __len__(self):  # Length
        return self.age

    def __add__(self, other):  # Addition: +
        return self.age + other.age

# Usage
alice = Person("Alice", 30)
bob = Person("Bob", 25)

print(alice > bob)   # Output: True (__gt__ called)
print(alice == bob)  # Output: False (__eq__ called)
print(alice)         # Output: Alice, 30 years old (__str__ called)
print(len(alice))    # Output: 30 (__len__ called)
print(alice + bob)   # Output: 55 (__add__ called)
```
[Back to Table of Contents](#table-of-contents)

### 43. What are class attributes vs instance attributes? What's the difference and when would you use each?

Class attributes are valid for every instance of the class, while instance attributes are unique to each specific instance.

For example, if we have a HumanBeing class, we can set "mammal" as a class attribute because this is valid for every human being. We assign name, age, and height as instance attributes because they're unique to each person.

It's important not to change class attributes through an instance, but only through the class itself. If we try to change a class attribute through an instance, it doesn't actually change the class attribute - it creates a new instance attribute with the same name for that specific instance only. This creates inconsistency because the rest of the instances would still have the original class attribute unchanged.

What actually happens is that the class attribute still exists unchanged, but Python creates a new instance attribute with the same name. When we access the attribute, Python checks instance attributes first, so we get the new instance value. However, the class attribute is still there in the background - other instances still see the original class value. So if we want to change a class attribute for all instances, we must do it through the class itself, not through an instance.

Class attributes are useful for constants, default values, or properties shared across all instances. Instance attributes are for data that varies between objects.

```python
class HumanBeing:
    species = "mammal"  # Class attribute - shared by all instances

    def __init__(self, name, age, height):
        self.name = name      # Instance attributes - unique per instance
        self.age = age
        self.height = height

# Create instances
alice = HumanBeing("Alice", 28, 165)
emma = HumanBeing("Emma", 32, 170)

print(alice.species)  # Output: mammal
print(emma.species)   # Output: mammal

# Changing class attribute through the CLASS - affects all instances
HumanBeing.species = "homo sapiens"
print(alice.species)  # Output: homo sapiens
print(emma.species)   # Output: homo sapiens

# Changing through INSTANCE - creates new instance attribute (wrong way!)
alice.species = "human"
print(alice.species)       # Output: human (instance attribute)
print(emma.species)        # Output: homo sapiens (still class attribute)
print(HumanBeing.species)  # Output: homo sapiens (class attribute unchanged)

# Instance attributes are always unique
print(alice.name)  # Output: Alice
print(emma.name)   # Output: Emma
```
[Back to Table of Contents](#table-of-contents)

### 44. What is the difference between `super()` and directly calling a parent class method? When would you use `super()`?

When we have multiple inheritance, the difference becomes important. If Child inherits from both Mother and Father, and both have a `greet()` method, `super().greet()` will follow the Method Resolution Order and call the method from the first parent in the inheritance chain, which would be Mother if we wrote `class Child(Mother, Father)`.

However, `super()` is smarter than just calling the first parent - it follows the complete MRO chain, which handles complex inheritance hierarchies correctly. If we want to explicitly call Father's method, we would write `Father.greet(self)`.

The advantage of using `super()` is that it respects the MRO and makes our code more maintainable. If we later change the inheritance order or add more parents, `super()` will automatically follow the correct chain. Using the parent class name directly is more explicit and useful when we specifically need a particular parent's implementation, regardless of the MRO.

I would use `super()` when I want to respect the inheritance hierarchy and MRO, and use the explicit parent class name when I need a specific parent's method regardless of the inheritance order.

```python
class Mother:
    def greet(self):
        return "Hello from Mother"

class Father:
    def greet(self):
        return "Hello from Father"

class Child(Mother, Father):  # Mother is first in MRO
    def greet_with_super(self):
        return super().greet()  # Follows MRO - calls Mother.greet()

    def greet_with_explicit(self):
        return Father.greet(self)  # Explicitly calls Father.greet()

# Usage
child = Child()

print(child.greet_with_super())      # Output: Hello from Mother
print(child.greet_with_explicit())   # Output: Hello from Father

# Check the Method Resolution Order
print(Child.__mro__)
# Output: (<class '__main__.Child'>, <class '__main__.Mother'>,
#          <class '__main__.Father'>, <class 'object'>)
```
[Back to Table of Contents](#table-of-contents)

### 45. What is a mixin in Python? When and why would you use one?

A mixin is a class that provides specific functionality but is not meant to be instantiated on its own. We use mixins to create classes that follow the Single Responsibility Principle by breaking down functionality into small, reusable pieces.

Python's multiple inheritance makes mixins very powerful. We can freely split different functionalities into different mixin classes and achieve composition and reusability. For example, if we need printing, scanning, and faxing functionality, we can create separate PrinterMixin and ScannerMixin classes. Then we can mix and match them: a Printer class inherits only PrinterMixin, a Scanner class inherits only ScannerMixin, and a Fax class inherits from both PrinterMixin and ScannerMixin.

This way we follow both Single Responsibility and DRY principles - each mixin handles one responsibility, and we can reuse them in different combinations without duplicating code. The key advantage is flexibility - we can create many different device types by combining mixins in different ways, without writing the same functionality multiple times.

Mixins are commonly used in frameworks like Django, where you see things like LoginRequiredMixin or PermissionRequiredMixin that add specific behavior to views.

```python
# Mixins - reusable functionality classes
class PrinterMixin:
    def print_document(self, doc):
        return f"Printing: {doc}"

class ScannerMixin:
    def scan_document(self, doc):
        return f"Scanning: {doc}"

# Different devices combining mixins
class Printer(PrinterMixin):
    pass

class Scanner(ScannerMixin):
    pass

class FaxMachine(PrinterMixin, ScannerMixin):
    pass

# Usage
printer = Printer()
print(printer.print_document("Report.pdf"))  # Output: Printing: Report.pdf

scanner = Scanner()
print(scanner.scan_document("Photo.jpg"))  # Output: Scanning: Photo.jpg

fax = FaxMachine()
print(fax.print_document("Contract.pdf"))  # Output: Printing: Contract.pdf
print(fax.scan_document("Invoice.jpg"))    # Output: Scanning: Invoice.jpg
```
[Back to Table of Contents](#table-of-contents)

### 46. What is the difference between `pass`, `continue`, and `break` statements in Python?

`pass` does nothing - it's a null statement used as a placeholder. We use it when Python syntax requires a statement but we don't want to execute any code, like in an empty class that only inherits from other classes, or in an empty function we'll implement later.

`continue` skips the rest of the current iteration and immediately jumps to the next iteration of the loop. Any code after `continue` in that iteration won't execute.

`break` completely stops the loop and exits it entirely. It's like a `return` statement for loops - once `break` is hit, the loop terminates and code continues after the loop.

The key difference between `pass` and `continue` is that `pass` does nothing and lets execution continue to the next line, while `continue` actively skips to the next iteration, ignoring any remaining code in the current iteration.

```python
# continue - skip to next iteration
print("continue example:")
for i in range(5):
    if i == 2:
        continue  # Skip when i is 2
    print(i)
# Output: 0, 1, 3, 4 (2 is skipped)

# break - exit loop completely
print("\nbreak example:")
for i in range(5):
    if i == 2:
        break  # Stop loop when i is 2
    print(i)
# Output: 0, 1 (loop stops at 2)

# pass vs continue difference
print("\npass vs continue:")
for i in range(3):
    if i == 1:
        pass  # Does nothing, continues to print
    print(i)
# Output: 0, 1, 2
```
[Back to Table of Contents](#table-of-contents)

### 47. What is the Singleton design pattern and how would you implement it in Python?

The Singleton design pattern ensures that only one instance of a class exists in memory. A perfect example is `None` in Python - there's only one `None` object that every variable points to.

We implement Singleton in Python by overriding the `__new__` method. The `__new__` method receives `cls` as its first argument. We define a class attribute like `_instance = None`, then in `__new__` we check if that class attribute is still None. If it is, we create a new instance by calling `super().__new__(cls)` and store it in `_instance`. If `_instance` already exists, we return the existing instance.

An important detail is that `__init__` will still be called every time we try to create an instance, even when returning the existing object. Only the object creation in `__new__` happens once, but `__init__` runs each time. If this is a problem, we can add a flag to prevent re-initialization.

This pattern saves memory by ensuring only one instance exists and improves performance by reusing the same object. It's useful for things like database connections, configuration managers, or logging objects where you want one shared instance across your application.

```python
class DatabaseConnection:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            print("Creating new instance")
            cls._instance = super().__new__(cls)
        else:
            print("Returning existing instance")
        return cls._instance

    def __init__(self):
        print("__init__ called")

# Usage
db1 = DatabaseConnection()
# Output: Creating new instance
#         __init__ called

db2 = DatabaseConnection()
# Output: Returning existing instance
#         __init__ called

print(db1 is db2)  # Output: True (same object in memory)
print(id(db1) == id(db2))  # Output: True (same memory address)

# Example with None - Python's built-in singleton
a = None
b = None
print(a is b)  # Output: True (same None object)
```
[Back to Table of Contents](#table-of-contents)

### 48. Can you explain method resolution order (MRO)? What is C3 linearization and why do we need it in Python?

MRO, or Method Resolution Order, is particularly important for multiple inheritance, which isn't even possible in many other languages. MRO determines the order in which Python searches for attributes and methods in the class hierarchy. It first checks the current class, then follows a specific order through the parent classes using the C3 linearization algorithm. Generally, it searches parents from left to right as they're listed in the class definition, but the algorithm ensures a consistent order even in complex inheritance hierarchies like diamond inheritance. Finally, it checks the Object class, which is the base of all classes in Python. If the attribute isn't found anywhere in the hierarchy, Python raises an AttributeError.

C3 linearization is the algorithm that Python uses to determine the Method Resolution Order when we use multiple inheritance. It solves a problem called the diamond problem.

The diamond problem occurs when a class inherits from multiple parent classes that share a common ancestor. For example, imagine class D inherits from both B and C, and both B and C inherit from class A. Without a clear rule, Python would not know which path to follow when looking for a method. It might check A multiple times or in the wrong order, causing confusion.

C3 linearization ensures that each class appears only once in the resolution order and that we respect the inheritance hierarchy. It follows a specific order: we check the child class first, then we check the parent classes in the order they were listed, and we continue down the hierarchy. The final result is a list called the MRO that tells Python exactly which order to check classes when looking for a method or attribute.

You can see the MRO of any class by using the `__mro__` attribute or the `mro()` method, which is helpful for debugging complex inheritance structures.

```python
# Diamond problem - multiple inheritance with common ancestor
class A:
    def method(self):
        return "A's method"

class B(A):
    def method(self):
        return "B's method"

class C(A):
    def method(self):
        return "C's method"

class D(B, C):  # Diamond inheritance
    pass

# C3 linearization determines the MRO
d = D()
print(d.method())  # Output: B's method (B comes before C in MRO)

# Check the Method Resolution Order
print([cls.__name__ for cls in D.mro()])
# Output: ['D', 'B', 'C', 'A', 'object']

# MRO ensures each class appears only once
print(D.__mro__)
# Output: (<class '__main__.D'>, <class '__main__.B'>,
#          <class '__main__.C'>, <class '__main__.A'>, <class 'object'>)

# Without method override - follows same MRO
class X:
    def greet(self):
        return "Hello from X"

class Y(X):
    pass

class Z(X):
    pass

class W(Y, Z):
    pass

w = W()
print(w.greet())  # Output: Hello from X
print([cls.__name__ for cls in W.mro()])
# Output: ['W', 'Y', 'Z', 'X', 'object']
```
[Back to Table of Contents](#table-of-contents)

### 49. How do we create an iterator in Python?

We create an iterator in Python by defining a class that implements the iterator protocol. This means the class should define two special methods: `__iter__` and `__next__`.

The `__iter__` method returns the iterator object itself, usually `self`. This method is called when iteration starts. The `__next__` method returns the next value from the sequence each time it is called. When there are no more values to return, the `__next__` method raises a `StopIteration` exception, which tells Python that the iteration is complete.

We typically keep track of the current state in the `__init__` method. This is where we initialize variables that track our position in the iteration, such as a counter or an index.

```python
# Creating a simple iterator
class CountUp:
    def __init__(self, max):
        self.max = max
        self.current = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self.current < self.max:
            self.current += 1
            return self.current
        else:
            raise StopIteration

# Using the iterator
counter = CountUp(3)
for number in counter:
    print(number)
# Output:
# 1
# 2
# 3

# We can also call __next__ manually
counter2 = CountUp(3)
print(next(counter2))  # Output: 1
print(next(counter2))  # Output: 2
print(next(counter2))  # Output: 3
try:
    print(next(counter2))  # Raises StopIteration
except StopIteration:
    print("Iteration complete")

# Another example - iterating through a list
class ListIterator:
    def __init__(self, data):
        self.data = data
        self.index = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self.index < len(self.data):
            value = self.data[self.index]
            self.index += 1
            return value
        else:
            raise StopIteration

my_list = ListIterator([10, 20, 30])
for item in my_list:
    print(item)
# Output:
# 10
# 20
# 30
```
[Back to Table of Contents](#table-of-contents)

### 50. What is the 3A pattern in testing?

The 3A pattern, or Triple A pattern, refers to the three stages that we should have in every test: Arrange, Act, and Assert.

In the Arrange step, we set up all the variables and dependencies that we need for the test. This includes creating objects, initializing data, and preparing the test environment.

In the Act step, we execute the functionality that we want to test. This is where we call the function or method that we are testing.

In the Assert step, we check if the actual result matches the expected result. We verify that the functionality behaves the way we expect it to.

Following this pattern makes our tests clear, organized, and easy to understand. Anyone reading the test can quickly see what is being tested, how it is being tested, and what the expected outcome is.

```python
def add(a, b):
    return a + b

def test_add():
    # Arrange
    num1 = 5
    num2 = 3
    expected = 8

    # Act
    result = add(num1, num2)

    # Assert
    assert result == expected
    print("Test passed")

test_add()
```
[Back to Table of Contents](#table-of-contents)

### 51. What is the difference between unit tests and integration tests?

Unit tests test an isolated part of code, such as a single function or method in isolation. A good unit test focuses on a pure function that receives all its parameters from outside and does not make additional calls to other components. If we need to test a function that depends on other dependencies, we can mock those dependencies so we only test the function itself.

Integration tests are needed when logic involves multiple steps and different components working together. For example, a user registration process might require an HTTP request client to receive user data, database operations to store the user, and an email service to send a confirmation email. In integration tests, we test the actual interactions between these components rather than mocking them. We use a test database, a test email service, or real instances of these components to verify that all the steps work correctly together.

Unit tests are fast and cheap to run because they test only small pieces of code in isolation. Integration tests are generally slower and more expensive to run because they involve multiple components and real or test versions of external services. For this reason, we typically write many unit tests and fewer integration tests. Unit tests catch bugs early and quickly, while integration tests verify that everything works together as expected.

```python
# Unit test example - testing a function in isolation
def add(a, b):
    return a + b

# Unit test with Arrange, Act, Assert
def test_add():
    # Arrange
    num1 = 2
    num2 = 3

    # Act
    result = add(num1, num2)

    # Assert
    assert result == 5
    print("Unit test passed")

test_add()

print("="*40 + "\n")

# Integration test example - testing multiple components together
class User:
    def __init__(self, name, email):
        self.name = name
        self.email = email

class Database:
    def __init__(self):
        self.users = []

    def save_user(self, user):
        self.users.append(user)
        return True

class EmailService:
    def send_confirmation(self, email):
        print(f"Sending confirmation email to {email}")
        return True

class UserRegistration:
    def __init__(self, database, email_service):
        self.database = database
        self.email_service = email_service

    def register(self, name, email):
        user = User(name, email)
        self.database.save_user(user)
        self.email_service.send_confirmation(email)
        return user

# Integration test with Arrange, Act, Assert
def test_user_registration():
    # Arrange
    database = Database()
    email_service = EmailService()
    registration = UserRegistration(database, email_service)
    name = "Alice"
    email = "alice@example.com"

    # Act
    user = registration.register(name, email)

    # Assert
    assert user.name == "Alice"
    assert user.email == "alice@example.com"
    assert len(database.users) == 1
    print("Integration test passed")

test_user_registration()
```
[Back to Table of Contents](#table-of-contents)

### 52. What is a thread and why would we use threads in our programs?

A thread is a way for a program to do multiple things at the same time. Think of a thread like a worker. If we have one worker, they do one task, then another task, then another. If we have multiple workers, they can work on different tasks simultaneously.

In programming, a thread is a separate flow of execution within our program. When we create a thread, we are telling our program to run a function or a piece of code alongside the main program. The main program does not have to wait for that thread to finish before it continues doing other things.

Threads are useful when we have tasks that involve waiting, like downloading files, reading from a database, or making web requests. Instead of waiting for one task to finish before starting the next one, we can create multiple threads and let them work at the same time. This makes our program faster and more responsive.

For example, imagine we have a program that needs to download three files from the internet. One file takes one second to download, another takes three seconds, and the third takes two seconds. Without threads, the program downloads one file, waits for it to finish, then downloads the second file, then the third. This takes six seconds total. With threads, we can tell our program to download all three files at the same time. While the program is waiting for the first file, it can start downloading the second and third files. All three downloads happen concurrently, and the total time is only three seconds, which is the longest individual download time.

```python
# Without threads - tasks run one after another
print("Without threads:")
print("Download file1 - waiting 1 second")
print("Download complete")
print("Download file2 - waiting 3 seconds")
print("Download complete")
print("Download file3 - waiting 2 seconds")
print("Download complete")
print("Total time: 6 seconds")

# With threads - tasks run at the same time
print("With threads:")
print("Download file1 - waiting 1 second")
print("Download file2 - waiting 3 seconds")
print("Download file3 - waiting 2 seconds")
print("All three downloads happen at the same time")
print("Total time: 3 seconds (the longest download)")
```
[Back to Table of Contents](#table-of-contents)

### 53. What is asynchronous programming?

Asynchronous programming is a way of writing code that allows our program to start a task and then move on to do something else without waiting for that task to finish. Instead of blocking and waiting for a task to complete, the program continues executing other code, and when the task finishes, the program handles the result.

The main difference between synchronous and asynchronous programming is how we wait. In synchronous programming, when we start a task, our program stops and waits for it to complete before moving to the next line of code. In asynchronous programming, when we start a task, our program does not wait. It continues to the next task, and when the first task finishes, our program comes back and handles the result.

The main benefit of asynchronous programming is that it allows us to handle many tasks concurrently without creating a separate thread for each one. This makes our programs more efficient and responsive.

```python
# Synchronous - program waits for each task to finish
print("Synchronous approach:")
print("Starting task 1 - waiting 2 seconds")
print("Task 1 complete")
print("Starting task 2 - waiting 3 seconds")
print("Task 2 complete")
print("Starting task 3 - waiting 1 second")
print("Task 3 complete")
print("Total time: 6 seconds")

# Asynchronous - program starts tasks and continues without waiting
print("Asynchronous approach:")
print("Starting task 1 - waiting 2 seconds")
print("Starting task 2 - waiting 3 seconds")
print("Starting task 3 - waiting 1 second")
print("All tasks started, program continues")
print("Task 3 complete")
print("Task 1 complete")
print("Task 2 complete")
print("Total time: 3 seconds (the longest task)")
```
[Back to Table of Contents](#table-of-contents)

### 54. What is I/O and why is it important to understand when writing asynchronous code?

I/O stands for Input/Output. I/O operations are tasks where our program needs to communicate with something outside of itself and wait for a response. During this waiting time, our program is not doing any calculations. It is just sitting idle, waiting for data to come back.

Examples of I/O operations are reading files from disk, making web requests to servers, querying databases, or waiting for user input. All of these involve our program asking for something and then waiting for an answer.

The reason I/O is important to understand is that it is where asynchronous programming with `async` and `await` becomes very useful. When our program is waiting for an I/O operation to complete, the CPU is not doing anything. This is the perfect time for the program to switch to another task and make progress on that one. While task one is waiting for a file to load or a web request to return, task two can run. By the time task one gets its response, task two might already be done or waiting for something else.

If we use regular synchronous code, the program has to wait for one I/O operation to finish before starting the next one, which is wasteful. With asynchronous code using `async` and `await`, multiple I/O operations can happen concurrently, and our program becomes much faster and more responsive.

```python
# Synchronous - program waits for each I/O operation
print("Synchronous approach:")
print("Reading file1 - waiting for disk")
print("File1 loaded")
print("Reading file2 - waiting for disk")
print("File2 loaded")
print("Reading file3 - waiting for disk")
print("File3 loaded")
print("Total time: 6 seconds (1 + 2 + 3)")

print("\n" + "="*40 + "\n")

# Asynchronous - program does not wait for each I/O operation
print("Asynchronous approach:")
print("Reading file1 - waiting for disk")
print("Reading file2 - waiting for disk")
print("Reading file3 - waiting for disk")
print("File1 loaded (1 second)")
print("File3 loaded (2 seconds)")
print("File2 loaded (3 seconds)")
print("Total time: 3 seconds (the longest I/O operation)")
```
[Back to Table of Contents](#table-of-contents)

### 55. What is `async` and `await` in Python and how do we use them?

`async` and `await` are keywords in Python that let us write asynchronous code. The `async` keyword is used to define an asynchronous function, and the `await` keyword is used to pause that function while waiting for a task to complete.

When we define a function with the `async` keyword, it becomes a coroutine. A coroutine is a special type of function that can pause its execution and resume later. Inside an asynchronous function, when we use the `await` keyword before a task, the function pauses and gives control back to the program so it can work on other tasks. When that task finishes, the function resumes and continues executing.

We can only use `await` inside an `async` function. When we want to run multiple asynchronous functions concurrently, we use `asyncio.gather()`. This function starts all the tasks concurrently. While one task is paused waiting for I/O, the program switches to another task. The tasks take turns executing, but because they spend most of their time waiting, they all make progress together. The total time is determined by the longest task because the program has to wait for all tasks to finish.

```python
import asyncio

# Define an asynchronous function with async keyword
async def fetch_data(data_id, wait_time):
    print(f"Starting to fetch data {data_id}")
    await asyncio.sleep(wait_time)  # Pause here, let other tasks run
    print(f"Finished fetching data {data_id}")
    return f"Data {data_id}"

# Run multiple async functions concurrently with asyncio.gather()
async def main():
    results = await asyncio.gather(
        fetch_data(1, 2),
        fetch_data(2, 3),
        fetch_data(3, 1)
    )
    print(results)

asyncio.run(main())
# Output: Takes ~3 seconds (all tasks run concurrently)
# Starting to fetch data 1
# Starting to fetch data 2
# Starting to fetch data 3
# Finished fetching data 3
# Finished fetching data 1
# Finished fetching data 2
# ['Data 1', 'Data 2', 'Data 3']
```
[Back to Table of Contents](#table-of-contents)

### 56. What are Celery and Redis, and how do they help us in Python?

Celery and Redis are tools that work together to help us run tasks asynchronously in the background. Celery is a task queue library that lets us send tasks to be processed by separate worker processes. Redis is a message broker that stores these tasks in a queue and manages communication between the main program and the workers.

When we use Celery and Redis, the main program does not execute heavy tasks directly. Instead, we send the task to a queue using Celery, and Redis stores it. Worker processes that are running separately pick up tasks from the queue and execute them in the background. This means the main program can continue immediately without waiting for the task to finish.

The main advantage of using Celery and Redis is that we can have multiple workers processing tasks simultaneously, even on different machines. We can also retry tasks if they fail, schedule tasks to run at specific times, and monitor task progress. This is much more powerful than `async` and `await` because we can distribute work across multiple machines and handle very heavy computations without blocking the main program.

```python
# Celery with Redis - distribute tasks to background workers
from celery import Celery

# Configure Celery to use Redis as the message broker
app = Celery('tasks', broker='redis://localhost:6379')

# Define a task with the @app.task decorator
@app.task
def process_file(file_name):
    print(f"Processing {file_name}")
    # Simulate heavy work
    total = sum(range(10000000))
    return f"Processed {file_name} with result {total}"

# Send tasks to the queue - main program does not wait
task1 = process_file.delay("file1.txt")
task2 = process_file.delay("file2.txt")
task3 = process_file.delay("file3.txt")

print("Tasks sent to queue, main program continues immediately")

# We can check the result later when it is ready
result = task1.get()  # This waits for the result
print(result)

# In a real application, we would not use get() in the main program
# We would handle results through callbacks or by checking status
```
[Back to Table of Contents](#table-of-contents)

### 57. What is the Global Interpreter Lock (GIL) in Python and how does it affect our programs?

The Global Interpreter Lock, or GIL, is a limitation in Python that means only one piece of code can run at a time within a single Python process, even if we try to use threads.

The GIL exists because Python needs to protect its internal memory. Without it, Python could crash or give us wrong results when multiple threads try to change data at the same time. However, this protection comes with a cost. It means that even if we have multiple threads, they cannot truly run in parallel. They have to take turns executing, which limits performance.

The GIL becomes a problem when we write code that does a lot of calculations or CPU-intensive work. If we try to use threads to speed up heavy calculations, the threads still take turns because of the GIL, so we do not actually get faster results.

However, the GIL is not a problem for tasks that involve waiting, like reading files or making web requests. When we use asynchronous programming with `async` and `await`, Python releases the GIL while waiting for I/O operations. This allows other tasks to run concurrently. Similarly, with Celery and Redis, each worker process has its own GIL, so they can run truly in parallel without being limited by a single GIL.

This is why in modern Python applications, developers use `async` and `await` for I/O-bound operations and Celery for background tasks that need to run independently or handle heavy computations, rather than trying to use the threading module for these purposes.

```python
# Example 1: GIL limitation with CPU-bound tasks
print("CPU-bound task - GIL causes slowdown:")
print("If we try to speed up heavy calculations with threads,")
print("threads still take turns because of GIL")
print("Total time: similar to running without threads")

print("\n" + "="*40 + "\n")

# Example 2: async/await handles I/O efficiently despite GIL
import asyncio

async def fetch_data(data_id, wait_time):
    print(f"Fetching data {data_id}")
    await asyncio.sleep(wait_time)  # GIL is released during wait
    print(f"Finished fetching data {data_id}")

async def main():
    print("I/O-bound tasks with async/await - GIL is not a problem:")
    await asyncio.gather(
        fetch_data(1, 2),
        fetch_data(2, 3),
        fetch_data(3, 1)
    )

asyncio.run(main())
print("Total time: ~3 seconds (tasks run concurrently)")

print("\n" + "="*40 + "\n")

# Example 3: Celery with separate processes - each has its own GIL
print("Celery with Redis - each worker has its own GIL:")
print("Workers can run truly in parallel on different cores")
print("Perfect for CPU-intensive tasks distributed across multiple processes")
```
[Back to Table of Contents](#table-of-contents)

### 58. What is the difference between concurrent and parallel execution?

Concurrency and parallelism are different concepts. Using a cook analogy: if I am one cook with multiple hot plates in different corners of the kitchen, I can work on them by switching between them. I work on one plate, then move to another plate, then back to the first one. It appears like I am working on all of them at the same time, but I am actually working on one at a time. This is concurrency.

True parallelism requires multiple cooks working simultaneously on different plates at the exact same time. Each cook works independently on their own plate without switching.

In programming, concurrency appears simultaneous but executes sequentially on one processor by rapidly switching between tasks. Parallelism is true simultaneous execution on multiple processors or CPU cores.

Python's Global Interpreter Lock, or GIL, prevents multiple threads from executing bytecode simultaneously. It ensures only one thread runs at a time within a single Python process. This means we cannot achieve true parallelism using threads in Python.

To achieve true parallelism, we use tools like Celery with Redis. Celery provides worker processes that wait for Redis to assign them tasks from a queue. Each worker runs in its own process with its own GIL, so multiple workers can process different tasks in parallel on different CPU cores. For example, if one task takes three seconds and another takes one second, synchronous execution takes four seconds total. But with Celery workers, they execute in parallel, so the total time is only three seconds, which is the longest task.

```python
# Concurrency example - one processor, rapid task switching
print("Concurrency - one cook switching between plates:")
print("Work on plate 1 (2 seconds)")
print("Switch to plate 2 (3 seconds)")
print("Switch to plate 3 (1 second)")
print("Total time: 6 seconds (sequential)")

print("\n" + "="*40 + "\n")

# Concurrency with async/await - tasks take turns, total time is longest task
import asyncio

async def cook_plate(plate_id, time_needed):
    print(f"Start cooking plate {plate_id}")
    await asyncio.sleep(time_needed)
    print(f"Plate {plate_id} done")

async def main():
    print("Concurrency with async/await - one cook switching between plates:")
    await asyncio.gather(
        cook_plate(1, 2),
        cook_plate(2, 3),
        cook_plate(3, 1)
    )

asyncio.run(main())
print("Total time: ~3 seconds (longest plate)")

print("\n" + "="*40 + "\n")

# Parallelism example - multiple workers, truly simultaneous
print("Parallelism with Celery - multiple cooks working simultaneously:")
print("Cook 1 working on plate 1 (2 seconds)")
print("Cook 2 working on plate 2 (3 seconds)")
print("Cook 3 working on plate 3 (1 second)")
print("All cooks work at the same time on different plates")
print("Total time: ~3 seconds (longest plate, executed in parallel)")
```
[Back to Table of Contents](#table-of-contents)

## POstgreSQL

### 59. What are database indexes? Explain clustered vs non-clustered indexes, their advantages and disadvantages, and how PostgreSQL handles them.

Indexes are database objects that improve the speed of data retrieval operations on a table. They work like a book's index, helping the database find data quickly without scanning the entire table. We use indexes on columns that are frequently used in WHERE clauses, JOIN conditions, or ORDER BY statements.

There are two main types of indexes: clustered and non-clustered. A clustered index determines the physical order of data in the table. When we create a clustered index on a column like id, the entire table gets physically reordered and sorted by that column. This makes range queries very fast because the database knows exactly where to find records. For example, records with ids between 100 and 200 are stored sequentially on disk. We can only have one clustered index per table because data can only be physically sorted one way. Without a clustered index, records are stored in insertion order and might be spread randomly across the table.

A non-clustered index does not physically reorder the table. Instead, it creates a separate sorted structure that contains pointers to the actual table rows. When we create an index on a column, the index itself is sorted, but the table remains in its original order. When we query for a specific value, the database looks it up in the sorted index, finds the pointer, and then jumps to the actual row location. We can have multiple non-clustered indexes on different columns, which is more flexible than clustered indexes.

PostgreSQL uses only non-clustered indexes by default. Tables in PostgreSQL are stored as heaps, meaning rows are stored in insertion order, not sorted by any column. PostgreSQL automatically creates indexes on PRIMARY KEY and UNIQUE constraint columns, but we can manually create additional indexes on any columns we frequently query using the CREATE INDEX command. PostgreSQL does have a CLUSTER command that can temporarily reorder a table based on an index, but this is a one-time operation and is not maintained automatically like true clustered indexes in other databases.

Indexes have significant advantages. They make querying data much faster, especially for large tables. They help enforce uniqueness through UNIQUE indexes. They improve performance for JOIN operations and ORDER BY clauses. However, indexes have important disadvantages that we must consider. They slow down INSERT, UPDATE, and DELETE operations because the index must be updated every time the data changes. Indexes also require additional disk space to store. Additionally, maintaining too many indexes can degrade overall performance. That is why we should not create indexes on every column. We should only create indexes on columns we query frequently and where the performance benefit outweighs the maintenance cost.

```python
class Product:
    def __init__(self, product_id, name, category, price):
        self.product_id = product_id
        self.name = name
        self.category = category
        self.price = price

    def __repr__(self):
        return f"Product(id={self.product_id}, '{self.name}', category='{self.category}', price=${self.price})"

print("=== What are Indexes? ===")
print("Indexes are database objects that speed up data retrieval")
print("They work like a book's index - helping find data quickly")
print("Instead of scanning entire table, database uses index to locate data")

print("\n=== Clustered Indexes ===")
print("Determines physical order of data in the table")
print("When created on 'id', entire table is physically sorted by id")
print("Example: Finding products with id 100-200 is fast (sequential disk access)")
print("Constraint: Only ONE clustered index per table")
print("Data can only be physically sorted one way")

print("\n=== Non-Clustered Indexes ===")
print("Does NOT physically reorder the table")
print("Creates separate sorted structure with pointers to actual rows")
print("Table remains in original (insertion) order")
print("When querying: lookup in index → find pointer → jump to actual row")
print("Benefit: Can have MULTIPLE non-clustered indexes on different columns")

print("\n=== PostgreSQL Index Approach ===")
print("PostgreSQL uses ONLY non-clustered indexes")
print("Tables stored as 'heaps' - rows in insertion order, not sorted")
print("Automatically indexes PRIMARY KEY and UNIQUE columns")
print("We manually create indexes with: CREATE INDEX idx_name ON table(column)")
print("PostgreSQL has CLUSTER command but it's one-time operation, not automatic")

print("\n=== Index Advantages ===")
products = [
    Product(1, 'Laptop', 'Electronics', 1500),
    Product(2, 'Mouse', 'Electronics', 25),
    Product(3, 'Desk', 'Furniture', 300),
    Product(4, 'Chair', 'Furniture', 200),
]
print("✓ Much faster data retrieval for large tables")
print("✓ Speeds up WHERE clause queries")
print("✓ Improves JOIN operation performance")
print("✓ Accelerates ORDER BY and GROUP BY")
print("✓ Enforces uniqueness through UNIQUE indexes")
print(f"\nExample: Without index on category:")
print("  Query: SELECT * FROM products WHERE category='Electronics'")
print("  Action: Scans ALL 4 products")
print("  Result: Found 2 products (Laptop, Mouse)")

print("\n=== Index Disadvantages ===")
print("✗ Slows down INSERT operations - index must be updated")
print("✗ Slows down UPDATE operations - index must be updated")
print("✗ Slows down DELETE operations - index must be updated")
print("✗ Requires additional disk space to store index")
print("✗ Too many indexes degrades overall performance")
print("✗ Maintenance overhead increases with more indexes")

print("\n=== When to Create Indexes ===")
print("✓ Columns frequently used in WHERE clauses")
print("✓ Columns used in JOIN ON conditions")
print("✓ Columns used in ORDER BY statements")
print("✓ Columns used in GROUP BY statements")
print("✓ Columns that benefit from range queries")

print("\n=== When NOT to Create Indexes ===")
print("✗ Columns rarely queried")
print("✗ Columns with mostly NULL values")
print("✗ Small tables (scanning is already fast)")
print("✗ Columns with low cardinality (few unique values)")
print("✗ Every column - creates maintenance overhead")

print("\n=== Best Practice ===")
print("Do NOT index every column")
print("Only index columns we query frequently")
print("Balance: Query performance benefit vs. maintenance cost")
print("Monitor query performance and adjust indexes accordingly")
print("Regularly review and remove unused indexes")

print("\n=== PostgreSQL CREATE INDEX Examples ===")
print("-- Single column index")
print("CREATE INDEX idx_product_category ON products(category);")
print("\n-- Composite index (multiple columns)")
print("CREATE INDEX idx_product_category_price ON products(category, price);")
print("\n-- Unique index")
print("CREATE UNIQUE INDEX idx_product_sku ON products(sku);")
print("\n-- Check existing indexes")
print("\\d products  -- Shows table structure and indexes")
```
[Back to Table of Contents](#table-of-contents)

### 60. What is the DISTINCT keyword in SQL? How does it work with multiple columns?

The DISTINCT keyword filters the result set to return only unique values, eliminating duplicates. It is useful when we want to see what distinct values exist in our data without seeing repeated entries.

When we use DISTINCT with a single column, like SELECT DISTINCT name FROM employees, the database returns each unique name only once, even if that name appears multiple times in the table. For example, if we have three employees named John, two named Sarah, and one named Michael, DISTINCT returns only three rows: John, Sarah, and Michael.

When we use DISTINCT with multiple columns, the behavior is important to understand. DISTINCT looks at the unique combination of all specified columns together, not each column individually. For example, SELECT DISTINCT first_name, last_name FROM employees returns unique combinations of first and last names. If two people have the same first name but different last names, both records are included because the combinations are different. The database considers the entire row across all selected columns when determining uniqueness.

It is important to note that DISTINCT applies to the entire row of selected columns as a complete set. PostgreSQL has a special DISTINCT ON syntax that allows us to specify which columns to check for uniqueness while keeping all other columns from the first matching row, but standard DISTINCT checks all selected columns together for uniqueness.

```python
# Simulating a database table
employees = [
    {'id': 1, 'first_name': 'John', 'last_name': 'Smith', 'department': 'Sales'},
    {'id': 2, 'first_name': 'Sarah', 'last_name': 'Jones', 'department': 'IT'},
    {'id': 3, 'first_name': 'John', 'last_name': 'Smith', 'department': 'Sales'},
    {'id': 4, 'first_name': 'John', 'last_name': 'Brown', 'department': 'HR'},
    {'id': 5, 'first_name': 'Sarah', 'last_name': 'Jones', 'department': 'IT'},
    {'id': 6, 'first_name': 'Michael', 'last_name': 'Davis', 'department': 'Finance'},
]

print("=== All Employees (no DISTINCT) ===")
for emp in employees:
    print(f"{emp['first_name']} {emp['last_name']} - {emp['department']}")

print("\n=== DISTINCT first_name (single column) ===")
first_names = set(emp['first_name'] for emp in employees)
for name in sorted(first_names):
    print(name)
print("Result: Each unique first name appears only once")

print("\n=== DISTINCT first_name, last_name (multiple columns) ===")
unique_names = set((emp['first_name'], emp['last_name']) for emp in employees)
for first, last in sorted(unique_names):
    print(f"{first} {last}")
print("Result: Unique combinations of first and last names")
print("John Smith appears once (even though 2 records exist)")
print("John Brown appears once (different last name)")

print("\n=== DISTINCT department (single column) ===")
departments = set(emp['department'] for emp in employees)
for dept in sorted(departments):
    print(dept)
print("Result: Each unique department appears once")

print("\n=== DISTINCT first_name, last_name, department (all columns) ===")
unique_combos = set((emp['first_name'], emp['last_name'], emp['department']) for emp in employees)
for first, last, dept in sorted(unique_combos):
    print(f"{first} {last} - {dept}")
print("Result: Unique combinations of all three columns")

print("\n=== How DISTINCT evaluates multiple columns ===")
print("DISTINCT looks at the ENTIRE ROW across all selected columns")
print("(John, Smith) from Sales and (John, Smith) from Sales are IDENTICAL")
print("→ Only 1 row returned")
print("\n(John, Smith) and (John, Brown) are DIFFERENT combinations")
print("→ Both rows returned (different last names)")

print("\n=== DISTINCT vs GROUP BY (similar but different) ===")
print("DISTINCT: Returns unique combinations, no aggregation")
print("GROUP BY: Groups rows and can aggregate data (COUNT, SUM, etc.)")
print("\nSELECT DISTINCT department FROM employees")
print("→ Returns: Sales, IT, HR, Finance")
print("\nSELECT department, COUNT(*) FROM employees GROUP BY department")
print("→ Returns: Sales (2), IT (2), HR (1), Finance (1)")

print("\n=== PostgreSQL DISTINCT ON (advanced) ===")
print("SELECT DISTINCT ON (first_name) first_name, last_name FROM employees")
print("→ Keeps only first matching row for each unique first_name")
print("→ Result: John (with first Smith encountered), Sarah (with Jones), Michael (with Davis)")
print("Note: Standard DISTINCT does not have this behavior")
```
[Back to Table of Contents](#table-of-contents)

### 61. What is the difference between the IN and EXISTS operators in SQL? When would you use one over the other?

The IN and EXISTS operators check conditions in different ways.

IN checks if a value matches any element in a collection. It can work with both a predefined list or a subquery result. For example, if we want to filter departments by specific IDs, we would use WHERE department_id IN (1, 2, 3). We can also use IN with a subquery like WHERE department_id IN (SELECT id FROM departments WHERE location = 'New York').

EXISTS checks if a subquery returns at least one row. It can only work with a subquery, we cannot use EXISTS with a predefined list. For example, WHERE EXISTS (SELECT 1 FROM orders WHERE orders.employee_id = employees.id) checks if there are any orders for that employee. If the subquery finds at least one order, EXISTS returns true. If it finds zero orders, it returns false. EXISTS stops searching immediately after finding the first matching row.

The key differences are: IN compares a value against a list and can use either a hardcoded list or subquery, while EXISTS checks whether a subquery returns any rows and must always use a subquery. EXISTS is typically more efficient for large datasets with correlated subqueries because it stops as soon as it finds one match, whereas IN evaluates the entire result set. I would use IN for simple value matching against small lists, and EXISTS when checking if related records exist in another table.

```sql
-- Sample tables
CREATE TABLE employees (id INT, name VARCHAR(50), department_id INT);
CREATE TABLE departments (id INT, location VARCHAR(50));
CREATE TABLE orders (id INT, employee_id INT, amount DECIMAL);

INSERT INTO employees VALUES (1, 'John', 1), (2, 'Jane', 2), (3, 'Bob', 3);
INSERT INTO departments VALUES (1, 'New York'), (2, 'Boston'), (3, 'Chicago');
INSERT INTO orders VALUES (1, 1, 100), (2, 1, 200), (3, 2, 150);

-- Using IN with hardcoded list
SELECT * FROM employees WHERE department_id IN (1, 2);
-- Output: Returns John and Jane (departments 1 and 2)

-- Using IN with subquery
SELECT * FROM employees
WHERE department_id IN (SELECT id FROM departments WHERE location = 'New York');
-- Output: Returns John (department 1 in New York)

-- Using EXISTS to find employees with orders
SELECT e.name FROM employees e
WHERE EXISTS (SELECT 1 FROM orders o WHERE o.employee_id = e.id);
-- Output: Returns John and Jane (they have orders)

-- EXISTS stops at first match - more efficient for checking existence
SELECT e.name FROM employees e
WHERE EXISTS (SELECT 1 FROM orders o WHERE o.employee_id = e.id AND o.amount > 50);
-- Output: Returns John and Jane (they have orders over 50)
```
[Back to Table of Contents](#table-of-contents)

### 62. What is the OFFSET clause in PostgreSQL? How would you use LIMIT and OFFSET together for pagination?

OFFSET specifies how many rows to skip before starting to return rows. It works together with LIMIT to implement pagination.

LIMIT takes a specified number of rows from the result, while OFFSET skips a specified number of rows before applying LIMIT. For example, OFFSET 10 LIMIT 10 means skip the first 10 rows, then return the next 10 rows.

For pagination with 10 records per page, I would use this pattern:

-   Page 1: SELECT \* FROM products ORDER BY id LIMIT 10 OFFSET 0, which skips 0 and takes 10, giving us records 1 through 10
-   Page 2: SELECT \* FROM products ORDER BY id LIMIT 10 OFFSET 10, which skips 10 and takes 10, giving us records 11 through 20
-   Page 3: SELECT \* FROM products ORDER BY id LIMIT 10 OFFSET 20, which skips 20 and takes 10, giving us records 21 through 30

The formula is: OFFSET equals page number minus 1, multiplied by page size. It is important to always use ORDER BY before LIMIT and OFFSET to ensure consistent results across pages.

```sql
-- Sample products table
CREATE TABLE products (id INT, name VARCHAR(50), price DECIMAL);
INSERT INTO products VALUES
(1, 'Laptop', 999), (2, 'Mouse', 25), (3, 'Keyboard', 75),
(4, 'Monitor', 299), (5, 'Webcam', 89), (6, 'Headset', 59),
(7, 'Speaker', 129), (8, 'Microphone', 99), (9, 'Desk', 399),
(10, 'Chair', 249), (11, 'Lamp', 45), (12, 'Cable', 15);

-- Page 1: First 5 products (OFFSET 0)
SELECT * FROM products ORDER BY id LIMIT 5 OFFSET 0;
-- Output: Products 1-5 (Laptop, Mouse, Keyboard, Monitor, Webcam)

-- Page 2: Next 5 products (OFFSET 5)
SELECT * FROM products ORDER BY id LIMIT 5 OFFSET 5;
-- Output: Products 6-10 (Headset, Speaker, Microphone, Desk, Chair)

-- Page 3: Next 5 products (OFFSET 10)
SELECT * FROM products ORDER BY id LIMIT 5 OFFSET 10;
-- Output: Products 11-12 (Lamp, Cable)

-- Pagination formula: OFFSET = (page_number - 1) * page_size
-- For page 3 with page_size 5: OFFSET = (3 - 1) * 5 = 10
```
[Back to Table of Contents](#table-of-contents)

### 63. What is the difference between an implicit and explicit JOIN in SQL? Can you give an example of each?

Implicit JOIN is an older syntax where we list multiple tables separated by commas in the FROM clause and specify the join condition in the WHERE clause. It can only be used as a substitute for INNER JOIN and CROSS JOIN. We cannot perform LEFT JOIN, RIGHT JOIN, or FULL JOIN with implicit syntax.

With implicit JOIN, we use the WHERE clause to specify the join condition, like WHERE users.id = orders.user_id. This works like INNER JOIN because the comparison naturally excludes nulls. If either users.id or orders.user_id is null, the condition null equals null evaluates to null, not true, so those rows are filtered out by the WHERE clause.

Explicit JOIN uses the JOIN keyword with an ON clause to clearly specify the join operation and condition. This is the preferred modern approach because it makes the code more readable and clearly separates the join logic from filtering conditions. With explicit JOIN, we can easily distinguish between join conditions in the ON clause and filter conditions in the WHERE clause, whereas with implicit JOIN everything is mixed together in the WHERE clause, making the query harder to understand and maintain.

```sql
-- Sample tables
CREATE TABLE users (id INT, name VARCHAR(50));
CREATE TABLE orders (id INT, user_id INT, amount DECIMAL);

INSERT INTO users VALUES (1, 'Alice'), (2, 'Bob'), (3, 'Charlie');
INSERT INTO orders VALUES (1, 1, 100), (2, 1, 200), (3, 2, 150), (4, NULL, 75);

-- Implicit JOIN (old syntax) - join condition in WHERE clause
SELECT users.name, orders.amount
FROM users, orders
WHERE users.id = orders.user_id;
-- Output: Alice-100, Alice-200, Bob-150

-- Explicit JOIN (modern syntax) - join condition in ON clause
SELECT users.name, orders.amount
FROM users
INNER JOIN orders ON users.id = orders.user_id;
-- Output: Alice-100, Alice-200, Bob-150

-- Explicit JOIN with additional filter - separation of concerns
SELECT users.name, orders.amount
FROM users
INNER JOIN orders ON users.id = orders.user_id
WHERE orders.amount > 100;
-- Output: Alice-200, Bob-150

-- Implicit JOIN cannot do LEFT JOIN - this requires explicit syntax
SELECT users.name, orders.amount
FROM users
LEFT JOIN orders ON users.id = orders.user_id;
-- Output: Alice-100, Alice-200, Bob-150, Charlie-NULL
```
[Back to Table of Contents](#table-of-contents)

### 64. What are aggregate functions in SQL? Can you name a few and explain when you would use them?

Aggregate functions perform calculations on multiple rows and return a single summary value. They are used to analyze and summarize data rather than viewing individual records.

For example, instead of seeing each employee with their salary on a separate row, I can use aggregate functions with GROUP BY to get summary statistics per department. The main aggregate functions are:

AVG calculates the average value. I would use it to find the average salary per department.

SUM adds up all values. I would use it to calculate the total salary budget for each department.

COUNT counts the number of rows. I would use it to find how many employees work in each department.

MIN and MAX find the minimum and maximum values. I would use them to find the lowest and highest salary in each department.

These functions are typically used with GROUP BY to calculate statistics for each group, but they can also be used without GROUP BY to calculate across all rows in a table.

```sql
-- Sample employees table
CREATE TABLE employees (id INT, name VARCHAR(50), department VARCHAR(50), salary DECIMAL);
INSERT INTO employees VALUES
(1, 'Alice', 'Engineering', 80000),
(2, 'Bob', 'Engineering', 90000),
(3, 'Charlie', 'Sales', 60000),
(4, 'Diana', 'Sales', 65000),
(5, 'Eve', 'HR', 55000);

-- AVG: Average salary per department
SELECT department, AVG(salary) as avg_salary
FROM employees
GROUP BY department;
-- Output: Engineering-85000, Sales-62500, HR-55000

-- SUM: Total salary budget per department
SELECT department, SUM(salary) as total_budget
FROM employees
GROUP BY department;
-- Output: Engineering-170000, Sales-125000, HR-55000

-- COUNT: Number of employees per department
SELECT department, COUNT(*) as employee_count
FROM employees
GROUP BY department;
-- Output: Engineering-2, Sales-2, HR-1

-- MIN and MAX: Salary range per department
SELECT department, MIN(salary) as min_salary, MAX(salary) as max_salary
FROM employees
GROUP BY department;
-- Output: Engineering (80000-90000), Sales (60000-65000), HR (55000-55000)

-- Without GROUP BY: Overall statistics
SELECT COUNT(*) as total_employees, AVG(salary) as company_avg, SUM(salary) as total_payroll
FROM employees;
-- Output: 5 employees, 70000 average, 350000 total payroll
```
[Back to Table of Contents](#table-of-contents)

### 65. What is a sequence in PostgreSQL? How is it used with auto-incrementing primary keys?

A sequence is a database object in PostgreSQL that generates unique sequential numbers. It acts like an automatic counter that keeps track of the last number it generated and provides the next number when requested.

Sequences are commonly used with auto-incrementing primary keys. When we create a column with the SERIAL data type, PostgreSQL automatically creates a sequence for that column and sets the column's default value to get the next number from that sequence. For example, when we define id SERIAL PRIMARY KEY, PostgreSQL creates a sequence and each new insert automatically gets the next sequential number.

An important characteristic of sequences is that they never reuse numbers. If we have records with ids 1, 2, 3, 4 and we delete the record with id 4, the next insert will get id 5, not id 4. The sequence always moves forward. This ensures uniqueness even in concurrent environments where multiple transactions might be requesting ids at the same time.

```sql
-- Create table with SERIAL (auto-creates sequence)
CREATE TABLE products (
id SERIAL PRIMARY KEY,
name VARCHAR(50)
);

-- Insert records - id auto-increments
INSERT INTO products (name) VALUES ('Laptop');
INSERT INTO products (name) VALUES ('Mouse');
INSERT INTO products (name) VALUES ('Keyboard');
INSERT INTO products (name) VALUES ('Monitor');

SELECT * FROM products;
-- Output: 1-Laptop, 2-Mouse, 3-Keyboard, 4-Monitor

-- Delete record with id 4
DELETE FROM products WHERE id = 4;

-- Next insert gets id 5, not 4 (sequence never reuses numbers)
INSERT INTO products (name) VALUES ('Webcam');

SELECT * FROM products;
-- Output: 1-Laptop, 2-Mouse, 3-Keyboard, 5-Webcam

-- Manually check sequence value
SELECT currval('products_id_seq');
-- Output: 5

-- Manually get next sequence value
SELECT nextval('products_id_seq');
-- Output: 6

-- Create and use sequence manually
CREATE SEQUENCE custom_seq START 100 INCREMENT 10;
SELECT nextval('custom_seq');  -- Output: 100
SELECT nextval('custom_seq');  -- Output: 110
SELECT nextval('custom_seq');  -- Output: 120
```
[Back to Table of Contents](#table-of-contents)

### 66. What is a NULL value in SQL? How does NULL behave differently from an empty string or zero?

NULL represents the absence of a value. It means unknown or no value exists. It is different from an empty string or zero because those are actual values. An empty string is like an empty glass on Earth, while NULL is like an empty glass in the cosmos. It represents something that does not exist in our reality.

NULL behaves differently in comparisons. We cannot use the equals sign with NULL because NULL is not equal to anything, not even to another NULL. Instead, we use IS NULL and IS NOT NULL to check for NULL values. For strings and numbers, we use normal comparison operators like equals and not equals.

NULL also has special behavior in other operations. Any mathematical operation with NULL returns NULL, like NULL plus 5 equals NULL. Aggregate functions like COUNT with a column name skip NULL values, while COUNT with asterisk counts all rows including those with NULLs.

```sql
-- Sample table with NULLs, empty strings, and zeros
CREATE TABLE products (id INT, name VARCHAR(50), price DECIMAL, description VARCHAR(100));
INSERT INTO products VALUES
(1, 'Laptop', 999, 'High performance'),
(2, 'Mouse', 0, ''),
(3, 'Keyboard', NULL, NULL),
(4, 'Monitor', 299, '');

-- NULL vs empty string vs zero
SELECT * FROM products WHERE price = 0;        -- Output: Mouse (zero is a value)
SELECT * FROM products WHERE description = ''; -- Output: Mouse, Monitor (empty string is a value)
SELECT * FROM products WHERE price IS NULL;    -- Output: Keyboard (NULL is absence of value)

-- Cannot use = with NULL (returns no results)
SELECT * FROM products WHERE price = NULL;     -- Output: Nothing (wrong way)
SELECT * FROM products WHERE price IS NULL;    -- Output: Keyboard (correct way)

-- NULL in mathematical operations
SELECT name, price, price + 10 as price_plus_10 FROM products;
-- Output: Laptop-1009, Mouse-10, Keyboard-NULL, Monitor-309

-- NULL in aggregate functions
SELECT COUNT(*) as total_rows, COUNT(price) as non_null_prices, COUNT(description) as non_null_desc
FROM products;
-- Output: 4 total rows, 3 non-null prices, 2 non-null descriptions

-- NULL vs NULL comparison
SELECT * FROM products WHERE price = price;     -- Output: Laptop, Mouse, Monitor (NULL excluded)
SELECT * FROM products WHERE price IS NULL OR price IS NOT NULL; -- Output: All 4 rows
```
[Back to Table of Contents](#table-of-contents)

### 67. What is the difference between DROP, TRUNCATE, and DELETE commands in PostgreSQL?

These three commands remove data at different levels.

DROP removes the entire table including its structure, data, indexes, and constraints. After DROP, the table no longer exists in the database. We would use DROP when we want to completely remove a table we no longer need.

TRUNCATE removes all rows from a table but keeps the table structure intact. It is very fast because it deallocates data pages without processing individual rows. It also resets auto-increment sequences. TRUNCATE cannot use a WHERE clause, it always removes all rows. However, it can have issues with foreign key constraints. I would use TRUNCATE when I need to quickly clear all data from a table during testing or maintenance.

DELETE removes specific rows based on a WHERE clause, or all rows if no WHERE is specified. It processes each row individually, which makes it slower than TRUNCATE for removing all records. However, DELETE is the only option when we need to selectively remove certain records. It also works safely with foreign key constraints and triggers.

The key difference for removing all records is that DELETE is slow because it processes each row individually and logs each deletion, while TRUNCATE is fast because it simply deallocates the data pages.

```sql
-- Sample table setup
CREATE TABLE test_delete (id SERIAL PRIMARY KEY, name VARCHAR(50));
CREATE TABLE test_truncate (id SERIAL PRIMARY KEY, name VARCHAR(50));
CREATE TABLE test_drop (id SERIAL PRIMARY KEY, name VARCHAR(50));

INSERT INTO test_delete VALUES (1, 'Alice'), (2, 'Bob'), (3, 'Charlie');
INSERT INTO test_truncate VALUES (1, 'Alice'), (2, 'Bob'), (3, 'Charlie');
INSERT INTO test_drop VALUES (1, 'Alice'), (2, 'Bob'), (3, 'Charlie');

-- DELETE: Remove specific rows with WHERE clause
DELETE FROM test_delete WHERE id = 2;
SELECT * FROM test_delete;
-- Output: 1-Alice, 3-Charlie (Bob removed)

INSERT INTO test_delete (name) VALUES ('David');
SELECT * FROM test_delete;
-- Output: 1-Alice, 3-Charlie, 4-David (sequence continues from 3)

-- TRUNCATE: Remove all rows, reset sequence
TRUNCATE test_truncate;
SELECT * FROM test_truncate;
-- Output: Empty table

INSERT INTO test_truncate (name) VALUES ('Eve');
SELECT * FROM test_truncate;
-- Output: 1-Eve (sequence reset to 1)

-- DROP: Remove entire table
DROP TABLE test_drop;
-- SELECT * FROM test_drop;  -- Error: table does not exist

-- Speed comparison: DELETE vs TRUNCATE (conceptual)
-- DELETE FROM large_table;    -- Slow: processes each row, logs each deletion
-- TRUNCATE large_table;       -- Fast: deallocates data pages directly
```
[Back to Table of Contents](#table-of-contents)

### 68. What are the different types of JOIN operations you know, and can you briefly explain what each one returns?

There are five main types of JOIN operations.

INNER JOIN is the most commonly used. It returns only the rows where there is a match in both tables. For example, if joining users and orders, it shows only users who have placed orders.

LEFT JOIN returns all rows from the left table and matching rows from the right table. If there is no match, the right table columns are filled with NULL. For example, it would show all users, including those who have not placed any orders yet.

RIGHT JOIN is the opposite of LEFT JOIN. It returns all rows from the right table and matching rows from the left table. It is rarely used because we can rewrite it as a LEFT JOIN by switching the table order.

FULL OUTER JOIN returns all rows from both tables. If there is a match, it shows the combined data. If there is no match on either side, it still includes the row with NULLs for the missing side. It is like combining LEFT JOIN and RIGHT JOIN together.

CROSS JOIN creates a Cartesian product. It matches every row from the first table with every row from the second table. If table A has 3 rows and table B has 2 rows, CROSS JOIN produces 6 rows, which is 3 times 2. This is rarely used in practice, mainly for generating combinations or test data.

```sql
-- Sample tables
CREATE TABLE users (id INT, name VARCHAR(50));
CREATE TABLE orders (id INT, user_id INT, amount DECIMAL);

INSERT INTO users VALUES (1, 'Alice'), (2, 'Bob'), (3, 'Charlie');
INSERT INTO orders VALUES (1, 1, 100), (2, 1, 200), (3, 2, 150);

-- INNER JOIN: Only users with orders
SELECT u.name, o.amount FROM users u
INNER JOIN orders o ON u.id = o.user_id;
-- Output: Alice-100, Alice-200, Bob-150

-- LEFT JOIN: All users, NULL for users without orders
SELECT u.name, o.amount FROM users u
LEFT JOIN orders o ON u.id = o.user_id;
-- Output: Alice-100, Alice-200, Bob-150, Charlie-NULL

-- RIGHT JOIN: All orders, NULL for orders without users
SELECT u.name, o.amount FROM users u
RIGHT JOIN orders o ON u.id = o.user_id;
-- Output: Alice-100, Alice-200, Bob-150

-- FULL OUTER JOIN: All users and all orders
SELECT u.name, o.amount FROM users u
FULL OUTER JOIN orders o ON u.id = o.user_id;
-- Output: Alice-100, Alice-200, Bob-150, Charlie-NULL

-- CROSS JOIN: Every user paired with every order (Cartesian product)
SELECT u.name, o.amount FROM users u
CROSS JOIN orders o;
-- Output: 9 rows (3 users × 3 orders)
-- Alice-100, Alice-200, Alice-150, Bob-100, Bob-200, Bob-150, Charlie-100, Charlie-200, Charlie-150
```
[Back to Table of Contents](#table-of-contents)

### 69. What is the difference between COUNT(\*), COUNT(column_name), and COUNT(DISTINCT column_name) in SQL?

These three COUNT variations work differently.

COUNT with asterisk counts all rows in the table, including rows where some columns might be NULL. For example, if a users table has 5 rows, COUNT with asterisk returns 5.

COUNT with column name counts only the rows where that specific column is not NULL. Using the same users table, if 2 users do not have an email address, meaning email is NULL, COUNT of email would return 3 instead of 5.

COUNT DISTINCT with column name counts the number of unique non-NULL values in that column. If multiple users have the same email address, COUNT DISTINCT email would count that email only once. So if 5 users have 3 different email addresses with 2 being NULL, COUNT DISTINCT email returns 2.

**Code Example:**

```sql
-- Sample users table with NULL and duplicate values
CREATE TABLE users (id INT, name VARCHAR(50), email VARCHAR(50));
INSERT INTO users VALUES
(1, 'Alice', 'alice@example.com'),
(2, 'Bob', 'bob@example.com'),
(3, 'Charlie', 'alice@example.com'),  -- Duplicate email
(4, 'Diana', NULL),                    -- NULL email
(5, 'Eve', NULL);                      -- NULL email

-- COUNT(*): All rows including NULLs
SELECT COUNT(*) as total_rows FROM users;
-- Output: 5

-- COUNT(column_name): Only non-NULL values
SELECT COUNT(email) as non_null_emails FROM users;
-- Output: 3 (excludes 2 NULLs)

-- COUNT(DISTINCT column_name): Unique non-NULL values
SELECT COUNT(DISTINCT email) as unique_emails FROM users;
-- Output: 2 (alice@example.com and bob@example.com)

-- Comparison example
SELECT
COUNT(*) as total_rows,
COUNT(email) as emails_count,
COUNT(DISTINCT email) as unique_emails
FROM users;
-- Output: 5 total rows, 3 emails (non-NULL), 2 unique emails
```
[Back to Table of Contents](#table-of-contents)

### 70. What is the difference between CHAR, VARCHAR, and TEXT data types in PostgreSQL? When would you use each one?

In PostgreSQL, these three data types handle text differently.

CHAR with n has a fixed length of exactly n characters. If we store less than n characters, PostgreSQL pads it with spaces to reach the fixed length. For example, CHAR(10) storing hello actually stores hello with 5 spaces. CHAR is rarely used in PostgreSQL, only for truly fixed-length data like two-letter country codes such as US, UK, or FR where every value is always the same length.

VARCHAR with n has a maximum length of n characters but only uses as much space as needed for the actual data, plus a small overhead. For example, VARCHAR(100) storing hello only uses space for 5 characters, not 100. This makes it more efficient than CHAR in most cases. I would use VARCHAR when I want to enforce a maximum length, like limiting usernames to 50 characters or email addresses to 255 characters.

TEXT has no length limit and works exactly like VARCHAR without a length specification. It is used for large, variable-length text like blog posts, comments, or product descriptions where we do not want to impose a maximum length. In PostgreSQL, TEXT and VARCHAR without a length limit perform identically.

**Code Example:**

```sql
-- Sample table with different text types
CREATE TABLE text_demo (
id SERIAL,
country_code CHAR(2),
username VARCHAR(50),
bio TEXT
);

INSERT INTO text_demo (country_code, username, bio) VALUES
('US', 'alice', 'Software engineer passionate about databases and backend development.'),
('UK', 'bob', 'Data analyst with 5 years of experience in SQL and Python.'),
('FR', 'charlie', 'Full-stack developer who loves building web applications.');

-- CHAR(2): Always 2 characters, padded with spaces if needed
SELECT country_code, LENGTH(country_code) as char_length FROM text_demo;
-- Output: US-2, UK-2, FR-2 (all exactly 2 characters)

-- VARCHAR(50): Variable length up to 50 characters
SELECT username, LENGTH(username) as varchar_length FROM text_demo;
-- Output: alice-5, bob-3, charlie-7 (only uses actual length)

-- TEXT: No length limit
SELECT bio, LENGTH(bio) as text_length FROM text_demo LIMIT 1;
-- Output: 63 characters for alice's bio

-- Attempting to exceed VARCHAR limit causes error
-- INSERT INTO text_demo (username) VALUES ('this_username_is_way_too_long_and_exceeds_fifty_characters_limit');
-- Error: value too long for type character varying(50)

-- TEXT has no such limitation
INSERT INTO text_demo (bio) VALUES (REPEAT('Long text ', 1000));
SELECT LENGTH(bio) FROM text_demo WHERE id = 4;
-- Output: 10000 (TEXT handles large content)
```
[Back to Table of Contents](#table-of-contents)

### 71. What is a composite index in PostgreSQL? When would you create one instead of multiple single-column indexes?

"A composite index is an index that includes multiple columns together, rather than just one column. The order of columns in a composite index matters significantly.

For example, in an e-commerce system with an order_items table, I might create a composite index on product_id and order_id if I frequently query orders for specific products. This is more efficient than having separate indexes on each column when I am filtering by both columns together.

I would create a composite index instead of multiple single-column indexes when I frequently query using those columns together in WHERE clauses or JOIN conditions. The composite index is more efficient for combined queries. However, it is important to know that a composite index on A and B can efficiently support queries filtering on A alone or on both A and B, but not efficiently on B alone. The order matters.

I would use multiple single-column indexes when I query each column independently in different queries, rather than together."

**Code Example:**

```sql
-- Sample order_items table
CREATE TABLE order_items (
id SERIAL PRIMARY KEY,
order_id INT,
product_id INT,
quantity INT,
price DECIMAL
);

INSERT INTO order_items (order_id, product_id, quantity, price) VALUES
(1, 101, 2, 29.99), (1, 102, 1, 49.99), (2, 101, 1, 29.99),
(2, 103, 3, 19.99), (3, 102, 2, 49.99), (3, 101, 1, 29.99);

-- Create composite index (order matters!)
CREATE INDEX idx_product_order ON order_items(product_id, order_id);

-- Query 1: Uses composite index efficiently (filters on first column)
SELECT * FROM order_items WHERE product_id = 101;
-- Output: 3 rows for product 101

-- Query 2: Uses composite index efficiently (filters on both columns)
SELECT * FROM order_items WHERE product_id = 101 AND order_id = 1;
-- Output: 1 row (order 1, product 101)

-- Query 3: Cannot use composite index efficiently (filters only on second column)
SELECT * FROM order_items WHERE order_id = 2;
-- Output: 2 rows, but index is not efficiently used

-- Comparison: Single-column indexes for independent queries
CREATE INDEX idx_order ON order_items(order_id);
CREATE INDEX idx_product ON order_items(product_id);

-- Now both queries use their respective indexes efficiently
SELECT * FROM order_items WHERE product_id = 102;  -- Uses idx_product
SELECT * FROM order_items WHERE order_id = 3;      -- Uses idx_order
```
[Back to Table of Contents](#table-of-contents)

### 72. What is a database trigger in PostgreSQL? Can you give an example of when you might use one?

A trigger is a database function that automatically executes when specific events occur on a table, such as INSERT, UPDATE, or DELETE. Triggers can run either BEFORE or AFTER these events.

For example, I might use an AFTER INSERT trigger on an orders table to automatically send a confirmation email when a new order is created. The trigger would fire every time a new row is inserted, and it would execute a function that sends the notification email to the customer.

Other common uses include audit logging to track who made changes and when, automatically updating calculated fields when related data changes, or enforcing complex business rules that go beyond simple constraints. For instance, I could use a trigger to automatically update a product's total_sales field whenever a new order is placed, or to record a timestamp and user ID in an audit table whenever sensitive data is modified.

**Code Example:**

```sql
-- Sample tables
CREATE TABLE products (id INT PRIMARY KEY, name VARCHAR(50), total_sales INT DEFAULT 0);
CREATE TABLE orders (id SERIAL PRIMARY KEY, product_id INT, quantity INT, created_at TIMESTAMP);
CREATE TABLE audit_log (id SERIAL, table_name VARCHAR(50), action VARCHAR(10), changed_at TIMESTAMP);

INSERT INTO products VALUES (101, 'Laptop', 0), (102, 'Mouse', 0);

-- Create trigger function to update total_sales
CREATE OR REPLACE FUNCTION update_product_sales()
RETURNS TRIGGER AS $$
BEGIN
UPDATE products
SET total_sales = total_sales + NEW.quantity
WHERE id = NEW.product_id;
RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Create AFTER INSERT trigger
CREATE TRIGGER after_order_insert
AFTER INSERT ON orders
FOR EACH ROW
EXECUTE FUNCTION update_product_sales();

-- Insert orders and watch total_sales update automatically
INSERT INTO orders (product_id, quantity, created_at) VALUES (101, 5, NOW());
SELECT * FROM products WHERE id = 101;
-- Output: Laptop with total_sales = 5

INSERT INTO orders (product_id, quantity, created_at) VALUES (101, 3, NOW());
SELECT * FROM products WHERE id = 101;
-- Output: Laptop with total_sales = 8 (automatically updated by trigger)

-- Audit log trigger example
CREATE OR REPLACE FUNCTION log_changes()
RETURNS TRIGGER AS $$
BEGIN
INSERT INTO audit_log (table_name, action, changed_at)
VALUES (TG_TABLE_NAME, TG_OP, NOW());
RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER audit_orders
AFTER INSERT OR UPDATE OR DELETE ON orders
FOR EACH ROW
EXECUTE FUNCTION log_changes();

INSERT INTO orders (product_id, quantity, created_at) VALUES (102, 2, NOW());
SELECT * FROM audit_log;
-- Output: orders, INSERT, timestamp
```
[Back to Table of Contents](#table-of-contents)

### 73. What is a subquery in SQL? Can you explain what a correlated subquery is?

A subquery is a query nested inside another query, written in parentheses. It allows us to use the result of one query as input for another query.

For example, if I want to find all employees earning more than the average salary, I can write: SELECT name, salary FROM employees WHERE salary is greater than, then in parentheses, SELECT AVG of salary FROM employees. The subquery calculates the average first, then the outer query uses that result.

A correlated subquery depends on the outer query. It references columns from the outer query and runs once for each row. For example, finding employees earning more than the average salary in their own department: SELECT e1.name, e1.salary FROM employees e1 WHERE e1.salary is greater than, then in parentheses, SELECT AVG of e2.salary FROM employees e2 WHERE e2.department_id equals e1.department_id. Here, the subquery runs for each employee, using their specific department_id from the outer query.

The key characteristic of correlated subqueries is that they run multiple times and depend on values from the outer query, making them more computationally expensive than regular subqueries.

**Code Example:**

```sql
-- Sample tables
CREATE TABLE departments (id INT, name VARCHAR(50));
CREATE TABLE employees (id INT, name VARCHAR(50), department_id INT, salary DECIMAL);

INSERT INTO departments VALUES (1, 'Engineering'), (2, 'Sales'), (3, 'HR');
INSERT INTO employees VALUES
(1, 'Alice', 1, 90000), (2, 'Bob', 1, 80000), (3, 'Charlie', 2, 60000),
(4, 'Diana', 2, 70000), (5, 'Eve', 3, 55000);

-- Simple subquery: Find employees earning more than average
SELECT name, salary FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
-- Output: Alice-90000, Bob-80000, Diana-70000

-- Correlated subquery: Find employees earning more than their department's average
SELECT e1.name, e1.salary, e1.department_id
FROM employees e1
WHERE e1.salary > (
SELECT AVG(e2.salary)
FROM employees e2
WHERE e2.department_id = e1.department_id
);
-- Output: Alice-90000-dept1, Diana-70000-dept2

-- Correlated subquery with EXISTS: Find departments with employees earning over 75000
SELECT d.name
FROM departments d
WHERE EXISTS (
SELECT 1 FROM employees e
WHERE e.department_id = d.id AND e.salary > 75000
);
-- Output: Engineering (has Alice and Bob)

-- Comparison: Show each employee with their department average
SELECT e1.name, e1.salary,
(SELECT AVG(e2.salary) FROM employees e2
WHERE e2.department_id = e1.department_id) as dept_avg
FROM employees e1;
-- Output: Alice-90000-85000, Bob-80000-85000, Charlie-60000-65000, Diana-70000-65000, Eve-55000-55000
```
[Back to Table of Contents](#table-of-contents)

### 74. What are database constraints in PostgreSQL? Can you name and explain a few types of constraints?

Database constraints enforce data integrity through validation rules and relationships between tables. They ensure our data stays consistent and follows business rules.

The main constraint types are:

PRIMARY KEY ensures each record has a unique identifier that cannot be null. Every table should have one primary key.

FOREIGN KEY creates relationships between tables and enforces referential integrity. By default, it prevents deletion of a parent record if child records exist. We can modify this behavior: with ON DELETE CASCADE, deleting the parent also deletes all child records. With ON DELETE SET NULL, the parent is deleted and the child's foreign key becomes NULL, keeping the child record but removing its reference.

UNIQUE ensures that all values in a column are unique. For example, in a users table, we use UNIQUE on the username or email column to prevent duplicate accounts.

CHECK allows us to define custom validation rules. For example, we can ensure that only users above 18 years old can process payments, or that a product price must be positive.

NOT NULL ensures that a column cannot contain null values, meaning it must always have data when inserting or updating records.

**Code Example:**

```sql
-- Create tables with various constraints
CREATE TABLE users (
id SERIAL PRIMARY KEY,                    -- PRIMARY KEY
email VARCHAR(100) UNIQUE NOT NULL,       -- UNIQUE and NOT NULL
username VARCHAR(50) UNIQUE NOT NULL,
age INT CHECK (age >= 18)                 -- CHECK constraint
);

CREATE TABLE orders (
id SERIAL PRIMARY KEY,
user_id INT NOT NULL,
amount DECIMAL CHECK (amount > 0),        -- CHECK: positive amount
FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);

-- Insert valid data
INSERT INTO users (email, username, age) VALUES ('alice@example.com', 'alice', 25);
INSERT INTO users (email, username, age) VALUES ('bob@example.com', 'bob', 30);
INSERT INTO orders (user_id, amount) VALUES (1, 99.99);

-- UNIQUE constraint violation
-- INSERT INTO users (email, username, age) VALUES ('alice@example.com', 'alice2', 28);
-- Error: duplicate key value violates unique constraint

-- CHECK constraint violation
-- INSERT INTO users (email, username, age) VALUES ('charlie@example.com', 'charlie', 16);
-- Error: violates check constraint (age >= 18)

-- NOT NULL constraint violation
-- INSERT INTO users (email, age) VALUES ('dave@example.com', 22);
-- Error: null value in column "username" violates not-null constraint

-- FOREIGN KEY with CASCADE
DELETE FROM users WHERE id = 1;
SELECT * FROM orders;
-- Output: Empty (order was deleted due to CASCADE)

-- FOREIGN KEY with SET NULL example
CREATE TABLE products (id INT PRIMARY KEY, name VARCHAR(50));
CREATE TABLE reviews (id SERIAL, product_id INT, rating INT,
FOREIGN KEY (product_id) REFERENCES products(id) ON DELETE SET NULL);

INSERT INTO products VALUES (1, 'Laptop');
INSERT INTO reviews (product_id, rating) VALUES (1, 5);
DELETE FROM products WHERE id = 1;
SELECT * FROM reviews;
-- Output: review remains but product_id is NULL
```
[Back to Table of Contents](#table-of-contents)

### 75. What is the difference between WHERE and HAVING clauses in SQL? Can you give an example of when you would use each?

The key difference is that WHERE filters individual rows before any grouping or aggregation happens, while HAVING filters groups after aggregation.

We use WHERE when we want to filter rows based on column values, like filtering employees by age, name, or id. This happens before any grouping.

We use HAVING when we need to filter based on aggregated results. For example, if I calculate the average salary per department using GROUP BY, and I want to show only departments where the average salary is greater than a certain amount, I would use HAVING after the GROUP BY clause.

Both can be used together in the same query: WHERE filters the rows first, then GROUP BY groups them, and finally HAVING filters those groups based on aggregate conditions. For instance, I could filter employees over 25 years old with WHERE, group them by department, and then use HAVING to show only departments where the average salary exceeds 50000.

**Code Example:**

```sql
-- Sample employees table
CREATE TABLE employees (id INT, name VARCHAR(50), department VARCHAR(50), age INT, salary DECIMAL);
INSERT INTO employees VALUES
(1, 'Alice', 'Engineering', 28, 80000),
(2, 'Bob', 'Engineering', 32, 90000),
(3, 'Charlie', 'Sales', 24, 50000),
(4, 'Diana', 'Sales', 29, 60000),
(5, 'Eve', 'HR', 26, 55000),
(6, 'Frank', 'HR', 23, 45000);

-- WHERE: Filter rows before grouping (employees over 25)
SELECT * FROM employees WHERE age > 25;
-- Output: Alice, Bob, Diana, Eve (4 employees)

-- HAVING: Filter groups after aggregation (departments with avg salary > 55000)
SELECT department, AVG(salary) as avg_salary
FROM employees
GROUP BY department
HAVING AVG(salary) > 55000;
-- Output: Engineering-85000

-- WHERE and HAVING together
SELECT department, AVG(salary) as avg_salary, COUNT(*) as employee_count
FROM employees
WHERE age > 25                          -- Filter rows: only employees over 25
GROUP BY department                     -- Group by department
HAVING AVG(salary) > 60000;            -- Filter groups: avg salary over 60000
-- Output: Engineering-85000-2 (Alice and Bob)

-- Comparison: HAVING without WHERE
SELECT department, COUNT(*) as total_employees
FROM employees
GROUP BY department
HAVING COUNT(*) >= 2;
-- Output: Engineering-2, Sales-2, HR-2

-- WHERE cannot use aggregate functions (this would cause error)
-- SELECT * FROM employees WHERE AVG(salary) > 60000;  -- Error!
-- Must use HAVING for aggregate conditions
```
[Back to Table of Contents](#table-of-contents)

### 76. What is a database view in PostgreSQL? When would you use a view instead of a regular table?

A view is a virtual table in PostgreSQL. It does not store data itself, but rather stores a SQL query. Every time we query a view, it executes the underlying query and returns the most current data from the database.

We use views for several reasons. First, they help us avoid writing complex queries repeatedly. If we have a complicated query with multiple joins and conditions that we need often, we can create a view once and then query it like a simple table. Second, views provide security and access control. For example, if a certain department should only see specific columns from a table, we can create a view that includes only those columns and give them access to the view instead of the whole table. Third, views can simplify the database structure for applications. We can hide complexity behind a simple view interface.

The key advantage is that views always show the latest data because they execute the query each time they are accessed, rather than storing a snapshot of the data.

**Code Example:**

```sql
-- Sample tables
CREATE TABLE employees (id INT, name VARCHAR(50), salary DECIMAL, department VARCHAR(50), hire_date DATE);
CREATE TABLE departments (name VARCHAR(50), location VARCHAR(50));

INSERT INTO employees VALUES
(1, 'Alice', 80000, 'Engineering', '2020-01-15'),
(2, 'Bob', 90000, 'Engineering', '2019-06-20'),
(3, 'Charlie', 60000, 'Sales', '2021-03-10');

INSERT INTO departments VALUES ('Engineering', 'New York'), ('Sales', 'Boston');

-- Create view: Complex query with joins
CREATE VIEW employee_details AS
SELECT e.name, e.salary, e.department, d.location
FROM employees e
JOIN departments d ON e.department = d.name
WHERE e.salary > 50000;

-- Query the view like a regular table
SELECT * FROM employee_details;
-- Output: Alice-80000-Engineering-New York, Bob-90000-Engineering-New York, Charlie-60000-Sales-Boston

-- Create view: Security - hide salary information
CREATE VIEW public_employee_info AS
SELECT name, department, hire_date
FROM employees;

SELECT * FROM public_employee_info;
-- Output: Shows name, department, hire_date but not salary

-- Views always show current data
INSERT INTO employees VALUES (4, 'Diana', 70000, 'Sales', '2022-05-15');
SELECT * FROM employee_details;
-- Output: Now includes Diana-70000-Sales-Boston (view reflects new data)

-- Update underlying table
UPDATE employees SET salary = 95000 WHERE name = 'Bob';
SELECT * FROM employee_details WHERE name = 'Bob';
-- Output: Bob-95000-Engineering-New York (view shows updated salary)
```
[Back to Table of Contents](#table-of-contents)

### 77. What is database normalization? Can you explain what First Normal Form (1NF), Second Normal Form (2NF), and Third Normal Form (3NF) mean?

Database normalization is the process of organizing data to reduce redundancy and improve data integrity. The goal is to store each piece of information only once, making updates easier and preventing inconsistencies.

First Normal Form, or 1NF, means each column should contain atomic values. No lists or multiple values in a single column. For example, if we have a product that could have multiple colors, we should not store them as red, blue, green in one field. Instead, each color should be a separate record or we should use a proper relationship structure to handle multiple colors per product.

Second Normal Form, or 2NF, means the table must be in 1NF and all non-key columns must depend on the entire primary key, not just part of it. For example, in an inventory system that tracks stock and pricing, we might have a composite key combining product_id and size_id. This follows 2NF because fields like quantity and price depend on the complete combination of the product and its size, not just the product alone or the size alone. If we stored size_name directly in the inventory table, it would violate 2NF because size_name would depend only on the size_id, not on the full composite key.

Third Normal Form, or 3NF, means the table must be in 2NF and non-key columns should depend only on the primary key, not on other non-key columns. For example, instead of storing material names like diamond, gold, or platinum repeatedly in every product record, we would create separate material type tables and use foreign keys to reference them. The benefit is clear: if we need to update a material type name, we only change it in one place in the material type table, and it automatically applies to all products that reference it. This eliminates data redundancy and makes maintenance much easier.

**Code Example:**

```sql
-- Violates 1NF: Multiple values in one column
CREATE TABLE products_bad (id INT, name VARCHAR(50), colors VARCHAR(100));
INSERT INTO products_bad VALUES (1, 'T-Shirt', 'red, blue, green');

-- 1NF: Atomic values, separate rows for each color
CREATE TABLE products (id INT, name VARCHAR(50));
CREATE TABLE product_colors (product_id INT, color VARCHAR(20));
INSERT INTO products VALUES (1, 'T-Shirt');
INSERT INTO product_colors VALUES (1, 'red'), (1, 'blue'), (1, 'green');

-- 2NF example: Inventory with composite key
CREATE TABLE sizes (id INT PRIMARY KEY, name VARCHAR(20));
CREATE TABLE inventory (
product_id INT,
size_id INT,
quantity INT,
price DECIMAL,
PRIMARY KEY (product_id, size_id),
FOREIGN KEY (size_id) REFERENCES sizes(id)
);
INSERT INTO sizes VALUES (1, 'Small'), (2, 'Medium'), (3, 'Large');
INSERT INTO inventory VALUES (1, 1, 50, 19.99), (1, 2, 75, 19.99), (1, 3, 30, 21.99);

-- 3NF example: Remove transitive dependencies
CREATE TABLE material_types (id INT PRIMARY KEY, name VARCHAR(50), description TEXT);
CREATE TABLE products_normalized (
id INT PRIMARY KEY,
name VARCHAR(50),
material_type_id INT,
FOREIGN KEY (material_type_id) REFERENCES material_types(id)
);

INSERT INTO material_types VALUES (1, 'Gold', 'Precious metal'), (2, 'Silver', 'Precious metal');
INSERT INTO products_normalized VALUES (1, 'Ring', 1), (2, 'Necklace', 1), (3, 'Bracelet', 2);

-- Update material name once, affects all products
UPDATE material_types SET name = 'Pure Gold' WHERE id = 1;
SELECT p.name, m.name as material FROM products_normalized p
JOIN material_types m ON p.material_type_id = m.id;
-- Output: Ring-Pure Gold, Necklace-Pure Gold, Bracelet-Silver
```
[Back to Table of Contents](#table-of-contents)

### 78. What is the difference between DELETE and TRUNCATE in PostgreSQL? When would you use one over the other?

DELETE removes rows from a table and can use a WHERE clause to target specific records. If used without a WHERE clause, it deletes all rows but processes each row individually, which can be slow for large tables. TRUNCATE, on the other hand, removes all rows from a table much faster because it does not scan each row individually. Instead, it simply deallocates the data pages.

I use DELETE when I need to remove specific records based on conditions. For example, if I need to delete all orders from last year or all inactive users matching certain criteria. I use TRUNCATE when I need to quickly clear an entire table, especially large tables with millions of rows.

However, TRUNCATE has limitations with foreign key constraints, so DELETE is often the safer choice when I am working with related tables that have referential integrity. We should also consider that DELETE generates transaction logs for each row, while TRUNCATE generates minimal logging, which is another factor when deciding between the two.

```python
# Example: DELETE with WHERE clause - removes specific records
DELETE FROM orders WHERE order_date < '2023-01-01';
# Affected rows: 1,250 (only older orders deleted)

# Example: TRUNCATE - removes all rows quickly
TRUNCATE TABLE orders;
# All rows removed, table reset to empty state

# Example: DELETE without WHERE - slower but same result as TRUNCATE
DELETE FROM orders;
# Affected rows: 50,000 (processes each row individually)

# TRUNCATE with foreign key - will fail
TRUNCATE TABLE orders;  -- ERROR if other tables reference orders via FK

# DELETE with foreign key - works if cascading delete is configured
DELETE FROM orders;  -- SUCCESS with proper FK constraints
```
[Back to Table of Contents](#table-of-contents)

### 79. What is a foreign key in PostgreSQL? What happens if you try to delete a record that is referenced by a foreign key in another table?

A foreign key is a constraint that creates a link between two tables by ensuring that a value in one table matches a value in another table's primary key. It enforces referential integrity, which means we cannot have orphaned records. For example, an order cannot reference a customer that does not exist.

When I try to delete a record that is referenced by a foreign key, PostgreSQL's behavior depends on the ON DELETE option that was configured when the foreign key was created.

By default, when we use RESTRICT or NO ACTION, the deletion is prevented and PostgreSQL returns an error. For example, if I try to delete a customer who has orders, the database will block it and tell me to delete the orders first.

With ON DELETE CASCADE, the parent record is deleted along with all child records that reference it. For example, if I delete a customer, all their orders are automatically deleted as well.

With ON DELETE SET NULL, the parent record is deleted, but the foreign key column in the child records is set to NULL instead of being deleted. For example, if I delete a customer, their orders remain in the database but the customer_id becomes NULL.

There are also ON DELETE SET DEFAULT options, but CASCADE, SET NULL, and the default RESTRICT behavior are the most commonly used in practice.

```python
# Create tables with foreign key relationship
CREATE TABLE customers (
    id INT PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE orders (
    id INT PRIMARY KEY,
    customer_id INT REFERENCES customers(id) ON DELETE RESTRICT
);

# Default behavior: RESTRICT - deletion fails
DELETE FROM customers WHERE id = 1;
# ERROR: update or delete on table "customers" violates foreign key

# ON DELETE CASCADE - deletes parent and all child records
CREATE TABLE orders (
    id INT PRIMARY KEY,
    customer_id INT REFERENCES customers(id) ON DELETE CASCADE
);

DELETE FROM customers WHERE id = 1;
# SUCCESS: customer deleted, all their orders deleted automatically

# ON DELETE SET NULL - deletes parent, sets child FK to NULL
CREATE TABLE orders (
    id INT PRIMARY KEY,
    customer_id INT REFERENCES customers(id) ON DELETE SET NULL
);

DELETE FROM customers WHERE id = 1;
# SUCCESS: customer deleted, orders remain with customer_id = NULL
```
[Back to Table of Contents](#table-of-contents)

### 80. What is the difference between INNER JOIN, LEFT JOIN, and RIGHT JOIN? Can you give an example of when you would use each one?

These JOINs differ in how they handle rows that do not have matching pairs between tables.

INNER JOIN returns only rows where there is a match in both tables. For example, if I have a users table and an orders table, INNER JOIN would show only users who have placed orders. If a user has not placed any order, they will not appear in the result. I would use this when I specifically need data that exists in both tables, like when I want to show all customers who made purchases this month.

LEFT JOIN returns all rows from the left table and matching rows from the right table. If there is no match, the right table columns are filled with NULL. Using the same example, LEFT JOIN would show all users, including those who have not placed any orders yet. Their order columns would be NULL. I would use this when I want to see all records from my main table regardless of whether they have related data. For instance, I might use it to show all users and their orders if they have any, or to find all users who have not placed an order yet.

RIGHT JOIN returns all rows from the right table and matching rows from the left table. It is the opposite of LEFT JOIN. However, RIGHT JOIN is rarely used in practice because any RIGHT JOIN can be rewritten as a LEFT JOIN by simply switching the table order, which most developers find clearer and more intuitive to read.

```python
# Sample data
users = [
    {"id": 1, "name": "Alice"},
    {"id": 2, "name": "Bob"},
    {"id": 3, "name": "Charlie"}
]

orders = [
    {"id": 101, "user_id": 1, "amount": 50},
    {"id": 102, "user_id": 1, "amount": 30},
    {"id": 103, "user_id": 2, "amount": 75}
]

# INNER JOIN - only users with orders
SELECT users.name, orders.amount FROM users
INNER JOIN orders ON users.id = orders.user_id;
# Result: Alice 50, Alice 30, Bob 75

# LEFT JOIN - all users, with orders if they exist
SELECT users.name, orders.amount FROM users
LEFT JOIN orders ON users.id = orders.user_id;
# Result: Alice 50, Alice 30, Bob 75, Charlie NULL

# RIGHT JOIN - all orders with matching users (rarely used)
SELECT users.name, orders.amount FROM users
RIGHT JOIN orders ON users.id = orders.user_id;
# Result: Alice 50, Alice 30, Bob 75
```
[Back to Table of Contents](#table-of-contents)

### 81. What are database transactions in PostgreSQL? Can you explain the ACID properties?

A transaction is a sequence of database operations that are treated as a single unit of work. It ensures that either all operations complete successfully, or if any operation fails, all changes are rolled back. This protects database integrity and prevents partial updates that could corrupt our data.

ACID stands for four properties that define how transactions work.

Atomicity means all operations in a transaction succeed or all fail together. It is all or nothing. For example, when transferring money between bank accounts, both the debit and the credit must happen, or neither should happen. We cannot have a situation where money leaves one account but never arrives in the other.

Consistency means the database always moves from one valid state to another, respecting all rules and constraints. For example, if a table has a constraint that age must be greater than zero, the database will reject any transaction trying to insert age equal to negative five. Or if I try to create an order for a user that does not exist, it will fail because of the foreign key constraint.

Isolation means concurrent transactions do not interfere with each other. Each one operates independently. For example, if two people try to book the last seat on a flight at the same time, isolation ensures only one person gets it. Without isolation, both might see the seat as available and both would book it, causing overbooking.

Durability means once a transaction is committed, the changes are permanent, even if the system crashes immediately after. PostgreSQL guarantees this through Write-Ahead Logging. Before making any changes to the actual data files, PostgreSQL writes the changes to a WAL file on disk. If the server crashes after a commit, PostgreSQL can read the WAL during restart and replay all committed transactions to recover the data.

```python
# ACID example: Bank transfer transaction
BEGIN TRANSACTION;

-- Atomicity: Both operations succeed or both fail
UPDATE accounts SET balance = balance - 100 WHERE id = 1;  -- Debit
UPDATE accounts SET balance = balance + 100 WHERE id = 2;  -- Credit

COMMIT;
# If either UPDATE fails, entire transaction rolls back

# Consistency: Constraint violation rejected
BEGIN TRANSACTION;
UPDATE users SET age = -5 WHERE id = 1;
COMMIT;
# ERROR: violates constraint, transaction rejected

# Isolation: Two concurrent transfers don't interfere
-- Transaction 1
BEGIN;
UPDATE accounts SET balance = balance - 50 WHERE id = 1;

-- Transaction 2 (runs simultaneously)
BEGIN;
SELECT balance FROM accounts WHERE id = 1;  -- Sees original value during isolation

COMMIT;  -- Transaction 1 commits first

# Durability: Data survives crashes
BEGIN TRANSACTION;
INSERT INTO transactions (amount, status) VALUES (500, 'completed');
COMMIT;
# Even if server crashes now, this data is safe on disk via WAL
```
[Back to Table of Contents](#table-of-contents)

### 82. What is the difference between a PRIMARY KEY and a UNIQUE constraint in PostgreSQL? Can a table have multiple primary keys?

A PRIMARY KEY is a unique identifier for each row in a table. It must contain unique values and cannot contain NULL values. A table can have only one primary key, though that primary key can consist of multiple columns. This is called a composite primary key.

A UNIQUE constraint also ensures that all values in a column are unique, but unlike a PRIMARY KEY, it allows NULL values. A table can have multiple UNIQUE constraints on different columns.

For example, in a users table, we might have an id column as the PRIMARY KEY and a UNIQUE constraint on the email column to ensure that no two users have the same email address. We could also add another UNIQUE constraint on a username column if we need to prevent duplicate usernames as well.

The key differences are that a PRIMARY KEY cannot be NULL and we can only have one per table, while UNIQUE constraints can accept NULL values and we can have multiple of them. Additionally, the database automatically creates an index on a PRIMARY KEY, which improves query performance when we search by that key.

```python
# PRIMARY KEY - single column
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100)
);
# id must be unique and cannot be NULL

# PRIMARY KEY - composite (multiple columns)
CREATE TABLE user_roles (
    user_id INT,
    role_id INT,
    PRIMARY KEY (user_id, role_id)
);
# Combination of user_id and role_id must be unique

# UNIQUE constraint - allows NULL
CREATE TABLE users (
    id INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE,
    username VARCHAR(100) UNIQUE
);
# email can be NULL, but if not NULL must be unique
# Multiple UNIQUE constraints allowed on different columns

# Multiple NULL values allowed in UNIQUE column
INSERT INTO users (id, email, username) VALUES (1, NULL, 'alice');
INSERT INTO users (id, email, username) VALUES (2, NULL, 'bob');
# SUCCESS: Both NULL values allowed in email

# PRIMARY KEY rejects NULL
INSERT INTO users (id, email, username) VALUES (NULL, 'test@example.com', 'charlie');
# ERROR: PRIMARY KEY cannot be NULL
```
[Back to Table of Contents](#table-of-contents)

## Django

### 83. What is HTTP and how does it work? What are HTTP methods?

HTTP stands for HyperText Transfer Protocol. It's a protocol that defines the rules for client-server communication over the Internet. Without these rules, clients and servers wouldn't understand each other.

HTTP defines the structure of requests and responses. An HTTP request starts with a request line containing the HTTP method and the URI. The response starts with a status line containing a numeric code and text representation, like '200 OK' for success or '404 Not Found' for missing resources.

HTTP also defines methods that specify the action to perform. GET retrieves data, POST creates new data, PUT performs a full update of existing data, PATCH does a partial update, and DELETE removes data. HTTP is stateless, meaning each request is independent and doesn't remember previous interactions.

```python
import requests

# Example HTTP methods
base_url = "https://jsonplaceholder.typicode.com/posts"

# GET - retrieve data
response = requests.get(f"{base_url}/1")
print(f"GET: {response.status_code} - {response.json()['title']}")

# POST - create new data
new_post = {"title": "New Post", "body": "Content", "userId": 1}
response = requests.post(base_url, json=new_post)
print(f"POST: {response.status_code} - Created ID: {response.json()['id']}")

# PUT - full update
updated_post = {"id": 1, "title": "Updated", "body": "New content", "userId": 1}
response = requests.put(f"{base_url}/1", json=updated_post)
print(f"PUT: {response.status_code}")

# PATCH - partial update
response = requests.patch(f"{base_url}/1", json={"title": "Patched Title"})
print(f"PATCH: {response.status_code}")

# DELETE - remove data
response = requests.delete(f"{base_url}/1")
print(f"DELETE: {response.status_code}")
```
[Back to Table of Contents](#table-of-contents)

### 84. What is Django and why would you choose it over Flask?

Django is a high-level, open-source Python web framework that follows the MTV architecture, which stands for Model-Template-View. It is designed to help developers build web applications quickly and efficiently.

I would choose Django over Flask when I need a full-featured framework with built-in functionality. Django comes with many features out of the box, such as a powerful ORM for database operations, an automatic admin panel, built-in authentication and authorization, form handling and validation, and security features like CSRF protection.

While I have not worked professionally with Flask, I understand it is more lightweight and gives developers more flexibility in choosing components. Flask is great for smaller projects or when we need more control over the architecture. However, Django is better suited for larger, complex applications where having these built-in features saves significant development time.

The trade-off is that Django can feel more opinionated and has a steeper learning curve initially, but once we understand its structure, it makes development much faster and the codebase easier to maintain.

**Code Example:**

```python
# Django Model Example - ORM in action
from django.db import models
from django.contrib.auth.models import User

class BlogPost(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title

# Django automatically generates SQL and provides query methods
# No need to write raw SQL queries

# Example usage:
post = BlogPost.objects.create(
    title="Getting Started with Django",
    author=user_instance,
    content="Django makes web development fast and clean."
)

# Querying is simple and pythonic
recent_posts = BlogPost.objects.filter(created_at__year=2025).order_by('-created_at')
print(f"Found {recent_posts.count()} posts from 2025")
# Output: Found 15 posts from 2025

# The admin panel is automatically generated for this model
# Just register it: admin.site.register(BlogPost)
```
[Back to Table of Contents](#table-of-contents)

### 85. What is the difference between Django's MVT and MVC? How does data flow through a Django application when a user makes a request?

The main difference between MVT and MVC is in the naming. In Django's MVT pattern, Model equals Model, which handles data, same in both. View in MVT equals Controller in MVC, which handles business logic. And Template in MVT equals View in MVC, which handles presentation. So essentially, what MVC calls "View" is called "Template" in Django, and what MVC calls "Controller" is called "View" in Django.

When a user makes a request to a Django application, here is how the data flows. First, the request goes through the request middleware, specifically the process_request and process_view methods. Middleware can modify the request or return an early response for things like authentication or logging. Second, Django's URL dispatcher checks the urls.py file to find the matching URL pattern and determines which view function should handle the request. Third, the view function executes the business logic. If database operations are needed, the view uses Models to query or modify data through Django's ORM. Fourth, the view calls the render function, passing the request object, the template name, and any context data needed for the template. Fifth, Django renders the Template with the provided context data. The template uses template tags and filters to display the data. Sixth, the rendered template is wrapped in an HttpResponse object. Seventh, the response passes back through the response middleware, specifically the process_response method. Middleware can modify the response before it is sent to the user. Finally, the response is sent back to the user's browser.

The Template is responsible for the presentation layer, which is how the data is displayed to the user. It is important to note that middleware runs at different stages: some methods run before the view on the request, and others run after the view on the response.

**Code Example:**

```python
# urls.py - URL dispatcher
from django.urls import path
from . import views

urlpatterns = [
    path('blog/<int:post_id>/', views.blog_detail, name='blog_detail'),
]

# views.py - View (Controller in MVC)
from django.shortcuts import render
from .models import BlogPost

def blog_detail(request, post_id):
    # View uses Model to fetch data
    post = BlogPost.objects.get(id=post_id)

    # View prepares context and calls Template
    context = {
        'post': post,
        'author_name': post.author.username
    }
    return render(request, 'blog/detail.html', context)

# models.py - Model (Data layer)
from django.db import models

class BlogPost(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    author = models.ForeignKey('auth.User', on_delete=models.CASCADE)

# Template: blog/detail.html - Template (View in MVC - Presentation)
"""
<h1>{{ post.title }}</h1>
<p>By {{ author_name }}</p>
<div>{{ post.content }}</div>
"""

# Request flow example:
# User visits: /blog/5/
# URL dispatcher matches pattern and calls blog_detail(request, post_id=5)
# View queries Model: BlogPost.objects.get(id=5)
# View renders Template with context: {'post': post, 'author_name': 'Jane'}
# Output: HTML page with blog post title, author, and content displayed
```
[Back to Table of Contents](#table-of-contents)

### 86. What is Django ORM and what are its advantages? What is the difference between `filter()` and `get()` methods?

Django ORM, which stands for Object-Relational Mapping, is an interface that allows us to interact with the database using Python code instead of writing raw SQL queries. It provides a Pythonic way to perform CRUD operations on the database.

The main advantages of using Django ORM are several. First, we have single language focus. We can write everything in Python without switching to SQL, which makes development faster and code more maintainable. Second, it is database agnostic. The same code works with different databases like PostgreSQL, MySQL, or SQLite without modifications. Third, it provides readable and concise code. For example, using methods like select_related achieves the same result as SQL JOIN statements but in a much shorter and more readable way. Fourth, we get version control and migrations. Our database schema lives in Python files called migrations, not just in executed SQL commands. This gives us a history of all database changes and allows us to rollback to previous versions if needed. And fifth, we have built-in protections. ORM helps prevent SQL injection attacks automatically.

Regarding filter versus get, there are important differences. The get method returns a single model instance and hits the database immediately. We use it when we are certain that exactly one object with the given properties exists in the database. If no object is found, it raises a DoesNotExist exception. If multiple objects are found, it raises a MultipleObjectsReturned exception. On the other hand, filter returns a QuerySet, which can contain zero, one, or multiple objects. QuerySets are lazy, meaning they do not hit the database until they are evaluated, such as when we iterate over them, convert to list, access an index, or call methods like count. This allows us to chain multiple filters together before executing the query. If no objects match, it returns an empty QuerySet instead of raising an error.

**Code Example:**

```python
from django.core.exceptions import ObjectDoesNotExist
from myapp.models import BlogPost

# get() - returns single instance, hits DB immediately
try:
    post = BlogPost.objects.get(id=1)
    print(f"Found: {post.title}")
    # Output: Found: Introduction to Django
except ObjectDoesNotExist:
    print("Post not found")

# get() with multiple results raises error
try:
    post = BlogPost.objects.get(author__username="jane")
    # Raises MultipleObjectsReturned if jane has multiple posts
except BlogPost.MultipleObjectsReturned:
    print("Multiple posts found for this author")

# filter() - returns QuerySet, lazy evaluation
posts = BlogPost.objects.filter(author__username="jane")
print(f"QuerySet created but DB not hit yet: {type(posts)}")
# Output: QuerySet created but DB not hit yet: <class 'django.db.models.query.QuerySet'>

# Chain multiple filters - still no DB hit
posts = posts.filter(created_at__year=2025).filter(title__icontains="django")
print("Still no database query executed")

# DB is hit only when we evaluate the QuerySet
for post in posts:
    print(post.title)
# Output: Introduction to Django
# Output: Django Best Practices

# Empty QuerySet - no exception raised
empty = BlogPost.objects.filter(title="Nonexistent Post")
print(f"Found {empty.count()} posts")
# Output: Found 0 posts
```
[Back to Table of Contents](#table-of-contents)

### 87. What are Django signals? When would you use them and what are the drawbacks?

Django signals are a mechanism that allows certain senders to notify receivers when specific actions or events occur in the application. They follow the observer pattern. When an event happens, all connected receiver functions get executed automatically.

Django provides several built-in signals, such as pre_save and post_save, which are fired before or after a model's save method is called. There are also pre_delete and post_delete, which are fired before or after a model is deleted. And m2m_changed, which is fired when a many-to-many relationship changes.

Signals are useful for decoupled functionality. We use them when we want different parts of our application to react to events without creating tight dependencies between apps. A good example is when a new user registers. I can use a post_save signal to automatically create related UserProfile and UserPhoto records. This keeps the user creation logic separate from the profile creation logic.

However, there are drawbacks to using signals. First, we have implicit behavior. The code flow becomes harder to understand because signal receivers execute behind the scenes. Without reading comments or documentation, developers might not expect that additional code runs when they save a model. Second, there are performance issues. Signals run synchronously within the same request or transaction, which can slow down operations if the receiver does heavy processing. Third, we face difficult debugging. When something goes wrong, it is harder to trace through the execution flow and find where the problem occurred. And fourth, there is testing complexity. Signals can make tests more complicated because we need to account for side effects that happen automatically.

Generally, if the logic is core to our business flow, it is better to implement it directly in the view or model method rather than using signals.

**Code Example:**

```python
# models.py
from django.db import models
from django.contrib.auth.models import User

class UserProfile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField(blank=True)
    location = models.CharField(max_length=100, blank=True)

class UserPhoto(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    photo_url = models.URLField(default='https://example.com/default.jpg')

# signals.py
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User

@receiver(post_save, sender=User)
def create_user_related_records(sender, instance, created, **kwargs):
    if created:
        # Automatically create profile and photo when user is created
        UserProfile.objects.create(user=instance)
        UserPhoto.objects.create(user=instance)
        print(f"Profile and photo created for {instance.username}")

# Usage example
new_user = User.objects.create(username='alice', email='alice@example.com')
# Output: Profile and photo created for alice
# UserProfile and UserPhoto are created automatically without explicit code

# The implicit nature - developers might not expect this:
print(UserProfile.objects.filter(user=new_user).exists())
# Output: True (even though we never explicitly created it)

# Performance concern - if receiver does heavy work:
@receiver(post_save, sender=User)
def send_welcome_email(sender, instance, created, **kwargs):
    if created:
        # This runs synchronously and blocks the request
        # send_email(instance.email, 'Welcome!')
        pass
```
[Back to Table of Contents](#table-of-contents)

### 88. When and how do we perform a data migration?

Data migrations are needed when the database schema changes in a way that requires transforming existing data. For example, if we have not extended the Django User model from the beginning, we might later realize our authentication is slow because we are storing too much data in one table. We might have usernames, emails, passwords, profile photos, first name, last name, and date of birth all in the same User table. Filtering usernames and passwords across so many records becomes slow because the table is too large.

The solution is to create two separate tables: one for credentials like usernames and passwords, and one for profile data like photos, first name, last name, and date of birth. This makes queries faster because we only load the data we need.

To perform this migration in Django, we first create a migration file using `python manage.py makemigrations`. Then we write custom migration logic using `RunPython` to transform and move the existing data safely from the old table structure to the new one. We must thoroughly test the migration in a development or staging environment before applying it to production. We should always backup our data before performing migrations because if something goes wrong, we need to be able to restore the original data.

```python
# Data migration example in Django
from django.db import migrations
from django.db.migrations.operations.special import RunPython

def move_profile_data(apps, schema_editor):
    # Arrange - get the old User model
    User = apps.get_model('auth', 'User')
    Profile = apps.get_model('myapp', 'Profile')

    # Act - transform and move the data
    for user in User.objects.all():
        Profile.objects.create(
            user=user,
            first_name=user.first_name,
            last_name=user.last_name,
            profile_photo=user.profile_photo
        )

def reverse_migration(apps, schema_editor):
    Profile = apps.get_model('myapp', 'Profile')
    Profile.objects.all().delete()

class Migration(migrations.Migration):
    dependencies = [
        ('myapp', '0001_previous_migration'),
    ]

    # Assert - verify migration completes successfully
    operations = [
        RunPython(move_profile_data, reverse_migration),
    ]
```
[Back to Table of Contents](#table-of-contents)

### 89. What is the difference between `select_related()` and `prefetch_related()`? When would you use each one?

The main differences between select_related and prefetch_related are how they fetch data and which relationships they work with.

Let me start with select_related. It makes a single database query using SQL JOIN to fetch the related objects together with the main objects. It works with one-to-one relationships and forward ForeignKey relationships, where the ForeignKey field is on the model we are querying. It is generally faster because it is one query. For example, if I am fetching books and want their authors, where Book has a ForeignKey to Author, I would use Book.objects.select_related with author.

Now for prefetch_related. It makes multiple separate queries. One for the main objects, then additional queries for the related objects, and joins them together in Python. It works with many-to-many relationships and reverse ForeignKey relationships, which means accessing related objects from the other side of a ForeignKey. It is necessary for these relationship types because they cannot be efficiently joined in a single SQL query.

Here is why. For many-to-many relationships, these use an intermediate through table. If we JOIN all tables together, the main object's data gets duplicated in every row. For example, if Author 1 has 5 books, the author's name and all other fields appear 5 times in the result set. This creates a lot of duplicate data being transferred from the database. For reverse ForeignKey, we have a similar issue. If we query Authors and JOIN their books, each author's data repeats for every book they wrote. An author with 10 books would have their data duplicated 10 times.

Prefetch_related solves this by making separate queries, one for authors and one for books, and joining them in Python. This avoids duplicate data and is more efficient overall. For example, if I am fetching authors and want all their books, which is a reverse ForeignKey, I would use Author.objects.prefetch_related with book_set, or books if a related_name is defined.

Both methods help avoid the N+1 query problem where we would otherwise make a separate database query for each related object. Using these methods properly is crucial for good performance in Django applications.

```python
from myapp.models import Book, Author

# Setup: Book has ForeignKey to Author
# class Book(models.Model):
#     title = models.CharField(max_length=200)
#     author = models.ForeignKey(Author, on_delete=models.CASCADE, related_name='books')

# WITHOUT optimization - N+1 problem
books = Book.objects.all()
for book in books:
    print(f"{book.title} by {book.author.name}")
    # This hits the database once per book!
# Result: 1 query for books + N queries for each author = N+1 queries

# WITH select_related() - Forward ForeignKey (Book -> Author)
books = Book.objects.select_related('author').all()
for book in books:
    print(f"{book.title} by {book.author.name}")
# Result: 1 query using SQL JOIN
# SQL: SELECT book.*, author.* FROM book INNER JOIN author ON book.author_id = author.id

# WITH prefetch_related() - Reverse ForeignKey (Author -> Books)
authors = Author.objects.prefetch_related('books').all()
for author in authors:
    print(f"{author.name} wrote:")
    for book in author.books.all():
        print(f"  - {book.title}")
# Result: 2 separate queries
# Query 1: SELECT * FROM author
# Query 2: SELECT * FROM book WHERE author_id IN (1, 2, 3, ...)
# Python joins them together

# Many-to-many example
# class Book(models.Model):
#     genres = models.ManyToManyField(Genre, related_name='books')

books = Book.objects.prefetch_related('genres').all()
for book in books:
    print(f"{book.title}: {', '.join(g.name for g in book.genres.all())}")
# Result: 2 queries (books + genres), avoiding duplicate data
```
[Back to Table of Contents](#table-of-contents)

### 90. How does Django store user passwords?

Django does not store user passwords in raw form. Instead, it uses a hashing algorithm with salt to hash passwords before saving them to the database. Hashing is a one-way process that converts a password into a fixed-length string of characters that cannot be reversed back to the original password.

Django uses SHA256 with salt for this hashing. Salt is random data that is added to the password before hashing, which ensures that even if two users have the same password, their hashed values in the database are different. This protects against certain types of attacks.

The hashing process in Django is made deliberately slow and computationally expensive by applying the algorithm multiple times. This slowness is intentional because it makes brute-force attacks much harder. A brute-force attack is when someone tries to guess the password by testing many combinations. If hashing is fast, an attacker can test millions of combinations quickly. If hashing is slow, testing even a thousand combinations takes a very long time.

When a user logs in, Django takes the password they enter, hashes it using the same algorithm and salt, and compares the resulting hash with the hash stored in the database. If the hashes match, the password is correct. Django never stores or compares raw passwords. It only works with their hashed versions, which makes it much safer.

```python
# How Django handles passwords
from django.contrib.auth.models import User
from django.contrib.auth.hashers import make_password, check_password

# Creating a user - password is hashed automatically
user = User.objects.create_user(username='alice', password='mypassword123')
# Django hashes 'mypassword123' and stores the hash in the database
print(user.password)  # Output: pbkdf2_sha256$600000$...hash...

# Logging in - Django hashes the entered password and compares
entered_password = 'mypassword123'
is_correct = check_password(entered_password, user.password)
print(is_correct)  # Output: True

# If password is wrong
wrong_password = 'wrongpassword'
is_correct = check_password(wrong_password, user.password)
print(is_correct)  # Output: False

# Django never compares raw passwords
# This would not work as expected
print(entered_password == user.password)  # Output: False (comparing raw to hash)
```
[Back to Table of Contents](#table-of-contents)

### 91. What are Django middlewares? Explain the execution order and give an example of a custom middleware.

Django middlewares are classes that process requests and responses globally across our application. They sit between the web server and our views, allowing us to run code before the view is called or after the view returns a response. They are commonly used for authentication, security, logging, session handling, and modifying requests or responses.

Regarding execution order during the request-response cycle, when a request comes in, middlewares execute in the order they appear in the MIDDLEWARE setting in settings.py, which is top to bottom. When the response goes back, they execute in reverse order, which is bottom to top.

The specific methods are as follows. First, process_request runs before Django determines which view to execute, going top to bottom. Second, process_view runs just before the view is called, also top to bottom. Then the view executes. After that, process_exception runs if the view raises an exception, going bottom to top. And finally, process_response runs after the view returns a response, also bottom to top.

Examples of built-in middlewares include AuthenticationMiddleware, which associates users with requests. SessionMiddleware, which enables session support. CsrfViewMiddleware, which protects against CSRF attacks. And SecurityMiddleware, which adds security-related headers.

**Code Example:**

```python
# custom_middleware.py
import time
from django.utils.deprecation import MiddlewareMixin

class RequestTimingMiddleware(MiddlewareMixin):
    """Custom middleware to log request processing time"""

    def process_request(self, request):
        # Runs before view - store start time
        request.start_time = time.time()
        print(f"[REQUEST] {request.method} {request.path} started")
        return None  # Continue to next middleware/view

    def process_view(self, request, view_func, view_args, view_kwargs):
        # Runs just before view executes
        print(f"[VIEW] About to call {view_func.__name__}")
        return None

    def process_response(self, request, response):
        # Runs after view returns response
        if hasattr(request, 'start_time'):
            duration = time.time() - request.start_time
            print(f"[RESPONSE] {request.path} completed in {duration:.3f}s")
            response['X-Request-Duration'] = str(duration)
        return response

    def process_exception(self, request, exception):
        # Runs if view raises an exception
        print(f"[ERROR] {request.path} raised {type(exception).__name__}: {exception}")
        return None  # Let Django handle the exception

# settings.py configuration
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',  # 1st in, last out
    'myapp.middleware.RequestTimingMiddleware',       # 2nd in, 2nd to last out
    'django.middleware.common.CommonMiddleware',      # 3rd in, 3rd to last out
    # ... more middlewares
]

# Example execution flow:
# Request arrives -> SecurityMiddleware.process_request()
#                 -> RequestTimingMiddleware.process_request() (prints "REQUEST started")
#                 -> CommonMiddleware.process_request()
#                 -> View executes
#                 -> CommonMiddleware.process_response()
#                 -> RequestTimingMiddleware.process_response() (prints duration)
#                 -> SecurityMiddleware.process_response()
# Response sent back

# Output example:
# [REQUEST] GET /api/posts/ started
# [VIEW] About to call post_list
# [RESPONSE] /api/posts/ completed in 0.045s
```
[Back to Table of Contents](#table-of-contents)

### 92. What is the difference between `Form` and `ModelForm`? When to use each? How does form validation work and how do you add custom validation?

The main difference between Form and ModelForm is how we define the fields.

With Form, we must define each field manually in the form class. We have full control over which fields to include and their properties.

With ModelForm, we only need to specify which model to use in the Meta class. Django automatically creates form fields based on the model's fields, including their types and validators. All model validations are automatically applied to the form as well.

Regarding when to use each, we use ModelForm when we need CRUD operations over a model, such as creating or editing database objects. We use Form when no model is involved, for example, a search form, contact form, or login form.

Now let me explain the form validation process. Form validation happens in several stages.

First is type coercion. User input comes as strings from the HTTP request and must be converted to the appropriate Python type, for example, string to integer.

Second are field-level validators. Built-in validators from the field definition run, like max_length, required, and so on.

Third are the clean_field_name methods. These validate individual fields separately. For example, clean_email validates just the email field.

Fourth is the clean method. This validates multiple fields together for cross-field validation. We use this when we need to check relationships between fields based on business logic, for example, ensuring end_date is after start_date.

Fifth, if we are using ModelForm, there is model validation. The model's clean method and field validators run.

For adding custom validation, there are two main ways.

First is using the clean_field_name method in the form. For example, we can define clean_email to check if the email ends with a specific domain, and raise a ValidationError if it does not.

Second is writing custom validator functions and applying them to fields. We can create a function like validate_positive that checks if a value is greater than zero, and then add it to the validators list of a field.

If we are using a ModelForm, it is usually better to add validators at the model level so they apply everywhere that model is used, not just in the form.

```python
from django import forms
from django.core.exceptions import ValidationError
from django.contrib.auth.models import User

# Custom validator function
def validate_positive(value):
    if value <= 0:
        raise ValidationError('Value must be positive')

# Regular Form - manual field definitions
class ContactForm(forms.Form):
    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    age = forms.IntegerField(validators=[validate_positive])
    message = forms.CharField(widget=forms.Textarea)

    # Field-level validation
    def clean_email(self):
        email = self.cleaned_data['email']
        if not email.endswith('@company.com'):
            raise ValidationError('Email must be from company.com domain')
        return email

    # Cross-field validation
    def clean(self):
        cleaned_data = super().clean()
        age = cleaned_data.get('age')
        message = cleaned_data.get('message')

        if age and age < 18 and len(message) > 500:
            raise ValidationError('Users under 18 cannot send messages longer than 500 characters')

        return cleaned_data

# Model and ModelForm
from django.db import models

class BlogPost(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    publish_date = models.DateField()
    end_date = models.DateField()

class BlogPostForm(forms.ModelForm):
    class Meta:
        model = BlogPost
        fields = ['title', 'content', 'publish_date', 'end_date']

    # Custom validation for ModelForm
    def clean(self):
        cleaned_data = super().clean()
        publish_date = cleaned_data.get('publish_date')
        end_date = cleaned_data.get('end_date')

        if publish_date and end_date and end_date <= publish_date:
            raise ValidationError('End date must be after publish date')

        return cleaned_data

# Usage example
form = ContactForm(data={'name': 'Alice', 'email': 'alice@company.com', 'age': 25, 'message': 'Hello'})
if form.is_valid():
    print("Form is valid!")
    print(form.cleaned_data)
    # Output: Form is valid!
    # Output: {'name': 'Alice', 'email': 'alice@company.com', 'age': 25, 'message': 'Hello'}
else:
    print(form.errors)

# Invalid example
invalid_form = ContactForm(data={'name': 'Bob', 'email': 'bob@gmail.com', 'age': -5, 'message': 'Hi'})
print(invalid_form.errors)
# Output: {'email': ['Email must be from company.com domain'], 'age': ['Value must be positive']}
```
[Back to Table of Contents](#table-of-contents)

### 93. What are class-based views, or CBVs? What advantages do they have over function-based views, or FBVs? Name some generic CBVs.

Django class-based views are classes that inherit from Django's View class. They provide an object-oriented approach to writing views.

Let me explain how CBVs work internally. The View class defines the as_view method, which is the entry point for each class-based view. When we call MyView.as_view in our urls.py, it returns a view function. When a request arrives, this function creates an instance of the class and calls the dispatch method. The dispatch method checks which HTTP method is in the request, such as GET, POST, PUT, DELETE, and so on, and looks for a corresponding method in our class. For example, if the request method is POST, dispatch checks if we have a post method defined. If it exists, that method becomes the handler and executes. If not, Django returns a Method Not Allowed error.

Now for the advantages of CBVs over FBVs. First, we have code reusability. CBVs can inherit from other classes and override specific methods, allowing us to reuse common logic across multiple views. Second, we have mixins. We can combine multiple behaviors by using mixins like LoginRequiredMixin or PermissionRequiredMixin. Third, they follow the DRY principle. Generic views handle common patterns like CRUD operations so we do not repeat the same code. Fourth, we have better organization. Related HTTP methods like GET and POST live together in one class. And fifth, they provide built-in functionality. Django's generic views provide tested, ready-to-use solutions.

Regarding built-in generic class-based views, there are several. ListView has a get method. We use it to display a list of objects, for example, a list of all books. DetailView has a get method. We use it to display details of a single object, for example, one book's details. CreateView has get and post methods. We use it to create new objects with a form. UpdateView has get and post methods. We use it to edit existing objects. DeleteView has get and post methods. We use it to delete objects with confirmation. FormView has get and post methods. We use it for forms not tied to models. And TemplateView has a get method. We use it to render a simple template.

When deciding between CBVs and FBVs, we use generic CBVs when we have standard CRUD operations without complex custom logic. We use FBVs when the view logic is simple and straightforward, or when CBVs would make it more complicated. We can also inherit directly from the View class when we need custom logic but still want the benefits of the class structure.

**Code Example:**

```python
from django.views.generic import ListView, DetailView, CreateView
from django.views import View
from django.shortcuts import render
from django.http import JsonResponse
from .models import Book
from .forms import BookForm

# Generic ListView - displays all books
class BookListView(ListView):
    model = Book
    template_name = 'books/list.html'
    context_object_name = 'books'

    # Internally calls get() method which queries Book.objects.all()

# Generic DetailView - displays single book
class BookDetailView(DetailView):
    model = Book
    template_name = 'books/detail.html'
    context_object_name = 'book'

    # Internally calls get() method and gets object by pk from URL

# Generic CreateView - form to create new book
class BookCreateView(CreateView):
    model = Book
    form_class = BookForm
    template_name = 'books/form.html'
    success_url = '/books/'

    # Has get() to display form and post() to handle submission

# Custom View inheriting from View class
class BookStatsView(View):
    def get(self, request, *args, **kwargs):
        total_books = Book.objects.count()
        return JsonResponse({'total_books': total_books})

    def post(self, request, *args, **kwargs):
        # Custom POST logic
        return JsonResponse({'message': 'POST not allowed'}, status=405)

# urls.py configuration
from django.urls import path

urlpatterns = [
    path('books/', BookListView.as_view(), name='book-list'),
    path('books/<int:pk>/', BookDetailView.as_view(), name='book-detail'),
    path('books/create/', BookCreateView.as_view(), name='book-create'),
    path('books/stats/', BookStatsView.as_view(), name='book-stats'),
]

# When request arrives at /books/:
# 1. BookListView.as_view() returns a view function
# 2. View function creates instance: instance = BookListView()
# 3. Calls instance.dispatch(request)
# 4. dispatch() sees GET request, calls instance.get()
# 5. get() queries Book.objects.all() and renders template
# Output: HTML page with list of all books

# Example with mixins
from django.contrib.auth.mixins import LoginRequiredMixin

class ProtectedBookListView(LoginRequiredMixin, ListView):
    model = Book
    template_name = 'books/list.html'
    # Now requires user to be logged in before accessing
```
[Back to Table of Contents](#table-of-contents)

### 94. What is the difference between `null=True` and `blank=True` in Django model fields? When would you use each?

Null equals True and blank equals True operate at different levels and serve different purposes.

Null equals True is at the database level. It allows the database column to store NULL values. It affects how data is stored in the database. When null equals False, which is the default, the database column must have a value.

Blank equals True is at the validation level. It allows the field to be empty in forms and during validation. It affects form validation, specifically whether the field is required when users submit forms. When blank equals False, which is the default, the field is required in forms. It has no effect on the database.

Now let me explain the common combinations.

First, null equals True and blank equals True. The field is optional both in the database and in forms. We use this for truly optional fields like a user's middle name or phone number.

Second, null equals False and blank equals True. The field can be empty in forms but cannot be NULL in the database. We must provide a default value at the model level. For example, a created_at field with DateTimeField can have blank equals True with default equals timezone.now. The form does not require it, but it always gets a value when saved.

Third, null equals True and blank equals False. The field is optional in the database but required in forms. This is less common but useful when a field might be populated programmatically rather than through forms, or when we want to allow NULL in the database but force users to provide a value through forms.

Fourth, null equals False and blank equals False, which is the default. The field is required both in forms and in the database.

An important note for text fields. For CharField and TextField, Django convention is to use blank equals True with default equals empty string instead of null equals True. This avoids having two empty states, NULL and empty string.

**Code Example:**

```python
from django.db import models
from django.utils import timezone

class UserProfile(models.Model):
    # Required field (default: null=False, blank=False)
    username = models.CharField(max_length=100)
    # Database requires value, form requires value

    # Optional in both forms and database (null=True, blank=True)
    middle_name = models.CharField(max_length=100, null=True, blank=True)
    phone_number = models.CharField(max_length=20, null=True, blank=True)
    # Can be empty in forms, can be NULL in database

    # Optional in forms, but has default value (null=False, blank=True)
    created_at = models.DateTimeField(blank=True, default=timezone.now)
    bio = models.TextField(blank=True, default='')
    # Form does not require it, but database always has a value

    # Required in forms, optional in database (null=True, blank=False)
    # Less common - might be set programmatically
    admin_notes = models.TextField(null=True, blank=False)
    # User must provide value in form, but can be NULL if set via code

# Testing the behavior
from django import forms

class UserProfileForm(forms.ModelForm):
    class Meta:
        model = UserProfile
        fields = ['username', 'middle_name', 'phone_number', 'created_at', 'bio']

# Creating instances
profile1 = UserProfile.objects.create(username='alice')
print(f"Username: {profile1.username}")
print(f"Middle name: {profile1.middle_name}")  # Output: None (NULL in database)
print(f"Bio: {profile1.bio}")  # Output: '' (empty string, not NULL)
print(f"Created at: {profile1.created_at}")  # Output: 2025-10-13 10:30:45.123456

# Form validation
form_data = {'username': 'bob', 'middle_name': '', 'bio': ''}
form = UserProfileForm(data=form_data)
print(f"Form valid: {form.is_valid()}")  # Output: True
# middle_name and bio can be empty in forms

form_data_invalid = {'middle_name': 'John'}  # missing required username
form_invalid = UserProfileForm(data=form_data_invalid)
print(f"Form valid: {form_invalid.is_valid()}")  # Output: False
print(form_invalid.errors)  # Output: {'username': ['This field is required.']}
```
[Back to Table of Contents](#table-of-contents)

### 95. What is Django's `Q` object? How does it differ from `F` object? Explain `&` versus `|` operators.

Q objects are used to create complex database queries with OR logic and combined conditions. Without Q objects, Django's filter method only allows AND conditions when we chain multiple filters.

When using Q with the ampersand and pipe operators, the ampersand operator represents AND, meaning all conditions must be True. The pipe operator represents OR, meaning at least one condition must be True. There is also the tilde operator, which represents NOT and negates the condition.

For example, to find users who are either staff OR superuser, we use Q with the pipe operator. To find users who are staff AND active, we use Q with the ampersand operator, though this is the same as using regular filter with both conditions. For complex combinations, like staff users OR active users who are not banned, we can nest Q objects with these operators.

The main benefit of Q objects is allowing OR conditions and complex nested logic that regular filter chaining cannot achieve.

Now for F objects. F objects are used to reference field values directly in database queries without loading data into Python first. They allow us to compare two fields in the same model, update fields based on their current values, and perform database-level operations, which is more efficient and prevents race conditions.

For example, to find products where price is greater than cost, we use F to reference the cost field. To find employees where salary is less than their bonus, we use F to reference the bonus field. To increase all product prices by ten percent, we use F in an update operation at the database level. To increase a view count by one, we use F to prevent race conditions by doing the increment at the database level.

The key difference between Q and F is this. Q objects are used for building complex query conditions, which means filtering logic with OR, AND, and NOT. F objects are used for referencing field values in queries and updates, which means field comparisons and calculations.

Q objects help us find records based on complex criteria. F objects help us work with actual field values without loading them into Python, making operations faster and safer.

**Code Example:**

```python
from django.db.models import Q, F
from myapp.models import User, Product, Article

# Q objects - Complex query conditions

# OR condition: staff OR superuser
staff_or_super = User.objects.filter(Q(is_staff=True) | Q(is_superuser=True))
print(f"Staff or superusers: {staff_or_super.count()}")
# Output: Staff or superusers: 15

# AND condition: staff AND active
staff_and_active = User.objects.filter(Q(is_staff=True) & Q(is_active=True))
print(f"Active staff: {staff_and_active.count()}")
# Output: Active staff: 8

# NOT condition: not banned
not_banned = User.objects.filter(~Q(is_banned=True))
print(f"Not banned users: {not_banned.count()}")
# Output: Not banned users: 142

# Complex nested: staff OR (active AND not banned)
complex_query = User.objects.filter(
    Q(is_staff=True) | (Q(is_active=True) & ~Q(is_banned=True))
)
print(f"Complex query results: {complex_query.count()}")
# Output: Complex query results: 138

# F objects - Field reference and calculations

# Compare two fields: price > cost
profitable = Product.objects.filter(price__gt=F('cost'))
print(f"Profitable products: {profitable.count()}")
# Output: Profitable products: 87

# Field comparison: salary < bonus
lucky_employees = Employee.objects.filter(salary__lt=F('bonus'))
print(f"Employees with bonus > salary: {lucky_employees.count()}")
# Output: Employees with bonus > salary: 12

# Database-level update: increase all prices by 10%
Product.objects.update(price=F('price') * 1.1)
print("All prices increased by 10%")
# Output: All prices increased by 10%

# Prevent race condition: increment view count
Article.objects.filter(pk=1).update(views=F('views') + 1)
article = Article.objects.get(pk=1)
print(f"Article views: {article.views}")
# Output: Article views: 156
# This happens at database level, avoiding race conditions from multiple requests
```
[Back to Table of Contents](#table-of-contents)

### 96. What are Django migrations? What is the difference between `makemigrations` and `migrate`? How do you handle migration conflicts?

Django migrations are Python files that record changes to your database schema. They allow us to version control our database structure and keep it synchronized across different environments and team members.

Each migration file contains operations like creating tables, adding fields, or deleting columns, and has a dependency chain. It knows which migration must run before it. This ensures changes are applied in the correct order.

Regarding makemigrations versus migrate, makemigrations examines our models and compares them to the current migration files. If it detects changes, it creates new migration files in our app's migrations folder. These files are not yet applied to the database. They just describe what changes need to happen. On the other hand, migrate takes all unapplied migrations and executes them against the database, actually modifying the database schema. Django tracks which migrations have been applied in a table called django_migrations.

The benefits of migrations include version control, where database schema lives in code, not just in executed SQL. Team collaboration, where when teammates clone the repository, they can recreate the exact database state by running migrate. Rollback capability, where we can migrate backward to previous states if needed. And deployment, which ensures production databases stay synchronized with code changes.

For modifying existing migrations, there is a critical rule. Never edit or delete migrations that have been committed, shared with the team, or deployed to production. This will cause inconsistencies across environments. For local-only migrations that are not yet committed or shared, we can delete the migration file, modify our model, and run makemigrations again. For shared migrations, we must create a new migration to fix the issue instead of editing the old one.

Migration conflicts happen when two developers create migrations in parallel, usually on different Git branches. When these branches merge, we might have two migrations with the same number or missing dependencies.

There are solutions for this. First is merge migrations. Django can detect conflicts and help us create a merge migration using the command python manage.py makemigrations with the merge flag. This creates a new migration that depends on both conflicting migrations. Second is manual resolution. If automatic merge does not work, we might need to manually adjust dependencies in migration files.

There are other useful migration commands as well. We can use migrate with app_name and migration_name to migrate to a specific migration, forward or backward. We can use migrate with app_name and zero to unapply all migrations for an app. We can use migrate with the fake flag to mark migrations as applied without actually running them, which is useful in specific scenarios like when we have manually made database changes. We can use squashmigrations to combine many old migrations into fewer files to reduce clutter. And we can create data migrations that modify data, not just schema, using RunPython operations.

**Code Example:**

```python
# Step 1: Create a model
# models.py
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.CharField(max_length=100)

# Step 2: Create initial migration
# Command: python manage.py makemigrations
# Output: Migrations for 'myapp':
#   myapp/migrations/0001_initial.py
#     - Create model Book

# Generated migration file: 0001_initial.py
"""
from django.db import migrations, models

class Migration(migrations.Migration):
    initial = True
    dependencies = []

    operations = [
        migrations.CreateModel(
            name='Book',
            fields=[
                ('id', models.BigAutoField(primary_key=True)),
                ('title', models.CharField(max_length=200)),
                ('author', models.CharField(max_length=100)),
            ],
        ),
    ]
"""

# Step 3: Apply migration to database
# Command: python manage.py migrate
# Output: Running migrations:
#   Applying myapp.0001_initial... OK

# Step 4: Add new field to model
# models.py
class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.CharField(max_length=100)
    published_date = models.DateField(null=True)  # New field

# Step 5: Create new migration
# Command: python manage.py makemigrations
# Output: Migrations for 'myapp':
#   myapp/migrations/0002_book_published_date.py
#     - Add field published_date to book

# Step 6: Check migration status
# Command: python manage.py showmigrations
# Output:
# myapp
#  [X] 0001_initial
#  [ ] 0002_book_published_date

# Step 7: Apply new migration
# Command: python manage.py migrate
# Output: Applying myapp.0002_book_published_date... OK

# Rollback example
# Command: python manage.py migrate myapp 0001_initial
# Output: Unapplying myapp.0002_book_published_date... OK

# Handling conflict: Developer A and B create migrations simultaneously
# Developer A: 0002_add_isbn.py
# Developer B: 0002_add_price.py (same number, conflict!)

# Resolution command: python manage.py makemigrations --merge
# Output: Merging will only work if the operations do not conflict.
#   Created new merge migration myapp/migrations/0003_merge_20251013_1200.py

# Data migration example using RunPython
"""
from django.db import migrations

def set_default_author(apps, schema_editor):
    Book = apps.get_model('myapp', 'Book')
    Book.objects.filter(author='').update(author='Unknown')

class Migration(migrations.Migration):
    dependencies = [('myapp', '0002_book_published_date')]

    operations = [
        migrations.RunPython(set_default_author),
    ]
"""
```
[Back to Table of Contents](#table-of-contents)

### 97. What is `related_name` in ForeignKey/ManyToMany relationships?

I define `related_name` as the name of the reverse relationship from the related model back to the model that defines the relationship. It allows us to query backwards through a foreign key relationship.

In Django, when we create a ForeignKey, we get a forward relationship using the field name itself. The `related_name` controls how we access the reverse relationship from the other side. For example, if we have an Author model and a Book model with a ForeignKey to Author, we can use the field name to go from book to author. But with `related_name`, we can go from author to all their books with a clean, readable name instead of Django's default automatic name.

If we do not specify `related_name`, Django automatically creates one using the pattern of the model name followed by underscore set, like `book_set`. This works, but it is not very readable. By specifying `related_name` explicitly, we make our code more intuitive.

Another important reason to use `related_name` is when we have multiple ForeignKeys pointing to the same model. Without different `related_name` values for each relationship, Django would not know which relationship we are referring to, and we would get conflicts. So `related_name` becomes required in those cases.

The benefits are clearer code readability, avoiding naming conflicts, and making our queries more expressive and easier to understand.

```python
from django.db import models

class Author(models.Model):
    name = models.CharField(max_length=100)

    def __repr__(self):
        return f"Author(name='{self.name}')"

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, related_name='books', on_delete=models.CASCADE)

    def __repr__(self):
        return f"Book(title='{self.title}', author='{self.author.name}')"

# Simulating database with in-memory objects
author = Author(name='J.K. Rowling')
book1 = Book(title='Harry Potter 1', author=author)
book2 = Book(title='Harry Potter 2', author=author)

# Forward relationship: book to author
print(book1.author)  # Output: Author(name='J.K. Rowling')

# Reverse relationship: author to books (using related_name)
print(f"Books by {author.name}: {[book1, book2]}")  # Output: Books by J.K. Rowling: [Book(title='Harry Potter 1', author='J.K. Rowling'), Book(title='Harry Potter 2', author='J.K. Rowling')]

# Without related_name, we would use: author.book_set.all()
# With related_name='books', we use: author.books.all() - much cleaner!

# Multiple ForeignKeys example - related_name is required
class BookMultiFK(models.Model):
    title = models.CharField(max_length=200)
    primary_author = models.ForeignKey(Author, related_name='primary_books', on_delete=models.CASCADE)
    co_author = models.ForeignKey(Author, related_name='co_authored_books', on_delete=models.CASCADE, null=True)

# Without different related_name values, Django would raise an error
print("\nMultiple ForeignKeys require different related_name values to avoid conflicts")
```
[Back to Table of Contents](#table-of-contents)

### 98. What is the N+1 query problem? How do you identify and fix it in Django?

The N+1 query problem occurs when we fetch a list of objects with one query, and then execute an additional database query for each object to fetch related data. This results in one query plus N additional queries, which is very inefficient and can severely impact application performance.

Let me explain with a concrete scenario. Imagine we fetch all authors from the database with one query. Then, when we iterate through each author to display their books, we execute a separate query for each author to fetch their books. If we have one hundred authors, we end up with one hundred and one queries total instead of just one or two well-optimized queries.

To identify the N+1 problem, we can use several methods. The Django Debug Toolbar is excellent during development because it shows all queries executed and highlights duplicate query patterns. We can also check the `django.db.connection.queries` object when DEBUG mode is enabled. Another approach is to look for any code where we access related objects inside a loop, which is often a sign of an N+1 problem. In production, we should use application performance monitoring tools like New Relic or Sentry to catch these issues.

To fix the N+1 problem, we have two main solutions. First, we use `select_related()` for forward ForeignKey and OneToOne relationships. This method makes one database query using a SQL JOIN to fetch related objects together with the main objects. Second, we use `prefetch_related()` for ManyToMany and reverse ForeignKey relationships. This method makes two separate queries, one for the main objects and one for all related objects, then joins them in Python. We can also combine both methods in a single query when we have nested relationships. For more advanced control, we can use the Prefetch object to filter which related objects we want to fetch.

The key difference is that `select_related()` uses SQL JOINs and is efficient for one-to-one relationships, while `prefetch_related()` uses separate queries joined in Python and is better for many-to-many relationships. Both approaches dramatically reduce the total number of database queries.

```python
from django.db import models
from django.db.models import Prefetch

class Author(models.Model):
    name = models.CharField(max_length=100)

    def __repr__(self):
        return f"Author('{self.name}')"

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, related_name='books', on_delete=models.CASCADE)
    published = models.BooleanField(default=True)

    def __repr__(self):
        return f"Book('{self.title}')"

# Simulating database with in-memory objects
author1 = Author(name='J.K. Rowling')
author2 = Author(name='George R.R. Martin')
book1 = Book(title='Harry Potter 1', author=author1, published=True)
book2 = Book(title='Harry Potter 2', author=author1, published=True)
book3 = Book(title='Game of Thrones', author=author2, published=True)

# PROBLEM: N+1 query pattern (simulated with comments)
print("=== N+1 Problem ===")
authors = [author1, author2]  # 1 query
for author in authors:
    books = [b for b in [book1, book2, book3] if b.author == author]  # N queries
    print(f"{author.name}: {len(books)} books")
# Total: 1 + 2 = 3 queries

# SOLUTION 1: select_related() for forward ForeignKey
print("\n=== Using select_related() ===")
books = [book1, book2, book3]  # Using select_related would be: Book.objects.select_related('author')
for book in books:
    print(f"{book.title} by {book.author.name}")  # No additional queries!

# SOLUTION 2: prefetch_related() for reverse ForeignKey
print("\n=== Using prefetch_related() ===")
authors = [author1, author2]  # Using prefetch_related would be: Author.objects.prefetch_related('books')
for author in authors:
    books = [b for b in [book1, book2, book3] if b.author == author]  # No additional queries!
    print(f"{author.name}: {[book.title for book in books]}")

# SOLUTION 3: prefetch_related() with Prefetch for advanced filtering
print("\n=== Using Prefetch for filtered related objects ===")
published_books_prefetch = Prefetch('books', queryset=Book.objects.filter(published=True))
# In real Django: authors = Author.objects.prefetch_related(published_books_prefetch)
# This would fetch only published books for each author
for author in authors:
    published = [b for b in [book1, book2, book3] if b.author == author and b.published]
    print(f"{author.name} - Published: {[book.title for book in published]}")
```
[Back to Table of Contents](#table-of-contents)

### 99. What are Django managers and querysets? How do you create a custom manager and why?

A manager is the interface through which we access database query operations on a Django model. Every model has at least one manager, which is called `objects` by default. Managers are attached to the model class itself, not to individual instances, and they provide methods like `all()`, `filter()`, and `create()`.

A QuerySet is a collection that represents a set of objects from our database. QuerySets are returned by manager methods, and they have two important characteristics. First, they are lazy, meaning they do not hit the database until we actually evaluate them. Second, they are chainable, which means we can add more filters and operations to them in sequence.

We create custom managers when we need to execute complex queries repeatedly throughout our application, when we want to add custom methods for retrieving objects in specific ways, or when we need to modify the default queryset behavior. For example, we might always want to exclude soft-deleted items from certain queries. Custom managers also help us encapsulate business logic related to object retrieval, which keeps our views and business logic cleaner and more maintainable.

There are two main approaches to creating a custom manager. We can override the default manager entirely by creating a custom manager class and assigning it to `objects`. Alternatively, we can keep the default manager and add additional custom managers alongside it. The second approach is often better because it gives us flexibility.

For more complex scenarios, we can create a custom QuerySet class that defines reusable query methods, and then create a manager that uses that QuerySet. This approach follows the DRY principle because we write complex queries once and reuse them everywhere. It also improves performance by ensuring we use proper prefetching and annotations, and it makes our code more readable because method names like `in_stock()` are clearer than repeating the same filters throughout our application. Additionally, custom managers make our query logic easier to test in isolation.

```python
from django.db import models
from django.db.models import QuerySet, Manager, Avg, Min, Max

# Custom QuerySet with reusable query methods
class ProductQuerySet(QuerySet):
    def in_stock(self):
        """Filter products that have inventory"""
        return self.filter(inventory_quantity__gt=0)

    def with_details(self):
        """Optimized query with aggregations"""
        return self.annotate(
            average_rating=Avg('reviews__rating'),
            min_price=Min('price'),
        )

# Custom Manager that uses the ProductQuerySet
class ProductManager(Manager):
    def get_queryset(self):
        return ProductQuerySet(self.model, using=self._db)

    def in_stock(self):
        return self.get_queryset().in_stock()

    def with_details(self):
        return self.get_queryset().with_details()

# Model using custom manager
class Product(models.Model):
    name = models.CharField(max_length=200)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    inventory_quantity = models.IntegerField(default=0)

    objects = ProductManager()

    def __repr__(self):
        return f"Product('{self.name}', price={self.price}, in_stock={self.inventory_quantity > 0})"

# Simulating database with in-memory objects
product1 = Product(name='Laptop', price=1500.00, inventory_quantity=5)
product2 = Product(name='Mouse', price=25.00, inventory_quantity=0)
product3 = Product(name='Keyboard', price=80.00, inventory_quantity=15)

print("=== Using custom manager methods ===")
print(f"Product 1: {product1}")
print(f"Product 2: {product2}")
print(f"Product 3: {product3}")

# In real Django, we would use:
# all_products = Product.objects.all()
# in_stock_products = Product.objects.in_stock()
# detailed_products = Product.objects.with_details()

print("\n=== Chainable QuerySet methods ===")
in_stock = [product1, product3]  # Simulating Product.objects.in_stock()
print(f"In stock products: {[p.name for p in in_stock]}")

with_details = [p for p in in_stock]  # Simulating .with_details()
print(f"With details: {[p.name for p in with_details]}")

print("\n=== Benefits ===")
print("DRY principle: Complex queries written once, reused everywhere")
print("Performance: Optimized queries with proper prefetching and annotations")
print("Readability: Product.objects.in_stock() is clearer than repeating filters")
print("Maintainability: Easy to update query logic in one place")
```
[Back to Table of Contents](#table-of-contents)

### 100. What is `on_delete` in ForeignKey relationships? Explain the different options and when to use each.

The `on_delete` parameter is mandatory when we define a ForeignKey field in Django models. It specifies what should happen to the object that contains the ForeignKey when the referenced object is deleted. This is an important distinction: the action happens to the object with the ForeignKey, not to the referenced object itself.

For example, if we have an Author model and a Book model with a ForeignKey to Author, the `on_delete` parameter determines what happens to the books when we delete an author.

We have several options for `on_delete` behavior, and choosing the right one depends on our specific use case. Let me explain each option.

CASCADE is the most common choice. It automatically deletes all related objects when the referenced object is deleted. If we delete an author, all their books are also deleted. We use CASCADE when the child object does not make sense without the parent, such as order items without an order or comments on a deleted post.

PROTECT prevents deletion of the referenced object if any related objects exist. When we try to delete an author who has books, Django raises a ProtectedError exception. We use PROTECT when we want to prevent accidental deletion of important data, such as not deleting a category if products exist or not deleting a company if employees exist.

SET_NULL sets the ForeignKey field to NULL when the referenced object is deleted. The related objects remain in the database with a null reference. This option requires the field to have `null=True`. We use SET_NULL when the relationship is optional and we want to keep the related objects, such as deleting a user but keeping their comments as anonymous, or deleting a manager but keeping employee records intact.

SET_DEFAULT sets the ForeignKey to a default value when the referenced object is deleted. This option requires a `default` parameter on the field. We use SET_DEFAULT when we want to reassign orphaned objects to a default parent, such as reassigning books to an unknown author when the original author is deleted.

SET allows us to set the field to a specific value or the result of calling a function. This option gives us custom logic for reassignment, and we use it when we need special handling beyond a simple default value.

DO_NOTHING does nothing automatically, which can cause database integrity errors if the database has foreign key constraints. This option is rarely used and generally not recommended.

RESTRICT is similar to PROTECT and prevents deletion if there are related objects. We use it for similar reasons as PROTECT, to prevent accidental deletions.

The key is to think about your data relationships and choose the behavior that makes sense for your application.

```python
from django.db import models

class Author(models.Model):
    name = models.CharField(max_length=100)

    def __repr__(self):
        return f"Author('{self.name}')"

class UnknownAuthor(models.Model):
    name = models.CharField(max_length=100, default='Unknown Author')

# Different on_delete strategies

# 1. CASCADE - delete all books when author is deleted
class Book_CASCADE(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)

    def __repr__(self):
        return f"Book('{self.title}', author='{self.author.name}')"

# 2. SET_NULL - keep book but set author to null
class Book_SET_NULL(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, on_delete=models.SET_NULL, null=True)

    def __repr__(self):
        author_name = self.author.name if self.author else "None (Anonymous)"
        return f"Book('{self.title}', author={author_name})"

# 3. SET_DEFAULT - reassign to default author
class Book_SET_DEFAULT(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, on_delete=models.SET_DEFAULT, default=1)

    def __repr__(self):
        return f"Book('{self.title}', author='{self.author.name}')"

# Simulating database with in-memory objects
author1 = Author(name='J.K. Rowling')
author2 = Author(name='George R.R. Martin')
unknown_author = Author(name='Unknown Author')

print("=== CASCADE Strategy ===")
book_cascade = Book_CASCADE(title='Harry Potter', author=author1)
print(f"Before deletion: {book_cascade}")
print("Action: Delete author → Book is also deleted")

print("\n=== SET_NULL Strategy ===")
book_setnull = Book_SET_NULL(title='Game of Thrones', author=author2)
print(f"Before deletion: {book_setnull}")
print("Action: Delete author → Book remains, author field set to null")
book_setnull.author = None  # Simulating SET_NULL behavior
print(f"After deletion: {book_setnull}")

print("\n=== SET_DEFAULT Strategy ===")
book_setdefault = Book_SET_DEFAULT(title='The Hobbit', author=author1)
print(f"Before deletion: {book_setdefault}")
print("Action: Delete author → Book remains, assigned to default author")
book_setdefault.author = unknown_author  # Simulating SET_DEFAULT behavior
print(f"After deletion: {book_setdefault}")

print("\n=== Decision Guide ===")
print("CASCADE: Use when child object is meaningless without parent (Order Items)")
print("SET_NULL: Use when relationship is optional and you keep orphaned objects (Comments)")
print("SET_DEFAULT: Use when you want to reassign to a default parent (Unknown Author)")
print("PROTECT: Use to prevent accidental deletion (Important Master Data)")
```
[Back to Table of Contents](#table-of-contents)

### 101. What is the difference between `save()` and `update()` methods? When to use each? What about signals?

The main difference between `save()` and `update()` is how they interact with the database and what additional features they trigger.

The `save()` method works on a single model instance. It loads the object into Python memory, modifies it, and then saves it back to the database. When we use `save()`, Django triggers `pre_save` and `post_save` signals, which means any signal handlers we have defined will execute. The `save()` method also runs validation, calling the model's `clean()` method and field validators to ensure data integrity. If we have overridden the `save()` method with custom validations or business logic, that custom code runs as well. However, `save()` is slower for bulk operations because it processes one object at a time.

The `update()` method works on a QuerySet and can update multiple objects at once. Unlike `save()`, it does not load objects into Python memory. Instead, it updates data directly at the database level using a single SQL UPDATE statement. Because `update()` bypasses the object loading process, it does not trigger `pre_save` or `post_save` signals, and it does not run validation or execute any custom `save()` logic. The major advantage is that `update()` is much faster for bulk updates because it uses a single database query regardless of how many objects we are updating. The method returns the number of rows that were updated.

We should use `save()` when we are updating a single object, when we need signals to fire for business logic reasons like sending notifications, when we need validation to run to ensure data integrity, when we have custom logic in the `save()` method that must execute, or when we are working with related objects that need to be updated through the save process.

We should use `update()` when we need to update multiple objects at once, when performance is critical and we need the fastest way to update, when we have simple field updates where we do not need signals or validation to run, or for bulk operations like status changes, incrementing counters, or archiving old records.

There is an important performance consideration. If we use a for loop with `save()` to update multiple objects, we execute N database queries, one for each object. Using `update()` instead executes just one query, which is dramatically faster.

One important warning about `auto_now` fields: fields with `auto_now=True`, such as an `updated_at` timestamp field, are not automatically updated when we use `update()`. We must set them manually in the update call.

```python
from django.db import models
from django.utils import timezone

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.CharField(max_length=100)
    published = models.BooleanField(default=False)
    updated_at = models.DateTimeField(auto_now=True)

    def __repr__(self):
        return f"Book('{self.title}', published={self.published}, updated_at='{self.updated_at}')"

# Simulating database with in-memory objects
books = [
    Book(title='Harry Potter 1', author='J.K. Rowling', published=False),
    Book(title='Harry Potter 2', author='J.K. Rowling', published=False),
    Book(title='Game of Thrones', author='George R.R. Martin', published=True),
]

print("=== Initial State ===")
for book in books:
    print(book)

print("\n=== Using save() - Single object update ===")
book1 = books[0]
book1.published = True
print(f"Calling save() on: {book1.title}")
print("Action: Triggers pre_save and post_save signals")
print("Action: Runs validation")
print("Action: Executes custom save() logic if defined")
print(f"Result: {book1}")

print("\n=== Using update() - Bulk update (1 query) ===")
print("Updating all books by J.K. Rowling to published=True")
print("Action: Does NOT trigger signals")
print("Action: Does NOT run validation")
print("Action: Single SQL query - very fast!")
updated_count = len([b for b in books if b.author == 'J.K. Rowling'])
for b in books:
    if b.author == 'J.K. Rowling':
        b.published = True
print(f"Rows updated: {updated_count}")

print("\n=== Performance Comparison ===")
print("Inefficient (N queries with save()):")
print("  for book in books:")
print("      book.published = True")
print("      book.save()  # N separate database queries")

print("\nEfficient (1 query with update()):")
print("  Book.objects.filter(author='J.K. Rowling').update(published=True)  # 1 query")

print("\n=== auto_now fields with update() ===")
print("When using update(), auto_now fields are NOT updated automatically")
print("Must set updated_at manually:")
print("  Book.objects.filter(pk=1).update(")
print("      published=True,")
print("      updated_at=timezone.now()  # Must set explicitly")
print("  )")

print("\n=== When to use each ===")
print("save(): Single object updates, need signals/validation, custom logic")
print("update(): Bulk updates, performance critical, simple field changes")
```
[Back to Table of Contents](#table-of-contents)

### 102. What are `get_absolute_url()` and `reverse()`? Why use them instead of hardcoded URLs?

`get_absolute_url()` is a model method that returns the canonical URL for viewing a specific object. It is a Django convention, and when we define this method on a model, Django and many third-party packages know how to get the URL for that object without needing to know the URL structure.

The `reverse()` function builds complete URL paths dynamically by looking up the URL pattern name instead of relying on hardcoded paths. We pass it the view name we defined in our `urls.py` file and any required arguments like a slug or primary key, and it returns the complete URL path.

The key difference is that `get_absolute_url()` is a method on a model instance that uses `reverse()` internally to generate the URL for that specific object. So if we have a product with a particular slug, calling `get_absolute_url()` on that product returns the URL for that product. The `reverse()` function is more general and can be used anywhere in our code to build URLs dynamically.

Hardcoded URLs are problematic because if we change our URL patterns in `urls.py`, we must find and update every hardcoded reference throughout our codebase. This is error-prone and difficult to maintain as our project grows. Using `reverse()` and `get_absolute_url()` solves this problem because we define the URL pattern once, and all references automatically use that definition.

In templates, we can use the `{% url %}` tag, which calls `reverse()` internally. This is cleaner than concatenating strings. We can also use `get_absolute_url()` directly on an object in templates.

The benefits of using `reverse()` and `get_absolute_url()` are significant. First, changes are automatic: when we update a URL pattern, all references update automatically without manual changes. Second, Django validates that view names exist, catching errors early. Third, our code is more maintainable because we organize URL references by name rather than duplicating paths everywhere. Fourth, we can use namespaces to organize our URLs by application, which makes our routing structure cleaner and more scalable.

When we combine `get_absolute_url()` with namespaces, we create a powerful pattern where the URL logic lives in one place and is referenced everywhere else by name. This follows the DRY principle: define the URL pattern once, reference it everywhere by name.

```python
from django.db import models
from django.urls import reverse

# models.py
class Product(models.Model):
    name = models.CharField(max_length=200)
    slug = models.SlugField(unique=True)

    def get_absolute_url(self):
        """Returns the canonical URL for this product"""
        return reverse('shop:product-detail', kwargs={'slug': self.slug})

    def __repr__(self):
        return f"Product('{self.name}', slug='{self.slug}')"

class User(models.Model):
    name = models.CharField(max_length=100)
    pk = None  # Will be set manually

    def get_absolute_url(self):
        """Returns the canonical URL for this user"""
        return reverse('shop:user-profile', kwargs={'pk': self.pk})

    def __repr__(self):
        return f"User('{self.name}', pk={self.pk})"

# Simulating URL patterns from urls.py
# app_name = 'shop'
# urlpatterns = [
#     path('products/<slug:slug>/', ProductDetailView.as_view(), name='product-detail'),
#     path('users/<int:pk>/', UserProfileView.as_view(), name='user-profile'),
# ]

# Simulating reverse() function behavior
def mock_reverse(view_name, kwargs=None):
    """Simulates Django's reverse() function"""
    patterns = {
        'shop:product-detail': '/products/{slug}/',
        'shop:user-profile': '/users/{pk}/',
    }
    if kwargs:
        return patterns[view_name].format(**kwargs)
    return patterns[view_name]

print("=== Using get_absolute_url() ===")
product = Product(name='Laptop', slug='gaming-laptop')
product.pk = 1
url = mock_reverse('shop:product-detail', kwargs={'slug': product.slug})
print(f"Product: {product}")
print(f"get_absolute_url(): {url}")

user = User(name='John Doe')
user.pk = 5
url = mock_reverse('shop:user-profile', kwargs={'pk': user.pk})
print(f"User: {user}")
print(f"get_absolute_url(): {url}")

print("\n=== Using reverse() directly ===")
product_url = mock_reverse('shop:product-detail', kwargs={'slug': 'gaming-laptop'})
print(f"reverse('shop:product-detail', {{'slug': 'gaming-laptop'}})")
print(f"Returns: {product_url}")

user_url = mock_reverse('shop:user-profile', kwargs={'pk': 5})
print(f"reverse('shop:user-profile', {{'pk': 5}})")
print(f"Returns: {user_url}")

print("\n=== Hardcoded URLs vs Dynamic URLs ===")
print("BAD - Hardcoded URL:")
print("  return redirect(f'/products/{product.slug}/')")
print("  Issue: If URL pattern changes, this breaks!")

print("\nGOOD - Using reverse():")
print("  return redirect(reverse('shop:product-detail', kwargs={'slug': product.slug}))")
print("  Benefit: URL pattern changes automatically propagate")

print("\nGOOD - Using get_absolute_url():")
print("  return redirect(product.get_absolute_url())")
print("  Benefit: Cleanest option, all logic in one place")

print("\n=== Template Usage ===")
print("BAD - Hardcoded in template:")
print("  <a href=\"/products/{{ product.slug }}/\">View</a>")

print("\nGOOD - Using {% url %} tag:")
print("  <a href=\"{% url 'shop:product-detail' slug=product.slug %}\">View</a>")

print("\nGOOD - Using get_absolute_url():")
print("  <a href=\"{{ product.get_absolute_url }}\">View</a>")

print("\n=== Benefits Summary ===")
print("✓ Automatic updates when URL patterns change")
print("✓ Django validates view names exist")
print("✓ Better maintainability and refactoring")
print("✓ Supports URL namespacing")
print("✓ Follows DRY principle: define once, reference everywhere")
```
[Back to Table of Contents](#table-of-contents)

### 103. What is the difference between `render()`, `redirect()`, and `HttpResponse()`? When to use each?

These three functions are used to return responses from views, but they serve different purposes and should be used in different situations.

`render()` is used when we want to display a template to the user with dynamic data. It takes the HTTP request object, a template name, and optional context data containing variables we want to pass to the template. Django renders the template with the provided context and returns it as an HTML response to the browser. We use `render()` whenever we need to show a page with data, such as displaying a list of products or showing a user profile.

`redirect()` redirects the user to a different URL. It returns an HTTP redirect response with a status code of 302 or 301 that tells the browser to navigate to a new location. We can pass it a view name with arguments, a URL path, a full URL, or even a model instance that has a `get_absolute_url()` method. We use `redirect()` after successful form submissions, which follows the Post-Redirect-Get pattern. This pattern prevents users from accidentally resubmitting a form if they refresh the page, saves server resources, and improves user experience by following web best practices.

`HttpResponse()` generates a custom HTTP response with content we provide. It is the most basic response type. In fact, `render()` actually creates an `HttpResponse` internally with the rendered template as content. We use `HttpResponse()` when we need to return plain text responses, custom status codes, downloadable files like CSV or PDF, non-HTML content, or when we need fine control over the response. We can also specify content types and headers on an `HttpResponse` to serve different types of data.

Django also provides specialized response classes for common scenarios. `JsonResponse()` is better than `HttpResponse()` when returning JSON data. `FileResponse()` is for serving files. `StreamingHttpResponse()` is for streaming large responses. `HttpResponseNotFound()` returns a 404 status, and `HttpResponseForbidden()` returns a 403 status.

The key is to choose the right response type for your situation: use `render()` to display pages, use `redirect()` after form submissions or important actions, and use `HttpResponse()` for custom or specialized content.

```python
from django.shortcuts import render, redirect
from django.http import HttpResponse, JsonResponse
from django.urls import reverse

class Product:
    def __init__(self, pk, name, slug):
        self.pk = pk
        self.name = name
        self.slug = slug

    def get_absolute_url(self):
        return f'/products/{self.slug}/'

    def __repr__(self):
        return f"Product(pk={self.pk}, '{self.name}')"

class ProductForm:
    def __init__(self, data=None, instance=None):
        self.data = data
        self.instance = instance
        self.is_valid_flag = True

    def is_valid(self):
        return self.is_valid_flag

    def save(self):
        return self.instance or Product(1, 'New Product', 'new-product')

# Simulating view functions
print("=== render() - Display template with data ===")
products = [
    Product(1, 'Laptop', 'gaming-laptop'),
    Product(2, 'Mouse', 'wireless-mouse'),
]
print("def product_list(request):")
print("    products = Product.objects.all()")
print("    return render(request, 'products/list.html', {'products': products})")
print(f"Context data passed to template: {products}")
print("Returns: HTML page with rendered template")

print("\n=== redirect() - Send user to another URL ===")
print("def create_product(request):")
print("    if request.method == 'POST':")
print("        form = ProductForm(request.POST)")
print("        if form.is_valid():")
print("            product = form.save()")
print("            return redirect('product-detail', slug=product.slug)")
print("Purpose: Prevent duplicate form submission on refresh (Post-Redirect-Get pattern)")
print("Returns: HTTP redirect response (status 302)")

print("\n=== HttpResponse() - Custom response ===")
print("def csv_download(request):")
print("    response = HttpResponse(content_type='text/csv')")
print("    response['Content-Disposition'] = 'attachment; filename=\"data.csv\"'")
print("    response.write('Name,Slug\\n')")
print("    response.write('Laptop,gaming-laptop\\n')")
print("    return response")
print("Returns: Custom content (CSV file download)")

print("\n=== Example: Combining all three ===")
def update_product(request, pk, request_method='POST'):
    """Simulated view using all three response types"""
    product = Product(pk, 'Laptop', 'gaming-laptop')

    if request_method == 'POST':
        form = ProductForm(data={'name': 'Updated Laptop'}, instance=product)
        if form.is_valid():
            updated = form.save()
            print(f"✓ Form valid, product saved: {updated}")
            print(f"→ redirect(product.get_absolute_url()) returns: {product.get_absolute_url()}")
            return f"Redirect to: {product.get_absolute_url()}"
        else:
            print("✗ Form validation failed")
            return "Form validation failed with status 400"
    else:
        form = ProductForm(instance=product)
        print(f"✓ GET request received")
        print(f"→ render(request, 'products/update.html', {{'form': form, 'product': product}})")
        return f"Display update form for: {product}"

print("\nSimulating POST request to update product:")
result = update_product(None, pk=1, request_method='POST')
print(f"Response: {result}")

print("\n=== Response Type Comparison ===")
print("render():      Display template → HTML page with data")
print("redirect():    Navigate to URL → HTTP redirect (302/301)")
print("HttpResponse(): Custom response → Plain text, files, JSON, etc.")

print("\n=== When to use each ===")
print("render():      Show pages with dynamic data (lists, details, forms)")
print("redirect():    After form submission, create/update/delete operations")
print("HttpResponse(): Plain text, downloads, custom status codes, non-HTML content")
```
[Back to Table of Contents](#table-of-contents)

### 104. What does `@transaction.atomic` decorator do? When and why would you use it?

The `@transaction.atomic` decorator ensures that either all database operations within that function succeed, or all of them fail and get rolled back. This is based on the Atomicity principle from ACID database principles. When we use this decorator, we guarantee that the database never ends up in a partially updated state.

If any operation inside the decorated function fails and raises an exception, Django automatically rolls back all database changes made within that transaction, as if nothing happened. This prevents data inconsistency.

The classic example is a bank transfer. Imagine a customer transfers money from their account to another account. The first operation withdraws money from their account, and the second operation deposits it into the recipient's account. Without `@transaction.atomic`, if the deposit fails after the withdrawal succeeds, the customer loses their money. The money is withdrawn but never deposited, leaving the database in an inconsistent state. With `@transaction.atomic`, when the deposit fails, Django automatically rolls back the withdrawal as well. The money returns to the customer's account, and the database remains consistent.

In Django applications, a common scenario is creating related objects together. For example, when we create a new user, we might also need to create a user profile. Without `@transaction.atomic`, if the profile creation fails after the user is created, we end up with a user that has no profile, which is an inconsistent state. With `@transaction.atomic`, if either operation fails, both are rolled back, and we end up with neither a new user nor a new profile.

We can use `@transaction.atomic` as a decorator on a function, or we can use it as a context manager with the `with` statement to wrap specific blocks of code. Both approaches give us the same guarantee: all operations within the transaction succeed together, or all fail together.

We should use `@transaction.atomic` when multiple related database operations must succeed together, when partial updates would cause data inconsistency, for financial transactions, when creating related objects that depend on each other, or for complex multi-step operations. We do not need it for single simple database operations or when operations are independent of each other.

The benefits are significant: we ensure data integrity by preventing inconsistent database states, we gain reliability with an all-or-nothing guarantee, we get automatic rollback on errors, and the atomicity works at the database transaction level, making it very reliable and secure.

```python
from django.db import transaction

class Account:
    def __init__(self, account_id, balance):
        self.account_id = account_id
        self.balance = balance

    def __repr__(self):
        return f"Account(id={self.account_id}, balance=${self.balance})"

class User:
    def __init__(self, username, email):
        self.username = username
        self.email = email

    def __repr__(self):
        return f"User('{self.username}', '{self.email}')"

class UserProfile:
    def __init__(self, user, bio):
        self.user = user
        self.bio = bio

    def __repr__(self):
        return f"UserProfile(user='{self.user.username}', bio='{self.bio}')"

print("=== Example 1: Bank Transfer with @transaction.atomic ===")
print("WITHOUT @transaction.atomic (DANGER):")
print("  1. Withdraw from account A ✓")
print("  2. Deposit to account B ✗ (fails)")
print("  Result: Money lost! Database inconsistent.")

print("\nWITH @transaction.atomic (SAFE):")
print("  1. Withdraw from account A ✓")
print("  2. Deposit to account B ✗ (fails)")
print("  Django rolls back step 1 automatically")
print("  Result: Both operations fail, database consistent")

def transfer_money_atomic(from_account, to_account, amount):
    """Simulating @transaction.atomic behavior"""
    try:
        # Withdraw from sender
        from_account.balance -= amount
        print(f"✓ Withdrew ${amount} from {from_account}")

        # Simulate deposit failure
        if to_account.balance < 0:  # Simulating error condition
            raise Exception("Recipient account closed!")

        to_account.balance += amount
        print(f"✓ Deposited ${amount} to {to_account}")

        return "Transfer successful"
    except Exception as e:
        print(f"✗ Error: {e}")
        print("→ Rolling back all changes (both withdraw and deposit)")
        from_account.balance += amount  # Rollback
        return "Transfer failed, all changes rolled back"

from_acc = Account(1001, 1000)
to_acc = Account(1002, 500)
print(f"\nBefore transfer: {from_acc}, {to_acc}")
result = transfer_money_atomic(from_acc, to_acc, 200)
print(f"Result: {result}")
print(f"After: {from_acc}, {to_acc}")

print("\n=== Example 2: Creating Related Objects ===")
print("WITHOUT @transaction.atomic (INCONSISTENT):")
print("  1. Create User ✓")
print("  2. Create UserProfile ✗ (fails)")
print("  Result: User exists without profile - inconsistent!")

print("\nWITH @transaction.atomic (CONSISTENT):")
print("  1. Create User ✓")
print("  2. Create UserProfile ✗ (fails)")
print("  Django rolls back user creation")
print("  Result: Neither user nor profile created - consistent")

def create_user_with_profile_atomic(username, email, bio):
    """Simulating @transaction.atomic with user creation"""
    try:
        user = User(username, email)
        print(f"✓ Created {user}")

        # Simulate profile creation failure
        if not bio:
            raise ValueError("Bio cannot be empty!")

        profile = UserProfile(user, bio)
        print(f"✓ Created {profile}")

        return f"User and profile created successfully"
    except Exception as e:
        print(f"✗ Error: {e}")
        print("→ Rolling back: User creation is undone")
        return "Creation failed, all changes rolled back"

print("\nAttempt 1 - Valid data:")
result = create_user_with_profile_atomic('john_doe', 'john@example.com', 'Software developer')
print(f"Result: {result}")

print("\nAttempt 2 - Invalid data (empty bio):")
result = create_user_with_profile_atomic('jane_doe', 'jane@example.com', '')
print(f"Result: {result}")

print("\n=== Example 3: Using @transaction.atomic as context manager ===")
print("with transaction.atomic():")
print("    # Deduct inventory")
print("    for item in order.items.all():")
print("        product.stock -= item.quantity")
print("        product.save()")
print("    ")
print("    # Update order status")
print("    order.status = 'completed'")
print("    order.save()")
print("    # If any operation fails, everything is rolled back")

print("\n=== When to use @transaction.atomic ===")
print("✓ Multiple related operations must succeed together")
print("✓ Partial updates would cause data inconsistency")
print("✓ Financial transactions (payments, transfers)")
print("✓ Creating dependent objects (User + Profile, Order + Items)")
print("✓ Complex multi-step operations")

print("\n=== When NOT to use @transaction.atomic ===")
print("✗ Single, simple database operation")
print("✗ Independent operations that do not need to be linked")
```
[Back to Table of Contents](#table-of-contents)

### 105. What are Django's `Meta` class options? Explain `ordering`, `verbose_name`, `verbose_name_plural`, and `unique_together`.

The `Meta` class in Django models contains metadata, which is data about the data. It provides configuration options for how the model behaves and how it is displayed throughout our application, including in the Django admin panel and forms. We define these properties inside an inner `Meta` class within the model itself.

The `ordering` option defines the default ordering for querysets of this model. When we specify `ordering`, Django automatically applies that sort order whenever we query the model without explicitly specifying an order. We can use a minus sign prefix to sort in descending order, and we can specify multiple fields to create a multi-level sort. For example, we might want to display products ordered by category first and then by price in descending order. The `ordering` option is applied automatically when we call `Product.objects.all()` or any other queryset without explicit ordering. We use `ordering` when we want a consistent default sort order across our application.

The `verbose_name` option changes how the model is displayed in human-readable form, especially in the Django admin panel and automatically generated forms. Instead of using the model class name, Django displays the value we provide. This is useful when the model name in code differs from what we want to display to users, or when we want a more descriptive name that is more user-friendly.

The `verbose_name_plural` option defines the plural form of the model name. Django automatically adds an "s" to create the plural form, but this does not work correctly for all words. For example, the plural of "Watch" is "Watches", not "Watchs". The plural of "Category" is "Categories", not "Categorys". We use `verbose_name_plural` when Django's automatic pluralization is grammatically incorrect.

The `unique_together` option enforces that a combination of fields must be unique together across all records. No two records can have the same combination of values for the specified fields. For example, in a student enrollment system, a student cannot enroll in the same course twice. In a hotel booking system, the same room cannot be booked for the same date twice. We use `unique_together` when uniqueness must be enforced across multiple fields together, not just a single field.

It is important to note that `unique_together` is deprecated in newer versions of Django. Django version 2.2 and later recommend using `UniqueConstraint` within the `constraints` option instead, which provides more flexibility and clearer syntax.

Django provides many other `Meta` options as well. The `db_table` option lets us specify a custom database table name. The `indexes` option creates database indexes for query performance. The `abstract` option makes a model abstract so it does not create a database table, which is useful for base models used in inheritance. The `permissions` option allows us to define custom permissions for the model.

```python
from django.db import models
from django.db.models import UniqueConstraint, Index

class Product(models.Model):
    name = models.CharField(max_length=200)
    category = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    created_at = models.DateTimeField(auto_now_add=True)

    class Meta:
        ordering = ['-created_at']  # Newest first
        verbose_name = "Product Item"
        verbose_name_plural = "Product Items"
        indexes = [
            Index(fields=['category']),
        ]

    def __repr__(self):
        return f"Product('{self.name}', category='{self.category}', price=${self.price})"

class Watch(models.Model):
    brand = models.CharField(max_length=100)
    model_name = models.CharField(max_length=100)

    class Meta:
        verbose_name = "Watch"
        verbose_name_plural = "Watches"  # Correct plural, not "Watchs"

    def __repr__(self):
        return f"Watch('{self.brand}', '{self.model_name}')"

class Student:
    def __init__(self, student_id, name):
        self.student_id = student_id
        self.name = name

    def __repr__(self):
        return f"Student(id={self.student_id}, '{self.name}')"

class Course:
    def __init__(self, course_id, title):
        self.course_id = course_id
        self.title = title

    def __repr__(self):
        return f"Course(id={self.course_id}, '{self.title}')"

class Enrollment(models.Model):
    student = models.ForeignKey(Student, on_delete=models.CASCADE)
    course = models.ForeignKey(Course, on_delete=models.CASCADE)
    enrolled_date = models.DateField(auto_now_add=True)

    class Meta:
        verbose_name = "Student Enrollment"
        verbose_name_plural = "Student Enrollments"
        # Old way (deprecated):
        unique_together = ['student', 'course']
        # New way (recommended):
        constraints = [
            UniqueConstraint(fields=['student', 'course'], name='unique_enrollment')
        ]

    def __repr__(self):
        return f"Enrollment(student={self.student.student_id}, course={self.course.course_id})"

class Room:
    def __init__(self, room_id, room_number):
        self.room_id = room_id
        self.room_number = room_number

    def __repr__(self):
        return f"Room('{self.room_number}')"

class Booking(models.Model):
    room = models.ForeignKey(Room, on_delete=models.CASCADE)
    date = models.DateField()
    guest_name = models.CharField(max_length=100)

    class Meta:
        verbose_name = "Hotel Booking"
        verbose_name_plural = "Hotel Bookings"
        unique_together = ['room', 'date']  # Can't book same room twice on same date
        ordering = ['date', 'room']

    def __repr__(self):
        return f"Booking(room={self.room.room_number}, date='{self.date}', guest='{self.guest_name}')"

print("=== ordering Meta option ===")
products = [
    Product(name='Laptop', category='Electronics', price=1500.00),
    Product(name='Mouse', category='Electronics', price=25.00),
    Product(name='Keyboard', category='Electronics', price=80.00),
]
print("class Meta:")
print("    ordering = ['-created_at']  # Newest products first")
print(f"Products will be ordered by created_at descending by default")

print("\n=== verbose_name and verbose_name_plural ===")
print("class Watch(models.Model):")
print("    class Meta:")
print("        verbose_name = 'Watch'")
print("        verbose_name_plural = 'Watches'")
print("\nWithout verbose_name_plural, Django would display 'Watchs' (incorrect)")
print("With it, Django displays 'Watches' (correct)")

print("\n=== unique_together Meta option ===")
student = Student(1, 'Alice')
course = Course(101, 'Python Basics')
enrollment1 = Enrollment(student=student, course=course)
print("class Enrollment(models.Model):")
print("    class Meta:")
print("        unique_together = ['student', 'course']")
print(f"Enrollment created: {enrollment1}")
print("Attempting to create same enrollment again would raise IntegrityError")
print("A student cannot enroll in the same course twice")

print("\n=== unique_together with Booking example ===")
room = Room(1, '101')
booking1 = Booking(room=room, date='2025-12-25', guest_name='John')
booking2 = Booking(room=room, date='2025-12-26', guest_name='Jane')
print("class Booking(models.Model):")
print("    class Meta:")
print("        unique_together = ['room', 'date']")
print(f"Booking 1: {booking1}")
print(f"Booking 2: {booking2}")
print("Same room, different dates - allowed ✓")
print("Same room, same date - would raise IntegrityError ✗")

print("\n=== Other Meta options ===")
print("db_table: Custom database table name")
print("indexes: Database indexes for query performance")
print("abstract: Makes model abstract (no database table created)")
print("permissions: Define custom permissions for the model")

print("\n=== New recommendation: UniqueConstraint ===")
print("Instead of unique_together (deprecated):")
print("class Meta:")
print("    constraints = [")
print("        UniqueConstraint(fields=['student', 'course'], name='unique_enrollment')")
print("    ]")
print("Provides more control and clearer syntax")
```
[Back to Table of Contents](#table-of-contents)

### 106. What is the difference between authentication and authorization?

Authentication is the process of verifying who a user is (confirming their identity), typically through credentials like username and password. Authorization is the process of determining what an authenticated user is allowed to do (their permissions and access rights). Authentication answers "who are you?" while authorization answers "what can you do?"

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework.permissions import IsAuthenticated, IsAdminUser
from rest_framework.authentication import TokenAuthentication
from rest_framework import status
from django.contrib.auth import authenticate

# AUTHENTICATION - "Who are you?"
class LoginView(APIView):
    def post(self, request):
        username = request.data.get('username')
        password = request.data.get('password')

        # Authentication: Verify the user's identity
        user = authenticate(username=username, password=password)

        if user is not None:
            # User identity confirmed - create token
            token, created = Token.objects.get_or_create(user=user)
            return Response({
                "message": "Authentication successful",
                "token": token.key,
                "username": user.username
            })
        else:
            return Response(
                {"error": "Invalid credentials"},
                status=status.HTTP_401_UNAUTHORIZED
            )

# AUTHORIZATION - "What can you do?"
class ProductView(APIView):
    authentication_classes = [TokenAuthentication]  # Step 1: Authenticate
    permission_classes = [IsAuthenticated]  # Step 2: Authorize (basic)

    def get(self, request):
        # User is authenticated, now check authorization
        products = Product.objects.all()
        return Response({"products": list(products.values())})

    def post(self, request):
        # Authorization check: Can this user create products?
        if not request.user.is_staff:
            return Response(
                {"error": "You don't have permission to create products"},
                status=status.HTTP_403_FORBIDDEN
            )

        # User is authorized to create
        product = Product.objects.create(**request.data)
        return Response({"message": "Product created", "id": product.id})

# Example flow:
# Step 1 - AUTHENTICATION: POST /api/login/
# Body: {"username": "john", "password": "pass123"}
# Response: {"token": "abc123xyz", "username": "john"}
# Result: User identity verified ✓

# Step 2 - AUTHORIZATION: GET /api/products/
# Headers: {"Authorization": "Token abc123xyz"}
# Authentication: Token validated - user is "john" ✓
# Authorization: IsAuthenticated checked - user can view ✓
# Response: {"products": [...]}

# Step 3 - AUTHORIZATION (different level): POST /api/products/
# Headers: {"Authorization": "Token abc123xyz"}
# Body: {"name": "Laptop", "price": 999}
# Authentication: Token validated - user is "john" ✓
# Authorization: is_staff check - john is not staff ✗
# Response: 403 Forbidden - "You don't have permission to create products"
```
[Back to Table of Contents](#table-of-contents)

**Clear Separation Example:**

```python
# Authentication happens FIRST - verifying identity
class OrderView(APIView):
    authentication_classes = [TokenAuthentication]  # WHO are you?
    permission_classes = [IsAuthenticated]  # Basic: are you logged in?

    def get(self, request):
        # AUTHORIZATION: What orders can you see?
        if request.user.is_staff:
            # Admins can see all orders
            orders = Order.objects.all()
        else:
            # Regular users can only see their own orders
            orders = Order.objects.filter(user=request.user)

        return Response({"orders": list(orders.values())})

    def delete(self, request, pk):
        order = Order.objects.get(pk=pk)

        # AUTHORIZATION: Can you delete this specific order?
        if not (request.user.is_staff or order.user == request.user):
            return Response(
                {"error": "You can only delete your own orders"},
                status=status.HTTP_403_FORBIDDEN
            )

        order.delete()
        return Response({"message": "Order deleted"})

# User A (regular user, authenticated):
# Authentication: Token valid ✓ (WHO: User A)
# GET /api/orders/ -> Authorization: Can see own orders only
# DELETE /api/orders/5/ (owned by User A) -> Authorization: Allowed ✓
# DELETE /api/orders/10/ (owned by User B) -> Authorization: Denied ✗ (403)

# Admin (staff user, authenticated):
# Authentication: Token valid ✓ (WHO: Admin)
# GET /api/orders/ -> Authorization: Can see all orders
# DELETE /api/orders/5/ -> Authorization: Allowed ✓ (admin privilege)
# DELETE /api/orders/10/ -> Authorization: Allowed ✓ (admin privilege)

# Anonymous user (not authenticated):
# Authentication: No token ✗ (WHO: Unknown)
# GET /api/orders/ -> Authentication fails, 401 Unauthorized
# Authorization never checked - can't get past authentication
```
[Back to Table of Contents](#table-of-contents)

**Real-World Scenario:**

```python
# Authentication: Multiple methods to verify identity
class MultiAuthView(APIView):
    authentication_classes = [TokenAuthentication, SessionAuthentication]

    def get(self, request):
        # At this point, DRF has already authenticated the user
        # request.user is populated with the authenticated user
        return Response({
            "authenticated_as": request.user.username,
            "authentication_method": type(request.auth).__name__
        })

# Authorization: Role-based access control
class DocumentView(APIView):
    authentication_classes = [TokenAuthentication]
    permission_classes = [IsAuthenticated]

    def get(self, request, pk):
        document = Document.objects.get(pk=pk)

        # AUTHORIZATION: Complex business rules
        # Can user access this document?
        if document.is_public:
            # Public documents - anyone authenticated can view
            can_access = True
        elif request.user.is_staff:
            # Staff can view all documents
            can_access = True
        elif document.owner == request.user:
            # Users can view their own documents
            can_access = True
        elif request.user in document.shared_with.all():
            # Users can view documents shared with them
            can_access = True
        else:
            can_access = False

        if not can_access:
            return Response(
                {"error": "You don't have permission to view this document"},
                status=status.HTTP_403_FORBIDDEN
            )

        return Response({"document": document.content})

# Summary of differences:
# AUTHENTICATION (401 Unauthorized):
# - Happens first
# - Verifies WHO you are
# - Login, tokens, sessions, passwords
# - Binary: authenticated or not
# - Example: Wrong password, invalid token, no credentials

# AUTHORIZATION (403 Forbidden):
# - Happens after authentication
# - Determines WHAT you can do
# - Permissions, roles, ownership
# - Granular: different levels of access
# - Example: Not admin, not owner, not in allowed group
```
[Back to Table of Contents](#table-of-contents)

### 107. What are Django's get_or_create() and update_or_create() methods? When would you use them?

**`get_or_create()`** retrieves an object from the database, or creates it if it doesn't exist. It returns a tuple `(object, created)` where `created` is `True` if a new object was created, `False` if it already existed.

**When to use:** When you need to ensure a record exists without checking first. Common use cases include creating tags, categories, configuration settings, or any scenario where duplicate prevention is important.

**`update_or_create()`** looks for an object matching certain criteria. If found, it updates the object with provided values. If not found, it creates a new object. It also returns `(object, created)`.

**When to use:** When you want to update existing records or create them if they don't exist. Perfect for syncing data, updating user profiles, or managing shopping cart items where quantity should be updated if the item already exists.

```python
from django.db import models

# Example models
class Tag(models.Model):
    name = models.CharField(max_length=50, unique=True)

class CartItem(models.Model):
    product_name = models.CharField(max_length=100)
    quantity = models.IntegerField(default=1)

# get_or_create() - ensures tag exists, doesn't create duplicates
tag, created = Tag.objects.get_or_create(name="Python")
print(f"Tag: {tag.name}, Created: {created}")  # First time: Created: True
tag, created = Tag.objects.get_or_create(name="Python")
print(f"Tag: {tag.name}, Created: {created}")  # Second time: Created: False

# update_or_create() - updates quantity if item exists, creates if not
item, created = CartItem.objects.update_or_create(
    product_name="Laptop",
    defaults={"quantity": 1}
)
print(f"Item: {item.product_name}, Qty: {item.quantity}, Created: {created}")  # Qty: 1, Created: True

item, created = CartItem.objects.update_or_create(
    product_name="Laptop",
    defaults={"quantity": 3}
)
print(f"Item: {item.product_name}, Qty: {item.quantity}, Created: {created}")  # Qty: 3, Created: False
```
[Back to Table of Contents](#table-of-contents)

### 108. What is lazy evaluation in Django querysets? When does a queryset actually hit the database?

Lazy evaluation in Django querysets means that a call to the database is not made until the data is actually needed. This allows us to chain filters and build complex queries that include annotations, aggregations, and ordering. The code lives in our files ready to be executed and waits until we actually need the data.

However, some ORM methods are not lazy. For example, `get()` immediately hits the database because we're clearly specifying that we want a single specific object from the table, so Django retrieves it directly.

For example, in an e-commerce application, we might have a complex query that needs to join with inventory to get information about availability and price, make annotations to get related data from other models that hold characteristics of our products, and use aggregations to calculate the min and max price per product depending on size, and finally sort the products by given criteria. We have that code ready but it will not be executed until we try to paginate it or serialize it in order to send it to the client.

```python
from django.db.models import Avg, Count, Min, Max

# get() is NOT lazy - hits database immediately
product = Product.objects.get(id=1)  # DB HIT happens right here
print(product.name)  # No additional DB hit

# Building a complex e-commerce query - lazy evaluation
products = Product.objects.all()  # Lazy
products = products.select_related('inventory')  # Still lazy
products = products.annotate(
    min_price=Min('inventory__price'),
    max_price=Max('inventory__price'),
    avg_rating=Avg('reviews__rating')
)  # Still lazy
products = products.order_by('-avg_rating')  # Still lazy

print("Query built, but database not hit yet!")

# DATABASE IS HIT when we paginate or serialize:
paginator = Paginator(products, 10)  # Still lazy
page = paginator.get_page(1)  # DB HIT - pagination needs the data

# Or when serializing for the client:
serializer = ProductSerializer(products, many=True)  # DB HIT - serializer needs data
response_data = serializer.data  # Data is now available
```
[Back to Table of Contents](#table-of-contents)

### 109. What are annotate() and aggregate() in Django ORM? What's the difference between them?

**Correct Answer:**

With `annotate()` we add calculated fields to each object in our queryset. For example, our inventory table doesn't have a minimum price per product - each row in the inventory table has a product id, size id, and a respective price. If we need to display on the product page the minimum and maximum price, we need to annotate it. `annotate()` returns a queryset with these additional fields added to each product.

We would use `aggregate()` if we need a single value calculated across an entire table. For example, if we need to get information about the total amount of money that our customers have spent this month, we would use the aggregate function. Just like in PostgreSQL where we would use aggregate functions like SUM, MIN, MAX, or AVG to get a single accumulated value across all rows.

In conclusion, `annotate()` returns a queryset with additional calculated fields per object, while `aggregate()` returns a dictionary with the key we specify and the aggregated result as the value.

```python
from django.db.models import Min, Max, Sum, Avg, Count

# annotate() - adds fields to EACH product (per-row calculation)
products = Product.objects.annotate(
    min_price=Min('inventory__price'),
    max_price=Max('inventory__price'),
    review_count=Count('reviews')
)

for product in products:
    print(f"{product.name}: ${product.min_price} - ${product.max_price}")
# Output:
# Laptop: $899 - $1299
# Phone: $599 - $899

# aggregate() - ONE result across ALL products (single value)
stats = Product.objects.aggregate(
    total_revenue=Sum('orders__total_price'),
    average_price=Avg('inventory__price'),
    total_products=Count('id')
)

print(stats)
# Output: {'total_revenue': 45000, 'average_price': 549.99, 'total_products': 150}

# Real use case - customer spending this month
from django.utils import timezone
from datetime import timedelta

one_month_ago = timezone.now() - timedelta(days=30)
monthly_stats = Order.objects.filter(created_at__gte=one_month_ago).aggregate(
    total_spent=Sum('total_price'),
    order_count=Count('id')
)

print(f"Customers spent ${monthly_stats['total_spent']} in {monthly_stats['order_count']} orders")
```
[Back to Table of Contents](#table-of-contents)

### 110. What is the difference between REST and RESTful APIs? What are the key principles of REST?

REST stands for Representational State Transfer and is an architectural style for designing networked applications, specifically for client-server communication over HTTP. RESTful APIs are applications that follow the REST principles and standards.

The key principles of REST are: First, statelessness - the server does not store any information about previous requests, so each request from the client must contain all the information needed to process it. Second, client-server separation - the client and server are independent, the client initiates requests and the server responds. Third, uniform interface - we use standard HTTP methods like GET, POST, PUT, PATCH, and DELETE to perform operations on resources, and URIs identify resources.

Following REST principles allows a single server to work with multiple different clients, as long as all clients follow the same REST standards. Resources are typically named in plural form in URIs, and HTTP methods indicate what action should be performed instead of having separate URLs for each CRUD operation.

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status

# RESTful API example following REST principles

class ProductListView(APIView):
    """
    Stateless API - each request contains all needed information
    URI: /api/products/ (resource in plural)
    """

    def get(self, request):
        """GET method retrieves resources"""
        products = Product.objects.all()
        serializer = ProductSerializer(products, many=True)
        # Cacheability - could add cache headers
        return Response(serializer.data, status=status.HTTP_200_OK)

    def post(self, request):
        """POST method creates a new resource"""
        # Stateless - all data needed is in the request
        serializer = ProductSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


class ProductDetailView(APIView):
    """
    URI: /api/products/{id}/ (specific resource)
    Same endpoint, different HTTP methods for different operations
    """

    def get(self, request, pk):
        """GET retrieves a specific resource"""
        product = Product.objects.get(pk=pk)
        serializer = ProductSerializer(product)
        return Response(serializer.data)

    def put(self, request, pk):
        """PUT updates entire resource"""
        product = Product.objects.get(pk=pk)
        serializer = ProductSerializer(product, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def patch(self, request, pk):
        """PATCH updates partial resource"""
        product = Product.objects.get(pk=pk)
        serializer = ProductSerializer(product, data=request.data, partial=True)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def delete(self, request, pk):
        """DELETE removes resource"""
        product = Product.objects.get(pk=pk)
        product.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)

# Example usage demonstrating statelessness:
# Each request is independent and contains all needed info
# GET /api/products/ - retrieve all products
# POST /api/products/ with body {"name": "Laptop", "price": 999}
# GET /api/products/1/ - retrieve product with id=1
# PUT /api/products/1/ with body {"name": "Gaming Laptop", "price": 1299}
# PATCH /api/products/1/ with body {"price": 1199}
# DELETE /api/products/1/ - delete product
```
[Back to Table of Contents](#table-of-contents)

### 111. What are HTTP status codes? Explain the difference between 2xx, 3xx, 4xx, and 5xx status codes with examples.

HTTP status codes give us a summary of what response the server has returned.

2xx codes mean the request was processed successfully. For example, 200 means OK - the request succeeded, and 201 means successfully created - a new resource was created.

3xx codes mean redirection - the requested resource has been moved. 301 means permanent redirect - the resource has moved permanently to a new location, like when a website permanently moves from HTTP to HTTPS. 302 means temporary redirect - the resource is temporarily at a different location but will return to the original, like redirecting to a maintenance page temporarily.

4xx codes mean the client did something wrong and that's why the server did not process the request successfully. For example, 400 means bad request - the client sent invalid data, 401 means unauthorized - the client doesn't have valid authentication credentials, 403 means forbidden - the client is authenticated but doesn't have permission to access the resource, and 404 means not found - the requested resource doesn't exist. These are client-side errors, not server problems.

5xx codes mean server-side errors - usually bugs that developers working on the server have not resolved. It might be related to error handling. For example, 500 means internal server error. Let's say there was no try-except block for a risky operation like trying to get a product by color using `get()`. The `get()` method would raise an error when more than one product with that color exists, causing a 500 error.

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from django.shortcuts import redirect

# 2xx - Success responses
class ProductCreateView(APIView):
    def post(self, request):
        serializer = ProductSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            # 201 Created - resource successfully created
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def get(self, request):
        products = Product.objects.all()
        serializer = ProductSerializer(products, many=True)
        # 200 OK - request succeeded
        return Response(serializer.data, status=status.HTTP_200_OK)


# 3xx - Redirection
def old_product_page(request, pk):
    # 301 Permanent Redirect - product pages moved permanently
    return redirect(f'/api/v2/products/{pk}/', permanent=True)

def maintenance_redirect(request):
    # 302 Temporary Redirect - site under maintenance temporarily
    if site_is_under_maintenance():
        return redirect('/maintenance/', permanent=False)


# 4xx - Client errors
class ProductDetailView(APIView):
    def get(self, request, pk):
        try:
            product = Product.objects.get(pk=pk)
            serializer = ProductSerializer(product)
            return Response(serializer.data)
        except Product.DoesNotExist:
            # 404 Not Found - client requested non-existent resource
            return Response(
                {"error": "Product not found"},
                status=status.HTTP_404_NOT_FOUND
            )

    def post(self, request):
        if not request.data.get('name'):
            # 400 Bad Request - client sent invalid data
            return Response(
                {"error": "Name is required"},
                status=status.HTTP_400_BAD_REQUEST
            )

    def delete(self, request, pk):
        if not request.user.is_authenticated:
            # 401 Unauthorized - no authentication credentials
            return Response(
                {"error": "Authentication required"},
                status=status.HTTP_401_UNAUTHORIZED
            )

        if not request.user.is_staff:
            # 403 Forbidden - authenticated but no permission
            return Response(
                {"error": "You don't have permission"},
                status=status.HTTP_403_FORBIDDEN
            )


# 5xx - Server errors (usually bugs)
class BuggyProductView(APIView):
    def get(self, request):
        try:
            # Risky operation without proper error handling
            color = request.GET.get('color')
            # This will cause 500 if multiple products have same color
            product = Product.objects.get(color=color)
            return Response(ProductSerializer(product).data)
        except Product.MultipleObjectsReturned:
            # Without this except block, Django would return 500 Internal Server Error
            return Response(
                {"error": "Multiple products found"},
                status=status.HTTP_400_BAD_REQUEST
            )
        except Exception as e:
            # Unhandled exceptions cause 500 errors
            # Better to catch and return proper error
            return Response(
                {"error": "Server error occurred"},
                status=status.HTTP_500_INTERNAL_SERVER_ERROR
            )
```
[Back to Table of Contents](#table-of-contents)

### 112. What is JWT (JSON Web Token) authentication? How does it differ from session-based authentication?

JWT authentication is a stateless authentication mechanism where the server generates two tokens upon login: an access token (short-lived, typically 15 minutes) and a refresh token (long-lived, typically 1-2 weeks). These tokens are sent to the client and stored on the client side, not on the server.

The access token is included in the Authorization header of each request to prove the user's identity. When it expires, the client automatically uses the refresh token to request a new access token from a dedicated refresh endpoint without requiring the user to log in again. This continues until the refresh token expires, at which point the user must log in again.

How does the server verify tokens without storing them? A JWT consists of three parts: header, payload, and signature. When creating a token, the server signs the header and payload using a secret key that only it knows. When the client sends a token back, the server takes the header and payload, signs them again with the same secret key, and compares the result with the token's signature. If they match and the token hasn't expired, it's valid. This is why JWT is stateless - the server doesn't need to look up anything in a database; it just needs its secret key to verify the token's authenticity.

In practice, most implementations use one or two database tables for token blacklisting. When a user logs out or a token is compromised, the refresh token is added to a blacklist table so it can be revoked before its natural expiration.

The key difference from session-based authentication is that JWT is stateless - the server doesn't store session data or look up sessions on each request. In contrast, session-based authentication is stateful. When a user logs in, Django creates a session object stored in the database with a unique session key. This session key is sent to the client as a cookie. On each request, the server looks up the session in the database using this key. When the user logs out, the session is deleted from the database.

Both methods have security considerations - if someone steals your JWT refresh token or session key, they can impersonate you until the token/session expires or you log out. That's why access tokens have short expiration times, and both approaches require HTTPS and secure storage mechanisms like httpOnly cookies.

```python
# JWT Authentication Flow (using djangorestframework-simplejwt)
from rest_framework_simplejwt.tokens import RefreshToken

# When user logs in, generate tokens
def login_user(user):
    refresh = RefreshToken.for_user(user)
    return {
        'refresh': str(refresh),
        'access': str(refresh.access_token),
    }

# Example output
user = User.objects.get(username='john')
tokens = login_user(user)
print(tokens)
# Output: {
#   'refresh': 'eyJ0eXAiOiJKV1QiLCJhbGc...',  # Valid for ~2 weeks
#   'access': 'eyJ0eXAiOiJKV1QiLCJhbGc...'    # Valid for ~15 min
# }

# How server verifies token (simplified concept)
import hmac
import hashlib

secret_key = "your-secret-key"
header_payload = "eyJhbGc.eyJ1c2VyX2lk"  # From incoming token
signature_from_token = "SflKxwRJ"  # From incoming token

# Server recreates signature
recreated_signature = hmac.new(
    secret_key.encode(),
    header_payload.encode(),
    hashlib.sha256
).hexdigest()

# Compare signatures
is_valid = (recreated_signature == signature_from_token)
print(f"Token valid: {is_valid}")  # Output: Token valid: True/False

# Session-based Authentication (Django built-in)
from django.contrib.auth import login, logout
from django.contrib.sessions.models import Session

# When user logs in
login(request, user)  # Creates session in database
print(request.session.session_key)  # Output: 'a7f8d9e2b3c4...'

# Check session in database
session = Session.objects.get(session_key=request.session.session_key)
print(session.get_decoded())  # Output: {'_auth_user_id': '1', ...}

# When user logs out
logout(request)  # Deletes session from database
```
[Back to Table of Contents](#table-of-contents)

### 113. What is the difference between values() and values_list() in Django ORM?

When we query the database without using values() or values_list(), like Product.objects.all(), we get a QuerySet containing full Product model instances - complete Python objects with all their fields, methods, and relationships.

With values(), we get a QuerySet of dictionaries. Each dictionary represents one database record, where the field names are the keys and the stored data are the values. We can specify which fields to retrieve by passing them as arguments, like values('name', 'price'), or get all fields if we don't pass any arguments.

With values_list(), we get a QuerySet of tuples. Each tuple contains the values of the fields we specified, in the order we specified them. For example, values_list('name', 'price') returns tuples like ('Laptop', 999.99). If we only need a single field and want a flat list instead of tuples, we can pass flat=True, like values_list('name', flat=True), which gives us a simple list of values rather than single-item tuples.

**Code Snippet:**

```python
# Assuming Product model with fields: id, name, price, category

# Without values() or values_list() - full model instances
products = Product.objects.all()
print(products)
# Output: <QuerySet [<Product: Product object (1)>, <Product: Product object (2)>]>
print(products[0].name)  # Access attributes
# Output: Laptop

# Using values() - QuerySet of dictionaries
products_values = Product.objects.values('name', 'price')
print(products_values)
# Output: <QuerySet [{'name': 'Laptop', 'price': 999.99}, {'name': 'Phone', 'price': 599.99}]>

# Using values_list() - QuerySet of tuples
products_tuples = Product.objects.values_list('name', 'price')
print(products_tuples)
# Output: <QuerySet [('Laptop', 999.99), ('Phone', 599.99)]>

# Using values_list() with flat=True - flat list (single field only)
product_names = Product.objects.values_list('name', flat=True)
print(product_names)
# Output: <QuerySet ['Laptop', 'Phone', 'Tablet']>
print(list(product_names))
# Output: ['Laptop', 'Phone', 'Tablet']
```
[Back to Table of Contents](#table-of-contents)

### 114. What are Django's ContentType framework and generic relations?

Django's ContentType framework is a registry system that keeps track of all models installed in your Django project. It stores this information in the django_content_type table with columns for app_label and model name, along with a unique id for each model.

Generic relations allow a model to have a foreign key relationship to any other model dynamically. This is useful when you have a base abstract class inherited by multiple models, and you need another model to relate to any of those child models. For example, if we have a BaseProduct abstract class inherited by Ring, Necklace, and Bracelet, and we need an Inventory model to track stock for all product types, we can't use a regular ForeignKey because you cannot point a ForeignKey to an abstract model. Without generic relations, we'd need separate Inventory tables for each product type, violating the DRY principle.

To implement a generic relation, we need three components in the Inventory model. First, a content_type field which is a ForeignKey to the ContentType model - this stores which model we're relating to. Second, an object_id field, typically a PositiveIntegerField, which stores the primary key of the specific instance we're relating to. Third, the GenericForeignKey field itself, which combines content_type and object_id to create the dynamic relationship. When creating an Inventory record, Django automatically looks up the appropriate ContentType id based on the model class you pass.

In the related models like Ring or Necklace, we define a GenericRelation field pointing back to Inventory. This enables reverse lookups, so we can access all inventory records for a specific product.

However, GenericForeignKey has important limitations. It makes CRUD operations more complex because you must manually handle content_type and object_id. More critically, it doesn't enforce database-level integrity. Unlike regular ForeignKeys which create actual database constraints, GenericForeignKey only exists in Django's Python layer. The database sees two separate integer fields with no relationship between them. This means if a product is deleted directly in the database, orphaned Inventory records can remain, pointing to non-existent products. For these reasons, generic relations should be used sparingly, only when the flexibility truly outweighs these drawbacks.

```python
from django.contrib.contenttypes.fields import GenericForeignKey, GenericRelation
from django.contrib.contenttypes.models import ContentType
from django.db import models

# Abstract base class
class BaseProduct(models.Model):
    name = models.CharField(max_length=100)
    inventory = GenericRelation('Inventory')  # Reverse relation

    class Meta:
        abstract = True

# Child models
class Ring(BaseProduct):
    pass

class Necklace(BaseProduct):
    pass

class Bracelet(BaseProduct):
    pass

# Model with GenericForeignKey
class Inventory(models.Model):
    quantity = models.PositiveIntegerField()
    price = models.DecimalField(max_digits=10, decimal_places=2)

    # Three required components for GenericForeignKey
    content_type = models.ForeignKey(ContentType, on_delete=models.CASCADE)
    object_id = models.PositiveIntegerField()
    product = GenericForeignKey('content_type', 'object_id')

# Usage example
ring = Ring.objects.create(name="Gold Ring")
print(ContentType.objects.get_for_model(Ring))
# Output: ContentType object (id=23, app_label='products', model='ring')

# Creating inventory - Django handles content_type automatically
inventory = Inventory.objects.create(
    quantity=10,
    price=299.99,
    product=ring  # Django sets content_type and object_id automatically
)

print(inventory.content_type.id)  # Output: 23 (ContentType id for Ring)
print(inventory.object_id)  # Output: 1 (Ring's primary key)
print(inventory.product)  # Output: Ring object
print(ring.inventory.all())  # Reverse lookup via GenericRelation
# Output: <QuerySet [<Inventory: Inventory object (1)>]>
```
[Back to Table of Contents](#table-of-contents)

### 115. What are the different ways to extend the Django User model? When would you use each approach?

There are four main ways to extend Django's User model, each suited for different scenarios.

The first and simplest approach is using a OneToOneField to create a Profile model. You keep Django's default User model and create a separate Profile model linked with a OneToOneField. This is best when you only need to add a few extra fields like bio, avatar, or phone number, and you're satisfied with Django's authentication fields. It's the easiest to implement and doesn't require changing AUTH_USER_MODEL in settings.

The second approach is extending AbstractUser by creating a custom user model that inherits from it. AbstractUser provides all the standard fields like username, email, first_name, last_name, is_active, and is_staff. It inherits from AbstractBaseUser, which handles password functionality, and PermissionsMixin, which handles permissions. You can add additional fields directly to this model. This approach is recommended when starting a new project because it gives you flexibility to add fields later. You must set AUTH_USER_MODEL in settings to point to your custom model.

The third approach is inheriting from AbstractBaseUser and PermissionsMixin directly. This gives you complete control over authentication fields. You define exactly which fields you want for authentication, such as using email instead of username. You must create a custom manager class inheriting from BaseUserManager to handle user creation. This approach is best when you need full control over authentication or want to use a different identifier than username. It also allows you to separate authentication data from profile data by keeping only essential auth fields in the User model and putting other information in a related Profile model.

The fourth approach is using a Proxy model. You inherit from Django's User model and add custom methods or change the default ordering, but you don't add new fields or create a new database table. This is useful when you only need to add behavior or methods to the User model without modifying its structure. Proxy models are lightweight and don't affect the database schema.

```python
from django.contrib.auth.models import AbstractUser, AbstractBaseUser, PermissionsMixin, BaseUserManager, User
from django.db import models

# Approach 1: OneToOneField (Profile pattern)
class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField(blank=True)
    avatar = models.ImageField(upload_to='avatars/', blank=True)
    phone = models.CharField(max_length=15, blank=True)

# Usage
user = User.objects.create_user(username='john', password='pass123')
profile = Profile.objects.create(user=user, bio='Software developer', phone='123-456-7890')
print(user.profile.bio)  # Output: Software developer

# Approach 2: Extending AbstractUser
class CustomUser(AbstractUser):
    age = models.PositiveIntegerField(null=True, blank=True)
    bio = models.TextField(blank=True)

# In settings.py: AUTH_USER_MODEL = 'myapp.CustomUser'

# Usage
custom_user = CustomUser.objects.create_user(
    username='alice',
    password='pass123',
    age=25,
    bio='Designer'
)
print(custom_user.username, custom_user.age)  # Output: alice 25

# Approach 3: AbstractBaseUser + PermissionsMixin (full control)
class CustomUserManager(BaseUserManager):
    def create_user(self, email, password=None, **extra_fields):
        if not email:
            raise ValueError('Email is required')
        email = self.normalize_email(email)
        user = self.model(email=email, **extra_fields)
        user.set_password(password)
        user.save()
        return user

    def create_superuser(self, email, password=None, **extra_fields):
        extra_fields.setdefault('is_staff', True)
        extra_fields.setdefault('is_superuser', True)
        return self.create_user(email, password, **extra_fields)

class FullCustomUser(AbstractBaseUser, PermissionsMixin):
    email = models.EmailField(unique=True)
    full_name = models.CharField(max_length=100)
    is_active = models.BooleanField(default=True)
    is_staff = models.BooleanField(default=False)

    objects = CustomUserManager()

    USERNAME_FIELD = 'email'  # Use email for authentication
    REQUIRED_FIELDS = ['full_name']

# Usage
full_user = FullCustomUser.objects.create_user(
    email='bob@example.com',
    password='pass123',
    full_name='Bob Smith'
)
print(full_user.email)  # Output: bob@example.com

# Approach 4: Proxy Model (add methods only, no new fields)
class ProxyUser(User):
    class Meta:
        proxy = True
        ordering = ['date_joined']

    def get_full_display_name(self):
        return f"{self.first_name} {self.last_name} (@{self.username})"

    def is_new_user(self):
        from django.utils import timezone
        return (timezone.now() - self.date_joined).days < 30

# Usage - uses same User table
proxy_user = ProxyUser.objects.get(username='john')
print(proxy_user.get_full_display_name())  # Output: John Doe (@john)
print(proxy_user.is_new_user())  # Output: True/False
```
[Back to Table of Contents](#table-of-contents)

### 116. Can you explain what the Same-Origin Policy (SOP) is and what defines a "same origin"?

The Same-Origin Policy (SOP) is a security feature built into all web browsers that's always active and cannot be disabled. It restricts how JavaScript running on one origin can interact with resources from another origin.

An origin is defined by three parts: protocol (like http or https), domain (like myapp.com), and port (like 80, 443, or 8000). All three must match for two URLs to be considered same-origin.

When you're using Server-Side Rendering like pure Django, everything happens on the same origin. For example, if your Django app is running at `myapp.com/page`, all your pages, forms, and API endpoints are on the same origin, so the browser allows JavaScript to read all responses.

However, if you have a separate frontend and backend, like React running at `myapp.com/page` and Django REST Framework running at `api.myapp.com/page`, these are different origins because of the subdomain. The browser will block JavaScript running on the React app from reading responses from the DRF backend.

SOP specifically blocks JavaScript from reading the content of responses when making requests to different origins. This means your JavaScript code cannot see what data came back from a cross-origin API call. However, the browser still allows certain things to happen across origins, like submitting forms or loading images and scripts, because these don't give JavaScript access to read the response content.

To allow specific origins to read your server responses, you configure CORS (Cross-Origin Resource Sharing). CORS sends headers with your server responses that tell the browser which origins are allowed to read the response.

```python
# Origin comparison examples for: https://myapp.com/page

# SAME-ORIGIN examples (protocol + domain + port match):
same_origin_urls = [
    "https://myapp.com/about",      # ✓ All parts match
    "https://myapp.com/api/users",  # ✓ All parts match
]

# DIFFERENT-ORIGIN examples:
different_origin_urls = {
    "http://myapp.com/page": "Different protocol (http vs https)",
    "https://api.myapp.com/page": "Different domain (subdomain matters)",
    "https://myapp.com:8000/page": "Different port (8000 vs 443)",
    "https://anotherapp.com/page": "Different domain completely",
}

# Visualization of origin parts:
print("Origin breakdown for: https://myapp.com/page")
print("Protocol: https")
print("Domain: myapp.com")
print("Port: 443 (default for https)")
print()

print("Same-origin examples:")
for url in same_origin_urls:
    print(f"  {url} ✓")
print()

print("Different-origin examples:")
for url, reason in different_origin_urls.items():
    print(f"  {url} ✗ ({reason})")

# Output:
# Origin breakdown for: https://myapp.com/page
# Protocol: https
# Domain: myapp.com
# Port: 443 (default for https)
#
# Same-origin examples:
#   https://myapp.com/about ✓
#   https://myapp.com/api/users ✓
#
# Different-origin examples:
#   http://myapp.com/page ✗ (Different protocol (http vs https))
#   https://api.myapp.com/page ✗ (Different domain (subdomain matters))
#   https://myapp.com:8000/page ✗ (Different port (8000 vs 443))
#   https://anotherapp.com/page ✗ (Different domain completely)

# What SOP blocks vs allows:
print("\n--- What SOP blocks ---")
print("JavaScript reading cross-origin fetch/AJAX responses")
"""
fetch('https://api.myapp.com/users')  # Request is sent
    .then(response => response.json())  # ✗ BLOCKED - can't read response
"""

print("\n--- What SOP allows (no JavaScript reading involved) ---")
print("1. Form submissions to other origins")
"""
<form action="https://api.myapp.com/submit" method="POST">
    <!-- ✓ ALLOWED - form submits, but JS can't read response -->
</form>
"""
print("2. Loading images from other origins")
"""
<img src="https://cdn.example.com/image.jpg">  # ✓ ALLOWED
"""
print("3. Loading scripts from other origins")
"""
<script src="https://cdn.example.com/script.js"></script>  # ✓ ALLOWED
"""

# CORS configuration example:
# Django settings.py
INSTALLED_APPS = [
    'corsheaders',
]

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware',
]

CORS_ALLOWED_ORIGINS = [
    "https://myapp.com",
]

print("\n--- With CORS configured ---")
print("Now https://myapp.com can read responses from https://api.myapp.com")
```
[Back to Table of Contents](#table-of-contents)

### 117. What is CSRF attack and how does Django protect against it?

CSRF (Cross-Site Request Forgery) is an attack where a malicious website tricks a user's browser into making unwanted requests to a different website where the user is authenticated. For example, if you're logged into your bank and visit a malicious site, that site could submit a form to your bank using your authenticated session since cookies are stored in the browser and automatically sent with requests to that domain, regardless of which tab or site initiates the request.

Django protects against CSRF using the CSRFViewMiddleware. When we create a template that contains a POST form, we put the `{% csrf_token %}` tag inside the form. Django generates a CSRF token (a random secret value) and includes it in the form as a hidden field. When the form is submitted via POST, Django checks if the token is present and valid. If it matches, the request is processed; if not, it's rejected with a 403 error.

The attacker cannot forge this request because the Same-Origin Policy prevents their malicious website from reading the CSRF token sent in the response - the HTML to be rendered. When the attacker's JavaScript tries to fetch your bank's form page to extract the token, the browser blocks the JavaScript from reading the response content. Even though the browser automatically sends cookies with requests, the attacker cannot access the token value from the form to include it in their fake form.

```python
# Django view with CSRF protection
from django.shortcuts import render
from django.views.decorators.csrf import csrf_protect

@csrf_protect
def transfer_money(request):
    if request.method == 'POST':
        # Django automatically checks CSRF token here
        amount = request.POST.get('amount')
        recipient = request.POST.get('recipient')
        print(f"Transfer {amount} to {recipient}")
        return render(request, 'success.html')
    return render(request, 'transfer_form.html')

# In template (transfer_form.html):
"""
<form method="POST" action="/transfer/">
    {% csrf_token %}
    <!-- Django generates hidden field with token: -->
    <!-- <input type="hidden" name="csrfmiddlewaretoken" value="abc123xyz..."> -->
    <input type="text" name="amount" placeholder="Amount">
    <input type="text" name="recipient" placeholder="Recipient">
    <button type="submit">Transfer</button>
</form>
"""

# How CSRF attack is prevented:
print("--- Attacker's attempt ---")
print("1. Attacker creates fake form on malicious.com")
print("2. Attacker's JavaScript tries to get the token:")
"""
// On malicious.com:
fetch('https://bank.com/transfer/')
    .then(response => response.text())
    .then(html => {
        // Try to extract CSRF token from the HTML form
        const token = extractToken(html);  # ✗ BLOCKED by SOP!
        // Cannot read the response - SOP prevents it!
    })
"""
print("3. SOP blocks JavaScript from reading bank.com's HTML response")
print("4. Attacker cannot get the token from the form")
print("5. Fake form submission fails - no valid CSRF token!")

# What the attacker CAN do (but it's useless):
"""
<!-- Attacker's fake form on malicious.com -->
<form action="https://bank.com/transfer/" method="POST">
    <input type="hidden" name="amount" value="10000">
    <input type="hidden" name="recipient" value="attacker_account">
    <!-- Missing: CSRF token! -->
    <button type="submit">Click here for prize!</button>
</form>
"""
print("\n--- Result ---")
print("Form submits to bank.com")
print("Cookies are sent automatically by the browser")
print("But Django checks: 'Where is the CSRF token?'")
print("No token → Django returns 403 Forbidden")
print("Transfer blocked! ✓")

# Example without CSRF protection (DANGEROUS - don't do this):
from django.views.decorators.csrf import csrf_exempt

@csrf_exempt  # DON'T DO THIS IN PRODUCTION!
def unsafe_transfer(request):
    if request.method == 'POST':
        # No CSRF check - vulnerable to attacks!
        amount = request.POST.get('amount')
        print(f"Unsafe transfer: {amount}")
    return render(request, 'form.html')

# Output demonstration:
# --- Attacker's attempt ---
# 1. Attacker creates fake form on malicious.com
# 2. Attacker's JavaScript tries to get the token:
# 3. SOP blocks JavaScript from reading bank.com's HTML response
# 4. Attacker cannot get the token from the form
# 5. Fake form submission fails - no valid CSRF token!
#
# --- Result ---
# Form submits to bank.com
# Cookies are sent automatically by the browser
# But Django checks: 'Where is the CSRF token?'
# No token → Django returns 403 Forbidden
# Transfer blocked! ✓
```
[Back to Table of Contents](#table-of-contents)

### 118. What is XSS (Cross-Site Scripting) attack? How does it work and how does Django protect against it?

XSS, or Cross-Site Scripting, is a security vulnerability where an attacker injects malicious JavaScript into a website that then executes in other users' browsers.

Let me give you an example with Stored XSS. Imagine a blog with a comment section. An attacker posts a comment containing `<script>alert('Hacked')</script>`, and this malicious code gets **saved directly into the database**. Now, when other users visit the page and view the comments, the browser reads this script from the database and executes it. In this simple example, users would just see an alert saying 'Hacked', but in a real attack, the attacker could use similar techniques to steal users' session cookies or login credentials.

Django protects against XSS automatically by escaping all variables in templates. When you render data using double curly braces like `{{ comment.content }}`, Django automatically converts dangerous HTML characters before sending them to the browser. Specifically, it converts the less-than sign `<` into `&lt;`, the greater-than sign `>` into `&gt;`, quotes into `&quot;`, and so on. These are called HTML entities. So when the browser receives `&lt;script&gt;`, it displays it as the text `<script>` instead of treating it as executable code.

Of course, Django also provides ways to mark content as safe when you intentionally want to render HTML, like the `safe` filter or `mark_safe` function, but those should be used very carefully and only with trusted content.

```python
# views.py
from django.shortcuts import render
from .models import Comment

def blog_post(request):
    comments = Comment.objects.all()
    return render(request, 'blog.html', {'comments': comments})


# models.py
from django.db import models

class Comment(models.Model):
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)


# blog.html (Django Template)
"""
<h2>Comments</h2>
{% for comment in comments %}
    <div class="comment">
        <!-- Django automatically escapes this - SAFE -->
        <p>{{ comment.content }}</p>
    </div>
{% endfor %}

<!-- Example of what NOT to do (disables protection) -->
<div>
    {{ comment.content|safe }}  <!-- DANGEROUS - only use with trusted content -->
</div>
"""

# What happens:
# Attacker saves: <script>alert('XSS')</script>
# Django displays: &lt;script&gt;alert('XSS')&lt;/script&gt;
# User sees: <script>alert('XSS')</script> (as plain text, not executed)
```
[Back to Table of Contents](#table-of-contents)

### 119. What is SQL Injection and how does Django protect against it?

SQL Injection is a security vulnerability where an attacker manipulates database queries by injecting malicious SQL code through user input.

Let me give you a concrete example. Imagine a login form where a user enters their username. Without protection, the application might construct a SQL query like this: `SELECT * FROM users WHERE username = 'user_input'`. Notice the single quotes around the user input - the developer puts them there because usernames are text values in SQL.

An attacker could exploit this by typing `admin' OR '1'='1` into the username field. Here's what happens step by step: The developer's code has opening and closing quotes around the username. When the attacker types `admin' OR '1'='1`, that single quote after 'admin' closes the developer's opening quote. This breaks the attacker out of the string, and now they're writing SQL code instead of text. Then they add `OR '1'='1` which is their own SQL logic. The developer's closing quote ends up closing the attacker's missing second quote. So the final query becomes `SELECT * FROM users WHERE username = 'admin' OR '1'='1'`.

Now here's why this returns all users: SQL evaluates the WHERE clause for each row in the table. Let's say we have users alice, bob, and admin. For alice, SQL checks: is username 'admin'? No, false. OR is '1' equal to '1'? Yes, true. Since we have false OR true, that equals true, so alice is included. For bob, same thing: username is not 'admin' so false, but '1'='1' is always true, so false OR true equals true, bob is included. For admin, username is 'admin' so true, and '1'='1' is also true, so true OR true equals true, admin is included. Because '1'='1' is always true regardless of what's in the row, every single row satisfies the WHERE condition and gets returned. The attacker bypasses authentication completely.

Django protects against SQL Injection by using parameterized queries through its ORM. This means Django separates the SQL command from the user input. When you write `User.objects.filter(username=user_input)`, Django creates a SQL query with a placeholder, something like `SELECT * FROM users WHERE username = %s`, and then sends the user input separately to the database. So even if an attacker enters `admin' OR '1'='1`, the database treats this entire string as literal data to search for, not as SQL code. It would look for a user whose username is exactly `admin' OR '1'='1`, which doesn't exist, so the attack fails.

Of course, Django also allows raw SQL queries for complex cases, but you should always use parameterized queries with placeholders instead of string formatting to maintain this protection.

```python
# views.py
from django.shortcuts import render
from .models import User

def login_view(request):
    username = request.POST.get('username')

    # SAFE: Django ORM uses parameterized queries automatically
    user = User.objects.filter(username=username).first()

    # What Django does behind the scenes:
    # SQL: SELECT * FROM users WHERE username = %s
    # Parameters: ['admin\' OR \'1\'=\'1']  (treated as literal text)
    # Result: Searches for username exactly matching "admin' OR '1'='1" - returns nothing

    return render(request, 'profile.html', {'user': user})


# UNSAFE EXAMPLE (DON'T DO THIS):
from django.db import connection

def unsafe_login(request):
    username = request.POST.get('username')

    # DANGEROUS: String formatting - vulnerable to SQL injection
    cursor = connection.cursor()
    query = f"SELECT * FROM users WHERE username = '{username}'"
    cursor.execute(query)

    # Developer expects: SELECT * FROM users WHERE username = 'alice'
    # Attacker enters: admin' OR '1'='1
    # Query becomes: SELECT * FROM users WHERE username = 'admin' OR '1'='1'
    #
    # How the quotes match:
    # SELECT * FROM users WHERE username = 'admin' OR '1'='1'
    #                                       ^     ^    ^   ^
    #                                       Dev   Att  Att Dev
    #
    # The attacker's quote closes the developer's opening quote!
    # Then they inject: OR '1'='1' (always true for every row)
    #
    # SQL evaluates for each row:
    # alice:  username='admin' (False) OR '1'='1' (True) = True ✓ Include
    # bob:    username='admin' (False) OR '1'='1' (True) = True ✓ Include
    # admin:  username='admin' (True)  OR '1'='1' (True) = True ✓ Include
    # Result: ALL users returned - authentication bypassed!


# SAFE RAW SQL (if you must use raw queries):
def safe_raw_query(request):
    username = request.POST.get('username')

    # SAFE: Using parameterized query with placeholder
    cursor = connection.cursor()
    cursor.execute("SELECT * FROM users WHERE username = %s", [username])

    # Even if attacker enters: admin' OR '1'='1
    # Database treats entire string as data, not SQL code
    # Searches for username = "admin' OR '1'='1" (literal text) - returns nothing
```
[Back to Table of Contents](#table-of-contents)

## Django REST framework

### 120. What is an API?

An Application Programming Interface, or API, is the way we interact with a given application. It defines a set of rules and standards for how different software can communicate with each other. An API allows us to request data or perform actions without needing to know all the internal details of how the application works.

For example, a REST API is a common type of API that follows REST principles. With a REST API, we communicate using HTTP methods and URLs. If we have a REST API for managing users, we can make requests like: a GET request to `/users/` to retrieve a list of all users, a POST request to `/users/` to create a new user, a GET request to `/users/123/` to retrieve a specific user with ID 123, a PUT request to `/users/123/` to update that user, or a DELETE request to `/users/123/` to delete that user. The server responds with data in a standard format, usually JSON, and a status code to indicate whether the request was successful.

This standardization allows different clients like web browsers, mobile applications, or other services to use the same API and communicate with the server in a consistent way. The API acts as a contract between the client and server, making it easy for developers to understand how to interact with the application.

```python
# REST API example - how a client uses an API
import requests

# GET - retrieve all users
response = requests.get('https://api.example.com/users/')
users = response.json()

# POST - create a new user
new_user = {'name': 'Alice', 'email': 'alice@example.com'}
response = requests.post('https://api.example.com/users/', json=new_user)
created_user = response.json()

# GET - retrieve a specific user
response = requests.get('https://api.example.com/users/123/')
user = response.json()

# PUT - update a user
updated_data = {'name': 'Alice Smith'}
response = requests.put('https://api.example.com/users/123/', json=updated_data)

# DELETE - delete a user
response = requests.delete('https://api.example.com/users/123/')
```
[Back to Table of Contents](#table-of-contents)

### 121. What are serializers in DRF and why do we use them?

Serializers in Django Rest Framework are classes that convert data between different formats. When a client sends data to our API, serializers deserialize it - meaning they convert JSON into Python objects and validate that the data is correct. When we send a response back, serializers serialize our Python objects like model instances or querysets into JSON format. Serializers also handle validation automatically, checking things like required fields, data types, and custom validation rules. This makes it easy to work with complex data and ensure data integrity in our API.

```python
from rest_framework import serializers
from django.contrib.auth.models import User

# Define a simple serializer
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'username', 'email']

# Deserialization: JSON to Python object
incoming_data = {'username': 'john_doe', 'email': 'john@example.com'}
serializer = UserSerializer(data=incoming_data)
if serializer.is_valid():
    user = serializer.save()
    print(f"Created user: {user.username}")  # Output: Created user: john_doe

# Serialization: Python object to JSON
user_instance = User.objects.get(username='john_doe')
serializer = UserSerializer(user_instance)
print(serializer.data)  # Output: {'id': 1, 'username': 'john_doe', 'email': 'john@example.com'}

# Validation example
invalid_data = {'username': '', 'email': 'not-an-email'}
serializer = UserSerializer(data=invalid_data)
print(serializer.is_valid())  # Output: False
print(serializer.errors)  # Output: {'username': ['This field may not be blank.'], 'email': ['Enter a valid email address.']}
```
[Back to Table of Contents](#table-of-contents)

### 122. What is the difference between ModelSerializer and regular Serializer?

Just like with ModelForm and Form in Django, we use ModelSerializer when we work with a model. Instead of writing all the fields manually, ModelSerializer creates them automatically based on the model fields. It also includes all the validations defined in the model. Additionally, ModelSerializer automatically provides `create()` and `update()` methods for saving data to the database, which saves us time. We use a regular Serializer when no model is involved or when we need custom data structures that don't match our models. For example, we would use a regular Serializer for a search form or for combining data from multiple models in a custom way.

The `create()` method is called when we save a new object to the database using `serializer.save()`. The `update()` method is called when we save changes to an existing object. With ModelSerializer, both methods are created automatically - `create()` uses `Model.objects.create()` to make a new record, and `update()` updates each field on the existing instance and saves it. With a regular Serializer, we must write these methods ourselves. There is no `delete()` method because serializers handle data conversion and validation, not deletion - we delete objects directly using `instance.delete()` in our views.

When using a regular Serializer, the method names `create` and `update` are special and recognized by Django REST Framework. When you call `serializer.save()`, DRF automatically looks for these methods and calls them without you explicitly naming them. If you name your methods something else, like `save_to_db()` or `create_record()`, you must call them manually instead of using `serializer.save()`. This is why we follow the convention of naming them `create` and `update` - it ensures that `serializer.save()` works automatically, making the code cleaner and more consistent with Django REST Framework's design.

```python
from rest_framework import serializers
from django.db import models

# Define a simple model
class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=100)
    published_year = models.IntegerField()

    class Meta:
        app_label = 'books'

# ModelSerializer - automatically creates fields and create()/update() methods
class BookModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ['id', 'title', 'author', 'published_year']

# Regular Serializer - fields must be defined manually
class BookSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    title = serializers.CharField(max_length=100)
    author = serializers.CharField(max_length=100)
    published_year = serializers.IntegerField()

    # Must write create() and update() manually with exact names
    def create(self, validated_data):
        return Book.objects.create(**validated_data)

    def update(self, instance, validated_data):
        for attr, value in validated_data.items():
            setattr(instance, attr, value)
        instance.save()
        return instance

# Example: Using ModelSerializer - create() is automatic
data = {'title': 'Django for Beginners', 'author': 'William Vincent', 'published_year': 2023}
serializer = BookModelSerializer(data=data)
if serializer.is_valid():
    book = serializer.save()  # Automatically calls create()
    print(f"Created: {book.title} by {book.author}")
    # Output: Created: Django for Beginners by William Vincent

# Example: Using regular Serializer - create() is called automatically because of the method name
data = {'title': 'REST APIs', 'author': 'Mark Williams', 'published_year': 2022}
serializer = BookSerializer(data=data)
if serializer.is_valid():
    book = serializer.save()  # Automatically calls create() because method is named 'create'
    print(f"Saved: {book.title}")
    # Output: Saved: REST APIs

# Example: If we name the method differently, we must call it explicitly
class BookSerializerCustom(serializers.Serializer):
    title = serializers.CharField(max_length=100)
    author = serializers.CharField(max_length=100)
    published_year = serializers.IntegerField()

    def save_to_db(self, validated_data):  # Different name - not 'create'
        return Book.objects.create(**validated_data)

data = {'title': 'Python Guide', 'author': 'John Doe', 'published_year': 2023}
serializer = BookSerializerCustom(data=data)
if serializer.is_valid():
    book = serializer.save_to_db(serializer.validated_data)  # Must call EXPLICITLY
    print(f"Saved with custom method: {book.title}")
    # Output: Saved with custom method: Python Guide

# Example: Updating with regular Serializer - update() is called automatically
book_instance = Book.objects.first()
update_data = {'title': 'Updated Title', 'published_year': 2024}
serializer = BookSerializer(book_instance, data=update_data, partial=True)
if serializer.is_valid():
    updated_book = serializer.save()  # Automatically calls update() because method is named 'update'
    print(f"Updated: {updated_book.title} ({updated_book.published_year})")
    # Output: Updated: Updated Title (2024)
```
[Back to Table of Contents](#table-of-contents)

### 123. What are ViewSets and how are they different from regular API views?

ViewSets differ from regular API views by providing all CRUD operations in a single class. A ViewSet includes methods like `list()`, `create()`, `retrieve()`, `update()`, and `destroy()` all in one place. When using ViewSets, we can take advantage of the DefaultRouter, which automatically creates all URL paths for every method in the ViewSet. This saves us time and reduces code. I would use a ViewSet when I need all or most CRUD operations for a resource and want uniform URL patterns. However, if I only need one or two operations, like just listing and retrieving data without creating or updating, I would use regular API views instead because they give me more control.

```python
from rest_framework import viewsets
from rest_framework.decorators import action
from rest_framework.response import Response
from rest_framework.views import APIView
from rest_framework import status
from django.db import models

# Define a simple model
class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    author = models.CharField(max_length=100)

    class Meta:
        app_label = 'articles'

# Define a serializer
from rest_framework import serializers

class ArticleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = ['id', 'title', 'content', 'author']

# ViewSet - provides all CRUD operations in one class
class ArticleViewSet(viewsets.ModelViewSet):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer

    # These methods are automatically included:
    # list() - GET /articles/
    # create() - POST /articles/
    # retrieve() - GET /articles/{id}/
    # update() - PUT /articles/{id}/
    # destroy() - DELETE /articles/{id}/

# Regular API Views - only provide specific operations
class ArticleListView(APIView):
    def get(self, request):
        articles = Article.objects.all()
        serializer = ArticleSerializer(articles, many=True)
        return Response(serializer.data)

    def post(self, request):
        serializer = ArticleSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

class ArticleDetailView(APIView):
    def get(self, request, pk):
        article = Article.objects.get(pk=pk)
        serializer = ArticleSerializer(article)
        return Response(serializer.data)

# Example: Using ViewSet with DefaultRouter (automatically creates all URLs)
from rest_framework.routers import DefaultRouter

router = DefaultRouter()
router.register(r'articles', ArticleViewSet)
# Automatically creates:
# GET /articles/ - list all articles
# POST /articles/ - create new article
# GET /articles/{id}/ - retrieve specific article
# PUT /articles/{id}/ - update article
# DELETE /articles/{id}/ - delete article

# Example: Using regular API Views (manual URL configuration needed)
# urlpatterns = [
#     path('articles/', ArticleListView.as_view()),  # list and create
#     path('articles/<int:pk>/', ArticleDetailView.as_view()),  # retrieve only
# ]
# With regular views, we only have GET (list and retrieve), POST (create)
# We don't have PUT (update) or DELETE unless we add them manually

# Output example:
print("ViewSet with DefaultRouter provides all CRUD operations automatically")
print("URL patterns created: /articles/, /articles/{id}/")
print()
print("Regular API Views give more control but require manual URL and method setup")
print("Only GET and POST are implemented in this example")
```
[Back to Table of Contents](#table-of-contents)

### 124. What is the difference between authentication and permission in DRF?

Authentication is the process in which we identify who we are and prove it. For example, when we sign in to a website, we say who we are by entering our username and we prove it by providing our password. In DRF, we have authentication classes like TokenAuthentication, SessionAuthentication, or JWT authentication. Permissions are related to authorization - what data we are authorized to access and what actions we are authorized to perform. In DRF we have permission classes like AllowAny, IsAuthenticated, and IsAdminUser. For example, if we set the permission_classes to IsAdminUser on a view, then only admin users would be able to access that endpoint. We can also create custom permissions and define, for example, that only admins from a specific group could execute CRUD operations on a given resource.

```python
from rest_framework.authentication import TokenAuthentication, SessionAuthentication
from rest_framework.permissions import AllowAny, IsAuthenticated, IsAdminUser, BasePermission
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from django.contrib.auth.models import User
from django.db import models

# Define a simple model
class BlogPost(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    author = models.ForeignKey(User, on_delete=models.CASCADE)

    class Meta:
        app_label = 'blog'

# Custom permission - only post author can edit or delete
class IsPostAuthor(BasePermission):
    def has_object_permission(self, request, view, obj):
        return obj.author == request.user

# View with Authentication - identifies WHO the user is
class BlogPostListView(APIView):
    authentication_classes = [TokenAuthentication, SessionAuthentication]
    # TokenAuthentication: User provides a token in the Authorization header
    # SessionAuthentication: User logs in and gets a session cookie
    # Both identify who the user is
    permission_classes = [AllowAny]

    def get(self, request):
        posts = BlogPost.objects.all()
        return Response({"posts": "Anyone can view this"})

# View with Permission - controls WHAT authenticated users can do
class BlogPostDetailView(APIView):
    authentication_classes = [TokenAuthentication]
    permission_classes = [IsAuthenticated]  # Only authenticated users can access
    # Even if we know who you are (authentication),
    # you still need permission to access this endpoint

    def get(self, request, pk):
        return Response({"message": f"Hello {request.user}, you are authenticated"})

# View with IsAdminUser permission - only admins can access
class AdminOnlyView(APIView):
    authentication_classes = [TokenAuthentication]
    permission_classes = [IsAdminUser]  # Only admin users can access
    # First we authenticate (identify the user)
    # Then we check if they have admin permission

    def get(self, request):
        return Response({"message": "Only admins can see this"})

# View with custom permission - only post author can edit
class BlogPostEditView(APIView):
    authentication_classes = [TokenAuthentication]
    permission_classes = [IsAuthenticated, IsPostAuthor]  # Must be logged in AND be the author

    def put(self, request, pk):
        post = BlogPost.objects.get(pk=pk)
        # First: Authentication identifies who you are
        # Second: IsAuthenticated checks if you're logged in
        # Third: IsPostAuthor checks if you're the post author
        return Response({"message": f"Post updated by {request.user}"})

# Example flow:
print("AUTHENTICATION - Who are you?")
print("Token: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...")
print("Session: sessionid=abc123xyz...")
print("Result: User is identified as 'john_doe'")
print()

print("PERMISSION - What can you do?")
print("AllowAny: Anyone can access (no permission needed)")
print("IsAuthenticated: Only logged-in users can access")
print("IsAdminUser: Only admin users can access")
print("IsPostAuthor: Only the post author can access")
print()

print("Combined flow:")
print("1. User sends request with token → Authentication identifies them as 'john_doe'")
print("2. System checks if 'john_doe' is authenticated → IsAuthenticated ✓")
print("3. System checks if 'john_doe' is the post author → IsPostAuthor ✓")
print("4. Access granted - john_doe can edit the post")
```
[Back to Table of Contents](#table-of-contents)

### 125. What is pagination in DRF and why do we use it?

Pagination means splitting large datasets into smaller portions. This makes the client-server communication faster in several ways. First, we retrieve a smaller amount of objects from the database. Second, the backend processes the request faster because it has to handle fewer objects. Third, because the data is smaller, it travels faster over the network. This also improves the user experience because the user has to wait less time to see results, especially when they're not interested in seeing the entire dataset at once. DRF provides built-in pagination classes that make this very easy to implement. I have used PageNumberPagination in my last project. I created a custom pagination class where I set the page size, and inherited from PageNumberPagination. Then I either set it in the view's pagination_class attribute or configured it globally in settings. DRF also provides other pagination styles like LimitOffsetPagination and CursorPagination for different use cases.

```python
from rest_framework.pagination import PageNumberPagination, LimitOffsetPagination, CursorPagination
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework.viewsets import ModelViewSet
from django.db import models
from rest_framework import serializers

# Define a simple model
class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=10, decimal_places=2)

    class Meta:
        app_label = 'shop'

class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = ['id', 'name', 'price']

# WITHOUT Pagination - returns all 10,000 products at once (slow!)
class ProductListNoPaginationView(APIView):
    def get(self, request):
        products = Product.objects.all()  # All 10,000 products loaded
        serializer = ProductSerializer(products, many=True)
        return Response(serializer.data)
        # Response is huge, network is slow, client waits forever

# WITH Pagination - returns only 10 products per page (fast!)
# Custom PageNumberPagination class
class CustomPageNumberPagination(PageNumberPagination):
    page_size = 10  # Show 10 products per page
    page_query_param = 'page'
    max_page_size = 100

# View using PageNumberPagination
class ProductListView(APIView):
    def get(self, request):
        products = Product.objects.all()
        paginator = CustomPageNumberPagination()
        paginated_products = paginator.paginate_queryset(products, request)
        serializer = ProductSerializer(paginated_products, many=True)
        return paginator.get_paginated_response(serializer.data)
        # GET /products/?page=1 returns products 1-10
        # GET /products/?page=2 returns products 11-20
        # Response: {"count": 10000, "next": "...", "previous": null, "results": [...]}

# ViewSet with pagination_class attribute
class ProductViewSet(ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    pagination_class = CustomPageNumberPagination
    # Automatically paginates all list responses

# LimitOffsetPagination - user specifies limit and offset
class ProductLimitOffsetView(APIView):
    def get(self, request):
        products = Product.objects.all()
        paginator = LimitOffsetPagination()
        paginated_products = paginator.paginate_queryset(products, request)
        serializer = ProductSerializer(paginated_products, many=True)
        return paginator.get_paginated_response(serializer.data)
        # GET /products/?limit=10&offset=0 returns first 10 products
        # GET /products/?limit=10&offset=10 returns products 11-20
        # More flexible than page numbers

# CursorPagination - efficient for large datasets
class ProductCursorView(APIView):
    def get(self, request):
        products = Product.objects.all()
        paginator = CursorPagination()
        paginated_products = paginator.paginate_queryset(products, request)
        serializer = ProductSerializer(paginated_products, many=True)
        return paginator.get_paginated_response(serializer.data)
        # GET /products/?cursor=cD0yNzI4NQ%3D%3D returns next 10 products
        # Best for real-time data that changes frequently

# Example responses:
print("WITHOUT Pagination:")
print("GET /products/")
print("Returns: 10,000 products in one response - SLOW!")
print()

print("WITH PageNumberPagination:")
print("GET /products/?page=1")
print('''Returns: {
    "count": 10000,
    "next": "http://api.example.com/products/?page=2",
    "previous": null,
    "results": [
        {"id": 1, "name": "Product 1", "price": "29.99"},
        {"id": 2, "name": "Product 2", "price": "39.99"},
        ...10 products total...
    ]
}''')
print()

print("WITH LimitOffsetPagination:")
print("GET /products/?limit=10&offset=20")
print("Returns: 10 products starting from position 20")
print()

print("Performance improvement:")
print("- Database retrieves only 10 products instead of 10,000")
print("- Backend processes 10 objects instead of 10,000")
print("- Network sends smaller response (faster transfer)")
print("- User sees results immediately")
```
[Back to Table of Contents](#table-of-contents)

### 126. What are nested serializers and when do you use them?

We use nested serializers when we need to include data from related models in our API response. For example, if we have a User model and an Order model with a relationship between them, we can nest the OrderSerializer inside the UserSerializer to show all orders for each user. We define this by adding a field like `orders = OrderSerializer(many=True, read_only=True)` in the UserSerializer. The `many=True` attribute means one user can have multiple orders. This way, in a single API call, we get both the user data and all their related orders. However, nested serializers are read-only by default. If we need to create or update nested data, we must override the `create()` and `update()` methods in the parent serializer to manually handle saving the nested objects. This is one of the more complex parts of working with DRF.

```python
from rest_framework import serializers
from django.db import models

# Define related models
class User(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField()

    class Meta:
        app_label = 'shop'

class Order(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE, related_name='orders')
    product_name = models.CharField(max_length=100)
    quantity = models.IntegerField()
    price = models.DecimalField(max_digits=10, decimal_places=2)

    class Meta:
        app_label = 'shop'

class OrderItem(models.Model):
    order = models.ForeignKey(Order, on_delete=models.CASCADE, related_name='items')
    item_name = models.CharField(max_length=100)
    item_price = models.DecimalField(max_digits=10, decimal_places=2)

    class Meta:
        app_label = 'shop'

# Nested Serializer - read-only (GET requests)
class OrderSerializer(serializers.ModelSerializer):
    class Meta:
        model = Order
        fields = ['id', 'product_name', 'quantity', 'price']

class UserSerializerReadOnly(serializers.ModelSerializer):
    orders = OrderSerializer(many=True, read_only=True)
    # many=True because one user has multiple orders
    # read_only=True because we can only GET, not POST/PUT

    class Meta:
        model = User
        fields = ['id', 'name', 'email', 'orders']

# Example: GET request with nested serializer
print("GET /users/1/")
print('''Response: {
    "id": 1,
    "name": "John Doe",
    "email": "john@example.com",
    "orders": [
        {
            "id": 101,
            "product_name": "Laptop",
            "quantity": 1,
            "price": "999.99"
        },
        {
            "id": 102,
            "product_name": "Mouse",
            "quantity": 2,
            "price": "29.99"
        }
    ]
}''')
print()

# Nested Serializer with write capability (POST/PUT requests)
class OrderSerializerWritable(serializers.ModelSerializer):
    class Meta:
        model = Order
        fields = ['id', 'product_name', 'quantity', 'price']

class UserSerializerWritable(serializers.ModelSerializer):
    orders = OrderSerializerWritable(many=True, read_only=False)
    # read_only=False allows creating/updating nested orders

    class Meta:
        model = User
        fields = ['id', 'name', 'email', 'orders']

    def create(self, validated_data):
        # Extract nested orders data
        orders_data = validated_data.pop('orders', [])

        # Create the user first
        user = User.objects.create(**validated_data)

        # Then create each order for this user
        for order_data in orders_data:
            Order.objects.create(user=user, **order_data)

        return user

    def update(self, instance, validated_data):
        # Update user fields
        instance.name = validated_data.get('name', instance.name)
        instance.email = validated_data.get('email', instance.email)
        instance.save()

        # Update nested orders
        orders_data = validated_data.get('orders', [])
        if orders_data:
            instance.orders.all().delete()  # Delete old orders
            for order_data in orders_data:
                Order.objects.create(user=instance, **order_data)

        return instance

# Example: POST request creating user with nested orders
print("POST /users/")
print('''Request body: {
    "name": "Jane Smith",
    "email": "jane@example.com",
    "orders": [
        {
            "product_name": "Monitor",
            "quantity": 1,
            "price": "299.99"
        },
        {
            "product_name": "Keyboard",
            "quantity": 1,
            "price": "79.99"
        }
    ]
}''')
print()

# Multiple levels of nesting
class OrderItemSerializer(serializers.ModelSerializer):
    class Meta:
        model = OrderItem
        fields = ['id', 'item_name', 'item_price']

class OrderDetailedSerializer(serializers.ModelSerializer):
    items = OrderItemSerializer(many=True, read_only=True)
    # Nested serializer within a nested serializer

    class Meta:
        model = Order
        fields = ['id', 'product_name', 'quantity', 'price', 'items']

class UserDetailedSerializer(serializers.ModelSerializer):
    orders = OrderDetailedSerializer(many=True, read_only=True)
    # Each order now includes its items

    class Meta:
        model = User
        fields = ['id', 'name', 'email', 'orders']

# Example: GET with multiple levels of nesting
print("GET /users/1/ (with multiple nested levels)")
print('''Response: {
    "id": 1,
    "name": "John Doe",
    "email": "john@example.com",
    "orders": [
        {
            "id": 101,
            "product_name": "Laptop",
            "quantity": 1,
            "price": "999.99",
            "items": [
                {"id": 1, "item_name": "SSD", "item_price": "150.00"},
                {"id": 2, "item_name": "RAM", "item_price": "80.00"}
            ]
        }
    ]
}''')
print()

print("Key points about nested serializers:")
print("- read_only=True (default): Can only display related data, cannot create/update")
print("- read_only=False: Can create and update nested data")
print("- many=True: One parent has multiple related objects")
print("- Must override create() and update() for writable nested serializers")
print("- Can nest multiple levels (serializers within serializers)")
```
[Back to Table of Contents](#table-of-contents)

### 127. What is the purpose of the @action decorator in DRF?

The `@action` decorator in DRF allows us to create custom endpoints on a ViewSet that don't correspond to standard CRUD operations. When we use a ViewSet, it provides predefined methods like `list()`, `create()`, `retrieve()`, `update()`, and `destroy()`. However, if we need a custom method for a specific business logic operation, we use the `@action` decorator. The decorator requires us to specify the HTTP methods using the `methods` parameter, such as `methods=['post']` or `methods=['get']`, and whether it's a detail action with `detail=True` for operations on a single object or `detail=False` for operations on collections. This automatically creates a custom endpoint with its own URL. For example, we might use this for operations like confirming a payment, changing an order status, or generating a report based on multiple objects. By using the `@action` decorator, we can keep related functionality grouped together in a single ViewSet while still having specialized endpoints for non-standard operations.

```python
from rest_framework import viewsets, status
from rest_framework.decorators import action
from rest_framework.response import Response
from django.db import models
from rest_framework import serializers

# Define models
class ShoppingBag(models.Model):
    user = models.CharField(max_length=100)
    product_name = models.CharField(max_length=100)
    quantity = models.IntegerField()
    price = models.DecimalField(max_digits=10, decimal_places=2)

    class Meta:
        app_label = 'shop'

class Order(models.Model):
    user = models.CharField(max_length=100)
    product_name = models.CharField(max_length=100)
    quantity = models.IntegerField()
    total_price = models.DecimalField(max_digits=10, decimal_places=2)

    class Meta:
        app_label = 'shop'

# Define serializers
class ShoppingBagSerializer(serializers.ModelSerializer):
    class Meta:
        model = ShoppingBag
        fields = ['id', 'user', 'product_name', 'quantity', 'price']

class OrderSerializer(serializers.ModelSerializer):
    class Meta:
        model = Order
        fields = ['id', 'user', 'product_name', 'quantity', 'total_price']

# Standard ViewSet with custom @action decorator
class OrderViewSet(viewsets.ModelViewSet):
    queryset = Order.objects.all()
    serializer_class = OrderSerializer

    # Standard CRUD methods (automatic):
    # list() - GET /orders/
    # create() - POST /orders/
    # retrieve() - GET /orders/{id}/
    # update() - PUT /orders/{id}/
    # destroy() - DELETE /orders/{id}/

    # Custom action - detail=False means it operates on collections, not single objects
    @action(methods=['post'], detail=False)
    def create_from_bag(self, request):
        """
        Custom endpoint: POST /orders/create_from_bag/
        Takes items from shopping bag and creates an order
        """
        bag_items = ShoppingBag.objects.filter(user=request.data.get('user'))

        if not bag_items.exists():
            return Response({'error': 'Shopping bag is empty'}, status=status.HTTP_400_BAD_REQUEST)

        # Create orders from bag items
        created_orders = []
        for bag_item in bag_items:
            order = Order.objects.create(
                user=bag_item.user,
                product_name=bag_item.product_name,
                quantity=bag_item.quantity,
                total_price=bag_item.price * bag_item.quantity
            )
            created_orders.append(order)

        # Clear the shopping bag
        bag_items.delete()

        serializer = OrderSerializer(created_orders, many=True)
        return Response({
            'message': f'Created {len(created_orders)} orders from shopping bag',
            'orders': serializer.data
        }, status=status.HTTP_201_CREATED)

    # Custom action - detail=True means it operates on a single object
    @action(methods=['post'], detail=True)
    def confirm_order(self, request, pk=None):
        """
        Custom endpoint: POST /orders/{id}/confirm_order/
        Confirms a specific order
        """
        order = self.get_object()
        order_data = OrderSerializer(order).data
        return Response({
            'message': f'Order {order.id} confirmed',
            'order': order_data
        }, status=status.HTTP_200_OK)

    # Custom action with GET method
    @action(methods=['get'], detail=False)
    def summary(self, request):
        """
        Custom endpoint: GET /orders/summary/
        Returns summary statistics about all orders
        """
        total_orders = Order.objects.count()
        total_revenue = sum(order.total_price for order in Order.objects.all())

        return Response({
            'total_orders': total_orders,
            'total_revenue': float(total_revenue),
            'average_order_value': float(total_revenue / total_orders) if total_orders > 0 else 0
        })

# Example API calls:
print("Standard CRUD endpoints (automatic):")
print("GET /orders/ - list all orders")
print("POST /orders/ - create new order")
print("GET /orders/1/ - retrieve order with id=1")
print("PUT /orders/1/ - update order with id=1")
print("DELETE /orders/1/ - delete order with id=1")
print()

print("Custom endpoints with @action decorator:")
print()

print("1. detail=False endpoint - operates on collection:")
print("POST /orders/create_from_bag/")
print('''Request body: {"user": "john_doe"}
Response: {
    "message": "Created 3 orders from shopping bag",
    "orders": [
        {"id": 1, "user": "john_doe", "product_name": "Laptop", "quantity": 1, "total_price": "999.99"},
        {"id": 2, "user": "john_doe", "product_name": "Mouse", "quantity": 2, "total_price": "59.98"},
        {"id": 3, "user": "john_doe", "product_name": "Keyboard", "quantity": 1, "total_price": "79.99"}
    ]
}''')
print()

print("2. detail=True endpoint - operates on single object:")
print("POST /orders/1/confirm_order/")
print('''Response: {
    "message": "Order 1 confirmed",
    "order": {"id": 1, "user": "john_doe", "product_name": "Laptop", "quantity": 1, "total_price": "999.99"}
}''')
print()

print("3. GET custom action:")
print("GET /orders/summary/")
print('''Response: {
    "total_orders": 150,
    "total_revenue": 25000.50,
    "average_order_value": 166.67
}''')
print()

print("@action decorator benefits:")
print("- detail=False: Custom endpoint for collection operations (/orders/create_from_bag/)")
print("- detail=True: Custom endpoint for single object operations (/orders/{id}/confirm_order/)")
print("- methods=['post']: Specify which HTTP methods are allowed")
print("- Keeps related logic grouped in one ViewSet")
```
[Back to Table of Contents](#table-of-contents)

### 128. What is the difference between APIView, generic views, and ViewSets?

APIView is the base class for all views in DRF, similar to how View works in Django. APIView inherits from Django's View and is used when our logic is very custom and we need full control over the implementation. We define methods like `get()`, `post()`, `put()`, and `delete()` ourselves to handle specific requests. Generic views like ListAPIView, CreateAPIView, or RetrieveUpdateDestroyAPIView provide pre-built logic for common patterns that most APIs follow. They come with methods like `get_queryset()` and `get_serializer_class()` that we can override, and they handle standard CRUD operations without requiring us to write the full method implementation. We use generic views when our endpoint matches these common patterns - for example, ListAPIView for listing objects or CreateAPIView for creating new objects. ViewSets combine multiple related views into one class and provide all CRUD operations together. They give us an additional advantage because they work with the DefaultRouter, which automatically creates URL patterns for us. Each approach offers different levels of flexibility and code reusability depending on the use case.

```python
from rest_framework.views import APIView
from rest_framework.generics import ListAPIView, CreateAPIView, RetrieveUpdateDestroyAPIView, ListCreateAPIView
from rest_framework.viewsets import ModelViewSet
from rest_framework.response import Response
from rest_framework import status
from django.db import models
from rest_framework import serializers

# Define a model
class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=100)
    isbn = models.CharField(max_length=20)

    class Meta:
        app_label = 'library'

class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ['id', 'title', 'author', 'isbn']

# 1. APIView - Full control, custom logic
class BookListAPIView(APIView):
    def get(self, request):
        """
        Custom logic: GET /api/books/
        We write the entire implementation ourselves
        """
        books = Book.objects.all()
        # Add custom business logic
        filtered_books = [b for b in books if len(b.title) > 5]
        serializer = BookSerializer(filtered_books, many=True)
        return Response(serializer.data)

    def post(self, request):
        """
        Custom logic: POST /api/books/
        We handle validation and creation ourselves
        """
        serializer = BookSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

# 2. Generic Views - Pre-built logic for common patterns
class BookListGenericView(ListCreateAPIView):
    """
    Generic view: GET /api/books/ and POST /api/books/
    Combines ListAPIView and CreateAPIView
    Pre-built logic handles listing and creation
    """
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    # That's it! No need to write get() or post() methods

class BookDetailGenericView(RetrieveUpdateDestroyAPIView):
    """
    Generic view: GET /api/books/{id}/, PUT, DELETE
    Pre-built logic handles retrieve, update, and destroy
    """
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    # Already implements retrieve(), update(), destroy()

# 3. ViewSets - All CRUD operations in one class with automatic routing
class BookViewSet(ModelViewSet):
    """
    ViewSet: Combines all CRUD operations
    Works with DefaultRouter for automatic URL generation
    """
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    # Automatically provides: list, create, retrieve, update, destroy

# URL Configuration Examples:
print("1. APIView - Manual URL routing required:")
print("""
urlpatterns = [
    path('api/books/', BookListAPIView.as_view(), name='book-list'),
    path('api/books/<int:pk>/', BookDetailAPIView.as_view(), name='book-detail'),
]
""")
print()

print("2. Generic Views - Manual URL routing required:")
print("""
urlpatterns = [
    path('api/books/', BookListGenericView.as_view(), name='book-list'),
    path('api/books/<int:pk>/', BookDetailGenericView.as_view(), name='book-detail'),
]
""")
print()

print("3. ViewSets - Automatic URL routing with DefaultRouter:")
print("""
from rest_framework.routers import DefaultRouter

router = DefaultRouter()
router.register(r'books', BookViewSet)

urlpatterns = router.urls
# Automatically creates:
# GET /api/books/ - list
# POST /api/books/ - create
# GET /api/books/{id}/ - retrieve
# PUT /api/books/{id}/ - update
# DELETE /api/books/{id}/ - destroy
""")
print()

print("Comparison:")
print()

print("APIView:")
print("- Full control over implementation")
print("- Write all methods (get, post, put, delete) yourself")
print("- Best for: Complex custom logic")
print("- URL routing: Manual")
print()

print("Generic Views:")
print("- Pre-built logic for common patterns")
print("- Override queryset and serializer_class")
print("- Best for: Standard CRUD on single endpoints")
print("- URL routing: Manual")
print()

print("ViewSets:")
print("- All CRUD operations in one class")
print("- Automatic URL routing with DefaultRouter")
print("- Best for: Complete REST API with multiple operations")
print("- URL routing: Automatic")
print()

print("Code comparison for listing books:")
print()

print("APIView approach:")
print("""
def get(self, request):
    books = Book.objects.all()
    serializer = BookSerializer(books, many=True)
    return Response(serializer.data)
""")
print()

print("Generic View approach:")
print("""
class BookListGenericView(ListAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    # That's it!
""")
print()

print("ViewSet approach:")
print("""
class BookViewSet(ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    # Includes list() plus create, retrieve, update, destroy
""")
```
[Back to Table of Contents](#table-of-contents)

### 129. What is SerializerMethodField in DRF?

SerializerMethodField is a convenient way to generate a computed value according to our business requirements. It's a read-only field that gets its value by calling a method defined in the serializer class. The method name follows a specific pattern: `get_` followed by the field name. For example, if we need a field showing how many days have passed since a user registered, we can define a field called `days_since_joined = serializers.SerializerMethodField()` and then create a method `get_days_since_joined(self, obj)`. The method receives two arguments: `self` (the serializer instance) and `obj` (the object we are serializing). Inside the method, we can perform any calculation or logic and return the computed value. This is useful when we need to include data that doesn't exist in the model itself but is derived from it.

```python
from rest_framework import serializers
from django.utils import timezone
from datetime import datetime

class User:
    def __init__(self, username, date_joined):
        self.username = username
        self.date_joined = date_joined

class UserSerializer(serializers.Serializer):
    username = serializers.CharField()
    date_joined = serializers.DateTimeField()
    days_since_joined = serializers.SerializerMethodField()

    def get_days_since_joined(self, obj):
        # Method name follows pattern: get_<field_name>
        delta = datetime.now() - obj.date_joined
        return delta.days

# Example usage
user = User(username="john_doe", date_joined=datetime(2024, 1, 15))
serializer = UserSerializer(user)
print(serializer.data)
# Output: {'username': 'john_doe', 'date_joined': '2024-01-15T00:00:00', 'days_since_joined': 273}
```
[Back to Table of Contents](#table-of-contents)

### 130. What are the main ViewSet methods and what do they correspond to?

Each ViewSet method corresponds to a specific HTTP method and URL pattern. The `retrieve` method corresponds to GET and is used when we want to display a single object at a URL like `/products/5/`. The `list` method also corresponds to GET but displays multiple objects at `/products/`. The `create` method corresponds to POST at `/products/` and is used when we want to create a new record in the database. The `update` method corresponds to PUT at `/products/5/` and is used when we want to entirely replace an existing record - meaning all fields must be provided. We also have `partial_update` that corresponds to PATCH at `/products/5/` and is used when we only need to update certain fields without providing all of them. The `destroy` method corresponds to DELETE at `/products/5/` and deletes an existing record. ViewSets work with the DefaultRouter, which automatically generates all these URL patterns for us. We only need to register the ViewSet with the router and specify the resource name. The router then determines which method to execute based on the HTTP method and URL pattern in the request.

```python
from rest_framework import viewsets
from rest_framework.routers import DefaultRouter
from .models import Product
from .serializers import ProductSerializer

class ProductViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

    # These methods are already implemented in ModelViewSet:
    # def list(self, request): GET /products/
    # def create(self, request): POST /products/
    # def retrieve(self, request, pk=None): GET /products/5/
    # def update(self, request, pk=None): PUT /products/5/
    # def partial_update(self, request, pk=None): PATCH /products/5/
    # def destroy(self, request, pk=None): DELETE /products/5/

# URL Configuration
router = DefaultRouter()
router.register(r'products', ProductViewSet, basename='product')
# Generates URLs:
# GET /products/ -> list()
# POST /products/ -> create()
# GET /products/5/ -> retrieve()
# PUT /products/5/ -> update()
# PATCH /products/5/ -> partial_update()
# DELETE /products/5/ -> destroy()

urlpatterns = router.urls
```
[Back to Table of Contents](#table-of-contents)

**Example Usage:**

```python
# Simulated HTTP requests and their corresponding ViewSet methods:

# GET /products/ -> list()
# Returns: [{"id": 1, "name": "Laptop", "price": 999}, {"id": 2, "name": "Mouse", "price": 25}]

# POST /products/ -> create()
# Body: {"name": "Keyboard", "price": 75}
# Returns: {"id": 3, "name": "Keyboard", "price": 75}

# GET /products/1/ -> retrieve()
# Returns: {"id": 1, "name": "Laptop", "price": 999}

# PUT /products/1/ -> update()
# Body: {"name": "Gaming Laptop", "price": 1499}
# Returns: {"id": 1, "name": "Gaming Laptop", "price": 1499}

# PATCH /products/1/ -> partial_update()
# Body: {"price": 1299}
# Returns: {"id": 1, "name": "Gaming Laptop", "price": 1299}

# DELETE /products/1/ -> destroy()
# Returns: 204 No Content
```
[Back to Table of Contents](#table-of-contents)

### 131. How do you handle validation in DRF serializers?

Field-level validation is performed to validate a single field in isolation. We can achieve field-level validation in several ways: using built-in field types like EmailField or URLField that have automatic validation, using built-in validators, or by creating a method in the serializer class following the pattern `validate_<field_name>`. For example, `validate_email(self, value)` would validate just the email field. Object-level validation is used when we need to validate relationships between multiple fields or the entire object as a whole. We do this by overriding the `validate(self, data)` method in the serializer. For example, in a booking system, we might check that the `start_date` comes before the `end_date`, or in a registration form, we verify that `password` matches `password_confirmation`. Field-level validation runs first, and only if all fields pass, then object-level validation runs. This allows us to catch simple errors early and complex relationship errors later.

```python
from rest_framework import serializers
from django.contrib.auth.password_validation import validate_password
from datetime import date

class BookingSerializer(serializers.Serializer):
    guest_name = serializers.CharField(max_length=100)
    email = serializers.EmailField()  # Built-in validation
    room_number = serializers.IntegerField()
    start_date = serializers.DateField()
    end_date = serializers.DateField()
    password = serializers.CharField(write_only=True)
    password_confirmation = serializers.CharField(write_only=True)

    # Field-level validation using method
    def validate_room_number(self, value):
        if value < 1 or value > 100:
            raise serializers.ValidationError("Room number must be between 1 and 100")
        return value

    def validate_start_date(self, value):
        if value < date.today():
            raise serializers.ValidationError("Start date cannot be in the past")
        return value

    # Object-level validation
    def validate(self, data):
        # Check date relationship
        if data['start_date'] >= data['end_date']:
            raise serializers.ValidationError("End date must be after start date")

        # Check password match
        if data['password'] != data['password_confirmation']:
            raise serializers.ValidationError("Passwords do not match")

        return data

# Testing the validation
booking_data = {
    "guest_name": "John Doe",
    "email": "john@example.com",
    "room_number": 42,
    "start_date": "2025-10-20",
    "end_date": "2025-10-25",
    "password": "securePass123",
    "password_confirmation": "securePass123"
}

serializer = BookingSerializer(data=booking_data)
print(serializer.is_valid())  # Output: True
print(serializer.validated_data)

# Invalid example - field-level fails first
invalid_data = {
    "guest_name": "Jane Doe",
    "email": "not-an-email",  # Invalid email format
    "room_number": 150,  # Out of range
    "start_date": "2025-10-20",
    "end_date": "2025-10-18",  # Before start_date
    "password": "pass123",
    "password_confirmation": "different123"
}

serializer = BookingSerializer(data=invalid_data)
print(serializer.is_valid())  # Output: False
print(serializer.errors)
# Output: {'email': ['Enter a valid email address.'], 'room_number': ['Room number must be between 1 and 100']}
```
[Back to Table of Contents](#table-of-contents)

### 132. What are mixins in DRF and when would you use them?

Mixins allow us to follow the Interface Segregation Principle by splitting different functionality into separate mixins that we can inherit only where we need them. In DRF, we have the following mixins: ListModelMixin, RetrieveModelMixin, UpdateModelMixin, CreateModelMixin, and DestroyModelMixin. We use mixins instead of a full ViewSet when we don't need all the CRUD operations that a ViewSet provides. We can create our custom class by inheriting from GenericAPIView and only the specific mixins we need. However, DRF already provides ready-to-use generic views that combine mixins for common use cases, such as CreateAPIView, ListAPIView, RetrieveAPIView, UpdateAPIView, DestroyAPIView, ListCreateAPIView, and RetrieveUpdateDestroyAPIView. For example, if I only need to list and create resources without update or delete functionality, I would use ListCreateAPIView instead of a full ModelViewSet. This gives us more control and keeps our API endpoints more focused.

```python
from rest_framework import generics, mixins
from rest_framework.response import Response
from django.db import models

# Example model
class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()

    def __str__(self):
        return self.title

# Serializer
from rest_framework import serializers

class ArticleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = ['id', 'title', 'content']

# Using ListCreateAPIView (combines ListModelMixin + CreateModelMixin)
class ArticleListCreateView(generics.ListCreateAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer

# Custom mixin combination - only List and Retrieve, no Create/Update/Delete
class ArticleListRetrieveView(mixins.ListModelMixin,
                            mixins.RetrieveModelMixin,
                            generics.GenericAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer

    def get(self, request, pk=None):
        if pk:
            return self.retrieve(request, pk)
        return self.list(request)

# Usage examples:
# ListCreateAPIView - GET (list) and POST (create)
# GET /articles/ - returns list of articles
# POST /articles/ - creates new article

# Custom mixin view - GET (list) and GET (retrieve) only
# GET /articles/ - returns list of articles
# GET /articles/1/ - returns single article with id=1
# No POST, PUT, DELETE endpoints available

# vs. Full ViewSet - would include all CRUD operations
# This focused approach keeps endpoints lean and follows Interface Segregation Principle
```
[Back to Table of Contents](#table-of-contents)

### 133. What is get_queryset() and why would you override it?

The `get_queryset()` method returns the queryset of all objects from the specified model. It's called automatically by DRF whenever we need to retrieve objects for list or retrieve operations. We override it when we need to perform custom filtering based on different conditions. A common use case is implementing role-based access control where different user types should see different data. For example, in a review moderation system, regular users might only see approved reviews, while moderators need to see all reviews including pending ones. By overriding `get_queryset()`, we can check user permissions and conditionally filter the queryset accordingly. This allows us to implement different access levels to data based on user roles without duplicating logic across multiple views.

```python
from rest_framework import viewsets
from rest_framework.permissions import IsAuthenticated
from django.contrib.auth.models import Group
from django.db import models

# Example model
class Review(models.Model):
    STATUS_CHOICES = [
        ('pending', 'Pending'),
        ('approved', 'Approved'),
        ('rejected', 'Rejected'),
    ]
    title = models.CharField(max_length=100)
    content = models.TextField()
    status = models.CharField(max_length=20, choices=STATUS_CHOICES, default='pending')
    created_by = models.ForeignKey('auth.User', on_delete=models.CASCADE)

# Serializer
from rest_framework import serializers

class ReviewSerializer(serializers.ModelSerializer):
    class Meta:
        model = Review
        fields = ['id', 'title', 'content', 'status', 'created_by']

# ViewSet with overridden get_queryset()
class ReviewViewSet(viewsets.ModelViewSet):
    serializer_class = ReviewSerializer
    permission_classes = [IsAuthenticated]

    def get_queryset(self):
        user = self.request.user

        # Check if user is in moderator group
        if user.groups.filter(name='review_moderators').exists():
            # Moderators see all reviews
            return Review.objects.all()
        else:
            # Regular users see only approved reviews
            return Review.objects.filter(status='approved')

# Usage examples:
# Regular user makes GET /reviews/
# Returns: [Review(id=1, status='approved'), Review(id=3, status='approved')]

# Moderator user makes GET /reviews/
# Returns: [Review(id=1, status='approved'), Review(id=2, status='pending'), Review(id=3, status='approved')]

# Same endpoint, different data based on user role - all handled in get_queryset()
```
[Back to Table of Contents](#table-of-contents)

### 134. Question 16: What is CORS and how do you handle it in DRF?

CORS stands for Cross-Origin Resource Sharing, and it's a browser security feature that prevents websites from making requests to a different domain than the one that served the webpage. For example, if a frontend runs on `myapp.com` and the API runs on `api.myapp.com`, the browser will block those requests by default for security reasons. It's important to understand that CORS is actually a browser-side security feature, not a server-side one. When a frontend makes a request to an API on a different domain, the request actually reaches the Django server, gets processed, and the server sends back a response with data. However, if the server doesn't include the `Access-Control-Allow-Origin` header in the response, the browser blocks JavaScript from accessing that data. This means the request was successful and the server did all the work, but the browser prevents the frontend JavaScript from using the response. Without CORS configured, users would see a CORS error in the browser console even though the server successfully processed the request. In Django Rest Framework, we handle CORS by installing the `django-cors-headers` package, adding it to installed apps and middleware, then configuring `CORS_ALLOWED_ORIGINS` to specify which domains can access our API, or using `CORS_ALLOW_ALL_ORIGINS = True` for development. The package adds special CORS headers to our API responses that tell the browser it's safe to use the data, allowing legitimate frontend applications to work while maintaining security.

```python
# settings.py configuration
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',
    'corsheaders',  # Add corsheaders
    'myapp',
]

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'corsheaders.middleware.CorsMiddleware',  # Add before CommonMiddleware
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
]

# CORS Configuration - Production
CORS_ALLOWED_ORIGINS = [
    "https://myapp.com",
    "https://www.myapp.com",
    "https://api.myapp.com",
]

# OR for Development (allow all origins)
# CORS_ALLOW_ALL_ORIGINS = True

# Example API View
from rest_framework import viewsets
from rest_framework.response import Response
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=10, decimal_places=2)

from rest_framework import serializers

class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = ['id', 'name', 'price']

class ProductViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

# Frontend request (on myapp.com)
# Browser makes request: GET https://api.myapp.com/products/
# Server responds with CORS headers:
# Access-Control-Allow-Origin: https://myapp.com
# Browser sees matching origin header and allows JavaScript to access response data
# Without CORS headers, browser would show: "Cross-Origin Request Blocked" error
```
[Back to Table of Contents](#table-of-contents)

### 135. What is a custom permission class in DRF?

A custom permission is a permission class that we create by inheriting from the DRF built-in BasePermission class. We override either the `has_permission()` method to define conditions for accessing an entire view, or the `has_object_permission()` method to define conditions for accessing specific objects. The `has_permission()` method is called for list and create operations and receives the request and view, while the `has_object_permission()` method is called for retrieve, update, and destroy operations on individual objects and receives the request, view, and the specific object. Both methods should return True if the user has permission and False otherwise. Custom permissions allow us to implement granular access control that goes beyond built-in permissions. For instance, we can restrict CRUD operations on reviews to only members of a specific admin group rather than all admin users, or implement logic based on object ownership, user roles, or any other business logic we need.

```python
from rest_framework import permissions
from rest_framework import viewsets
from django.contrib.auth.models import Group
from django.db import models

# Example model
class Review(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    created_by = models.ForeignKey('auth.User', on_delete=models.CASCADE)

# Custom Permission Class
class IsReviewModerator(permissions.BasePermission):
    message = "Only review moderators can perform this action."

    def has_permission(self, request, view):
        # Check if user is authenticated
        if not request.user or not request.user.is_authenticated:
            return False
        # Allow all authenticated users to list reviews
        if request.method == 'GET':
            return True
        # Only moderators can create, update, delete
        return request.user.groups.filter(name='review_moderators').exists()

    def has_object_permission(self, request, view, obj):
        # Moderators can perform any action on review objects
        return request.user.groups.filter(name='review_moderators').exists()

# Serializer
from rest_framework import serializers

class ReviewSerializer(serializers.ModelSerializer):
    class Meta:
        model = Review
        fields = ['id', 'title', 'content', 'created_by']

# ViewSet using custom permission
class ReviewViewSet(viewsets.ModelViewSet):
    queryset = Review.objects.all()
    serializer_class = ReviewSerializer
    permission_classes = [IsReviewModerator]

# Usage examples:
# Regular user: GET /reviews/ - Allowed (has_permission returns True for GET)
# Regular user: POST /reviews/ - Denied (has_permission returns False, user not in group)
# Regular user: PUT /reviews/1/ - Denied (has_object_permission returns False)

# Moderator user: GET /reviews/ - Allowed
# Moderator user: POST /reviews/ - Allowed (user in review_moderators group)
# Moderator user: PUT /reviews/1/ - Allowed (has_object_permission returns True)
# Moderator user: DELETE /reviews/1/ - Allowed
```
[Back to Table of Contents](#table-of-contents)

### 136. Question 19: What is StringRelatedField in DRF?

StringRelatedField is a relational field that represents a related object using its `__str__()` method. When you define a StringRelatedField in a serializer, instead of showing the object's ID or full nested data, it shows the string representation of that object. For example, if an Author model has a `__str__()` method that returns the author's name, and you use StringRelatedField in a BookSerializer, the API response will show the author's name instead of just the ID number. StringRelatedField is read-only, meaning you can use it to display data but not to update relationships. It's useful when you want a simple, human-readable representation of a related object without including all its details or the overhead of a full nested serializer. This makes it different from PrimaryKeyRelatedField which shows only the ID, SlugRelatedField which shows a specific field like a slug, and nested serializers which show the complete object data.

```python
from rest_framework import serializers
from django.db import models

# Example models
class Author(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField()

    def __str__(self):
        return self.name

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
    published_year = models.IntegerField()

# ===== EXAMPLE 1: Using Nested Serializer (requires AuthorSerializer) =====
class AuthorSerializer(serializers.ModelSerializer):
    class Meta:
        model = Author
        fields = ['id', 'name', 'email']

class BookSerializerWithNested(serializers.ModelSerializer):
    # Nested serializer - needs AuthorSerializer to be defined
    author = AuthorSerializer()

    class Meta:
        model = Book
        fields = ['id', 'title', 'author', 'published_year']

# Usage Example 1:
# GET /books/
# Response:
# [
#     {
#         "id": 1,
#         "title": "Django for Beginners",
#         "author": {
#             "id": 1,
#             "name": "John Smith",
#             "email": "john@example.com"
#         },
#         "published_year": 2023
#     }
# ]

# ===== EXAMPLE 2: Using StringRelatedField (NO serializer needed) =====
class BookSerializerWithString(serializers.ModelSerializer):
    # StringRelatedField - does NOT need AuthorSerializer
    # Directly uses Author.__str__() method
    author = serializers.StringRelatedField()

    class Meta:
        model = Book
        fields = ['id', 'title', 'author', 'published_year']

# Usage Example 2:
# GET /books/
# Response:
# [
#     {
#         "id": 1,
#         "title": "Django for Beginners",
#         "author": "John Smith",  # Only uses __str__() output
#         "published_year": 2023
#     }
# ]

# Key Difference:
# Nested serializer: Full object data, requires AuthorSerializer class
# StringRelatedField: Simple string representation, only needs __str__() method
```
[Back to Table of Contents](#table-of-contents)

### 137. What is read_only and write_only in serializer fields?

`read_only=True` means a field only appears in API responses when we GET data, but users cannot send it in requests like POST or PUT. This is useful for auto-generated fields like created_at timestamps or IDs that the server creates automatically. For example, if we have a created_at field marked as `read_only=True`, when users create a new object, any value they send for created_at is ignored and the server uses its own timestamp. `write_only=True` means the opposite - users can send this field in POST or PUT requests, but it does not appear in GET responses. This is useful for sensitive data like passwords. When users create an account, they send their password in the request, but the server never sends the password back in responses for security reasons. Normal fields without these flags can be both sent in requests and appear in responses. So `read_only` is for data the server generates that users should only see, and `write_only` is for data users should send but never see back from the server.

```python
from rest_framework import serializers
from django.db import models
from django.contrib.auth.models import User
from django.utils import timezone

# Example model
class UserProfile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField(blank=True)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

# Serializer with read_only and write_only fields
class UserProfileSerializer(serializers.ModelSerializer):
    # read_only - appears in GET responses only
    id = serializers.IntegerField(read_only=True)
    created_at = serializers.DateTimeField(read_only=True)
    updated_at = serializers.DateTimeField(read_only=True)

    # write_only - appears in POST/PUT requests only (not in responses)
    password = serializers.CharField(write_only=True, required=True)

    # Normal field - appears in both requests and responses
    bio = serializers.CharField()

    class Meta:
        model = UserProfile
        fields = ['id', 'bio', 'password', 'created_at', 'updated_at']

# Usage Example 1: POST request (Create)
# Request body:
# {
#     "bio": "I love Django",
#     "password": "securepass123"
# }
# Response:
# {
#     "id": 5,
#     "bio": "I love Django",
#     "created_at": "2025-10-14T10:30:00Z",
#     "updated_at": "2025-10-14T10:30:00Z"
# }
# Note: password is NOT in response (write_only)

# Usage Example 2: GET request (Retrieve)
# Response:
# {
#     "id": 5,
#     "bio": "I love Django",
#     "created_at": "2025-10-14T10:30:00Z",
#     "updated_at": "2025-10-14T10:30:00Z"
# }
# Note: password does NOT appear (write_only), id/timestamps appear (read_only)

# Usage Example 3: PUT request (Update)
# Request body:
# {
#     "bio": "I love Python and Django",
#     "password": "newpass456"
# }
# Response:
# {
#     "id": 5,
#     "bio": "I love Python and Django",
#     "created_at": "2025-10-14T10:30:00Z",
#     "updated_at": "2025-10-14T11:45:00Z"
# }
# Note: id and created_at unchanged (read_only), password accepted but not returned
```
[Back to Table of Contents](#table-of-contents)

### 138. What is a HyperlinkedModelSerializer in DRF?

A HyperlinkedModelSerializer is similar to a regular ModelSerializer, but instead of returning object IDs, it returns URLs to those objects. When using a HyperlinkedModelSerializer, the API response includes direct links to related resources. For example, instead of returning `"author": 5`, it returns `"author": "http://api.example.com/authors/5/"`. This follows the HATEOAS principle, which means the API provides links that tell clients where to go next. The benefit is that API clients don't need to know the URL structure - they can simply follow the links provided in the response. If you change your URL structure in Django, the HyperlinkedModelSerializer automatically generates the correct new URLs, so client code doesn't break. The serializer gets the URLs from either manually defined URL names in urls.py or, more commonly, from the DefaultRouter which automatically creates URL names based on the basename you provide. HyperlinkedModelSerializer is useful for building discoverable, well-designed REST APIs, especially when the API structure might change in the future.

```python
from rest_framework import serializers, viewsets, routers
from django.urls import path, include
from django.db import models

# Example models
class Author(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField()

    def __str__(self):
        return self.name

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
    published_year = models.IntegerField()

# ===== EXAMPLE 1: Regular ModelSerializer (returns IDs) =====
class BookModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ['id', 'title', 'author', 'published_year']

# Response:
# {
#     "id": 1,
#     "title": "Django for Beginners",
#     "author": 5,
#     "published_year": 2023
# }

# ===== EXAMPLE 2: HyperlinkedModelSerializer (returns URLs) =====
class AuthorHyperlinkedSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = Author
        fields = ['url', 'id', 'name', 'email']
        extra_kwargs = {
            'url': {'view_name': 'author-detail', 'lookup_field': 'pk'}
        }

class BookHyperlinkedSerializer(serializers.HyperlinkedModelSerializer):
    author = AuthorHyperlinkedSerializer(read_only=True)

    class Meta:
        model = Book
        fields = ['url', 'id', 'title', 'author', 'published_year']
        extra_kwargs = {
            'url': {'view_name': 'book-detail', 'lookup_field': 'pk'}
        }

# Response:
# {
#     "url": "http://api.example.com/books/1/",
#     "id": 1,
#     "title": "Django for Beginners",
#     "author": {
#         "url": "http://api.example.com/authors/5/",
#         "id": 5,
#         "name": "John Smith",
#         "email": "john@example.com"
#     },
#     "published_year": 2023
# }

# ViewSets
class AuthorViewSet(viewsets.ModelViewSet):
    queryset = Author.objects.all()
    serializer_class = AuthorHyperlinkedSerializer

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookHyperlinkedSerializer

# URL Configuration with DefaultRouter (automatically creates URL names)
router = routers.DefaultRouter()
router.register(r'authors', AuthorViewSet, basename='author')
router.register(r'books', BookViewSet, basename='book')

urlpatterns = [
    path('api/', include(router.urls)),
]

# Usage:
# GET /api/books/1/
# Client sees URL to author and can follow it
# If you change URLs later, HyperlinkedModelSerializer auto-generates correct links
# HATEOAS principle: API tells client where to go next via links
```
[Back to Table of Contents](#table-of-contents)

### 139. What is the source parameter in DRF serializer fields?

The `source` parameter in DRF serializer fields tells the serializer which field or method on the model to use as the data source. It's useful when you want to rename a field in your API response, access data from related objects, or call custom methods. For example, if a model has a field called `author_name` but you want the API to show it as just `name`, you use `source='author_name'`. If you want to show an author's name when displaying a book, you use `source='author.name'` to access the name field from the related Author object. You can also use source with custom methods like `source='get_full_name'` to display computed values. The source parameter is particularly useful when you want to customize the API response without changing your database models.

```python
from rest_framework import serializers
from django.db import models

# Example models
class Author(models.Model):
    first_name = models.CharField(max_length=100)
    last_name = models.CharField(max_length=100)
    birth_year = models.IntegerField()

    def get_full_name(self):
        return f"{self.first_name} {self.last_name}"

    def __str__(self):
        return self.get_full_name()

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
    publication_year = models.IntegerField()
    pages = models.IntegerField()

    def get_book_info(self):
        return f"{self.title} ({self.publication_year})"

# ===== EXAMPLE 1: Renaming a field =====
class BookSerializer1(serializers.ModelSerializer):
    # Rename 'publication_year' to 'year' in API response
    year = serializers.IntegerField(source='publication_year')

    class Meta:
        model = Book
        fields = ['id', 'title', 'year', 'pages']

# Response:
# {
#     "id": 1,
#     "title": "Django for Beginners",
#     "year": 2023,
#     "pages": 350
# }

# ===== EXAMPLE 2: Accessing related object fields =====
class BookSerializer2(serializers.ModelSerializer):
    # Access author's name directly using dot notation
    author_name = serializers.CharField(source='author.first_name')

    class Meta:
        model = Book
        fields = ['id', 'title', 'author_name', 'publication_year']

# Response:
# {
#     "id": 1,
#     "title": "Django for Beginners",
#     "author_name": "John",
#     "publication_year": 2023
# }

# ===== EXAMPLE 3: Calling custom model methods =====
class BookSerializer3(serializers.ModelSerializer):
    # Call custom method on the model
    book_info = serializers.CharField(source='get_book_info', read_only=True)
    author_full_name = serializers.CharField(source='author.get_full_name', read_only=True)

    class Meta:
        model = Book
        fields = ['id', 'book_info', 'author_full_name', 'pages']

# Response:
# {
#     "id": 1,
#     "book_info": "Django for Beginners (2023)",
#     "author_full_name": "John Smith",
#     "pages": 350
# }

# ===== EXAMPLE 4: Complete example with multiple source uses =====
class BookCompleteSerializer(serializers.ModelSerializer):
    # Rename field
    year = serializers.IntegerField(source='publication_year')
    # Access related object field
    author_name = serializers.CharField(source='author.first_name')
    # Call custom method
    info = serializers.CharField(source='get_book_info', read_only=True)

    class Meta:
        model = Book
        fields = ['id', 'title', 'author_name', 'year', 'pages', 'info']

# Response:
# {
#     "id": 1,
#     "title": "Django for Beginners",
#     "author_name": "John",
#     "year": 2023,
#     "pages": 350,
#     "info": "Django for Beginners (2023)"
# }
```
[Back to Table of Contents](#table-of-contents)

### 140. What is SlugRelatedField and how is it different from PrimaryKeyRelatedField?

SlugRelatedField and PrimaryKeyRelatedField are both used to represent related objects, but in different ways. SlugRelatedField returns a meaningful field from the related object instead of just the ID. For example, instead of returning the author ID like `5`, it can return the author's email `john@example.com` or slug `john-smith`. PrimaryKeyRelatedField, on the other hand, returns only the ID of the related object. You would choose SlugRelatedField when you need to present meaningful data about the related object to the client, making the API more readable and self-explanatory. You would choose PrimaryKeyRelatedField when the client needs the ID for further actions, such as when a user wants to rate an author and you need the author ID to perform that action. SlugRelatedField is better for human-readable APIs, while PrimaryKeyRelatedField is better when you have many related objects and want lightweight data transfer, or when you need simple IDs for subsequent API calls.

```python
from rest_framework import serializers
from django.db import models

# Example models
class Author(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField(unique=True)
    slug = models.SlugField(unique=True)

    def __str__(self):
        return self.name

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
    isbn = models.CharField(max_length=13)

# ===== EXAMPLE 1: PrimaryKeyRelatedField (returns ID only) =====
class BookSerializerWithPK(serializers.ModelSerializer):
    # Returns only the author's ID
    author = serializers.PrimaryKeyRelatedField(queryset=Author.objects.all())

    class Meta:
        model = Book
        fields = ['id', 'title', 'author', 'isbn']

# GET Response:
# {
#     "id": 1,
#     "title": "Django for Beginners",
#     "author": 5,
#     "isbn": "9781234567890"
# }

# POST Request:
# {
#     "title": "Django for Beginners",
#     "author": 5,
#     "isbn": "9781234567890"
# }

# ===== EXAMPLE 2: SlugRelatedField (returns meaningful field) =====
class BookSerializerWithSlug(serializers.ModelSerializer):
    # Returns the author's slug instead of ID
    author = serializers.SlugRelatedField(
        slug_field='slug',
        queryset=Author.objects.all()
    )

    class Meta:
        model = Book
        fields = ['id', 'title', 'author', 'isbn']

# GET Response:
# {
#     "id": 1,
#     "title": "Django for Beginners",
#     "author": "john-smith",
#     "isbn": "9781234567890"
# }

# POST Request:
# {
#     "title": "Django for Beginners",
#     "author": "john-smith",
#     "isbn": "9781234567890"
# }

# ===== EXAMPLE 3: SlugRelatedField with email =====
class BookSerializerWithEmail(serializers.ModelSerializer):
    # Returns the author's email instead of ID
    author_email = serializers.SlugRelatedField(
        source='author',
        slug_field='email',
        queryset=Author.objects.all(),
        read_only=True
    )

    class Meta:
        model = Book
        fields = ['id', 'title', 'author_email', 'isbn']

# GET Response:
# {
#     "id": 1,
#     "title": "Django for Beginners",
#     "author_email": "john@example.com",
#     "isbn": "9781234567890"
# }

# ===== COMPARISON =====
# PrimaryKeyRelatedField response:
# "author": 5

# SlugRelatedField with slug:
# "author": "john-smith"

# SlugRelatedField with email:
# "author_email": "john@example.com"

# Use PrimaryKeyRelatedField when:
# - You need lightweight data transfer
# - Client will use the ID for subsequent API calls
# - You're working with many related objects

# Use SlugRelatedField when:
# - You want human-readable, meaningful data in responses
# - The API clients benefit from seeing descriptive information
# - You want a more discoverable and self-explanatory API
```
[Back to Table of Contents](#table-of-contents)

### 141. What is get_serializer_class() and why would you override it?

The `get_serializer_class()` method is a built-in method in both ViewSets and generic views that returns the serializer class to be used. If not overridden, it will use the `serializer_class` attribute defined in the view. We can override it to decide which serializer class to use based on conditions. For example, if a user accesses the list endpoint, we would use `ProductListSerializer` which is lightweight and contains only essential fields. If a user accesses the detail endpoint, we would use `ProductDetailSerializer` which contains more detailed information and nested objects. We check the action using `self.action` to determine which endpoint is being accessed. This approach helps optimize API responses by sending only the necessary data for each endpoint, reducing bandwidth and improving performance. We can also use this pattern to return different serializers based on user permissions, request methods, or other business logic.

```python
from rest_framework import viewsets, serializers
from django.db import models

# Example models
class Category(models.Model):
    name = models.CharField(max_length=100)

    def __str__(self):
        return self.name

class Product(models.Model):
    name = models.CharField(max_length=200)
    description = models.TextField()
    price = models.DecimalField(max_digits=10, decimal_places=2)
    category = models.ForeignKey(Category, on_delete=models.CASCADE)
    stock = models.IntegerField()
    created_at = models.DateTimeField(auto_now_add=True)

# ===== SERIALIZERS =====
class CategorySerializer(serializers.ModelSerializer):
    class Meta:
        model = Category
        fields = ['id', 'name']

# Lightweight serializer for list endpoint
class ProductListSerializer(serializers.ModelSerializer):
    category = serializers.StringRelatedField()

    class Meta:
        model = Product
        fields = ['id', 'name', 'price', 'category']

# Detailed serializer for detail/retrieve endpoint
class ProductDetailSerializer(serializers.ModelSerializer):
    category = CategorySerializer(read_only=True)

    class Meta:
        model = Product
        fields = ['id', 'name', 'description', 'price', 'category', 'stock', 'created_at']

# ===== VIEWSET WITH OVERRIDDEN get_serializer_class() =====
class ProductViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()

    def get_serializer_class(self):
        # Return different serializers based on the action
        if self.action == 'list':
            return ProductListSerializer
        elif self.action == 'retrieve':
            return ProductDetailSerializer
        elif self.action in ['create', 'update', 'partial_update']:
            return ProductDetailSerializer
        return ProductDetailSerializer

# Usage Examples:
# GET /products/
# Response uses ProductListSerializer:
# [
#     {
#         "id": 1,
#         "name": "Laptop",
#         "price": "999.99",
#         "category": "Electronics"
#     }
# ]

# GET /products/1/
# Response uses ProductDetailSerializer:
# {
#     "id": 1,
#     "name": "Laptop",
#     "description": "High performance laptop for professionals",
#     "price": "999.99",
#     "category": {
#         "id": 1,
#         "name": "Electronics"
#     },
#     "stock": 50,
#     "created_at": "2025-10-14T10:30:00Z"
# }

# POST /products/
# Request and Response use ProductDetailSerializer:
# {
#     "name": "Mouse",
#     "description": "Wireless optical mouse",
#     "price": "25.99",
#     "category": 1,
#     "stock": 100
# }

# ===== ALTERNATIVE: Override based on user permissions =====
class AlternativeProductViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()

    def get_serializer_class(self):
        # Admin users see detailed information
        if self.request.user and self.request.user.is_staff:
            return ProductDetailSerializer
        # Regular users see limited information
        return ProductListSerializer
```
[Back to Table of Contents](#table-of-contents)

### 142. What is get_object() and when would you override it?

`get_object()` is the opposite of `get_queryset()` - `get_queryset()` returns a list of objects while `get_object()` returns a single object. Internally, `get_object()` uses `get_queryset()` to acquire all available objects and then filters them by either the pk or slug from the URL. If the object is not found, it raises a 404 error. We would override `get_object()` when we need to filter by another criteria instead of pk or slug, for example by username instead of pk. We also override `get_object()` when we need to check permissions, such as verifying if the current user is the owner of that object. This ensures users can only access objects they're authorized to see. We might also override it to apply different filtering based on user roles, like allowing admin users to see archived objects while regular users only see active objects.

```python
from rest_framework import viewsets, generics
from rest_framework.permissions import IsAuthenticated
from rest_framework.exceptions import PermissionDenied
from django.shortcuts import get_object_or_404
from django.db import models

# Example models
class UserProfile(models.Model):
    user = models.OneToOneField('auth.User', on_delete=models.CASCADE)
    bio = models.TextField(blank=True)
    is_verified = models.BooleanField(default=False)

class BlogPost(models.Model):
    STATUS_CHOICES = [
        ('draft', 'Draft'),
        ('published', 'Published'),
        ('archived', 'Archived'),
    ]
    title = models.CharField(max_length=200)
    content = models.TextField()
    author = models.ForeignKey('auth.User', on_delete=models.CASCADE)
    status = models.CharField(max_length=20, choices=STATUS_CHOICES, default='draft')
    created_at = models.DateTimeField(auto_now_add=True)

# Serializers
from rest_framework import serializers

class UserProfileSerializer(serializers.ModelSerializer):
    class Meta:
        model = UserProfile
        fields = ['id', 'user', 'bio', 'is_verified']

class BlogPostSerializer(serializers.ModelSerializer):
    class Meta:
        model = BlogPost
        fields = ['id', 'title', 'content', 'author', 'status', 'created_at']

# ===== EXAMPLE 1: Override get_object() to filter by username =====
class UserProfileDetailView(generics.RetrieveUpdateDestroyAPIView):
    serializer_class = UserProfileSerializer
    permission_classes = [IsAuthenticated]
    lookup_field = 'username'  # Use username instead of pk

    def get_object(self):
        # Filter by username from URL instead of pk
        username = self.kwargs.get('username')
        return get_object_or_404(UserProfile, user__username=username)

# Usage:
# GET /users/john-smith/
# Returns the profile of user with username 'john-smith'

# ===== EXAMPLE 2: Override get_object() to check ownership =====
class BlogPostDetailView(generics.RetrieveUpdateDestroyAPIView):
    serializer_class = BlogPostSerializer
    permission_classes = [IsAuthenticated]
    lookup_field = 'pk'

    def get_object(self):
        # Get the object using default logic
        post = super().get_object()

        # Check if current user is the author
        if post.author != self.request.user:
            raise PermissionDenied("You can only edit your own posts.")

        return post

# Usage:
# GET /posts/5/ - Works if current user is the author
# GET /posts/5/ - Returns 403 Forbidden if current user is not the author

# ===== EXAMPLE 3: Override get_object() with role-based filtering =====
class BlogPostViewSet(viewsets.ModelViewSet):
    serializer_class = BlogPostSerializer
    permission_classes = [IsAuthenticated]
    lookup_field = 'pk'

    def get_queryset(self):
        # Regular users see only published posts
        if not self.request.user.is_staff:
            return BlogPost.objects.filter(status='published')
        # Admin users see all posts including archived
        return BlogPost.objects.all()

    def get_object(self):
        # Get the object from filtered queryset
        post = super().get_object()

        # Additional check: allow admins and authors to see draft posts
        if post.status == 'draft':
            if post.author != self.request.user and not self.request.user.is_staff:
                raise PermissionDenied("You cannot view draft posts.")

        return post

# Usage:
# Regular user GET /posts/1/
# Returns post only if status is 'published'
# Returns 403 Forbidden if post is 'draft' or 'archived'

# Admin user GET /posts/1/
# Returns any post regardless of status

# ===== EXAMPLE 4: Default get_object() behavior (filter by pk) =====
class DefaultBlogPostView(generics.RetrieveAPIView):
    queryset = BlogPost.objects.all()
    serializer_class = BlogPostSerializer
    lookup_field = 'pk'  # Default - filters by primary key

# Usage:
# GET /posts/1/
# Returns the post with pk=1 (if exists, otherwise 404)
# Internally calls: BlogPost.objects.get(pk=1)
```
[Back to Table of Contents](#table-of-contents)

### 143. What are lookup_field and lookup_url_kwarg in DRF ViewSets?

Both `lookup_field` and `lookup_url_kwarg` are defined in GenericAPIView. The default value of `lookup_field` is `'pk'` and the default value of `lookup_url_kwarg` is `None`. The `lookup_field` must be an existing field in our model and should be a unique one, such as pk, its equivalent id, slug, username, email, or barcode. We override `lookup_field` when we want to filter by a different unique identifier instead of pk, such as username, slug, email, or barcode. `lookup_url_kwarg` refers to how we name that parameter in the URL. If we use a custom parameter name like `product_slug` or `user_email` instead of the default ones, we must override `lookup_url_kwarg`. For example, if our URL is `<slug:product_slug>`, we need to set `lookup_url_kwarg` to `'product_slug'`. We override both attributes when we want the URL parameter name to differ from the model field name. For instance, when the model field is `'slug'` but the URL parameter is `'product_slug'`, we set `lookup_field='slug'` and `lookup_url_kwarg='product_slug'`.

```python
from rest_framework import viewsets, routers
from django.urls import path, include
from django.db import models
from rest_framework import serializers

# Example models
class Product(models.Model):
    name = models.CharField(max_length=200)
    slug = models.SlugField(unique=True)
    barcode = models.CharField(max_length=50, unique=True)
    price = models.DecimalField(max_digits=10, decimal_places=2)

class User(models.Model):
    username = models.CharField(max_length=100, unique=True)
    email = models.EmailField(unique=True)
    first_name = models.CharField(max_length=100)

# Serializers
class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = ['id', 'name', 'slug', 'barcode', 'price']

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'username', 'email', 'first_name']

# ===== EXAMPLE 1: Default lookup_field (pk) =====
class DefaultProductViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    # lookup_field = 'pk' (default)
    # lookup_url_kwarg = None (default)

# URL: path('products/<int:pk>/', ...)
# Usage: GET /products/1/
# Filters by: Product.objects.get(pk=1)

# ===== EXAMPLE 2: Override lookup_field to use slug =====
class ProductBySlugViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    lookup_field = 'slug'
    # lookup_url_kwarg = None (defaults to 'slug')

# URL: path('products/<slug:slug>/', ...)
# Usage: GET /products/laptop-pro/
# Filters by: Product.objects.get(slug='laptop-pro')

# ===== EXAMPLE 3: Override lookup_field AND lookup_url_kwarg =====
class ProductDetailViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    lookup_field = 'slug'  # Model field is 'slug'
    lookup_url_kwarg = 'product_slug'  # URL parameter is 'product_slug'

# URL: path('products/<slug:product_slug>/', ...)
# Usage: GET /products/laptop-pro/
# Filters by: Product.objects.get(slug='laptop-pro')
# The viewset maps URL parameter 'product_slug' to model field 'slug'

# ===== EXAMPLE 4: Filter by barcode =====
class ProductByBarcodeViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    lookup_field = 'barcode'
    lookup_url_kwarg = 'barcode'

# URL: path('products/<str:barcode>/', ...)
# Usage: GET /products/5901234123457/
# Filters by: Product.objects.get(barcode='5901234123457')

# ===== EXAMPLE 5: Filter by username =====
class UserByUsernameViewSet(viewsets.ModelViewSet):
    queryset = User.objects.all()
    serializer_class = UserSerializer
    lookup_field = 'username'
    lookup_url_kwarg = 'username'

# URL: path('users/<str:username>/', ...)
# Usage: GET /users/john-smith/
# Filters by: User.objects.get(username='john-smith')

# ===== EXAMPLE 6: Using routers with custom lookup =====
router = routers.SimpleRouter()
router.register(r'products-by-slug', ProductBySlugViewSet, basename='product-slug')
router.register(r'products-by-barcode', ProductByBarcodeViewSet, basename='product-barcode')
router.register(r'users', UserByUsernameViewSet, basename='user')

urlpatterns = [
    path('api/', include(router.urls)),
]

# Generated URLs from SimpleRouter:
# GET /api/products-by-slug/laptop-pro/ - lookup_field='slug'
# GET /api/products-by-barcode/5901234123457/ - lookup_field='barcode'
# GET /api/users/john-smith/ - lookup_field='username'

# ===== COMPARISON TABLE =====
# lookup_field='pk', lookup_url_kwarg=None
# URL: /products/<int:pk>/
# Filters: Product.objects.get(pk=1)

# lookup_field='slug', lookup_url_kwarg=None
# URL: /products/<slug:slug>/
# Filters: Product.objects.get(slug='laptop-pro')

# lookup_field='slug', lookup_url_kwarg='product_slug'
# URL: /products/<slug:product_slug>/
# Filters: Product.objects.get(slug='laptop-pro')
```
[Back to Table of Contents](#table-of-contents)

### 144. What is the difference between ListAPIView and ListCreateAPIView?

ListAPIView and ListCreateAPIView differ in their functionality and supported HTTP methods. ListAPIView supports only the GET HTTP method and is used for retrieving a list of objects in a read-only manner. ListCreateAPIView supports both GET and POST HTTP methods and allows both retrieving and creating objects. We would use ListAPIView when we only want to display a list of items and users should not be able to create new items directly through that endpoint. For example, we might use ListAPIView for displaying a list of published products that users can only view. We would use ListCreateAPIView when we want to provide both listing and creating functionality on the same endpoint. For example, we might use ListCreateAPIView for a reviews endpoint where users can both view all reviews and submit new reviews through the same endpoint. ListAPIView is read-only while ListCreateAPIView is read-write.

```python
from rest_framework import generics
from rest_framework.permissions import IsAuthenticated, IsAdminUser
from django.db import models
from rest_framework import serializers

# Example models
class Product(models.Model):
    name = models.CharField(max_length=200)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    description = models.TextField()
    is_published = models.BooleanField(default=False)

class Review(models.Model):
    product = models.ForeignKey(Product, on_delete=models.CASCADE)
    user = models.ForeignKey('auth.User', on_delete=models.CASCADE)
    rating = models.IntegerField(choices=[(1, '1'), (2, '2'), (3, '3'), (4, '4'), (5, '5')])
    comment = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)

# Serializers
class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = ['id', 'name', 'price', 'description']

class ReviewSerializer(serializers.ModelSerializer):
    class Meta:
        model = Review
        fields = ['id', 'product', 'user', 'rating', 'comment', 'created_at']

# ===== EXAMPLE 1: ListAPIView (Read-only) =====
class PublishedProductListView(generics.ListAPIView):
    queryset = Product.objects.filter(is_published=True)
    serializer_class = ProductSerializer
    # Supports only GET method

# Usage:
# GET /products/published/
# Response:
# [
#     {
#         "id": 1,
#         "name": "Laptop",
#         "price": "999.99",
#         "description": "High performance laptop"
#     }
# ]

# POST /products/published/ - Returns 405 Method Not Allowed
# PUT /products/published/1/ - Returns 405 Method Not Allowed
# DELETE /products/published/1/ - Returns 405 Method Not Allowed

# ===== EXAMPLE 2: ListCreateAPIView (Read-Write) =====
class ReviewListCreateView(generics.ListCreateAPIView):
    queryset = Review.objects.all()
    serializer_class = ReviewSerializer
    permission_classes = [IsAuthenticated]
    # Supports GET and POST methods

# Usage:
# GET /reviews/
# Response:
# [
#     {
#         "id": 1,
#         "product": 1,
#         "user": 5,
#         "rating": 5,
#         "comment": "Excellent product!",
#         "created_at": "2025-10-14T10:30:00Z"
#     }
# ]

# POST /reviews/
# Request:
# {
#     "product": 1,
#     "rating": 5,
#     "comment": "Excellent product!"
# }
# Response (201 Created):
# {
#     "id": 2,
#     "product": 1,
#     "user": 5,
#     "rating": 5,
#     "comment": "Excellent product!",
#     "created_at": "2025-10-14T11:45:00Z"
# }

# PUT /reviews/ - Returns 405 Method Not Allowed
# DELETE /reviews/ - Returns 405 Method Not Allowed

# ===== EXAMPLE 3: Comparison with different endpoints =====
class AdminProductListView(generics.ListCreateAPIView):
    # Admin can create new products
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    permission_classes = [IsAdminUser]

class PublicProductListView(generics.ListAPIView):
    # Public users can only view published products
    queryset = Product.objects.filter(is_published=True)
    serializer_class = ProductSerializer

# URL Configuration:
# GET /api/admin/products/ - Admin list (ListCreateAPIView) - supports GET, POST
# POST /api/admin/products/ - Create new product (ListCreateAPIView)
# GET /api/products/ - Public view (ListAPIView) - supports only GET
# POST /api/products/ - Returns 405 Method Not Allowed (ListAPIView)

# ===== SUPPORTED HTTP METHODS =====
# ListAPIView:
# - GET: List all objects (read-only)

# ListCreateAPIView:
# - GET: List all objects
# - POST: Create a new object

# ===== WHEN TO USE =====
# Use ListAPIView when:
# - You want to display data in a read-only manner
# - Users should only view, not create items through this endpoint
# - You want to restrict write operations

# Use ListCreateAPIView when:
# - You want both viewing and creating on the same endpoint
# - Users need to both retrieve and submit data
# - You want a complete resource interface for list operations
```
[Back to Table of Contents](#table-of-contents)

### 145. What is the queryset attribute in DRF views and why is it important?

The `queryset` attribute in DRF views specifies which model objects the view operates on. It must be assigned a queryset (like `Product.objects.all()`), not the model class itself. The queryset is used internally by DRF for multiple operations: it provides the base set of objects for listing, it's filtered by `get_object()` to retrieve single objects, it's used for permissions checking, and it's the source for create, update, and delete operations. If you don't define the `queryset` attribute, DRF will raise an AssertionError saying you must define either the `'queryset'` attribute or override the `'get_queryset()'` method. Without a queryset, DRF doesn't know which model to query, so you can't list, retrieve, update, or delete any objects. Alternatively, instead of using the `queryset` attribute, you can override the `get_queryset()` method for more control, such as filtering based on user permissions or request parameters. This approach is often preferred because it allows dynamic queryset filtering based on conditions.

```python
from rest_framework import generics, viewsets
from rest_framework.permissions import IsAuthenticated
from django.db import models
from rest_framework import serializers

# Example models
class Category(models.Model):
    name = models.CharField(max_length=100)

    def __str__(self):
        return self.name

class Product(models.Model):
    name = models.CharField(max_length=200)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    category = models.ForeignKey(Category, on_delete=models.CASCADE)
    is_active = models.BooleanField(default=True)
    created_by = models.ForeignKey('auth.User', on_delete=models.CASCADE)

# Serializer
class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = ['id', 'name', 'price', 'category', 'is_active']

# ===== EXAMPLE 1: Using queryset attribute (Static) =====
class ProductListView(generics.ListCreateAPIView):
    queryset = Product.objects.all()  # Base queryset for all operations
    serializer_class = ProductSerializer

# Usage:
# GET /products/ - Lists all products from the queryset
# POST /products/ - Creates a new product using the queryset's model
# GET /products/1/ - Retrieves product with id=1 from the queryset
# PUT /products/1/ - Updates product with id=1 using the queryset
# DELETE /products/1/ - Deletes product with id=1 from the queryset

# ===== EXAMPLE 2: INCORRECT - Missing queryset (will raise error) =====
class IncorrectProductListView(generics.ListCreateAPIView):
    # Missing queryset attribute
    serializer_class = ProductSerializer

# Error: AssertionError: 'queryset' or 'get_queryset()' must be defined

# ===== EXAMPLE 3: Using get_queryset() for dynamic filtering =====
class ActiveProductListView(generics.ListCreateAPIView):
    serializer_class = ProductSerializer
    permission_classes = [IsAuthenticated]

    def get_queryset(self):
        # Dynamic filtering based on conditions
        user = self.request.user

        # Show only active products to regular users
        if not user.is_staff:
            return Product.objects.filter(is_active=True)

        # Show all products to staff users
        return Product.objects.all()

# Usage:
# Regular user GET /products/ - Returns only active products
# Staff user GET /products/ - Returns all products including inactive ones

# ===== EXAMPLE 4: Filtering by user =====
class UserProductListView(generics.ListCreateAPIView):
    serializer_class = ProductSerializer
    permission_classes = [IsAuthenticated]

    def get_queryset(self):
        # Each user sees only their own products
        return Product.objects.filter(created_by=self.request.user)

    def perform_create(self, serializer):
        # Automatically set the creator
        serializer.save(created_by=self.request.user)

# Usage:
# User 1 GET /my-products/ - Returns only products created by User 1
# User 2 GET /my-products/ - Returns only products created by User 2

# ===== EXAMPLE 5: Complex filtering =====
class FilteredProductViewSet(viewsets.ModelViewSet):
    serializer_class = ProductSerializer

    def get_queryset(self):
        queryset = Product.objects.all()

        # Filter by category query parameter
        category = self.request.query_params.get('category')
        if category:
            queryset = queryset.filter(category__name=category)

        # Filter by price range
        min_price = self.request.query_params.get('min_price')
        if min_price:
            queryset = queryset.filter(price__gte=min_price)

        max_price = self.request.query_params.get('max_price')
        if max_price:
            queryset = queryset.filter(price__lte=max_price)

        # Filter by active status
        if not self.request.user.is_staff:
            queryset = queryset.filter(is_active=True)

        return queryset

# Usage:
# GET /products/?category=Electronics&min_price=100&max_price=500
# GET /products/?category=Books - Filters by category
# GET /products/?min_price=50 - Filters by minimum price

# ===== QUERYSET vs MODEL CLASS =====
# CORRECT:
# queryset = Product.objects.all()  # Queryset
# queryset = Product.objects.filter(is_active=True)  # Queryset

# INCORRECT:
# queryset = Product  # Model class (will cause error)

# ===== WHAT QUERYSET IS USED FOR =====
# 1. Listing: GET /products/ - Returns objects from queryset
# 2. Retrieving: GET /products/1/ - Filters queryset by pk=1
# 3. Creating: POST /products/ - Creates new instance of queryset's model
# 4. Updating: PUT /products/1/ - Fetches from queryset and updates
# 5. Deleting: DELETE /products/1/ - Fetches from queryset and deletes
# 6. Permissions: Used by permission classes to check access
```
[Back to Table of Contents](#table-of-contents)

### 146. What is AllowAny permission class in DRF?

AllowAny is a permission class in Django Rest Framework that allows everyone - both authenticated and unauthenticated users - to access an endpoint. By default, DRF has no global permission restriction, so all endpoints are accessible to everyone. However, you can set a global permission class in settings.py using the DEFAULT_PERMISSION_CLASSES setting to require authentication for all endpoints. In that case, you would use AllowAny on specific endpoints that should remain public, such as registration, login, or public product listings. AllowAny is useful for endpoints that don't contain sensitive information. However, there are significant security implications: you must carefully consider what data you expose with AllowAny. For example, exposing a user list with AllowAny could be a security breach if it contains sensitive information like emails or phone numbers. AllowAny should only be used on endpoints with public or non-sensitive data, such as published products or documentation. For any endpoint containing user information, orders, or sensitive data, you should use more restrictive permissions like IsAuthenticated or IsAdminUser.

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework.permissions import AllowAny, IsAuthenticated
from rest_framework import status

# Global permission setting in settings.py
# REST_FRAMEWORK = {
#     'DEFAULT_PERMISSION_CLASSES': [
#         'rest_framework.permissions.IsAuthenticated',
#     ]
# }

class PublicProductListView(APIView):
    permission_classes = [AllowAny]  # Override global settings

    def get(self, request):
        products = [
            {"id": 1, "name": "Laptop", "price": 999},
            {"id": 2, "name": "Mouse", "price": 25}
        ]
        return Response(products)

class RegisterView(APIView):
    permission_classes = [AllowAny]  # Public registration endpoint

    def post(self, request):
        username = request.data.get('username')
        return Response({"message": f"User {username} registered successfully"})

class UserProfileView(APIView):
    permission_classes = [IsAuthenticated]  # Requires authentication

    def get(self, request):
        return Response({
            "username": request.user.username,
            "email": request.user.email
        })

# Testing access
# Anonymous user can access:
# GET /api/products/ -> Returns product list (AllowAny)
# POST /api/register/ -> Can register (AllowAny)

# Anonymous user CANNOT access:
# GET /api/profile/ -> Returns 401 Unauthorized (IsAuthenticated required)

# Authenticated user can access all endpoints
```
[Back to Table of Contents](#table-of-contents)

**Security Example:**

```python
# WRONG - Security risk with AllowAny
class UserListView(APIView):
    permission_classes = [AllowAny]  # DANGEROUS!

    def get(self, request):
        # Exposes sensitive user data to everyone
        users = User.objects.all().values('username', 'email', 'phone')
        return Response(users)

# CORRECT - Appropriate permission
class UserListView(APIView):
    permission_classes = [IsAuthenticated]  # Only authenticated users

    def get(self, request):
        users = User.objects.all().values('username', 'email', 'phone')
        return Response(users)

# CORRECT - AllowAny with public data only
class PublicBlogPostsView(APIView):
    permission_classes = [AllowAny]

    def get(self, request):
        # Only public, non-sensitive data
        posts = BlogPost.objects.filter(published=True).values('title', 'content', 'published_date')
        return Response(posts)
```
[Back to Table of Contents](#table-of-contents)

### 147. What is exception handling in DRF and what are common exceptions?

DRF formats errors that we raise or catch into proper JSON responses. Some common DRF exceptions are NotFound, NotAuthenticated, PermissionDenied, and ValidationError. We may manually raise exceptions when we need to handle specific business logic errors. For example, we might raise ValidationError if a user is not old enough to process a transaction in our app, or raise NotFound if the primary key passed in the URL is invalid. When we raise these exceptions, DRF catches them and returns a properly formatted JSON response with the key 'detail' containing the message we provided. The response also includes the appropriate HTTP status code like 400, 401, 403, or 404. Without proper exception handling, if an error occurs in our application, it would crash and send a 500 Internal Server Error to the client. This has serious business implications: it damages customer trust, can cause lost sales, and may require expensive server resources to debug. By using DRF's exception handling, we provide clear, informative error messages to clients and prevent our application from crashing.

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework.exceptions import NotFound, ValidationError, PermissionDenied, NotAuthenticated
from rest_framework import status

class ProductDetailView(APIView):
    def get(self, request, pk):
        # Manual exception raising - NotFound
        if pk > 1000:
            raise NotFound(detail="Product not found")

        return Response({"id": pk, "name": "Laptop", "price": 999})

class PurchaseView(APIView):
    def post(self, request):
        user_age = request.data.get('age')
        amount = request.data.get('amount')

        # Manual exception raising - ValidationError
        if user_age < 18:
            raise ValidationError(detail="User must be at least 18 years old to make a purchase")

        if amount > 10000:
            raise ValidationError(detail="Purchase amount exceeds maximum allowed limit")

        return Response({"message": "Purchase successful", "amount": amount})

class AdminOnlyView(APIView):
    def get(self, request):
        # Manual exception raising - PermissionDenied
        if not request.user.is_staff:
            raise PermissionDenied(detail="Only administrators can access this resource")

        return Response({"message": "Admin data"})

# Testing exception responses
# GET /products/1500/
# Response: {"detail": "Product not found"} - Status: 404

# POST /purchase/ with body: {"age": 16, "amount": 500}
# Response: {"detail": "User must be at least 18 years old to make a purchase"} - Status: 400

# POST /purchase/ with body: {"age": 25, "amount": 15000}
# Response: {"detail": "Purchase amount exceeds maximum allowed limit"} - Status: 400

# GET /admin-only/ (non-staff user)
# Response: {"detail": "Only administrators can access this resource"} - Status: 403
```
[Back to Table of Contents](#table-of-contents)

**Without Exception Handling (Bad Practice):**

```python
# WITHOUT proper exception handling - causes crashes
class BadProductView(APIView):
    def get(self, request, pk):
        product = Product.objects.get(id=pk)  # Crashes if not found!
        return Response({"name": product.name})

# GET /products/999/ (non-existent)
# Response: 500 Internal Server Error - Application crashes!

# WITH proper exception handling (Good Practice)
class GoodProductView(APIView):
    def get(self, request, pk):
        try:
            product = Product.objects.get(id=pk)
            return Response({"name": product.name})
        except Product.DoesNotExist:
            raise NotFound(detail=f"Product with id {pk} not found")

# GET /products/999/ (non-existent)
# Response: {"detail": "Product with id 999 not found"} - Status: 404
# Application continues running smoothly!
```
[Back to Table of Contents](#table-of-contents)

### 148. What is get_permissions() method in DRF ViewSets?

The `get_permissions()` method allows us to dynamically decide which permissions to apply based on conditions, whereas `permission_classes` applies the same permissions to all actions. We override `get_permissions()` when we need different permissions for different actions or different user types. A common use case is when we want public read access but restrict write operations - for example, anyone can view products with AllowAny, but only authenticated users can create, and only admins can delete. We check `self.action` to determine which operation is being performed - 'list', 'create', 'retrieve', 'update', or 'destroy' - and return the appropriate permission class. Another use case is role-based permissions: if a user is an admin, they have full access, but regular users might have limited access. The method returns a list of permission instances that DRF checks before allowing the request. This approach gives us fine-grained control over who can perform specific operations on our API.

```python
from rest_framework import viewsets
from rest_framework.permissions import AllowAny, IsAuthenticated, IsAdminUser
from rest_framework.response import Response

class ProductViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

    def get_permissions(self):
        # Different permissions based on action
        if self.action in ['list', 'retrieve']:
            # Anyone can view products
            permission_classes = [AllowAny]
        elif self.action == 'create':
            # Only authenticated users can create
            permission_classes = [IsAuthenticated]
        elif self.action in ['update', 'partial_update', 'destroy']:
            # Only admins can modify or delete
            permission_classes = [IsAdminUser]
        else:
            # Default: require authentication
            permission_classes = [IsAuthenticated]

        return [permission() for permission in permission_classes]

# Testing different actions:
# GET /products/ (list) -> AllowAny - Everyone can access
# GET /products/1/ (retrieve) -> AllowAny - Everyone can access
# POST /products/ (create) -> IsAuthenticated - Only logged-in users
# PUT /products/1/ (update) -> IsAdminUser - Only admins
# PATCH /products/1/ (partial_update) -> IsAdminUser - Only admins
# DELETE /products/1/ (destroy) -> IsAdminUser - Only admins
```
[Back to Table of Contents](#table-of-contents)

**Role-Based Permissions Example:**

```python
from rest_framework.permissions import BasePermission

class IsOwnerOrAdmin(BasePermission):
    def has_object_permission(self, request, view, obj):
        return obj.owner == request.user or request.user.is_staff

class OrderViewSet(viewsets.ModelViewSet):
    queryset = Order.objects.all()
    serializer_class = OrderSerializer

    def get_permissions(self):
        if self.action == 'list':
            # Admins see all orders, users see only their own
            if self.request.user.is_staff:
                permission_classes = [IsAdminUser]
            else:
                permission_classes = [IsAuthenticated]
        elif self.action == 'create':
            # Any authenticated user can create orders
            permission_classes = [IsAuthenticated]
        elif self.action in ['retrieve', 'update', 'partial_update', 'destroy']:
            # Owner or admin can access specific orders
            permission_classes = [IsOwnerOrAdmin]
        else:
            permission_classes = [IsAuthenticated]

        return [permission() for permission in permission_classes]

    def get_queryset(self):
        # Filter queryset based on user role
        if self.request.user.is_staff:
            return Order.objects.all()
        return Order.objects.filter(owner=self.request.user)

# Admin user: Can see and modify all orders
# Regular user: Can only see and modify their own orders
# Anonymous user: Cannot access any orders
```
[Back to Table of Contents](#table-of-contents)

**Comparison with permission_classes:**

```python
# Using permission_classes - same permissions for all actions
class SimpleProductViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    permission_classes = [IsAuthenticated]  # Applied to ALL actions

# Using get_permissions() - flexible permissions per action
class FlexibleProductViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

    def get_permissions(self):
        if self.action in ['list', 'retrieve']:
            return [AllowAny()]
        return [IsAuthenticated()]
```
[Back to Table of Contents](#table-of-contents)

### 149. What is get_serializer_class() with different serializers per action?

The `get_serializer_class()` method allows us to dynamically choose which serializer to use based on conditions. In a ViewSet, I would use ProductListSerializer for the list action to send minimal data per product, ProductDetailSerializer for the retrieve action to send comprehensive product information, and ProductCreateSerializer for the create action to handle creation-specific validation. This approach significantly improves performance because when listing many products, sending minimal data per product is much faster than sending full details for each item. Users psychologically don't need detailed information on a product list - they first browse products quickly, then decide which one to explore further by opening its detail page. For the create action, we need a separate serializer because creation requires specific validation rules that are different from retrieval. For example, a create serializer might require a category field and validate that the price is positive, while a list serializer doesn't need to validate these fields since the data already exists. Using different serializers per action saves bandwidth, reduces database load, and ensures proper validation for each operation.

```python
from rest_framework import viewsets, serializers

# Minimal serializer for list view - only essential fields
class ProductListSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = ['id', 'name', 'price', 'thumbnail']

# Detailed serializer for single product view
class ProductDetailSerializer(serializers.ModelSerializer):
    reviews = serializers.StringRelatedField(many=True, read_only=True)
    category_name = serializers.CharField(source='category.name', read_only=True)

    class Meta:
        model = Product
        fields = ['id', 'name', 'description', 'price', 'stock', 'thumbnail',
                'category', 'category_name', 'reviews', 'created_at', 'updated_at']

# Serializer for creation with specific validation
class ProductCreateSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = ['name', 'description', 'price', 'stock', 'category', 'thumbnail']

    def validate_price(self, value):
        if value <= 0:
            raise serializers.ValidationError("Price must be positive")
        return value

    def validate_stock(self, value):
        if value < 0:
            raise serializers.ValidationError("Stock cannot be negative")
        return value

class ProductViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()

    def get_serializer_class(self):
        if self.action == 'list':
            return ProductListSerializer
        elif self.action == 'retrieve':
            return ProductDetailSerializer
        elif self.action == 'create':
            return ProductCreateSerializer
        return ProductDetailSerializer  # Default for update/partial_update

# GET /products/
# Returns: [
#   {"id": 1, "name": "Laptop", "price": 999, "thumbnail": "laptop.jpg"},
#   {"id": 2, "name": "Mouse", "price": 25, "thumbnail": "mouse.jpg"}
# ]
# Fast response with minimal data - only 4 fields per product

# GET /products/1/
# Returns: {
#   "id": 1, "name": "Laptop", "description": "High-performance laptop...",
#   "price": 999, "stock": 50, "thumbnail": "laptop.jpg",
#   "category": 2, "category_name": "Electronics",
#   "reviews": ["Great product!", "Fast delivery"],
#   "created_at": "2025-01-15", "updated_at": "2025-10-10"
# }
# Comprehensive data with related information - 10+ fields

# POST /products/
# Body: {"name": "Keyboard", "price": -10, "stock": 5, "category": 1}
# Returns: {"price": ["Price must be positive"]}
# Creation-specific validation applied
```
[Back to Table of Contents](#table-of-contents)

**Performance Comparison:**

```python
# WITHOUT get_serializer_class() - using same serializer for all actions
class IneffecientProductViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductDetailSerializer  # Always returns full data

# GET /products/ with 100 products
# Returns: ~50KB of data (all fields + relations for 100 products)
# Database: 101 queries (1 for products + 100 for reviews per product)
# Load time: ~2 seconds

# WITH get_serializer_class() - optimized per action
class OptimizedProductViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()

    def get_serializer_class(self):
        if self.action == 'list':
            return ProductListSerializer
        return ProductDetailSerializer

# GET /products/ with 100 products
# Returns: ~5KB of data (only 4 fields for 100 products)
# Database: 1 query (just products, no relations)
# Load time: ~0.2 seconds
# 10x faster response!
```
[Back to Table of Contents](#table-of-contents)

**Real-World Example with User Management:**

```python
class UserListSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'username', 'email']

class UserDetailSerializer(serializers.ModelSerializer):
    orders_count = serializers.IntegerField(source='orders.count', read_only=True)

    class Meta:
        model = User
        fields = ['id', 'username', 'email', 'first_name', 'last_name',
                'date_joined', 'orders_count', 'is_active']

class UserRegistrationSerializer(serializers.ModelSerializer):
    password = serializers.CharField(write_only=True, min_length=8)
    password_confirmation = serializers.CharField(write_only=True)

    class Meta:
        model = User
        fields = ['username', 'email', 'password', 'password_confirmation']

    def validate(self, data):
        if data['password'] != data['password_confirmation']:
            raise serializers.ValidationError("Passwords must match")
        return data

    def create(self, validated_data):
        validated_data.pop('password_confirmation')
        return User.objects.create_user(**validated_data)

class UserViewSet(viewsets.ModelViewSet):
    queryset = User.objects.all()

    def get_serializer_class(self):
        if self.action == 'list':
            return UserListSerializer
        elif self.action == 'create':
            return UserRegistrationSerializer
        return UserDetailSerializer
```
[Back to Table of Contents](#table-of-contents)

### 150. What is request.data vs request.query_params in DRF?

request.data and request.query_params serve different purposes in DRF. request.data contains the body of the request - the data sent BY the client TO the server. It's used in write operations like POST (create), PUT (full update), and PATCH (partial update). For example, when a user registers, fills out a form, or updates their profile, the submitted data is accessed through request.data and is then validated and saved. request.query_params contains URL parameters - data that comes after the `?` in the URL. It's used to filter, search, or configure read operations. For example, GET /api/products/?category=electronics&min_price=500 uses query parameters to filter products. In your view, you access request.query_params.get('category') to get the filter values. The key difference is that request.data is for sending data TO the server (creating/updating), while request.query_params is for telling the server HOW to retrieve data (filtering/searching). Both are read-only and cannot be modified.

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status

class ProductListView(APIView):
    def get(self, request):
        # Using request.query_params for filtering
        category = request.query_params.get('category')
        min_price = request.query_params.get('min_price')
        max_price = request.query_params.get('max_price')
        search = request.query_params.get('search')

        products = Product.objects.all()

        if category:
            products = products.filter(category=category)
        if min_price:
            products = products.filter(price__gte=min_price)
        if max_price:
            products = products.filter(price__lte=max_price)
        if search:
            products = products.filter(name__icontains=search)

        serializer = ProductSerializer(products, many=True)
        return Response(serializer.data)

    def post(self, request):
        # Using request.data for creation
        name = request.data.get('name')
        price = request.data.get('price')
        category = request.data.get('category')
        description = request.data.get('description')

        serializer = ProductSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

# GET request with query_params:
# GET /api/products/?category=electronics&min_price=500&max_price=2000&search=laptop
# request.query_params = {'category': 'electronics', 'min_price': '500', 'max_price': '2000', 'search': 'laptop'}
# Returns filtered products based on URL parameters

# POST request with request.data:
# POST /api/products/
# Body: {"name": "Gaming Laptop", "price": 1500, "category": "electronics", "description": "High-end gaming"}
# request.data = {'name': 'Gaming Laptop', 'price': 1500, 'category': 'electronics', 'description': 'High-end gaming'}
# Creates new product with submitted data
```
[Back to Table of Contents](#table-of-contents)

**User Registration and Login Example:**

```python
class UserRegistrationView(APIView):
    def post(self, request):
        # Using request.data for user registration
        username = request.data.get('username')
        email = request.data.get('email')
        password = request.data.get('password')
        first_name = request.data.get('first_name')
        last_name = request.data.get('last_name')

        serializer = UserRegistrationSerializer(data=request.data)
        if serializer.is_valid():
            user = serializer.save()
            return Response({
                "message": "User registered successfully",
                "username": user.username
            }, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

# POST /api/register/
# Body: {
#   "username": "john_doe",
#   "email": "john@example.com",
#   "password": "securePass123",
#   "first_name": "John",
#   "last_name": "Doe"
# }
# request.data contains all the registration information sent by the user

class UserListView(APIView):
    def get(self, request):
        # Using request.query_params for filtering users
        is_active = request.query_params.get('is_active')
        role = request.query_params.get('role')
        search = request.query_params.get('search')

        users = User.objects.all()

        if is_active is not None:
            users = users.filter(is_active=is_active == 'true')
        if role:
            users = users.filter(role=role)
        if search:
            users = users.filter(username__icontains=search)

        serializer = UserSerializer(users, many=True)
        return Response(serializer.data)

# GET /api/users/?is_active=true&role=admin&search=john
# request.query_params = {'is_active': 'true', 'role': 'admin', 'search': 'john'}
# Returns filtered list of users
```
[Back to Table of Contents](#table-of-contents)

**Update Operations Example:**

```python
class ProductUpdateView(APIView):
    def patch(self, request, pk):
        # Using request.data for partial update
        try:
            product = Product.objects.get(pk=pk)
        except Product.DoesNotExist:
            return Response({"error": "Product not found"}, status=status.HTTP_404_NOT_FOUND)

        # Only fields present in request.data will be updated
        serializer = ProductSerializer(product, data=request.data, partial=True)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def put(self, request, pk):
        # Using request.data for full update
        try:
            product = Product.objects.get(pk=pk)
        except Product.DoesNotExist:
            return Response({"error": "Product not found"}, status=status.HTTP_404_NOT_FOUND)

        # All fields must be present in request.data
        serializer = ProductSerializer(product, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

# PATCH /api/products/5/
# Body: {"price": 1299}
# request.data = {'price': 1299}
# Only updates the price field

# PUT /api/products/5/
# Body: {"name": "Updated Laptop", "price": 1299, "category": "electronics", "description": "New description"}
# request.data = {'name': 'Updated Laptop', 'price': 1299, 'category': 'electronics', 'description': 'New description'}
# Updates all fields (all must be provided)
```
[Back to Table of Contents](#table-of-contents)

**Key Differences Illustrated:**

```python
# request.data - Data sent TO the server (POST/PUT/PATCH)
# Used for: Creating, updating, submitting forms
# Example URLs:
# POST /api/products/ with Body: {"name": "Laptop", "price": 999}
# PUT /api/products/5/ with Body: {"name": "Gaming Laptop", "price": 1499}
# PATCH /api/products/5/ with Body: {"price": 1299}

# request.query_params - Parameters in URL (GET)
# Used for: Filtering, searching, pagination, sorting
# Example URLs:
# GET /api/products/?category=electronics&min_price=500
# GET /api/users/?is_active=true&page=2
# GET /api/orders/?status=pending&date=2025-10-14
```
[Back to Table of Contents](#table-of-contents)

### 151. What is the difference between IsAuthenticated and IsAdminUser?

IsAuthenticated is a permission class that allows only authenticated users to access an endpoint. IsAdminUser is a more restrictive permission class that requires the authenticated user to have `is_staff=True`, which is a Django user attribute that marks someone as an admin or staff member. We would use IsAuthenticated when we want to restrict access to logged-in users only, such as allowing users to view or update their own profiles. We would use IsAdminUser on endpoints that only administrators should access, such as approving or disapproving customer reviews, viewing user statistics, or managing system settings. You're correct that IsAdminUser already implies IsAuthenticated since users must be authenticated to be staff. However, we can combine permissions in different ways. We can use the OR operator to allow either condition, or we can use method-based permission checking with `get_permissions()` to apply different permissions based on the action. For example, we might allow any authenticated user to create a review but only allow admin users to approve reviews. This gives us fine-grained control over who can perform specific operations.

```python
from rest_framework.views import APIView
from rest_framework.viewsets import ModelViewSet
from rest_framework.permissions import IsAuthenticated, IsAdminUser, BasePermission
from rest_framework.response import Response
from rest_framework import status

# IsAuthenticated - allows any logged-in user
class UserProfileView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request):
        return Response({
            "username": request.user.username,
            "email": request.user.email
        })

    def patch(self, request):
        # Any authenticated user can update their own profile
        request.user.email = request.data.get('email', request.user.email)
        request.user.save()
        return Response({"message": "Profile updated"})

# IsAdminUser - requires is_staff=True
class UserManagementView(APIView):
    permission_classes = [IsAdminUser]

    def get(self, request):
        # Only admins can view all users
        users = User.objects.all().values('id', 'username', 'email', 'is_staff')
        return Response(list(users))

    def delete(self, request, pk):
        # Only admins can delete users
        user = User.objects.get(pk=pk)
        user.delete()
        return Response({"message": "User deleted"}, status=status.HTTP_204_NO_CONTENT)

# Testing access:
# Regular authenticated user (is_staff=False):
# GET /api/profile/ -> Success (IsAuthenticated allows)
# GET /api/admin/users/ -> 403 Forbidden (IsAdminUser blocks)

# Admin user (is_staff=True):
# GET /api/profile/ -> Success (IsAuthenticated allows)
# GET /api/admin/users/ -> Success (IsAdminUser allows)

# Anonymous user:
# GET /api/profile/ -> 401 Unauthorized (IsAuthenticated blocks)
# GET /api/admin/users/ -> 401 Unauthorized (IsAdminUser blocks)
```
[Back to Table of Contents](#table-of-contents)

**Combining Permissions with OR:**

```python
from rest_framework.permissions import OR

class CustomPermission(BasePermission):
    def has_permission(self, request, view):
        return request.user.email.endswith('@company.com')

class SpecialAccessView(APIView):
    # Allow if user is admin OR has company email
    permission_classes = [IsAdminUser | CustomPermission]

    def get(self, request):
        return Response({"message": "Special access granted"})

# User A: is_staff=False, email='john@company.com' -> Access granted (CustomPermission)
# User B: is_staff=True, email='admin@gmail.com' -> Access granted (IsAdminUser)
# User C: is_staff=False, email='user@gmail.com' -> Access denied (neither condition)
```
[Back to Table of Contents](#table-of-contents)

**Different Permissions Per Action:**

```python
class ReviewViewSet(ModelViewSet):
    queryset = Review.objects.all()
    serializer_class = ReviewSerializer

    def get_permissions(self):
        if self.action == 'create':
            # Any authenticated user can create reviews
            return [IsAuthenticated()]
        elif self.action in ['update', 'partial_update', 'destroy']:
            # Only admins can edit or delete reviews
            return [IsAdminUser()]
        else:
            # Anyone can list and view reviews
            return []

        return super().get_permissions()

# POST /api/reviews/ (create)
# Authenticated user -> Success (IsAuthenticated)
# Admin user -> Success (IsAuthenticated)
# Anonymous user -> 401 Unauthorized

# PATCH /api/reviews/5/ (partial_update - approve review)
# Authenticated user -> 403 Forbidden (IsAdminUser required)
# Admin user -> Success (IsAdminUser)
# Anonymous user -> 401 Unauthorized

# GET /api/reviews/ (list)
# Anyone -> Success (no permission required)
```
[Back to Table of Contents](#table-of-contents)

**Real-World Admin Operations:**

```python
class ReviewApprovalView(APIView):
    permission_classes = [IsAdminUser]

    def post(self, request, pk):
        # Only admins can approve reviews
        review = Review.objects.get(pk=pk)
        review.is_approved = True
        review.approved_by = request.user
        review.save()
        return Response({"message": "Review approved"})

class SystemStatsView(APIView):
    permission_classes = [IsAdminUser]

    def get(self, request):
        # Only admins can view system statistics
        stats = {
            "total_users": User.objects.count(),
            "total_products": Product.objects.count(),
            "pending_reviews": Review.objects.filter(is_approved=False).count(),
            "total_revenue": Order.objects.aggregate(total=Sum('amount'))['total']
        }
        return Response(stats)

class UserOrdersView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request):
        # Any authenticated user can view their own orders
        orders = Order.objects.filter(user=request.user)
        serializer = OrderSerializer(orders, many=True)
        return Response(serializer.data)

# Admin operations (IsAdminUser):
# POST /api/reviews/5/approve/ -> Only admins
# GET /api/admin/stats/ -> Only admins

# User operations (IsAuthenticated):
# GET /api/orders/ -> Any authenticated user (their own orders)
```
[Back to Table of Contents](#table-of-contents)