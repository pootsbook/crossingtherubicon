---
layout: post
title: "Sanity with 11ty"
---

I’ve mentioned that in some projects I use Sanity as a headless CMS. I normally hook this up to a static site generator, and my weapon of choice recently has been [Eleventy][11ty] (a.k.a. 11ty).

11ty fits this model very well as it natively handles the asynchronous fetching of remote resources in [JavaScript data files][jdf].

Sanity has an API for its content lake using a custom query language called GROQ, which stands for Graph-Relational Object Queries. It is in some ways similar to GraphQL and shares many of the same properties.

Making a request to the Sanity API to get content for a `post` document with fields, `title`, `slug`, `publishedAt` and `body` fields would look like this:

```
const query = `
    *[ _type == "post" && !(_id in path("drafts.**")) ]{
      title,
      slug { current },
      publishedAt,
      body
    } | order(publishedAt desc)
  `
```

This can be neatly divided up into a GROQ query’s three constituent parts for readability, a _filter_, a _projection_ and an _order_.

```
const filter = `*[ 
  _type == "post" && 
  !(_id in path("drafts.**")) 
]`

const projection = `{
  title,
  slug { current },
  publishedAt,
  body
}`

const order = `| order(publishedAt desc)`
```

A filter defines the search filters for the query; a projection defines the fields that should be returned, and an order defines the order the results should be in. These can then be concatenated before the query is executed against the Sanity API.

```
const query = [filter, projection, order].join(' ')
```

You can also use the official [`groq` package][groq] to classify the strings as GROQ query strings which I believe at this stage only offers you the convenience of syntax highlighting but promises much more.

```
const groq = require('groq')

const filter = groq`*[ 
  _type == "post" && 
  !(_id in path("drafts.**")) 
]`

…
```

To execute the query you need the official [Sanity client]:

```
const sanityClient = require('@sanity/client')
```

And then you need to configure it with your project, dataset and token.

```
const client = sanityClient({
  projectId: 'my-project-id',
  dataset: 'blog',
  token: 'sanity-auth-token'
})
```

Once that’s done you can execute your query with the asynchronous `fetch` function:

```
const posts = await client.fetch(query)
```

If you put that all together then you arrive at a JavaScript file `posts.js` that you can place in your 11ty `_data` folder.

```
const groq = require('groq')
const sanityClient = require('@sanity/client')

const client = sanityClient({
  projectId: 'my-project-id',
  dataset: 'blog',
  token: 'sanity-auth-token'
})

module.exports = async function() {
  const filter = groq`*[ 
    _type == "post" && 
    !(_id in path("drafts.**")) 
  ]`
  const projection = groq`{
    title,
    slug { current },
    publishedAt,
    body
  }`
  const order = groq`| order(publishedAt desc)`
  const query = [filter, projection, order].join(' ')
  
  return await client.fetch(query)
}
```

This data, which is fetched when building the site, is made available to 11ty templates. Tomorrow I’ll demonstrate how you can instruct 11ty to use this data for generating pages.

—*Sunday 16th May 2021.*

[11ty]: https://www.11ty.dev/
[jdf]: https://www.11ty.dev/docs/data-js/
[groq]: https://www.npmjs.com/package/groq
[sc]: https://www.npmjs.com/package/@sanity/client
