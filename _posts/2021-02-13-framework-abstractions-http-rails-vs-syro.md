---
layout: post
title: "Rails-Syro Routing Comparison"
---

In my overview of how both [Rails][rhr] and [Syro][shr] abstract HTTP (and CGI) with a focus on routing, we saw that Rails has a large resource-oriented DSL for creating routes while Syro relies on a few core primitives to model routes as a tree.

When diving a little deeper into some of the methods that Rails provides which I didn’t have time to cover in the previous post it struck me that many of Rails’ routing behaviours are easily covered by the primitives that Syro provides, without needing to add a layer of abstraction via a DSL.

The strength of Syro’s flexibility lies in two methods, `on` and `match`. `on` allows you to nest route segments (both static and dynamic) or conditions to an arbitrary depth. The implementation is as follows, with the `default` method included for context:

```
def default
  yield; finish!
end

def on(arg)
  default { yield } if match(arg)
end
```

`default` ensures that when you get to the furthest leaf on the tree, or in this context, the last segment of the route, it will finish the request process and return a response.

`on` then leans on `match` to achieve it’s flexibility, determining whether or not the tree should be further walked, or in this context, that a new route segment has been matched and the code in the block must be executed. This code is either a new leaf with `on` or some application code to be run—and in some cases both. This is the implementation:

```
def match(arg)
  case arg
  when String then consume(arg)
  when Symbol then capture(arg)
  when true   then true
  else false
  end
end
```

It is a simple case statement with three conditions and four potential return values. Either it is a string, `on "admin" do`, or it is a symbol, `on :id do`, or it is a boolean expression, `on user.authenticated? do`. If this last expression is false, or the expression happens not to be a boolean then it will return false.

A value of `false` will ensure that the code nested within the statement will not be run and Syro will continue walking to the next sibling. 

A value of `true` will enter that branch and execute the code and/or visit the children. 

A symbol, identifying a dynamic segment, is intended for capturing a variable taking that segment of the URL and putting it in the ‘inbox’. This also has a relatively straightforward implementation, ultimately calling out to the `seg` library:

```
# set on initialization with the path from Rack
@syro_path = Seg.new(env.fetch(Rack::PATH_INFO))

# use Seg to extract the variable and place it in the inbox
def capture(arg)
  @syro_path.capture(arg, inbox)
end
```

A string, identifying a static segment, simply ensures that this segment of the route has been ‘consumed’ and that the path passed to any children relates to everything on the right of that segment.

In some ways, you could say that as Syro walks the tree of code, it is also walking the path of the path. And while doing so it is recording where precisely in the path it is; leaving breadcrumbs as it were.

And that’s really all there is to know about how `on` works internally. Maybe you could dive a little deeper into all 82 SLOC in [Seg][seg] to see precisely how segmenting of the path and tracking one’s position on that path is accomplished.

So let’s see what we can do with that compared with what Rails offers us. Are we losing any functionality because we only have a fraction of the code?

### Namespacing

Rails:

```
namespace :admin do
  get '/books', to: 'books#index' 
end
```

Syro:

```
on "admin" do
  on "books"
    get do
      # index
    end
  end
end
```

### Constraints

Rails:

```
constraints(id: /\d+\.\d+/) do
  get '/books/:id', to: 'books#show'
end
```

Syro:

```
on "books" do
  on :id do
    on inbox[:id].match?(/\d+\.\d+/) do
      get do
        # show
      end
    end
  end
end
```

### Scoping

Rails:

```
scope module: "admin" do
  resources :books
end

scope path: '/admin' do
  resources :books
end

scope as: "sekret" do
  resources :books
end
```

Now these code examples require a bit of explanation. The first leaves the URL as normal, but sends the request to the `Admin::BooksController`. The second sends the request for, e.g. `/admin/books`, to the `BooksController`. This then is distinguished from the namespace example above which adjusts both the path _and_ the module. The third example allows a different prefix to be used in the Rails URL helpers.

Syro:

```

```

Since Syro doesn’t do any implicit mapping of routes to controllers and since Syro doesn’t offer URL helpers then there is no need for an equivalent. Indeed one is free to question whether Rails URL helpers are really _helpers_ (but that’s a subject for another day).


### Controllers

Rails:

```
controller "books" do
  match "bestsellers", action: :bestsellers, via: :get
end
```

Syro:

```

```

The `controller` method is also one for which Syro needs no equivalent.

### Direct

Rails:

```
direct :homepage do
  "http://www.rubyonrails.org"
end
```

Syro:

```

```

Since Syro relieves us from the burden of reverse-parsing URL helpers there is no need for an equivalent.

### Resolve

Rails:

```
resource :basket

resolve "Basket" do
  [:basket]
end
```
Allows customization of polymorphic model mapping.

Syro:

```

```

I think you’re getting the idea.


### Concerns

Rails:

```
concern :reviewable do
  resources :reviews
end
```
Allows this route to be reused, nested under multiple other routes.

Syro:

```
Reviewable = Syro.new do
  on "reviews" do
    # …
  end
end

Syro.new do
  on "books" do
    run Reviewable, inbox
  end
  
  on "dvds" do
    run Reviewable, inbox
  end
end
```

Syro doesn’t only offer the ability to arbitrarily nest routes but also to nest Syro _applications_.

## Summing Up

With the exception of some methods which don’t apply in the context of Syro, Syro is able to accomplish the same routing functionality as Rails. And it does so with the primitives described at the beginning of the post. 

It is astonishing that Syro comes in at 386 SLOC _total_ ([code][syco]) while just the `ActionDispatch::Routing::Mapper` ([code][adrm]) which only forms a _part_ of Rails routing is a whopping 2039 SLOC.

—*Saturday 13th February 2021.*

[rhr]: https://www.crossingtheruby.com/2021/02/10/framework-abstractions-http-rails-routing.html
[shr]: https://www.crossingtheruby.com/2021/02/11/framework-abstractions-http-syro-routing.html
[seg]: https://github.com/soveran/seg.rb/blob/master/lib/seg.rb
[syco]: https://github.com/soveran/syro/blob/master/lib/syro.rb
[adrm]: https://github.com/rails/rails/blob/130c128eae233bf71231c73b9c3c3b3f3ede918b/actionpack/lib/action_dispatch/routing/mapper.rb#L959
