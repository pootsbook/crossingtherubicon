---
layout: post
title: "Form Formation: Formex"
---

[Formex][fex] is a form library for Elixir and Phoenix. The tendency in the Elixir community to settle on one accepted solution makes this a rare departure from the norm. I touched on the conventional method in my post about [Phoenix forms][pf].

Formex is interesting because it draws its inspiration from PHP’s Symfony framework form handling. This in turn looks a lot like Django’s declarative approach. I haven’t been able to trace any connection except that Django was released a few months before Symfony.

It thus not only represents an alternative with the same conceptual underpinnings, like in the Rails community, but a truly alternative design.

It does however build it’s HTML generation on the basis of the `Phoenix.HTML.Form` module.

Similar to other modules I have looked at in the past on this blog under the broad theme [operations as data][oad] the Formex library revolves around a `Form` struct. This is a data structure which captures all of the data surrounding a form, with the “form object” of course, but also with details incidental to the Formex implementation.

This is supplemented with a form builder (similar to Rails, but more explicit and declarative) which, with the functions available, builds up the form struct incrementally. This is a common pattern in Elixir and is similar to [Ecto.Query and Ecto.Changeset][ecto].

This is what it looks like in practice, building a form for an `Article` struct:

```
defmodule App.Article
  defstruct [:title, :body]
end

defmodule App.ArticleType
  use Formex.Type
  
  def build_form(form) do
    form
    |> add(:title, :text_input, label: "Title")
    |> add(:body, :textarea, label: "Content", validation: [presence: true])
  end
end
```

You can then create a form in the controller:

```
def new(conn, _params) do
  form = create_form(App.ArticleType, %App.Article{})
  render(conn, "form.html", form: form)
end
```

And having exposed it to your view and template, render it as follows:

```
<%= formex_form_for @form, article_path(@conn, :create), fn f -> %>
  <%= formex_rows f %>
<% end %>
```

It is a nice and concise declaration and the form itself, including the various fields, is built up in a manner consistent with Elixir conventions.

There’s not much to choose between this and the [conventional Phoenix method][pf] on the surface. However, in this example the form is built up as a data structure outside of the view in your code and simply rendered in the view. But if you squint, the `build_form` function doesn’t look that different from the corresponding code in a template.

If anything this is something that blurs the lines a little. It is an imperative and a declarative approach to roughly the same thing: arriving at something that can be output to HTML. The mapping from attribute to control is almost identical to the conventional approach since the Formex approach is still using functions from `Phoenix.HTML.Form`. There are no widgets or components here.

—*Saturday 22nd May 2021.*

[fex]: https://github.com/jakub-zawislak/formex
[pf]: https://www.crossingtheruby.com/2021/05/20/form-formation-phoenix.html
[oad]: https://www.crossingtheruby.com/2021/01/17/operations-as-data.html
[ecto]: https://www.crossingtheruby.com/2021/01/15/elixir-ecto-pure-impure-separation.html
