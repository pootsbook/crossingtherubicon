---
layout: post
title: "Operations as Data: Ruby HTTP"
---

I was revisiting a small Ruby HTTP client wrapper that I wrote a while ago when my curiosity got the better of me and I attempted to see what it might look like with [operations as data][oad].

The wrapper itself came as a result of a propensity to try and visit problems at a more fundamental level instead of building upon layer and layer of abstraction at the library level. That propensity was borne out of my experiences with functional programming in Elm and the dawning realisation that much of what I learned there could be [applied back to Ruby][abtr].

So instead of opting for one of the more popular high-level Ruby HTTP clients I instead wrote a small wrapper around `net/http`, the client that comes as part of the Ruby standard library. And as far as caveats go, the use-case was relatively simple: making standard requests to a third-party REST-ful API.

Here is the client in all of 48 lines, implementing `GET`, `POST`, `PUT` and `DELETE` HTTP methods:

```ruby
require "net/http"

class HTTPClient
  def initialize(host, path_prefix, default_params)
    @host = host
    @path_prefix = path_prefix
    @default_params = default_params
  end

  def get(path, query_params)
    Net::HTTP.get(uri(path, query_params))
  end

  def post(path, query_params, body)
    Net::HTTP.post(
      uri(path, query_params),
      body.to_json,
      "Content-Type" => "application/json"
    ).body
  end

  def put(path, query_params, body)
    uri = uri(path, query_params)
    req = Net::HTTP::Put.new(uri, "Content-Type" => "application/json")
    req.body = body.to_json
    Net::HTTP.start(uri.host, uri.port, use_ssl: true) do |http|
      http.request req
    end.body
  end

  def delete(path, query_params={})
    uri = uri(path, query_params)
    req = Net::HTTP::Delete.new(uri, {})
    Net::HTTP.start(uri.host, uri.port, use_ssl: true) do |http|
      http.request req
    end.body
  end

  def uri(path, query_params = {})
    query_params.merge!(@default_params)

    URI::HTTPS.build({
      host: @host,
      path: path.prepend(@path_prefix),
      query: URI.encode_www_form(query_params)
    })
  end
end
```

Here’s how I would have instantiated the client:

```
HTTPClient.new("api.pipedrive.com", "/v1", {"api_token" => "<SECRET…>"})
```

And here is an example of how I used it to work against a third-party API in an API wrapper which takes the HTTP client above as a parameter on initialization:

```
class Pipedrive
  def initialize(client)
    @client = client
  end
  … 
  def create_organization(attrs)
    jd client.post("/organizations", {}, attrs)
  end
  
  def delete_organization(id)
    jd client.delete("/organizations/#{id}")
  end
  
  def update_organization(attrs)
    id = attrs.delete("id")
    jd client.put("/organizations/#{id}", {}, attrs)
  end

  def find_organization(id)
    jd client.get("/oganizations/#{id}")
  end
  
  private
  
  attr_reader :client
  
  def jd(body)
    response = JSON.parse(body)
    return nil unless respons["success"]
    response["data"]
  end
end
```

I was fairly happy with this abstraction. Most of the HTTP details are hidden away in the client, the body parsing is a helper method, the  methods take the most limited number of arguments necessary for the request, and there is a nice one-to-one mapping of a function in the API wrapper to an HTTP request.

Now, having seen how Elixir extracts operations as data in its modelling of an HTTP request, I wanted to give it a go in Ruby and compare the two approaches.

I came up with the following implementation, a thin wrapper around `net/http` as before, but with all the request data lifted out into a Ruby `Struct`. 

```ruby
# lib/http_client/request.rb
require "uri"

module HTTPClient
  Request = Struct.new(
    :body,
    :headers,
    :host,
    :method,
    :path,
    :port,
    :query,
    :scheme,
    keyword_init: true) do

    def uri
      scheme == :https ? https_uri : http_uri
    end

    def https_uri
      URI::HTTPS.build(uri_params)
    end

    def http_uri
      URI::HTTP.build(uri_params)
    end

    def uri_params
      params = {
        host: host,
        path: path,
        port: port
      }.tap do |p|
        p.merge!({
          query: URI.encode_www_form(query)
        }) if query
      end
    end
  end
end
```

I have one convenience on this struct which is the generation of the URI from its constituent parts. Apart from that it is as identical a data structure to [`Finch.Request`][fr] as a Ruby struct and an Elixir struct can be.

Then there is the client itself:

```ruby
# lib/http_client.rb
require "net/http"
require_relative "./http_client/request"

module HTTPClient
  def self.request(req)
    case req.method
    when :get
      get(req)
    when :post
      post(req)
    when :put
      put(req)
    when :delete
      delete(req)
    else
      puts "nonsense"
    end
  end

  def self.delete(req)
    request = Net::HTTP::Delete.new(req.uri, req.headers || {})
    process_request(req, request)
  end

  def self.get(req)
    Net::HTTP.get(req.uri)
  end

  def self.post(req)
    Net::HTTP.post(req.uri, req.body, req.headers)
  end

  def self.put(req)
    request = Net::HTTP::Put.new(req.uri, req.headers || {})
    request.body = req.body
    process_request(req, request)
  end

  def self.process_request(req, net_http_request)
    Net::HTTP.start(req.host, req.port, use_ssl: req.scheme == :https) do |http|
      http.request net_http_request
    end
  end
end
```

The client certainly feels tighter although that’s maybe down to moving the URI generation out to the `HTTPClient::Request` struct. The other element conspicuous by its absence is any sort of configuration. The client is a module, not a struct. As such there are no default values stored at this level.

This is intentional, by separating out all of the request data into the struct, any default settings can inhabit a layer between the struct and the client. The default settings can essentially be merged with the request just before it gets passed on to the client.

I think this is a good example of the neat separation of concerns that functional programming forces you into. It ends up being slightly more verbose, there is an extra step, but the steps themselves are more comprehensible and more closely suited to a particular responsibility. The `HTTPClient` module is not bothered with configuration concerns, neither is the request. 

Here’s what that extra layer could look like:

```ruby
def with_defaults(req)
  req.tap do |r|
    r.headers = r.headers.merge({"Content-Type" => "application/json"})
    r.query   = r.query.merge({"api_token" => "<SECRET…"})
    r.scheme  = :https
    r.host    = "api.pipedrive.com"
  end
end

HTTPClient.post(with_defaults(req))
```

Or better, we could attach the defaults before creating the request struct. If we stuck to hashes with the same keys then it is simply a matter of deep merging.

This could live in the API wrapper where it likely belongs. The API wrapper would therefore also look different:

```
class Pipedrive
  def initialize(req_params)
    @default_req_params = req_params
  end
  … 
  def create_organization(attrs)
    request {
      method: :post,
      path: "/organizations",
      body: attrs.to_json
    }
  end
  
  def delete_organization(id)
    request {
      method: :delete,
      path: "/organizations/#{id}"
    }
  end
  
  def update_organization(attrs)
    id = attrs.delete("id")
    request {
      method: :put,
      path: "/organizations/#{id}",
      body: attrs.to_json
    }
  end

  def find_organization(id)
    request {
      method: :get,
      path: "/organizations/#{id}"
    }
  end
  
  private
    
  def request(params)
    req = HTTPClient::Request.new(with_defaults(params))
    jd HTTPClient.request(req)
  end
  
  def with_defaults(params)
    params.deep_merge(@default_req_params, params)    
  end
  
  def jd(body)
    response = JSON.parse(body)
    return nil unless response["success"]
    response["data"]
  end
end
```

Although I was worried that having to call the module `HTTPClient::Request.new` each time would end up making the code a little ugly, the uniformity of the interface means we can put that all behind the scenes. What we are left with in each method is a neat and tidy, declarative specification of the HTTP request:

```ruby
request {
  method: :put,
  path: "/organizations/#{id}",
  body: attrs.to_json
}
```

And I must admit, I really like it. I’ve really surprised myself there. Tomorrow I’ll review it for you in more detail.

—*Tuesday 26th January 2021.*

[abtr]: https://www.youtube.com/watch?v=s5A4recPPC4
[oad]: https://www.crossingtheruby.com/2021/01/17/operations-as-data.html
[fr]: https://www.crossingtheruby.com/2021/01/21/operations-as-data-finch.html
