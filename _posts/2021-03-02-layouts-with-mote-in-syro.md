---
layout: post
title: Layouts with Mote in Syro
---

Now if you read the preceding post about [rendering with Mote in Syro][rms] then you might have spotted an uncanny resemblance to [rendering with Mote in Sew][rmse]. And that is perfectly justified because in reality there is no difference.

So if we want to include a layout (or partials) for our Syro views then we can follow the same lines.

Say we have a template like this:

```
<!doctype html>
  <html lang="en">
    <head>
    <meta charset="utf-8">
    <title>
      {% raw %}{{ title }}{% endraw %}
    </title>
  </head>
  <body>
    {% raw %}{{ content }}{% endraw %}
  </body>
</html>
```

We have `title` and `content` variables. The `title` could be set in the route and the `content` will be the output of a Syro view. We can rejig our deck to cater for the presence of the layout:

```
class Web < Syro::Deck
  include Mote::Helpers
  
  def render(template, params = {})
    res.html mote("views/layout.mote",
      title: params[:title],
      content: mote("views/#{template}.mote", params))
  end
end
```

As you can see, we’re just nesting calls to the `mote` helper method, once to render the view itself, and then passing the result of that render to the layout as the `content` variable.

If we wanted to render a partial inside a template we could simply call out to the `mote` helper directly, or wrap it up in another method as necessary. For example, if you wanted to reduce the repetition of the `views` directory in the calls to `mote` then we can extract that out:

```
class Web < Syro::Deck
  include Mote::Helpers
  
  def view(template)
    sprintf("views/%s.mote", template)
  end

  def partial(template, params = {})
    mote(view(template), params)
  end

  def render(template, params = {})
    res.html partial("layout",
      title: params[:title],
      content: partial(template, params))
  end
end
```

The above code is taken from one of my production applications and also aliases `mote` to `partial` for convention’s sake.

I really enjoy the freedom and flexibility this gives me. The code itself is simpler than the configuration I would otherwise need in a larger framework and I’m not locked into one particular way of doing things.

—*Tuesday 2nd March 2021.*

[rms]: https://www.crossingtheruby.com/2021/03/01/rendering-with-mote-in-syro.html
[rnse]: https://www.crossingtheruby.com/2021/02/22/rendering-with-mote-in-sew.html
