---
layout: post
title: "Karate Chop Code Kata - First Run"
date: 2011-11-07 18:40
comments: true
categories: [Ruby, Code Kata]
---
##The Idea

Here is my first attempt at Code Kata #2 - Karate Chop, which is an exercise you can find on Dave Thomas' website [Code Kata](http://codekata.pragprog.com/2007/01/kata_two_karate.html). Now mind you, I never had a formal introduction to computer algorithms. I had to look at a computer algorithms book to make sure I understood what was meant by the term binary search routine. The idea behind Karate Chop is to write a binary search routine that takes an integer and an array, splits the array in two, determines which half of the array the integer should be on and repeats the process until it finds the integer and returns it or doesn't find it and returns a ```-1```. In the end I should have a function that can be described as follows:

```ruby
chop(integer, array_of_integers) => integer
```

The integer it returns should be the index of the array in which I found the matching number. In the event that I do not find the integer in the array, I should get a ```-1```.

##The Solution

```ruby Karate Chop Code Kata - First Run https://github.com/dyba/katas-samples/blob/master/karate_chop/karate_chop.rb See On Github
def chop(int,array_of_int)
  return -1 if array_of_int.empty?

  min, max = 0, array_of_int.size - 1
  midpoint = max / 2

  while int != array_of_int[midpoint]

    if int < array_of_int[midpoint]
      max = midpoint - 1
    else
      min = midpoint + 1
    end
    

    midpoint = min + (max - min) / 2
    return -1 if max < min || min > midpoint
  end

  midpoint 
end
```

##The Walkthrough

###Edge Cases

I implement the binary search routine using a while loop. I address the the edge case when the array is empty. When that hapens, I know that I must return ```-1``` because no integer will ever be present in an empty array.

```ruby
return -1 if array_of_int.empty?
```

###Markers Point You In the Right Direction

In this solution, I use markers to let me know what portion of the array I am focusing on. I start off by initializing my markers. I have a min marker which tells me the first element of the array, and I have a max marker which tells me the last element of the array. The midpoint marker tells me which index is the midpoint of the array. These markers will change during the course of the program as I search through smaller subsections of the array.

```ruby
  min, max = 0, array_of_int.size - 1
  midpoint = max / 2
```

###The Search Is On

Now comes the interesting part, the loop construct. I decided to use the while loop to iterate through the array because it was the first solution that came to mind. Only if I found the matching integer would I exit the while loop and return the index of the array in which I found the matching number. 

```ruby
while int != array_of_int[midpoint]
```

Inside the while loop, I check for the condition where the integer I seek is less than the integer I find at the midpoint of the array. If it is less, then I move my max marker to 1 less than the midpoint; if more, then I move my min marker to 1 more than the midpoint. This is easily illustrated with a diagram.
    
```ruby
    if int < array_of_int[midpoint]
      max = midpoint - 1
    else
      min = midpoint + 1
    end
```

Notice that with each iteration, I am focusing on a smaller portion of the array. After the if-else construct, I advance the midpoint marker to reflect the new midpoint for the smaller portion of the array that I am focusing on. 

```ruby
    midpoint = min + (max - min) / 2
```

###When Markers Go Wrong

Here is where it gets tricky. A strange thing happens when the integer is not present in the array: the max, min, and midpoint markers go berserk! They no longer begin to make sense. That's when I know it's time to jump out of the loop and return a ```-1``` to indicate that there was no matching integer inside the array.

```ruby
    return -1 if max < min || min > midpoint
```

Finally, the last line returns the index of the array that matched the integer I passed to the function.

```ruby
  midpoint 
```

##A Final Note

Don't let my explanation fool you, this was a difficult exercise for me. At first I was thinking about chopping the array in half and then searching through each half iterating through each element one by one to see if I found the integer. That wasn't the correct way to go about it; it would have been an incorrect implementation of a binary search. When you come across unfamiliar terms, you must start by understanding those terms before you can tackle a problem. My problem was that I didn't understand the idea of a binary search. 

This exercise took me somewhere close to 8 hours. I'm hoping to cut my time in half in the next implementation. Stick around as I try to solve this again using a different implementation.
