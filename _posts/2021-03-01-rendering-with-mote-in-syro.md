---
layout: post
title: Templating with Mote in Syro
---

I have covered both the [Syro routing library][srl] and the [Mote templating library][mtl] in the past. In a previous post I demonstrated how one might integrate the [Scrivener validation library][svl] into Syro and glossed over the rendering aspect. In this post I’d like to demonstrate how I have integrated Syro with Mote for rendering templates.

One of the benefits of working with minimal libraries is that you have the freedom to put things together the way you want them. With Syro it’s conventional to use a Syro Deck (`Syro::Deck`) to provide a set of methods that can be mixed into the router for rendering purposes. (In fact a `Syro::Deck` is the context in which the router is executed.)

```
class Web < Syro::Deck
end
```

This deck can then be passed into the router as follows:

```
Syro.new(Web) do
end
```

Let’s use Mote to implement rendering in our router. It comes with some helpers that allow us to use a `mote` method. It takes a file name and a hash of parameters and returns the rendered version of the content.

```
class Web < Syro::Deck
  include Mote::Helpers
end
```

We can then write a render method which builds on the `mote` method:

```
class Web < Syro::Deck
  include Mote::Helpers
  
  def render(template, params = {})
    res.html mote("views/#{template}.mote", params)
  end
end
```

In this particular case we can keep our templates in the `views` directory and suffix them with the `.mote` file type. We’re also passing the rendered template to Syro’s `res.html` which defines an HTML response (setting the content-type to `text/html` and the response body to the output of the template).

To use it in a route we can then do the following:

```
Syro.new(Web) do
  on "contact" do
    get do
      contact_form = ContactForm.new({})
      render("contact", contact_form: contact_form)
    end
  end
end
```

This will look for the file `views/contact.mote` and it will pass `contact_form` as a local variable with the value assigned for use in the template.

That’s it for the basic case. Rendering for a web ‘framework’ in a one-line method. 

We’ll see how this can be expanded to include more involved elements like the provision of a layout in future posts.

—*Monday 1st March 2021.*

[srl]: https://www.crossingtheruby.com/2021/02/11/framework-abstractions-http-syro-routing.html
[mtl]: https://www.crossingtheruby.com/2021/02/20/ruby-templating-with-mote.html
[svl]: https://www.crossingtheruby.com/2021/02/28/integrating-scrivener-with-syro.html
