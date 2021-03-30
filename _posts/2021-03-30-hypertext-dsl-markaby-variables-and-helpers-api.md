---
layout: post
title: "Hypertext DSL: Markaby Variables and Helpers API"
---

In a previous post I looked at how [Markaby][mvh] integrated variables (assigns) and helper methods into its API. In this post I’d like to explore what a similar API in Hypertext DSL would look like.

Again, the `Hypertext::DSL` class is [incredibly concise][hic] (twenty-five percent of it’s 61 lines contain nothing other than an enumeration of HTML5 tags), so we’ll simply reproduce it in this post with a class called `DSL` which gives us the freedom to experiment. 

If we start with what Markaby calls the _assigns_, and what I am calling the _params_, then we can adjust the `initialize` method from this:

```
def initialize(&block)
  @ht = Hypertext.new
  instance_eval(&block)
end
```

to this:

```
def initialize(params, &block)
  @ht = Hypertext.new
  @params = params
  @params.each do |key, val|
    next if [:ht, :dom].include?(key)
    instance_variable_set(sprintf("@%s", key), val)
  end
  instance_eval(&block)
end
```

The implementation of this method is similar to our [previous experiment with variables][pev] although we can make one improvement by partitioning “private” instance variables with those that will be set via `params` using an underscore (`_`):

```
def initialize(params, &block)
  @_ht = Hypertext.new
  @_params = params
  @_params.each do |key, val|
    instance_variable_set(sprintf("@%s", key), val)
  end
  instance_eval(&block)
end
```

The other references to `@ht` in the DSL should now be replaced by `@_ht`.

Running the following code which loads our `DSL` class then confirms that variables are being defined on the DSL instance and are available to the template:

```
html = DSL.new page_title: "Hello, World!" do
  html lang: "en-GB" do
    head do
      title do
        text @page_title
      end
    end
    body do
      h1 do
        text "Welcome"
      end
    end
  end
end

puts html.to_s

#=> 
# <html lang="en-GB">
#   <head>
#     <title>
#       Hello, World!
#     </title>
#   </head>
#   <body>
#     <h1>
#       Welcome
#     </h1>
#   </body>
# </html>
```

We can check params/variables/assigns off our to-do list. Moving onto helper methods, we’ll again use the same approach, the introduction of a helper object to which we delegate unknown messages. Now we’ll extend the `DSL#initialize` method even further, adding a `helper` parameter and assigning it to an instance variable:

```
def initialize(params, helper = nil, &block)
  @_ht = Hypertext.new
  @_params = params
  @_params.each do |key, val|
    instance_variable_set(sprintf("@%s", key), val)
  end
  @_helper = helper
  instance_eval(&block)
end
```

And we’ll need to pair that assignment with method delegation via `method_missing`.

```
def method_missing(sym, *args, &block)
  if @_helper.respond_to?(sym, true)
    @_helper.send(sym, *args, &block)
  end
end
```

This is indeed a simplified version which only looks for methods on the helper object and doesn’t take into account instance variables. This is enough for us for now. Let’s reintroduce a helper with our `id_generator` method:

```
class Helper
  def id_generator
    "id-#{rand(100)}"
  end
end
```

And then, introducing this method into the template and running the example:

```
html = DSL.new({page_title: "Hello, World!"}, Helper.new) do
  html lang: "en-GB" do
    head do
      title do
        text @page_title
      end
    end
    body do
      h1 id: id_generator do
        text "Welcome"
      end
    end
  end
end

puts html.to_s

# =>
# <html lang="en-GB">
#   <head>
#     <title>
#       Hello, World!
#     </title>
#   </head>
#   <body>
#     <h1 id="id-90">
#       Welcome
#     </h1>
#   </body>
# </html>
```

Who knew it would be so simple? The only other thing to test out at this stage is how it works with loading in templates from a file. Then we can move on to figuring out if these primitives allow us to build higher level constructs, HTML helpers, components and the like.

In the meantime, is this the best way forward? Are there other, simpler, easier, more fitting APIs? Is there a way to accomplish the delegation of unknown methods to the helper without `method_missing`?

—*Tuesday 30th March 2021.*

[mvh]: https://www.crossingtheruby.com/2021/03/29/hypertext-dsl-template-variables-helpers-2.html
[hic]: https://github.com/soveran/hypertext/blob/master/lib/hypertext/dsl.rb
[pev]: https://www.crossingtheruby.com/2021/03/28/hypertext-dsl-template-variables-helpers.html
