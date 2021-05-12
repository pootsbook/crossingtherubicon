---
layout: post
title: "Form Formation: Sanity Document Schemas"
---

Excuse this digression from the usual programming to discuss the headless CMS [Sanity][sanity]. Sanity itself would balk at the idea of being called a headless CMS, they are in fact a _unified content platform_. However, CMS is a category everyone understands, _headless_ CMS is just about coming into common parlance, and unified content platform is the future.

If you don’t inhabit the future then allow me to break it down: Sanity and their ilk will replace WordPress for building websites. Sanity is the missing piece of the [Jamstack][jam] puzzle, the content API which is as accessible to marketers (content people) as it is to developers.

It is a flexible and unconstrained back-end for your content. With your content in Sanity you can present the same elements of content in different ways. You are no longer tied to the traditional “page” model, but can combine different types of content together on various media quickly and easily. More quickly and more easily than you could with a traditional CMS.

There is a lot more to be said about this future, a future that I have been living in for seven years already, but today I want to focus on Sanity’s document schema definitions.

The reason that’s interesting is because off the back of your schema they are able to persist and query documents in the provided structure. More than that, they automatically create a form for you using the schema as a guide.

You could say that the schema acts as a form definition. I use Sanity regularly for iterating quickly on the [ClubCollect marketing site][cc]. I was adding a new document type today and it struck me that this form definition had been hiding in plain sight. I hadn’t associated this with my quest to build declarative forms.

But that’s what Sanity does. Build declarative forms.

This is a traditional schema file for a document, written in JavaScript:

```
export default {
  name: 'post',
  title: 'Post',
  type: 'document',
  fields: [
    {
      name: 'title',
      title: 'Title',
      type: 'string'
    },
    {
      name: 'body',
      title: 'Body',
      type: 'blockContent'
    }
  ]
}
```

It’s a definition for a `post` document which contains a `title` and a `body` field. The title has a type of `string` and the body has a content of `blockContent`.

This tells Sanity the structure of the document. In Sanity’s back-end a document of type `post` will be a JSON object like this:

```
{
  "_id": "drafts.578eae32-57a3-4162-b77c-074571c389bf",
  "_type": "post",
  "title": "Hello, World!",
  "body": [
    {
      "_type": "block",
      "_key": "18903bfebce6",
      "style": "normal",
      "markDefs": [],
      "children": [
        {
          "_type": "span",
          "_key": "08633769c5e8",
          "text": "This is a post.",
          "marks": []
        }
      ]
    }
  ],
  "_rev": "qhqjex-e65-lfs-nwq-prnrfild3",
  "_updatedAt": "2021-05-12T19:35:53.707Z"
}
```

All the properties with a name beginning with an underscore are internal Sanity fields, but you can clearly see the fields I defined, `title` and `body` with the corresponding content “Hello, World!” and “This is a post.”

But it does something more impressive. Without batting an eye-lid Sanity uses the schema to produce a form which looks like this:

![form with two fields](/assets/images/sanity-post-form.png)

The signal to noise ratio is impressive. We defined two fields with three properties each, `name`, `title` and `type`. `name` is the name of the property in the resulting document, `title` is a user-friendly term used as the _label_ on the form. `type` is the data-type from a predefined list of Sanity data-types which defines how the data will be stored and, for our purposes, what kind of form-control will appear on the form. 

Sanity is using React.js under the hood. Similar to Django, it maps a data-type to a particular form ‘widget’, a React component. Everything else is wired up for you, no need to lift a finger. Obviously it locks you in to a particular end result, although the Sanity ‘CMS’ is fully customizable if you know React.

If anything, this proves that given certain constraints, declarative forms are not only practical but highly desirable.

—*Wednesday 12th May 2021.*

[sanity]: https://www.sanity.io
[jam]: https://jamstack.org
[cc]: https://www.clubcollect.com/en/
