---
title: Function Capturing
date: 2016-06-07 19:05:28
tags: [functional programming, elixir, lazy evaluation, lambda, streams, haskell, euler]
categories:
- tech
- Functional
- Elixir
---
#### Function Capturing
These functional programming languages with their list generation and lazy evaluation make solving Project Euler Problems a real joy!

>Find the sum of all the multiples of 3 or 5 below 1000.


```.hs
-- haskell
sum [x | x <- [1..999], mod x 3 == 0 || mod x 5 == 0]
```

```.ex
# elixir
1..999 |> Stream.filter(&(rem(&1,3) === 0 or rem(&1,5) === 0)) |> Enum.sum
```

noticed those pipes `|>` in elixir eh? yeah real nice!
here's what it'd look like without pipes:

```.ex
Enum.sum(Enum.filter(1..999,fn x -> rem(x,3) === 0 or rem(x,5) === 0 end))
```
noticed I used `Enum` instead of `Stream` this time? Yes it let's you choose between Lazy and Eager evaluation.

where does the `fn ... end` come from? yeah this is how you define a lambda function, whereas in the first example I was using [_Function Capturing_](http://elixir-lang.org/getting-started/modules.html#function-capturing). Yeah .. haven't seen it outside of elixir either.

Basically works like this:
```.ex
# regular function inside a module
defmodule Math do
  def square(x) do
      x*x
  end
end
IO.write Math.square(5)

# immediately invoked lambda function
IO.write (fn x -> x*x end).(5)

# Function capture
IO.write (&(&1*&1)).(5)

# outputs 252525
```
this makes it extremely comftie to pass as lambda :)
