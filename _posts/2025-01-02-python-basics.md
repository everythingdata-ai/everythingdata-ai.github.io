---
layout: post
title: Python tips
categories: [Python]
---

After building a project from scratch yesterday, I decided to take it easy today and revisit some Python basics.
While the best way to learn is by doing, sometimes I just enjoy following along a Youtube video.
So today I will be doing just that !


## 5 good Python habits to build

A greta video by that you can watch [here](https://www.youtube.com/watch?v=I72uD8ED73U).

### _ _ name _ _ == _ _ main _ _

Let's say we have a this module  :
```python
import time

def connect() -> none
	print("Connecting to the internet")
	time.sleep(3)
	print("You are connected !")

connect()
```

Now let's go to the main.py and import our module : 
```python
from module import connect

connect()
```

Running the main.py script will call the connect function twice, once when importing the module, and the second time in the script.

A good habit to have is to check that name is equal to main, no matter the script you're inside, even main.py. 
This ensures that the code will only run if we execute the module directly, and not when importing it :
```python
if __name__ == '__main__':
	connect()
```

It also works as a self documentation, showing that its meant to be run, contrary to other modules that have functions exclusively for imports.

### main()

Suppose you have some functionalities in your script, it's good to define a main entry point to glue all the functionalities together :

```python
def bye() -> none:
	print("Bye, world !")

def hi() -> none:
	print("Hi, world!")

def main() -> none
	hi()
	bye()

if __name__ == '__main__':
	main()
```

Having a main entry point makes the code more readable, and more in-line with other programming languages like Java.

### Big functions

Instead of defining all of the functionalities in one big function, it's better to use multiple functions.

Let's take this functions that checks if a person is allowed to enter in a club :

```python
def enter_club(name: str, age: int, has_id: bool) -> None:
	if name.lower() == "bob":
		print("Get out of here Bob, we don't want your trouble !")
		return
	if age >= 21 and has_id:
		print("You may enter the club !")
	else:
		print("You may not enter the club!")

def main() -> None:
	enter_club("Bob", 21, True)
	enter_club("Alice", 22, False)
	enter_club("Charlie", 20, True)
	enter_club("David", 25, True)

  

if __name__ == "__main__":
	main()

```
We can change it to this :
```python
def is_an_adult(age: int, has_id: bool) -> bool:
	return age >= 21 and has_id

def is_bob(name: str) -> bool:
	return name.lower() == "bob"

def enter_club(name: str, age: int, has_id: bool) -> None:
	if is_bob(name):
		print("Get out of here Bob, we don't want your trouble !")
		return
	if is_an_adult(age, has_id):
		print("You may enter the club !")
	else:
		print("You may not enter the club!")

def main() -> None:
	enter_club("Bob", 21, True)
	enter_club("Alice", 22, False)
	enter_club("Charlie", 20, True)
	enter_club("David", 25, True)

if __name__ == "__main__":
	main()
```
Always keep the functions simple, this improves readability and reusability.

### Type annotations

Let's say you define this function 
```python
def upper_everything(elements):
	return [element.upper() for element in elements]
```
Obviously the function expects elements of type String, but in programming obvious is never obvious enough.
But if you give the function an integer, the interpreter will not raise an error until you execute the code.
In order to avoid that, it's better to specify the type :
```python
def upper_everything(elements: list[str]) -> list[str]:
	return [element.upper() for element in elements]

loud_list: list[str] = upper_everything(["Mario", "Luigi", "James"])
# This will give a warning
loud_list: list[int] = upper_everything(["Mario", "Luigi", "James"])
```
### List comprehensions

List annotations are faster and easier to read in certain situations.

Let's say we want to get the list of names that are longer than 6 characters :
```python
people: list[str] = ["Mario", "Luiiigi", "James", "Stephany"]

long_names: list[str] = []
for person in people:
	if len(person) > 6:
		long_names.append(person)
print(long_names)```
Now let's perform the same thing with list comprehensions :
```python
names: list[str] = ["Mario", "Luiiigi", "James", "Stephany"]

long_names: list[str] = [name for name in names if len(p) > 6]
print(long_names)
```

List comprehensions can very easily become very hard to read, so it's better not to overuse them and keep them simple.

## 10 Python functions you need to master

I really enjoy the way Tech With Tim explains things so simply, you can't watch the video [here](https://www.youtube.com/watch?v=kGcUtckifXc)

### Print
```python
age = 23
name = "Rad"

print("My name is", name, "and I am", age, "years old !", sep = ',', end=' | ')
```

### Help
Print out the documentation
```python
help(print)
```

### Range
Generate a range of numbers, starting at a value, stopping at another value and stepping at some value
```python
rng = range(2, 10, 3)
print(list(rng))
```

### Map
Apply a function to every single item in an iterable object, a.k.a any object you can loop thourgh (string, list, tupple, set...)
```python
string = ["my", "world", "apple", "pear"]
lengths = map(len, string)
print(list(lengths))
# It's common to use a lamba function : a one anynomous line function
add = map(lambda x: x + "s", string)
print(list(add))
```

### Filter
Similar to Map. It will take all items in the iterable object, pass it through the filter function, if that function returns true, it will keep that item, if not the item will be removed.
```python
def longer_than_4(string):
	return len(string) > 4

strings = ["my", "world", "apple", "pear"]

filtered = filter(longer_than_4, strings)  
# You can also use a lambda function
filtered = filter(lambda x: len(x) > 4, strings)

print(list(filtered))
```

### Sum
Returns the sum of all the numbers of an iterable object.
```python
numbers = {1, 4.5, 2, 6, 23}  
print(sum(numbers, start = -10))
```

### Sorted
Sort an iterable object in ascending or descending order.
```python
numbers = [1, 4, 2, 6, 23, 11]
sorted_numbers = sorted(numbers, reverse = True)
print(sorted_numbers)

people = [
		  {"name": "Alice", "age":30},
		  {"name": "Bob", "age":25},
		  {"name": "Charlie", "age":27}
]
sorted_people = sorted(people, key = lambda person : person["age"])
print(sorted_people)
```

### Enumerate
Allows you to have access to both the index and the value of an iterable object.
```python
tasks = ["Wake up", "Work", "Exercice", "Cook"]
for index, task in enumerate(tasks):
	print(f"{index + 1}.{task}")

print(list(enumerate(tasks)))
```

### Zip
Combine different iterable objects together and automatically handle when one object is longer than another, allowing an easier loop 
```python
# Two lists with corresponding values
names = ["Mou", "Rad", "Elgh", "Test1", "Test2"]
ages = [30, 29, 32, 19]
genders = ["Male" , "Female", "Male"]

# Without the zip function
# We use min in case one list is longer than the other
for idx in range(min(len(names), len(ages))):
	name = names[idx]
	age = ages[idx]
	print(f"{name} is {age} years old")

# With the zip function
combined = list(zip(names, ages, genders))

for name, age, gender in combined:
	print(f"{name} is {age} years old and is {gender}")
```

### Open
Used to open a file, read it...
```python
# The second variable is mode, r stands for read, w stands for write after clearing it if it already exists, and a stands for append to add to the file instead 
file = open("test.txt", "w")
file.write("Hello world")
file.close()

# To avoid forgetting to close the file, this will automatically handle it, rather than opening and closing it yourself
with open("test.txt", "r") as file:
	text = file.read()
	print(text)
```

## Python Cheat sheet

And to end today's post, I camme across this gem that I had to share.
Thanks to [The PyCoach](https://thepycoach.com/) for this incredible Python cheat sheet ! 

### Basics
![Python Basics 1](/images/posts/py-basics1.png)
![Python Basics 2](/images/posts/py-basics2.png)

### Pandas
![Pandas 1](/images/posts/py-web1.png)
![Pandas 2](/images/posts/py-pandas2.png)
### NumPy
![NumPy](/images/posts/py-numpy.png)

### Scikit-Learn
![SciKit 1](/images/posts/py-scikit1.png)
![SciKit 2](/images/posts/py-scikit2.png)

### Data Viz
![Data Viz](/images/posts/py-data-viz.png)

### Web Scrapping
![Web Scrapping 1](/images/posts/py-web1.png)
![Web Scrapping 2](/images/posts/py-web2.png)
