---
layout: post
title: "Operations as Data: ExClubhouse"
---

My foray into HTTP with Elixir and HTTPoison arose when needing to patch the ExClubhouse HTTP client library for the Clubhouse API (v3). The precise details are in this GitHub [Pull Request][pr] and working with the machinery of ExClubhouse is what introduced me to the elegance of the [`%HTTPoison.Request{}` struct][hr].

The ExClubhouse library builds on the foundation of this declarative way of making HTTP requests. It essentially wraps the individual API calls into `%ExClubhouse.Operation{}`. You can immediately spot the similarities with the `HTTPoison.Request{}` struct:

```
%ExClubhouse.Operation{
  id: atom(),
  method: :delete | :get | :head | :options | :patch | :post | :put,
  path: binary(),
  params: map() | nil,
  headers: [any()] | nil,
  body: map() | tuple() | nil,
  options: [any()] | nil
}
```

It’s a simple layer on top of `HTTPoison.Request{}` and serves as a data structure to codify requests to individual API endpoints. Each endpoint in the API has a definition in the code similar to the following example, for fetching stories, using the `%ExClubhouse.Operation{}` as a base:

```
def get(story_public_id) do
  %ExClubhouse.Operation{
    id: :story_get,
    method: :get,
    path: "stories/#{story_public_id}"
  }
end
```

So the details of making a `GET` request to the stories endpoint on the Clubhouse API for a story with a given ID is captured in this `%ExClubhouse.Operation` data structure. This is later merged with the default request data to form the eventual HTTP request to be executed by the client.

Again, the operation to be executed is being separated from the execution itself. Although it makes for a more verbose library, the code is crystal clear due to the transparency of this approach. Everything is explicit. Even as a relative newcomer to Elixir I was able to fix a bug quickly and easily.

—*Tuesday 19th January 2021.*

[pr]: https://github.com/humberaquino/exclubhouse/pull/6
[hr]: https://www.crossingtheruby.com/2021/01/18/operations-as-data-httpoison.html
