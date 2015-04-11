# Functional Programming With Ruby
#### This is a WIP

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
$a = 0
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

> ...

I have skipped one exercise I felt I was not doing justice. I will add it later once I get it right.

> ### Write declaratively, not imperatively

> The program below runs a race between three cars. At each time step, each car may move forwards or it may stall. At each time step, the program prints out the paths of the cars so far. After five time steps, the race is over.

> This is some sample output:

```
 -
 - -
 - -

 - -
 - -
 - - -

 - - -
 - -
 - - -

 - - - -
 - - -
 - - - -

 - - - -
 - - - -
 - - - - -
```

This is the program (in Ruby):

```RUBY
time = 5
car_positions = [1, 1, 1]
while time >= 0 do
  time = time -1; puts # newline
  car_positions.each_with_index do |pos, index|
    if Random.rand > 0.3
      car_positions[index] += 1
    end
    print '-' * car_positions[index];puts # newline
  end
end
```

> The code is written imperatively. A functional version would be declarative. It would describe what to do, rather than how to do it.

### Use Functions
> ...

```RUBY
def move_cars(car_positions, time)
  { cars: car_positions.map { |pos| pos += 1 ? Random.rand > 0.3 : pos },
    time: time - 1}
end

def output_car car_position
  puts '-' * car_position
end

def run_step_of_race state
  move_cars(state[:cars], state[:time])
end

def draw car_positions
  car_positions.each { |c| output_car c }
end

def race car_positions, time
  draw car_positions
  state = move_cars car_positions, time
  if state[:time] >= 0
    res = run_step_of_race(state)
    race(res[:cars], res[:time])
  end
end

race [1, 1, 1], 5
```

> ...

> The code is still split into functions, but the functions are functional. There are three signs of this. First, there are no longer any shared variables. time and car_positions get passed straight into race(). Second, functions take parameters. Third, no variables are instantiated inside functions. All data changes are done with return values. race() recurses with the result of run_step_of_race(). Each time a step generates a new state, it is passed immediately into the next step.

In Mary's original there was not use of the maps and other functional paradigms at this point, but due to the ease of
all of this with Ruby it felt natural to write it in such a manner already.

> Now, here are two functions, zero() and one():

```RUBY
def zero(s)
  s[1..-1] if s[0] == "0"
end

def one(s)
  s[1..-1] if s[0] == "1"
end
```

> zero() takes a string, s. If the first character is '0', it returns the rest of the string. If it is not, it returns None, the default return value of Python functions. one() does the same, but for a first character of '1'.

> Imagine a function called rule_sequence(). It takes a string and a list of rule functions of the form of zero() and one(). It calls the first rule on the string. Unless None is returned, it takes the return value and calls the second rule on it. Unless None is returned, it takes the return value and calls the third rule on it. And so forth. If any rule returns None, rule_sequence() stops and returns None. Otherwise, it returns the return value of the final rule.

> This is some sample input and output:

```RUBY
puts rule_sequence('0101', [zero, one, zero])
# => 1
puts rule_sequence('0101', [zero, zero])
# => nil
puts rule_sequence('0101', [zero, one, zero])
# => 1
puts rule_sequence('0101', [zero, zero])
# => nil
```

> This is the imperative version of rule_sequence():

```RUBY
def rule_sequence(s, rules)
  rules.each do |rule|
    s = rule s
    break unless s
  end
end
```


> ### Exercise 3. 
> The code above uses a loop to do its work. Make it more declarative by rewriting it as a recursion.

> My solution:

```RUBY
def rule_sequence s, rules
  s ? !s || !rules : rule_sequence(rules[0] s, rules[1..-1])
```

> ### Use Pipelines

> In the previous section, some imperative loops were rewritten as recursions that called out to auxiliary functions. In this section, a different type of imperative loop will be rewritten using a technique called pipelining.

> The loop below performs transformations on dictionaries that hold the name, incorrect country of origin and active status of some bands.

```RUBY

bands = [{'name': 'sunset rubdown', 'country': 'UK', 'active': False},
         {'name': 'women', 'country': 'Germany', 'active': False},
         {'name': 'a silver mt. zion', 'country': 'Spain', 'active': True}]

def format_bands(bands):
  new_bands = []
  bands.each do |band|
    band['country'] = 'Canada'
    band['name'] = band['name'].gsub!('.', '')
    band['name'] = band['name'].titlecase
    new_band << band
  end
  bew_bands
end

format_bands(bands)

print bands
# => [{'name': 'Sunset Rubdown', 'active': False, 'country': 'Canada'},
#     {'name': 'Women', 'active': False, 'country': 'Canada' },
#     {'name': 'A Silver Mt Zion', 'active': True, 'country': 'Canada'}]

```

> Worries are stirred by the name of the function. “format” is very vague. Upon closer inspection of the code, these worries begin to claw. Three things happen in the same loop. The value at the 'country' key gets set to 'Canada'. Punctuation is removed from the band name. The band name gets capitalized. It is hard to tell what the code is intended to do and hard to tell if it does what it appears to do. The code is hard to reuse, hard to test and hard to parallelize.

> Compare it with this:

```RUBY
pipeline bands, [[set_canada_as_country,
                  strip_name_punctuation,
                  capitalize])]

```

> This code is easy to understand. It gives the impression that the auxiliary functions are functional because they seem to be chained together. The output from the previous one comprises the input to the next. If they are functional, they are easy to verify. They are also easy to reuse, easy to test and easy to parallelize.

> The job of pipeline_each() is to pass the bands, one at a time, to a transformation function, like set_canada_as_country(). After the function has been applied to all the bands, pipeline_each() bundles up the transformed bands. Then, it passes each one to the next function.

> Let’s look at the transformation functions.

```RUBY
def assoc(d, key, value):
    e = Hash.new
    e[key] = value; e

def set_canada_as_country(band):
    assoc(band, 'country', "Canada")

def strip_name_punctuation(band):
    return assoc(band, 'name', band['name'].gsub!('.', ''))

def capitalize(band):
    return assoc(band, 'name', band['name'].titlecase)
```

> Each one associates a key on a band with a new value. There is no easy way to do this without mutating the original band. assoc() solves this problem by using deepcopy() to produce a copy of the passed dictionary. Each transformation function makes its modification to the copy and returns that copy.

> Everything seems fine. Band dictionary originals are protected from mutation when a key is associated with a new value. But there are two other potential mutations in the code above. In strip_punctuation_from_name(), the unpunctuated name is generated by calling replace() on the original name. In capitalize_names(), the capitalized name is generated by calling title() on the original name. If replace() and title() are not functional, strip_punctuation_from_name() and capitalize_names() are not functional.

> Fortunately, replace() and title() do not mutate the strings they operate on. This is because strings are immutable in Python. When, for example, replace() operates on a band name string, the original band name is copied and replace() is called on the copy. Phew.

> This contrast between the mutability of strings and dictionaries in Python illustrates the appeal of languages like Clojure. The programmer need never think about whether they are mutating data. They aren’t.

Note that some naming has changed from the original in our version.

### Moving On
A deeper examination of functional programming brings us to some very interesting mathematical roots:

#### Lambda Calculus

Sounds scary, huh?

The most basic, pure function we could do in Ruby is:

```RUBY
func = ->(x) { x }
func.call 1
# => 1
```


```
to be continued when I have more free time...
```
