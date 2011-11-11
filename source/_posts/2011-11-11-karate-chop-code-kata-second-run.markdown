---
layout: post
title: "Karate Chop Code Kata - Second Run"
date: 2011-11-11 12:41
comments: true
categories: [Code Kata, Ruby]
---
## Several Attempts With No Solution In Sight

The second run at implementing the binary chop method was excruciating. After several attempts, I finally decided to check out what others were doing. Was I blinded to a particular kind of solution I had in mind? Was my understanding of recursion incorrect? I felt I probably had a blind spot that was keeping me from seeing the problem in a new way. After going out on a Google search for the solution, I came across [Stephen Doyle's Blog](http://softwareramblings.com/2009/11/codekata-kata-two-solution.html)

Here is his solution:

```ruby Stephen Doyle's Solution
def chop(target, values)
  # Special handling for zero and single element arrays
  return -1 if values.empty?
 
  return ((target == values[0]) ? 0 : -1) if values.length == 1

  # Try the bottom half first
  pos = chop(target, values[0, values.length/2])
  return pos if pos != -1
 
  # Then the upper half ... remember that the returned
  # position is relative to the middle of the array.
  pos = chop(target, values[values.length/2, values.length-1])
  return pos + (values.length/2) if pos != -1

  # Didn't find what we were looking for
  return -1 

end
```

Since I didn't solve this, I'm going to talk about some of the approaches I took while trying to solve this problem and why they failed.

## My Failed Ideas

###  The Tweaking Approach

I solved the problem once. What if I reused the same ideas but changed things just a bit? Here is what I came up with.

```ruby
defchop(int,array_of_int)
  return -1 if array_of_int.empty?

  @max ||= array_of_int.size - 1
  @min ||= 0
  @mid ||= @max / 2 + @min

  return @mid if int == array_of_int[@mid]

  return -1 if @mid == @max

  if int < array_of_int[@mid]
    @max = @mid
  else
    @min = @mid
  end

  chop(int,array_of_int) 
end
```

As you can see, it uses the same idea of markers to indicate which portion of the array I am focusing on. The problem was I used instance variables to maintain state in subsequent calls to the chop method. This was a bad idea because it gave me bogus results in between assertions in my test suite. So I had to do away with instance variables as a way to maintain state.

### The If-Else Construct Approach

You can see that from the above attempt, I continued using the if-else construct to determine which path to take in order to find the index of the integer. I used this for EVERY solution thereafter. That If-Else construct stuck to me like a leech. I didn't even think of the possibility of doing away with it because I thought that was the only way you could traverse a binary tree. One approach I didn't consider was simply picking one half of the array and carrying out the search until you find nothing; only then would you search for the other half. I kept thinking I MUST know whether it is greater or smaller than the integer found at the midpoint of the array. Later in this post, I discuss why I got stuck to the if-else construct approach.

## My Successful Ideas

Even as I was making several attempts at the solution, there was one approach I took that led me closer to the solution.

### Passing Smaller Array Slices Approach

What I did figure out was that I needed to call the chop method and pass it a smaller slice of the array. If the integer was less than the integer I found at the midpoint, I would call the chop method like so:

```ruby
chop (int, array[0..mid-1])
```

If the integer was greater than the integer I found at the midpoint, I would call the chop method like so:

```ruby
chop(int, array[mid + 1..-1])
```

This approach is one which Stephen Doyle used when solving the problem with recursion. I was on the right track.

## The Missing Links

Stepping back and seeing Doyle's recursive solution, I noticed there were a few things I didn't understand which hampered my progress toward a solution.

### Just Pick a Slice!

I didn't even know this would have been allowed as part of the spec. Below is a quote from Dave Thomas' CodeKata website:

{% blockquote %}
A binary chop (sometimes called the more prosaic binary search) finds the position of value in a sorted array of values. It achieves some efficiency by halving the number of items under consideration each time it probes the values: in the first pass it determines whether the required value is in the top or the bottom half of the list of values. In the second pass in considers only this half, again dividing it in to two. It stops when it finds the value it is looking for, or when it runs out of array to search.
{% endblockquote %}

What I zeroed in on was this:

{% blockquote %}
...in the first pass it determines whether the required value is in the top of bottom half of the list of values
{% endblockquote %}

I thought that the implementation involved the decision-making logic of an if-else construct where you would check if the required value is in the top half or bottom half of the list of values. That was my train of thought. But nowhere in that sentence does it say you have to use an if-else construct. To me, the sentence is misleading. Perhaps I read too much into it which ultimately led to my brain focusing on solutions that only used if-else constructs.

Stephen's solution involves picking the bottom slice first. If the integer is not in the bottom slice, then the logic will go on to check the top slice until it finds the integer or returns a ```-1``` to indicate it found no matching integer.

## The Solution

There are a few things I would change in Stephen's solution to make things a little more concise. Here is my solution:

```ruby Karate Chop Code Kata - Second Run https://github.com/dyba/katas-samples/blob/master/karate_chop/karate_chop_2.rb See on Github
def chop(int, array_of_int)
  return -1 if array_of_int.empty?

  mid = array_of_int.size / 2
 
  return ((int == array_of_int[mid]) ? mid : -1) if array_of_int.size == 1
 
  pos = chop(int, array_of_int[0...mid])
  return pos if pos != -1
 
  pos = chop(int, array_of_int[mid..-1])
  return pos + mid if pos != -1
 
  return -1
end
```
Stephen's solution starts by checking the 0 index of the array. I went ahead and started in the middle. Also, by defining the midpoint with the ```mid``` variable, I can condense the portion of the array I pass to the chop method. I also went ahead and used ranges instead of the comma syntax inside the brackets. Rather than use ```array_of_int[array_of_int / 2, array_of_int.size - 1]```, I preferred the use of ranges and negative indexing to shorten the parameter I pass.

## A Final Note

Dave Thomas hints at a third solution:

{% blockquote %}
...one might use a functional style passing array slices around...
{% endblockquote %}

Let's see how well I fare solving it in that style.
