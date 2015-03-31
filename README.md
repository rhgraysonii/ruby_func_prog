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

