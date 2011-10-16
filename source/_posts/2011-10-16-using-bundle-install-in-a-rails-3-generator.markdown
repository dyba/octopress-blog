---
layout: post
title: "Using 'Bundle Install' in a Rails 3 Generator"
date: 2011-10-16 00:04
comments: true
published: true
categories: [Rails]
---
### Problem

I came across Rails issue [#3153](https://github.com/rails/rails/issues/3153) on Github which involved someone thinking ```bundle install``` didn't work with Rails generators.

When working with Rails generators it's important to remember that generators use Yehuda Katz's Thor gem. The issue arose because the user tried to run the command ```bundle install``` from inside the directory in which the generator file had been created.

```ruby
class SomegenGenerator < Rails::Generators::Base
 source_root File.expand_path('../templates', __FILE__)

 def install
   gem "unicorn"
   run "bundle install"
 end
end
```

### Solution
Again, the issue is that the command ```bundle install``` is being run from inside the directory ```Issue3153/lib/generators/something/```. I'll let you take a quick look at the Thor source code at [lib/thor/actions.rb](https://github.com/wycats/thor/blob/master/lib/thor/actions.rb#L161). As you have now seen, we can use the ```inside``` method to run the ```bundle install``` command from the root directory of our app Issue3153.

```ruby 
# Issue3153/lib/generators/something/something_generator.rb
class SomethingGenerator < Rails::Generators::Base
  source_root File.expand_path('../templates', __FILE__)

  def install
    gem "unicorn"
    inside Rails.root do
      run "bundle install"
    end
  end
end
```

This now works! 

### Take Aways

- Rails generators use Thor Actions
- The bundle install command must be run in the directory where the Gemfile of interest resides

