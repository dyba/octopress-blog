---
layout: post
title: "Code Kata 4: A TDD Attempt Toward Readable Code"
date: 2011-11-25 13:02
comments: true
categories: [Ruby, Code Kata]
---
## Code Kata #4 - Data Munging Part One

### Overview
The fourth Code Kata in Dave Thomas' Code Kata blog asks you to do the following:

{% blockquote %}
write a program to output the day number (column one) with the smallest temperature spread (the maximum temperature is the second column, the minimum the third column).
{% endblockquote %}

He provides a weather.dat file for you to download and which you will parse through in order to obtain the solution. In this post, I will only cover the first part of the code kata. I completed the first part of the kata over the course of 3 days working up to 4 hours on one day and thinking about the problem when not in front of the computer screen. Let's take a look at a solution I developed.

### The Completed Code

```ruby WeatherReader program at /lib/weather_reader.rb
class WeatherReader
  attr_reader :data, :spread
 
  def read(filename)
    @data = File.readlines(filename).reject(&no_data)
  end

  def date_of_smallest_spread
    @data.map(&spread).find_index(smallest_spread) + 1
  end

  def smallest_spread
    @data.map(&spread).min
  end

  def spread
    @spread ||= ->(line) { (temperature(line, :max) - temperature(line, :min)).abs }
  end

  private
 
    def temperature(line, kind)
      line.match(regex)[kind].to_i
    end

    def no_data
      ->(line) { line.match(regex).nil? }
    end

    def regex
      %r{
        (?<day> \d+){0}
        (?<max> \d+){0}
        (?<min> \d+){0}

        \g<day>\s+\g<max>\s+\g<min>
      }x
    end
end

```

```ruby RSpec Examples /spec/weather_reader_spec.rb
require 'rspec'
require 'spec_helper'

describe WeatherReader do
  describe "Utility methods" do
    let(:weather_reader) { WeatherReader.new }

    before(:each) do
      @data = weather_reader.read("weather.dat")
    end

    it "filters lines that don't contain weather data" do
      @data.should_not include(" MMU June 2002\n")
    end

    context "given a line containing weather data for a day" do
      let(:line) { "   1  88    59    74          53.8       0.00 F       280  9.6 270  17  1.6  93 23 1004.5" }
      it "returns the difference between the maximum temperature and minimum temperature" do
        weather_reader.spread.call(line).should == 29
      end
    end

    context "given lines containing weather data for several days" do
      it "returns the smallest temperature spread" do
        weather_reader.smallest_spread.should == 2
      end

      it "returns the date that had the smallest temperature spread" do
        weather_reader.date_of_smallest_spread.should == 14
      end
    end
  end
end

```

```bash Contents of the weather.dat file
(Unofficial, Preliminary Data). Source: <a
href="http://www.erh.noaa.gov/er/box/dailystns.html">www.erh.noaa.gov/er/box/dailystns.html</a>

<pre>
 MMU June 2002

  Dy MxT   MnT   AvT   HDDay  AvDP 1HrP TPcpn WxType PDir AvSp Dir MxS SkyC MxR MnR AvSLP

   1  88    59    74          53.8       0.00 F       280  9.6 270  17  1.6  93 23 1004.5
   2  79    63    71          46.5       0.00         330  8.7 340  23  3.3  70 28 1004.5
   3  77    55    66          39.6       0.00         350  5.0 350   9  2.8  59 24 1016.8
   4  77    59    68          51.1       0.00         110  9.1 130  12  8.6  62 40 1021.1
   5  90    66    78          68.3       0.00 TFH     220  8.3 260  12  6.9  84 55 1014.4
   6  81    61    71          63.7       0.00 RFH     030  6.2 030  13  9.7  93 60 1012.7
   7  73    57    65          53.0       0.00 RF      050  9.5 050  17  5.3  90 48 1021.8
   8  75    54    65          50.0       0.00 FH      160  4.2 150  10  2.6  93 41 1026.3
   9  86    32*   59       6  61.5       0.00         240  7.6 220  12  6.0  78 46 1018.6
  10  84    64    74          57.5       0.00 F       210  6.6 050   9  3.4  84 40 1019.0
  11  91    59    75          66.3       0.00 H       250  7.1 230  12  2.5  93 45 1012.6
  12  88    73    81          68.7       0.00 RTH     250  8.1 270  21  7.9  94 51 1007.0
  13  70    59    65          55.0       0.00 H       150  3.0 150   8 10.0  83 59 1012.6
  14  61    59    60       5  55.9       0.00 RF      060  6.7 080   9 10.0  93 87 1008.6
  15  64    55    60       5  54.9       0.00 F       040  4.3 200   7  9.6  96 70 1006.1
  16  79    59    69          56.7       0.00 F       250  7.6 240  21  7.8  87 44 1007.0
  17  81    57    69          51.7       0.00 T       260  9.1 270  29* 5.2  90 34 1012.5
  18  82    52    67          52.6       0.00         230  4.0 190  12  5.0  93 34 1021.3
  19  81    61    71          58.9       0.00 H       250  5.2 230  12  5.3  87 44 1028.5
  20  84    57    71          58.9       0.00 FH      150  6.3 160  13  3.6  90 43 1032.5
  21  86    59    73          57.7       0.00 F       240  6.1 250  12  1.0  87 35 1030.7
  22  90    64    77          61.1       0.00 H       250  6.4 230   9  0.2  78 38 1026.4
  23  90    68    79          63.1       0.00 H       240  8.3 230  12  0.2  68 42 1021.3
  24  90    77    84          67.5       0.00 H       350  8.5 010  14  6.9  74 48 1018.2
  25  90    72    81          61.3       0.00         190  4.9 230   9  5.6  81 29 1019.6
  26  97*   64    81          70.4       0.00 H       050  5.1 200  12  4.0 107 45 1014.9
  27  91    72    82          69.7       0.00 RTH     250 12.1 230  17  7.1  90 47 1009.0
  28  84    68    76          65.6       0.00 RTFH    280  7.6 340  16  7.0 100 51 1011.0
  29  88    66    77          59.7       0.00         040  5.4 020   9  5.3  84 33 1020.6
  30  90    45    68          63.6       0.00 H       240  6.0 220  17  4.8 200 41 1022.7
  mo  82.9  60.5  71.7    16  58.8       0.00              6.9          5.3
</pre>

```

## TDD is Software Improv
It is unfortunate that I didn't capture the dynamic interplay between writing/rewriting the tests and writing/rewriting the code in this example. I think I originally heard it from David Chelimsky at SCNA 2011 who said that Test Driven Development is Software Improv. TDD is a sweeping movement involving writing your tests, writing code to pass your tests, refactoring your code, refactoring your tests, critiquing your code to determine how you should write your next test, and critiquing your tests to inform the design of your code. TDD truly is an art form. 

Even as I write this post, I understand how difficult it is to capture the TDD process in writing. Have you ever seen Live Improv as a series of photos? For one thing, you won't catch the humor in the dialogue. You might laugh at the pictures, but you won't get the jokes unless you had understood the context in which they were delivered; that's where the dialogue is important. But listening to the dialogue alone isn't enough. You wouldn't be able to capture the subtle nuances in the gestures of the performers. The photos wouldn't capture that because they are minutes, even seconds apart. Now what happens if you see Live Improv as an audience member? You finally get it. In the same way, TDD cannot be expressed as a static blog post. I do not do it justice by simply talking about it; however, I will do my best to inform you what happens in the process of doing TDD.

## Debrief
### How'd you get there?
At the beginning, I started writing very trivial test cases that would check for the return type of the ```read``` method. It would check to make sure that the return values were not nil. Of course, you don't see these test cases in the final product. That's because those tests acted like scaffolding that I temporarily used to get me started. After I build the right structure in my program, I wrote other tests that rendered that scaffolding test code obsolete.

TDD is an incremental process. You should only be writing the minimal amount of code to make your tests pass. I think this is where I didn't do as great a job executing TDD. Once I understood the pattern of the code I needed to write, I went ahead and wrote that as production code. In the real world, the patterns won't be as obvious and it will take having to write code for every case until you see an overarching picture of the kind of code that would cover the general case. 

The example I will cite here is when I test a single line of data to determine if I get the right value for the spread of the maximum and minimum temperature. The next thing I do is write the code that does that for every line of code. Probably the way I should have gone about that was to write a regular expression that was hardcoded to that particular string. Then I should have thrown in another case where there is an asterisk next to the maximum or minimum temperature as is true for day 9 or day 26 in the weather.dat file. When you work on a larger project, it may not be obvious how you can generalize the production code you need to write. Thus, it is a good habit to build your program incrementally when you do TDD.

### Programming With Lambdas
Recently, I have been making more of an effort to introduce lambdas in my code. Lambdas and Procs were one of the most nebulous ideas for me when I first started programming in Ruby. Only recently did I start playing with passing lambdas as arguments to methods. Originally, the ```no_data``` method wasn't in my code. It came about during the refactoring phase when I wanted to make the read method more concise (and curiously also wanted to see if I could extract the code block into something more meaningful). The code had read like so:

```ruby
def read(filename)
  File.readlines(filename) do |line|
    line.match(regex).nil?
  end
end
```
Now, there's nothing wrong with that. However, if I were another programmer trying to read this code, I would be asking myself, "What are you matching against?" So I decided to write this instead:

```ruby
def read(filename)
  File.readlines(filename).reject(&no_data)
end
```
To me that reads more clearly than the first example. It may be a little difficult for newer programmers to understand what the syntax is doing. But it is clear that I am rejecting data from the lines of the file I read. Here is the code for the ```no_data``` method.

```ruby
def no_data
  ->(line) { line.match(regex).nil? }
end
```
The name of the method communicates its function. It defines what data is not; however, I probably could have written a different name for ```regex```. Looking at it right now it doesn't communicate its intent. Perhaps, I should have written something like this:

```ruby
def no_data
  ->(line) { line.match(weather_data).nil? }
end
```
That would have been more helpful to someone who is trying to find out how the ```no_data``` method works. 

### Being Expressive with Regular Expressions
If that same programmer wanted to see what ```regex``` would look like, they would see this:

```ruby
def regex
  %r{
    (?<day> \d+){0}
    (?<max> \d+){0}
    (?<min> \d+){0}

    \g<day>\s+\g<max>\s+\g<min>
  }x
end

```
Originally, I had written down this expression:
```ruby
def regex
  /(\d+)\s+(\d+)\s+(\d+)/
end
```
What is that expression supposed to be doing? It is capturing three groups of numbers that are separated by spaces. But those numbers don't have any meaning at all! That's when I decided to use the 'x' option to allow spaces in my regular expression. I went ahead and added named subroutines in order to layer the regular expression with meaning. Notice that I named the groups as ```<day>```,```<max>```,```<min>```---all names that are relevant to the problem at hand. Unfortunately, those names alone don't provide much meaning to the problem, I probably should have been a bit more descriptive with the names: "Maximum of what?", "Minimum of what?" "Are we talking about heights? weights? distances?" A better implementation would have been:
```ruby
def weather_data
  %r{
    (?<day>             \d+){0}
    (?<max_temperature> \d+){0}
    (?<min_temperature> \d+){0}

    \g<day>\s+\g<max_temperature>\s+\g<min_temperature>
  }x
end

```
The reason why I didn't add the names ```max_temperature```, and ```min_temperature```, was because of the way I implemented the ```temperature``` method in my program.

### Something Smells
Take a look at the following code:
```ruby
  def spread
    @spread ||= ->(line) { (temperature(line, :max) - temperature(line, :min)).abs }
  end

  private
 
    def temperature(line, kind)
      line.match(regex)[kind].to_i
    end
```
Had I changed the names in my regular expressions, I would have had to change the above code to this:
```ruby
  def spread
    @spread ||= ->(line) { (temperature(line, :max_temperature) - temperature(line, :min_temperature)).abs }
  end

  private
 
    def temperature(line, kind)
      line.match(regex)[kind].to_i
    end
```
Why is temperature mentioned twice in a method call? It is adding too much noise. I should have just gotten rid of the ```temperature``` method and attempted a better implementation like so:

```ruby
  def spread
    @spread ||= ->(line) { (line.match(weather_data)[:max_temperature].to_i - line.match(weather_data)[:min_temperature].to_i).abs }
  end

  private
=begin 
Smells bad
    def temperature(line, kind)
      line.match(regex)[kind].to_i
    end
=end
```
Even the above implementation is too long to read, but it communicates its intent better than the method I had written.

## Take Aways
When doing TDD, you always need to keep in mind the other programmer who will be reading your code. It is not enough to make the program work; it needs to be readable and easy to understand for the rest of your team. 

There are other things I didn't get a chance to cover in this post that would have made it too long: one of those things included the implementation of finding the smallest spread and the date for that spread. I'd like to see your implementation of this code kata. Perhaps there is some other way of implementing this that had not occurred to me. I welcome your insight. I invite you to provide any feedback in the comments below.

