---
layout: post
title: "String Calculator Kata Without If, Else, Switch, or Case"
date: 2011-11-23 14:06
comments: true
categories: [Ruby, Code Kata]
---
The goal of this kata is to create a calculator that can add the numbers found in a string. I found this kata on [Roy Osherove's website](http://osherove.com/tdd-kata-1/). Here are the instructions for doing this kata:

## String Calculator Kata Overview
## The Rules
  1. Create a simple String calculator with a method __int Add(string numbers)__
     1. The method can take 0, 1, or 2 numbers, and will return their sum (for an empty string it will return 0) for example "" or "1", or "1,2"
     2. Start with the simplest test case of an empty string and move to 1 and two numbers
     3. Remember to solve things as simply as possible so that you force yourself to write tests you did not think about
     4. Remember to refactor after each passing test
  2. Allow the Add method to handle an unknown amount of numbers
  3. Allow the Add method to handle new lines between numbers (instead of commas).
     1. The following input is OK: "1\n2,3" (will equal 6)
     2. The following input is NOT OK: "1,\n" (not need to price it - just clarifying)
  4. Support different delimiters
     1. To change a delimiter, the beginning of the string will contain a separate  line that looks like this: "//[delimiter]\n[numbers…]" for example "//;\n1;2" should return three where the default delimiter is ';'.

There are more instructions to this kata but that was as far as I was able to get without going too far over the time limit. I imposed a 30 minute time limit to complete this kata.

### Constraints Inspire Creative-Thinking

I want to make a very important observation here. I could have solved this kata with programming techniques with which I am familiar; however, familiarity does not breed creativity. If you want to take your creativity to the next level, you need to put constraints. Constraints are your friends. They are the ones that prod you to think about novel ways of doing things. In this kata, I added the constraint of avoiding these keywords:

  -   ```if```
  -   ```else```
  -   ```switch```
  -   ```case```

It's amazing what you'll discover when you impose these constraints. I began to write my solution in a Lispy sort of fashion as you'll soon see.

## The Completed Kata

```ruby The Completed String Calculator Kata
require 'rspec'

class StringCalculator
 
  def add(string)
    string.empty? && (return 0)
    string.end_with?("\n") && (return nil)

    string.start_with?("//") &&
      (@delimiter ||= string.match('^//(.)')[1]) &&
        string.sub!(/^\/\/(.)/, '')

    string.include?("\n") &&
      string.gsub!(/\n/, ' ')

    @delimiter &&
      (return string.split("#{@delimiter}").map(&:to_i).inject(&:+))
   
    string.split(/[,\s]/).map(&:to_i).inject(&:+)
  end

end

describe StringCalculator do
  let(:calc) { StringCalculator.new }
   
  it "returns 0 for empty string" do
    calc.add("").should == 0
  end

  it "returns 1 for '1'" do
    calc.add("1").should == 1
  end

  it "returns 3 for '1,2'" do
    calc.add("1,2").should == 3
  end

  it "returns 55 for '1,2,3,4,5,6,7,8,9,10'" do
    calc.add("1,2,3,4,5,6,7,8,9,10").should == 55
  end

  it "returns 6 for '1\\n2,3'" do
    calc.add("1\n2,3").should == 6
  end

  it "returns nil for '1,\\n'" do
    calc.add("1,\n").should be_nil
  end

  context "when changing the delimiter to ';'" do
    it "returns 3 for '//;\\n1;2" do
      calc.add("//;\n1;2").should == 3
    end
  end

  context "when changing the delimiter to ': and terminating the string with '\\n'" do
    it "returns nil for '//:\\n1:\\n" do
      calc.add("//:\n1:\n").should be_nil
    end
  end
end
```

### Lispifying The Solution

Since I wasn't able to use the ```if```, ```else```, ```switch```, and ```case``` keywords, I had to think of a new way of having my program make decisions. I observed that I was using methods that returned booleans such as ```empty?```, ```start_with?```, ```end_with?```, and ```include?```. My mind was screaming at me: "USE AN IF STATEMENT HERE!" I refrained. All I knew was that I wanted my program to make decisions based on whether or not the results of those method calls were true. Then I had my 'Ah ha!' moment. Part of what led me to use the ```&&``` operator in my code came about because I am learning Common Lisp. Here is a snippet of Lisp code that checks to see if an atom belongs to a list:

```ruby
(defun member? (a lst)
  (cond
    ((null lst) nil)
    (T (or
         (eq a (car lst))
         (member? a (cdr lst))))))
```
Take a look at the OR statement. If the atom ```a``` is equal to the first item of the list in ```lst```, then ```member?``` returns true and it exits the function; otherwise it will evaluate the statement ```member?``` and pass the atom ```a``` and the remaining items in the ```lst``` not including the first item. Did you see how you can use an a function call as a statement to be used with an ```or``` operator? In my solution, I went ahead and using this logic except I used the ```&&``` operator to accomplish this. Let's walk through some of the code in the solution.

### A Walkthrough

We begin by recalling the requirements of the string calculator. The first requirement is to return 0 for an empty string. To meet that requirement, I write code to query the string and ask if it is ```empty?```. When I pass an empty string, the response will be ```true```. Since the statement to the left of the ```&&``` operator is true, the ```&&``` must take the next step of evaluating the statement to the right. 
```ruby
 string.empty? && (return 0)
```
The statement on the right is a ```return``` statement, so we leave the ```add``` function with the result of ```0``` for an empty string. The same logic applies to the next call ```end_with?```. 
```ruby
    string.end_with?("\n") && (return nil)
```
If the string ends with a newline character ```"\n"```, the ```&&``` operator is forced to execute the statement to the right.

In the next few lines, I took the ```&&``` operator further. I know that all Ruby objects return ```true```. With that understanding, I went ahead and chained three statements with the ```&&``` operator:

```ruby
    string.start_with?("//") &&
      (@delimiter ||= string.match('^//(.)')[1]) &&
        string.sub!(/^\/\/(.)/, '')
```
Doesn't that cascading style remind you of Lisp? I think this is really cool! This code covers the case when the user wants to change the delimiter in the string. Instead of using commas and passing a string such as ```"1,2"```, the user can now pass a string like this: ```"//;\n1;2"```. Passing this kind of string to the ```add``` method should change the delimiter from a comma to a semicolon. Let's suppose we receive the string ```"//;\n1;2"```. We see it starts with ```"//"``` so the method ```start_with?``` returns ```true```. Since we are ```&&```ing our statements together, all of them must return true in order for all three to be executed, otherwise, we will skip to the next portion of the program. The next step involves capturing the new delimiter and caching the results to the ```@delimiter``` instance variable. That entire statement returns true because at the end ```@delimiter``` has a non-nil value. We move on to the next statement, which removes the delimiter information from the original string so that we can parse the number information.

This next line is my favorite one.

```ruby
    @delimiter &&
      (return string.split("#{@delimiter}").map(&:to_i).inject(&:+))
```

Since we received a cached result from the previous chain of statements for ```@delimiter```, ```@delimiter``` is a non-nil object which evaluates to ```true```. Since ```@delimiter``` evaluates to ```true```, we move to the next statement and evaluate it. I especially like how I passed the methods ```to_i``` and ```+``` as symbols to the ```map``` and ```inject``` methods. It saved me space and communicates its intent! We split on the new delimiter, convert each string represented as a number to an integer, and then collect a running sum and return the result.

## Take Aways

The most valuable thing I got from having done this kata were the constraints I placed on myself. They pushed me outside of the comfort of the familiar techniques I used and demanded more from me. I did notice the repetition toward the bottom of the ```add``` method. I had some difficulty refactoring the code in time. Perhaps next time I perform this kata, I'll have a DRYer version.
