# Functional Programming With Ruby
Recent I read through [Mary Rose Cook](http://www.maryrosecook.com/)'s wonderful [Introduction To Functional Programming](https://codewords.recurse.com/issues/one/an-introduction-to-functional-programming). I have always had
quite the interest in functional style programming, and found her introduction to demystify many of the harder points
that I came across in my own foray into the field. 

Being A Rubyist, I thought it would be fun to use it for the same case. 

## A Note
All quoted text are Mary Rose Cook's words from her original blog entry linked to before. I have omitted certain sections
that were irrelevent to Ruby due to language differences. These are marked with `...`.

That said, let's get started!

>## A guide rope

>When people talk about functional programming, they mention a dizzying number of “functional” characteristics. They mention immutable data1, first class functions2 and tail call optimisation3. These are language features that aid functional programming. They mention mapping, reducing, pipelining, recursing, currying4 and the use of higher order functions. These are programming techniques used to write functional code. They mention parallelization5, lazy evaluation6 and determinism7. These are advantageous properties of functional programs.

>Ignore all that. Functional code is characterised by one thing: the absence of side effects. It doesn’t rely on data outside the current function, and it doesn’t change data that exists outside the current function. Every other “functional” thing can be derived from this property. Use it as a guide rope as you learn.

>This is an unfunctional function:

```RUBY
a = 0
def increment1
    $a
    $a += 1
end
```

>This is a functional function:

```RUBY
def increment2 a
    a + 1
```

> ## Don’t iterate over lists. Use map and reduce.
> ### Map
>Map takes a function and a collection of items. It makes a new, empty collection, runs the function on each item in the original collection and inserts each return value into the new collection. It returns the new collection.

> This is a simple map that takes a list of names and returns a list of the lengths of those names:

```RUBY
name_lengths = ["Mary", "Isla", "Sam"].map &:length

puts name_lengths
# => [4, 4, 3]
```

In Ruby, we can pass a block into the map function. When we do this, we are also able to pass logic, like so:

```RUBY
name_lengths = ["Mary", "Isla", "Sam"].map { |n| n.length }

puts name_lengths
# => [4, 4, 3]
```

We achieve identical output, but the block/symbol syntax is quite nice for std llib functions being passed.

> This is a map that squares every number in the passed collection:

```RUBY
squares = [0, 1, 2, 3, 4].map { |n| n^2 }
puts squares
# => [0, 1, 4, 9, 16]
```

Since we do not have a std lib function to square an integer, we pass in the block logic like this.

> ...

> The unfunctional code below takes a list of real names and replaces them with randomly assigned code names.

```RUBY
names = ['Mary', 'Isla', 'Sam']
code_names = ['Mr. Pink', 'Mr. Orange', 'Mr. Blonde']

names.length.times do |t|
  names[t] = code_names.sample
end

puts names
# => ['Mr. Blonde', 'Mr. Blonde', 'Mr. Blonde']
```

> (As you can see, this algorithm can potentially assign the same secret code name to multiple secret agents. Hopefully, this won’t be a source of confusion during the secret mission.)

> This can be rewritten as a map:

```RUBY
names = ['Mary', 'Isla', 'Sam']
names.map { ['Mr. Pink','Mr. Orange','Mr. Blonde'].sample }
```
> ### Exercise 1. 
> Try rewriting the code below as a map. It takes a list of real names and replaces them with code names produced using a
> more robust strategy.

```RUBY
names = ['Mary', 'Isla', 'Sam']
names.length.times do |n|
  names[n] = names[n].hash
end

puts names
# => [2800420116425024173, 3235553524345710207, 3883702095636943701]
```

> (Hopefully, the secret agents will have good memories and won’t forget each other’s secret code names during the secret mission.)

> ...

Our Ruby solution:

```RUBY
names = ['Mary', 'Isla', 'Sam']

secret_names = names.map &:hash

# => [2800420116425024173, 3235553524345710207, 3883702095636943701]
```

> ### Reduce

> Reduce takes a function and a collection of items. It returns a value that is created by combining the items.

> This is a simple reduce. It returns the sum of all the items in the collection.

```RUBY
sum = [0, 1, 2, 3, 4].reduce :+
puts sum
# => 10
```
I would just like to note at this point, if you have note seen the Python version of this code, it really speaks to the eloquence of Ruby. For example:

```PYTHON
sum = reduce(lambda a, x: a + x, [0, 1, 2, 3, 4])

print sum
# => 10
```
Is the original code from the article

As you can see, Ruby's robust function library for its standard types really helps us out with making these core ops readable.

> ...
> This code counts how often the word ‘Sam’ appears in a list of strings:

```RUBY
sentences = ['Mary read a story to Sam and Isla.',
             'Isla cuddled Sam.',
             'Sam chortled.']

sam_count = 0
sentences.each do |s|
  if s.include? 'Sam'
    sam_count += 1 if s.include? 'Sam'

puts sam_count
# => 3
```

> ...

For the next operation, we don't actually have to use reduce in Ruby. We can simply use the `select` method to invoke a filter.

```RUBY
sentences = ['Mary read a story to Sam and Isla.','Isla cuddled Sam.','Sam chortled.']
sam_count = sentences.select('Sam').count
puts sam_count

# => 3
                   
```

> ...

> ### Why are map and reduce better?

> First, they are often one-liners.

> Second, the important parts of the iteration – the collection, the operation and the return value – are always in the same places in every map and reduce.

> Third, the code in a loop may affect variables defined before it or code that runs after it. By convention, maps and reduces are functional.

> Fourth, map and reduce are elemental operations. Every time a person reads a for loop, they have to work through the logic line by line. There are few structural regularities they can use to create a scaffolding on which to hang their understanding of the code. In contrast, map and reduce are at once building blocks that can be combined into complex algorithms, and elements that the code reader can instantly understand and abstract in their mind. “Ah, this code is transforming each item in this collection. It’s throwing some of the transformations away. It’s combining the remainder into a single output.”

> ...


