---
layout: post
title: "Form Formation: Sanity UI"
---

Yesterday I stumbled upon [Sanity’s declarative forms][sdf] which had been sitting right under my nose for a long time—so close that I hadn’t even noticed.

I mentioned that Sanity maps a field on a document to a React component when automatically rendering a form for that document in the Sanity Studio. The mapping is defined in the field’s data-type. The following is a field definition which appears in the `fields` property of a schema object.

```
{
  name: 'title',
  title: 'Title',
  type: 'string'
}
```

The type here is `string` and maps to an HTML text input. Components for the Studio are to be found in Sanity UI which in their words is a “composable, accessible, beautiful React component library”.
  
After diving in to see how everything is wired up I came across the `@sanity/form-builder` package. The `FormBuilder` component takes a schema (`schema`), a type from the schema (as `value`) and a function to handle any changes to values in the form (`onChange`) as props:

```
<FormBuilder
  schema={schema}
  value={currentValue}
  onChange={handleChange}
/>
```

That’s about as declarative and concise as you can get. 

Now reading JavaScript (or TypeScript as it is in this case) is not my favourite pastime, so forgive me if I gloss over some details in trying to sketch out the big picture. 

Based on the document type from the schema it goes through the fields, taking their type and rendering a component based on that type which contains the corresponding form control.

There’s all manner of complexity and jiggery-pokery thanks to this being on the front-end, written in JavaScript, and being such an ambitious generic abstraction with bells and whistles.

So let’s keep it simple, a field of type `string` will produce an HTML `input` form control of type `text`. If you want to render your own control, you can write your own component and then map a field to that component in your schema:

```
{
  name: 'title',
  title: 'Title',
  type: 'string',
  inputComponent: MyCustomString
}
```

The declaration and mapping is nice and tidy (which unfortunately I can never quite get myself to say about JavaScript code, including the implementation behind this mapping).

To be fair, there are a million and one things going on in Sanity that requires a heightened level of complexity and thus enabling the power of the platform.

The schema, mapping a data attribute to a form field, however, is the essence of simplicity and concision. And it bodes well in my own quest.

—*Thursday 13th May 2021.*

[sdf]: https://www.crossingtheruby.com/2021/05/12/form-formation-sanity-document-schemas.html
