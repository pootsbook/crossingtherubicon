---
layout: post
title: "Form Formation: Hanami"
---

Hanami is a framework for developing web applications in Ruby by Luca Guidi. It is designed for simplicity and productivity. Without having used it too much in anger, it very much seems to be crafted with a lot of care and attention to detail.

Hanami includes a form builder with form helpers. This is what defining a simple form looks like:

```

<%=
  form_for :book, '/books' do
    div class: 'input' do
      label      :title
      text_field :title
    end

    div class: 'input' do
      label      :author
      text_field :author
    end

    div class: 'controls' do
      submit 'Create Book'
    end
  end
%>
```

I smiled when I saw this. If you squint, it looks like a mix of Rails and Hypertext. The syntax is based on Hanami’s own HTML5 generator. What’s exciting is that you can actually specify your form in the view class rather than the template:

```
module Web
  module Views
    module Books
      class New
        include Web::View

        def form
          form_for :book, routes.books_path do
            text_field :title

            submit 'Create'
          end
        end
      end
    end
  end
end
```

And then call it in the associated template with:

```
<%= form %>
```

I think I’ll spend a little bit of time diving into Hanami in more detail to examine how it handles the mapping of HTML to a “form object” and how it handles the form object itself.

—*Tuesday 8th June 2021.*

