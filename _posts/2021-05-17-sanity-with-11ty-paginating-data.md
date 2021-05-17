---
layout: post
title: "Sanity with 11ty: Paginating Data"
---

Yesterday I explored how to pull down a set of documents stored in [Sanity into Eleventy][sie] (11ty) using 11ty’s remote data capabilities in JavaScript data files.

If you provide an asynchronous function, 11ty can treat remote data identically to static, local data. 11ty automatically exposes all data from data files to templates in the local context. 

For a file `_data/posts.js` you can access the post data in the (Nunjucks) templates with the variable `posts`.

```
{% raw %}
<ul>
  {% for post in posts %}
    <li>
      {{ post.title }}
    </li>
  {% endfor %}
</ul>
{% endraw %}
```

The above would be one way to create an “index” or archive page for all blog posts. To create one page in the static site per post in the data collection from Sanity then you need to use a key built-in feature from 11ty, [pagination][page].

Pagination entails defining the `pagination` key in the YAML front matter so that 11ty can use it to generate multiple pages from a collection, in this case our posts data.

```
{% raw %}
---
pagination:
  data: posts
  size: 1
  alias: post
permalink: "/blog/{{post.slug.current}}/"
---

<h1>
  {{ post.title }}
</h1>
{% endraw %}
```

That’s pretty much it. This will generate a page for each post contained in my posts array which has been pulled down from Sanity in the `_data/posts.js` data file.

- `pagination.data` does the obvious thing, referencing the data that will be used to paginate.
- `pagination.size` defines how many elements from the collection should be present on on “page”. A value of `1` equals one page per item, whereas a value of `10` would lead to ten elements being available to the template each time (e.g. for a paginated blog archive).
- `pagination.alias` allows us to redefine how we reference the item(s) that are passed to the template. By default this is `pagination.items` which in our case would be a collection of one item (thus `pagination.items[0]`). As well as renaming the reference, `alias` also transforms a collection of one into a single value which is a nice touch.

This is the skeleton that provides a simple blog with posts written in Sanity and rendered in a static site. The common approach is to set up a webhook in Sanity that triggers a build on Netlify after content has been published which then rebuilds the site and deploys.

—*Monday 17th May 2021.*

[sie]: https://www.crossingtheruby.com/2021/05/16/sanity-with-eleventy.html
[page]: https://www.11ty.dev/docs/pagination/#paginate-a-global-or-local-data-file
