---
layout: post
title: "Ruby Monk Problem - Capitalizing an Array With Names (Revisited)"
date: 2011-11-15 18:27
comments: true
categories: [Ruby]
---
## Another Look
This is a follow up to my [previous blog post](http://danieldyba.com/blog/2011/11/13/ruby-monk-problem-capitalizing-an-array-of-names/) on the Ruby Monk problem. The challenge with which I ended that post was to solve the problem with the constraint of using the ```inject``` method. Here's a solution that meets that challenge:

```ruby
["emperor", "john", "abraham", "norton"].map(&:capitalize).inject do |full_name, name|
  "#{full_name} #{name}"
end
```

Instead of initializing the inject method with an empty string, I used the ```map``` method to apply the ```capitalize``` method on each of the elements in the array. Once I have done that, then using method chaining, I can start combining each element in the array in a cumulative manner with the ```inject``` method.

## Let's Race
Here's an interesting question: If I had several thousands elements in the array, which solution would be better in terms of performance? It would probably be more efficient to use the hack in the previous post. In this solution, I am iterating through the array twice: once using the map method and a second time using the inject method.

I use Benchmark to give me an idea of what performance looks like between the two solutions.
```ruby Benchmarking the new solution
require 'benchmark'

names = []

10000.times do
  names << "emperor".split(//).shuffle.join
end

Benchmark.bm do |trial|
  trial.report("Map & inject:             ") do 
    names.map(&:capitalize).inject do |full_name, name|
      "#{full_name} #{name}"
    end
  end
  trial.report("Inject with initial value:") do
    names.inject('') do |full_name, name|
      "#{full_name} #{name.capitalize}".strip
    end
  end
end
```

## Double-take

Take a look at these results:

```console Benchmark Results
                                user     system      total        real
Map and inject:             0.310000   0.100000   0.410000 (  0.399476)
Inject with initial value:  0.500000   0.190000   0.690000 (  0.691769)
```
The new solution is __40% faster__ than the solution in my previous post! This was not what I expected at all! It seems counter-intuitive that iterating through an array twice is faster than iterating through it once.

## Final Note

Yup, I'm guilty of having relied too readily on my assumptions. This will serve as a reminder to keep asking questions that challenge my thinking. What may seem obvious may not be so. 
