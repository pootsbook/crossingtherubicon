---
layout: post
title: "Form Formation: Django vs. Rails (2)"
---

Yesterday I wrote about [Tom Dalling][td]’s observations comparing [forms in Django vs. Rails][fdr]. Today I’d like to look at the reflections of Joshua Jarvis who moved from Rails to Django sharing his [initial thoughts][rtd].

Mr. Jarvis’ article is a general article about the whole framework rather than being specifically about forms but forms do make an appearance.

The first observation is that the template helpers that Django provides are most welcome, and that as I have noted, rendering a form with one helper method is a boon.

> Django includes a myriad of built in template helpers that make it easy to iterate through objects, format data, and even **render complete forms**.

Of course this is possible because by default the forms are generic:

> Django forms are generic by default and can be injected into Django templates via a Django view.

There is some code showing the creation of such a form, along with some customisation (the specific fields to be included):

```
# Django mysite/blog/form.py
class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ('title', 'text',)
```

Then in the view (equivalent to a Rail’s controller) the form is instantiated and passed to the view:

```
# mysite/blog/views.py

def post_edit(request, pk):
    post = get_object_or_404(Post, pk=pk)
    if request.method == "POST":
      …
    else:
        form = PostForm(instance=post)
    return render(request, 'blog/post_edit.html', {'form': form}
```

Where Rails passes a model object, and then builds the form on the basis of a combination of model object and explicit template helpers per field, Django builds a form object with data not only about the model but also about what should appear in the form.

This generic approach allows for customisation but is not as flexible as the Rails approach, especially with respect to HTML.

> Injecting class names for styling purposes is a little tricky and involves customization of the `form.py` file in ways that I think over complicates forms. Rails, in contrast, makes the building blocks of the form and it’s controls available as simple template helper functions. Integrating helpers with standard HTML code is very easy to do in Rails.

It seems that choosing to keep the configuration of a form at the “application” level instead of the view/template level muddies the waters a little. It points out the space that forms inhabit as the connecting piece for model interaction in templates/views. It is in a sense the puzzle piece that has interlocking concerns in a two-way flow. 

Django grants that a deliberate and unique layer in the framework, where Rails punts the concerns to the view. I’m coming from the Rails side hypothesising that there should be more, a declarative layer, while here the presence of such a layer is seen as a drawback when it comes to customising the form HTML itself.

It’s an interesting perspective. What is ironic is the conclusion in the post about the frameworks as a whole:

> I think Rails will always feel more elegant and productive when you choose to work within its strict and well documented best practices. Django is literal representation code and is probably more approachable for developers looking for a little more flexibility.

With the framework as a whole Django generally offers more flexibility while Rails is stricter. With forms on the other hand Django is the one that is strict while Rails gives you all the flexibility.

—*Wednesday 26th May 2021.*

[td]: https://twitter.com/tom_dalling
[fdr]: https://www.crossingtheruby.com/2021/05/25/form-formation-django-vs-rails.html
[rtd]: https://www.refactorednoise.io/2020/12/12/from-ruby-on-rails-to-django.html
