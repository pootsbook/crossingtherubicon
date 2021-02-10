---
layout: post
title: "Framework Abstractions: HTTP Routing in Rails"
---

In [Framework Abstractions: HTTP][fa1] I looked at how, in Ruby, the Rack library abstracts HTTP (and CGI) in a faithful manner, providing all of the necessary details in a `Rack::Request` object. Almost all Ruby web frameworks conform to the Rack specification and build their routing mechanisms on top of it.

A routing mechanism is essentially a mapper (router) that maps (routes) a given HTTP request to the corresponding and appropriate endpoint, controller or section of code that is designed to handle that particular request. It is the entry point for the transformation of an HTTP request into some or other action in, or response from, your web application.

I’d like to compare two contrasting approaches to routing mechanisms in two Ruby web frameworks: [Ruby on Rails][ror] and [Syro][syro]. Since routing is common to all web applications, this is prime territory for a framework to abstract away the lower-level details of the HTTP request so that we can start building at a higher level.

## Rails

Rails is by far and away the most popular web framework written in Ruby. It could even be argued that without Rails, Ruby would never have found its way into the mainstream. Rails has a built-in router that relies on a route definition file (`config/routes.rb`) which loads this set of routes into memory when the application is booted.

Rails also, as would be expected in a framework with a doctrine of convention over configuration, builds heavily on REST-ful routes or _resource-oriented routing_. That is, building loosely on Roy Fielding’s dissertation on REpresentational State Transfer as a means of using the stateless HTTP protocol to architect applications, Rails maps common HTTP actions to operations on a given resource, which normally takes the form of an entity stored in a database row whose table corresponds to that resource type.

In concrete terms, a resource could be a book and would have a database table to store different books. The standard create, read, update, delete actions (CRUD) can be executed in the database using SQL queries. The router then provides a way to map certain HTTP requests to these different actions in a predictable manner. The actions themselves in Rails are methods (called actions) in a given controller for that resource.

```
HTTP Verb   Route           Database  Controller
------------------------------------------------
POST        /books          Create    create
GET         /books/:id      Read      show
PUT/PATCH   /books/:id      Update    update
DELETE      /books/:id      Delete    destroy
```

On top of that Rails also provides routes for ancillary actions, only one of which has a corresponding operation involving the database:

```
HTTP Verb  Route            Database  Controller
------------------------------------------------
GET        /books/new       -         new
GET        /books/:id/edit  -         edit
GET        /books           Read      index
```

Now, all of that context is required to understand the core of Rails’s routing configuration. For a given resource, e.g. books, creating a mapping for each of these seven URLs is as easy as defining the following in the routes file:

```
resources :books
```

It can be modified to either exclude certain actions, or include only certain actions by passing some options:

```
resources :books, only: [:show, :create, :update, :destroy]
```

```
resources :books, except: [:new, :edit, :index]
```

Additional routes and actions can be defined on the resource by passing a block with the `member` or `collection` method:

```
resources :books do
  collection do
    get 'search'
  end

  member do
    delete 'avatar'
    options '…'
    patch '…'
    post '…'
    put '…'
  end
end
```

These allow adding additional routes to the resource using provided methods to HTTP verbs. For non-resource routes these HTTP verb methods can be used to create one-off routes and map them to a given controller and action:

```
get 'search', to: 'books#search'
delete 'avatar', to: 'books#destroy_avatar'
```

Or you can use the generic `match` method while specifying the HTTP verb(s):

```
match 'avatar', to: 'books#avatar', via: [:get, :delete]
```

The `to: 'books#avatar` syntax will map the given URL (`/avatar`) to the `BooksController` and `avatar` action. Here the mapping is explicit compared to `resources` implicit map.

I don’t have time to go into the details of `namespace`, `scope`, `constraints`, `controller`, `nested`, `shallow`, `direct`, `resolve` and `concern`. They are all additional methods designed to help you manage your routing. If you want you can go read the [guide][rout]. 

So in Rails, all of these different methods are tools for mapping the URL to a particular controller and a particular action in your application. It attempts to provide an easy and concise way to define where each HTTP Request and corresponding URL should go.

—*Wednesday 10th February 2021.*

[fa1]: https://www.crossingtheruby.com/2021/02/09/framework-abstractions-http.html
[ror]: https://rubyonrails.org
[syro]: http://soveran.github.io/syro/
[rout]: https://guides.rubyonrails.org/routing.html
