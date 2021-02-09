---
layout: post
title: "Framework Abstractions: HTTP"
---

In [Framework Abstractions][fa] I observed that while giving you a good head start, frameworks can stand in the way of understanding the things that they abstract. In this post I’d like to look at the HTTP 1.1 protocol.

Now you probably won’t go as far as I did. Given my aversion to reading long documents on screen I actually typeset a version of the HTTP 1.1 specification and [made it available via Lulu][lulu] at cost price. I also think I might be the only person that ordered a copy.

You don’t need to read the spec, the basics are actually very simple. Now I didn’t know this until around six to seven years into my career. If I look back then I think, how on earth could I have survived (and thrived) as a web developer without this most basic of knowledge? The answer of course is that the framework dealt with it for me.

But there comes a time when you have to dive deeper into how things work in order to fix strange bugs or accomplish more ambitious tasks. And when that happens, the framework can get in the way of a true understanding. You have not only got to learn the basics of HTTP but you also have to understand the framework’s abstraction for HTTP. Here we get into the territory of ‘good’ and ‘bad’ abstractions.

I grant that distinguishing between good and bad is largely a subjective exercise and relies heavily on your point of view. Perhaps it is more useful to use the term faithful. I view a good abstraction to be one that is faithful to the underlying shape of what is being abstracted; that there is little to no cognitive dissonance between the library or framework and what it is abstracting.

The opposing viewpoint is that a good abstraction is one that means you don’t have to understand what is being abstracted. The point being that if you had to understand the underlying technology then a wrapper ceases to be an abstraction. A good abstraction is therefore a shield.

Although the point of this post is not to convince you that faithful abstractions are better, one key benefit is that you become acquainted with the soil in which you plant your application. That you’re working with the raw materials for your construction, and that you can achieve a greater level of mechanical sympathy—you know how the engine works.

So what is being abstracted away from us? This is the raw format of an HTTP 1.1 GET request:

```
GET /?q=http HTTP/1.1
Host: duckduckgo.com
```

This makes a request to the path `/` with the query string `?q=http` on the host `duckduckgo.com` via a `GET` request using `HTTP 1.1`. In tabular form:

```
Name            Example
----------------------------------------------
Method       *  GET
Host         *  duckduckgo.com
Path         *  /
HTTP version *  HTTP/1.1
Query String    ?q=http
Headers         Content-Type: application/json
Body            {"q": "http"}
```

The Method, Host, Path and HTTP version are required while the Query String, Headers and Body are optional. Our example request contained a query string, but no headers or body. In the table above I have shared an example of an HTTP header and a body. A request containing those would look like this:

```
GET / HTTP/1.1
Host: duckduckgo.com
Content-Type: application/json

{"q": "http"}
```

Note that the body is separated from the ‘head’ of the document by a blank line. Headers live in the head of the request.

As a developer you will likely never have to deal with the request at such a low level unless you are implementing your own webserver. There are libraries to abstract this away. In Ruby, similar to Python’s [WSGI][wsgi], Clojure’s [Ring][ring] or Elixir’s [Plug][plug], Ruby has [Rack][rack]. So these are technically not wrappers for HTTP directly, but for CGI, the Common Gateway Interface. This is an accepted [standard][cgi] for web servers designed to be able to interpret and translate requests for static documents into a form that web _applications_ can respond to. This is what kicked off dynamic websites. It’s like a gateway that hands over relevant details of the request so that an application written in a programming language like PHP, Python, Ruby etc. can handle the request and serve up a response while keeping the document semantics of HTTP.

So CGI is in a sense a superset of HTTP, and hands over all of the details of the HTTP request to a web application. Rack wraps all of this information in a hash/dictionary called the _environment_ or just ‘env’, mimicking the CGI environment. It includes server details over and above the raw HTTP request but the HTTP-related details can be found in `PATH_INFO`, `QUERY_STRING` and anything beginning with the prefix `HTTP_`. This is what an example env (in Rack) might look like:

```
{
 "SCRIPT_NAME"=>"",
 "QUERY_STRING"=>"q=http",
 "SERVER_PROTOCOL"=>"HTTP/1.1",
 "SERVER_SOFTWARE"=>"puma 5.1.1 At Your Service",
 "GATEWAY_INTERFACE"=>"CGI/1.2",
 "REQUEST_METHOD"=>"GET",                           
 "REQUEST_PATH"=>"/search",                         
 "REQUEST_URI"=>"/search?q=http",                   
 "HTTP_VERSION"=>"HTTP/1.1",                        
 "HTTP_HOST"=>"localhost:9292",                     
 "HTTP_USER_AGENT"=>"curl/7.64.1",                  
 "HTTP_ACCEPT"=>"*/*",                              
 "SERVER_NAME"=>"localhost",
 "SERVER_PORT"=>"9292",
 "PATH_INFO"=>"/search",                            
 "REMOTE_ADDR"=>"::1",
 "puma.socket"=>#<TCPSocket:fd 16, AF_INET6, ::1, 9292>,
 "rack.input"=> …,
 …
}
```

I’ve redacted some of the noise out but you can see the `HTTP_` prefixed data alongside `PATH_INFO` and `QUERY_STRING` and useful additions with the `REQUEST_` and `SERVER_` prefixed variables.

Rack then wraps this environment in a `Rack::Request` object that provides a number of convenience methods to access and inspect this data. For example `Rack::Request#request_method` will return the value of the `REQUEST_METHOD` key from the hash. `Rack::Request#get?` will return a boolean if the request method is `GET`. 

In this way Rack acts as the intermediary between the web server and a Ruby web application written in a framework like Rails, Hanami or Syro. Their request handling logic uses the primitives that Rack provides with `Rack::Request` and `Rack::Response`. 

Rack is an example for me of a faithful abstraction and one that brings many benefits. Not least that Ruby web application frameworks don’t have to deal with the nitty-gritty details of integrating with different web servers instead relying on Rack to abstract away that complexity and provide a common interface. Rack is an adapter which allows you to plug any web server into any web framework.

Next I’d like to explore how different Ruby web frameworks build on Rack and HTTP in presenting framework primitives for you to handle HTTP requests and route them to your application handling logic.

—*Tuesday 9th February 2021.*

[fa]: https://www.crossingtheruby.com/2021/02/08/framework-abstractions.html
[lulu]: https://www.lulu.com/en/gb/shop/t-berners-lee-and-p-leach-and-l-masinter-and-h-frystyk/hypertext-transfer-protocol-http11/paperback/product-1gvw8456.html
[wsgi]: https://wsgi.readthedocs.io/en/latest/
[ring]: https://github.com/ring-clojure/ring
[plug]: https://github.com/elixir-plug/plug
[rack]: https://github.com/rack/rack
[cgi]: https://tools.ietf.org/html/rfc3875
