---
layout: post
title: "Ruby Monk Problem - Capitalizing an Array of Names"
date: 2011-11-13 12:09
comments: true
categories: [Ruby]
---
## The Exercise

In lesson 5 of [RubyMonk.com](http://rubymonk.com), there's an exercise with the following objective:

{% blockquote %}
to capitalize the first character of each part of the name as it combined them.
{% endblockquote %}
The code they provide is:
```ruby
# bust out a beefy reduce on this bad boy:
["emperor", "joshua", "abraham", "norton"]
```
### First Attempt
I worked out the code below but then realized it didn't solve the problem.

```ruby
["emperor", "joshua", "abraham", "norton"].inject do |full_name, name|
  "#{full_name} #{name.capitalize}"
end
```
This ends up with ```"emperor Joshua Abraham Norton"```. I didn't capitalize the first word ```emperor```. 

### Second Attempt
Ok, no problem. I'll just capitalize ```full_name```. 
```ruby
["emperor", "joshua", "abraham", "norton"].inject do |full_name, name|
  "#{full_name.capitalize} #{name.capitalize}"
end
```
No. You can't do that. That will end up capitalizing just the first word ```emperor```. You will end up with ```"Emperor joshua abraham Norton"```. The reason is the block variable ```full_name``` remembers the last value calculated inside the block. Because we did not pass an initial value to the inject method, it uses the first value in your collection by default, in this case ```"emperor"```. Inside the body of the block, we take ```"emperor"``` and capitalize it followed by ```"joshua"``` and capitalize that. We put the two together to get the complete string ```"Emperor Joshua"```. You can try this out in irb by truncating your array to just two elements, ```["emperor", "joshua"]```.

So far so good. Next, we use this result of the calculation carried out in the body of the block and pass it to the block variable ```full_name```. The block variable ```name``` is set to the next item in our collection, which is ```"abraham"```. We run through the body of the block again which leads us to capitalize ```"Emperor Joshua"``` and ```"abraham"```. That gives us ```"Emperor joshua"``` and ```"Abraham"```. That's where things go wrong. When you ```capitalize``` a string, you are putting all characters except the first character in their lower case form. 

### A Hack
How then, do you capitalize the first name? By default, ```inject``` will use ```"emperor"``` as its initial value since I have passed no parameter to the ```inject``` method. What if I did try to pass in a parameter? What would that parameter be? What if I passed in an empty string? Ah, in that case, the ```full_name``` will be equal to ```''```, the empty string. 

```ruby
["emperor", "joshua", "abraham", "norton"].inject('') do |full_name, name|
  "#{full_name} #{name.capitalize}".strip
end
```

If I only made a call to ```"#{full_name} #{name.capitalize}"```, I would have an extra space in front of the name, ```" Emperor Joshua Abraham Norton"```, by the time block iterated through all the items in the array. To get rid of this, I just ```strip``` the whitespace. Do you have another of solving this problem with the constraint of using the ```inject``` method? I'd love to hear your ideas.
