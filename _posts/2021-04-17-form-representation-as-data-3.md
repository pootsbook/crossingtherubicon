---
layout: post
title: "Form Representation as Data: select"
---

In the last [couple][frd1] of [posts][frd2] I have been experimenting with ways to represent a form as data with the goal of rendering it optimally in Hypertext DSL.

I have covered `form`, `label`, `input`, and `textarea` alongside a dynamic and generic approach to rendering the form field elements.

The next element will test all of the invention listed above and stretch me out of my comfort zone. Now this journey is a little haphazard. I didn’t arrive with a plan but simply got stuck in. If I were to be more circumspect then I would probably have listed out the properties of all of the form field elements and sought to find a solution that fits them all. Now I’m going step by step.

The benefit of the HTML standard is that we can know everything up front because it is all well defined. This approach then looks more like product development in the wild, where the act of development is also an act of discovery, and where new knowledge sometimes invalidates the approaches taken until that point in time.

I’m going to look at the `select` tag. This is different because the tag has children; not just text but actual tags within it.

```
<label for="enquiry_field">
  And what might be the nature of your enquiry?
</label>

<select name="enquiry" id="enquiry_field">
  <option value="uninterested">
    I’m just having a look around.
  </option>
  <option value="interested">
    I’d really like to buy something.
  </option>
</select>
```

This blows a lot of our thinking out of the water. Up until now we have had two general cases: self-closing tags and tags containing a text node. Now we must add a third into the mix, a tag containing tags, containing text nodes.

Really this is a special case of a form (something containing fields) and a tag containing a text node (the option list) so maybe there is a way we can piggyback on top of what we’ve already got.

I also think that this then represents the full range of what is involved with form fields, there are no further surprises (I hope). If we get this right then including other field types should not be such a chore.

How would we then represent a select? Let’s start with the option since it closely resembles what we have already seen and work upwards from there.

```
field = {
  option: {
    value: "interested",
    text: "I’d really like to buy something"
  }
}
```

This is nothing new, it’s the same as what we’ve been dealing with all along. Now the other fields had many more attributes because we were generating them from the Scrivener object attribute definitions. The options don’t have a direct correspondence to data on the Scrivener object. In some cases they might do, if the list is dynamic, but we’ll assume static for now, so they will have to have a source, defined by us in the Scrivener class, similar to the `tag_mapping` and `type_mapping` fields. This will return an array of hashes bearing a similarity to the example above.

```
def enquiry_options
  [
    {
      "option" => {
        value: "uninterested",
        text: "I’m just having a look around."
      }
    },
    {
      "option" => {
        value: "interested",
        text: "I’d really like to buy something"
    }
  ]
end
```

The `select` then needs at least an extra attribute to accommodate this list:

```
field = {
  label: {
    text: "And what might be the nature of your enquiry?",
    for: "enquiry_field"
  },
  select: {
    name: "enquiry",
    id: "enquiry_field",
    value: "",
    options: [
      … # the return value of enquiry_options 
    ]
  }
}
```

We can then modify our Scrivener class to introduce this attribute and modify the `form` method to ensure that the options are included with the select.

```diff
  class RegistrationForm < Scrivener
    attr_accessor :name
    attr_accessor :email
    attr_accessor :message
+   attr_accessor :enquiry
    …
    def form
      {
        action: "/contact",
        method: "post",
        errors: errors,
        fields: attributes.keys.map do |attr|
          {
            "label" => {
              for: "#{attr}_field",
              text: attr.capitalize.to_s 
            },
            tag_mapping[attr] => {
              type: type_mapping[attr],
              name: attr,
              id: "#{attr}_field",
              value: send(attr),
              attribute: attr
            }.tap do |f|
              f[:text] = send(attr) if tag_mapping[attr] == "textarea"
+             f[:options] = send("#{attr}_options") if tag_mapping[attr] == "select"
            end
          }
        end.push({
          "input" => {
            type: :submit,
            value: "send"
          }
        })
      }
    end

+   def enquiry_options
+     [
+       {
+         "option" => {
+           value: "uninterested",
+           text: "I’m just having a look around."
+         }
+       },
+       {
+         "option" => {
+           value: "interested",
+           text: "I’d really like to buy something"
+         }
+       }
+     ]
+   end 
  end
```

We now need to revisit the `Template#render_field` method to incorporate a `select` and it’s `option`s into the field rendering. This is what it currently looks like:

```
def render_field(f)
  f.each do |tag, att|
    if att[:text]
      public_send tag, att.slice(*ATTRS.fetch(tag)) do
        text att[:text]
      end
    else
      public_send tag, att.slice(*ATTRS.fetch(tag))
    end
  end
end
```

A `select` will likely require the introduction of another branch. First we’ll clean up what we had.

```diff
  def render_field(f)
    f.each do |tag, att|
-     if att[:text]
+     if att.has_key? :text
        public_send tag, att.slice(*ATTRS.fetch(tag)) do
          text att[:text]
        end
      else
        public_send tag, att.slice(*ATTRS.fetch(tag))
      end
    end
  end
```

This represents a slight improvement on the original with the introduction of `Hash#has_key?` which will ensure that cases where the `text` attribute is `nil` does not erroneously generate a self-closing tag.)

Now we’ll add a branch for `select`.

```diff
  def render_field(f)
    f.each do |tag, att|
      if att.has_key? :text
        public_send tag, att.slice(*ATTRS.fetch(tag)) do
          text att[:text]
        end
+     elsif att.has_key? :options
+       ?
      else
        public_send tag, att.slice(*ATTRS.fetch(tag))
      end
    end
  end
```

This is where it gets interesting. Remember I was keen to leverage the machinery that we already had? If we squint then a `form` data structure similar to a `select` data structure in that they both have children (`form[:fields]`, `select[:options]`).

So we can do some form of recursion to get the `option`s rendered. Let’s give that a go:

```diff
  def render_field(f)
    f.each do |tag, att|
      if att.has_key? :text
        public_send tag, att.slice(*ATTRS.fetch(tag)) do
          text att[:text]
        end
      elsif att.has_key? :options
-       ?
+       public_send tag, att.slice(*ATTRS.fetch(tag)) do
+         att[:options].each do |option|
+           render_field(option)
+         end
+       end
      else
        public_send tag, att.slice(*ATTRS.fetch(tag))
      end
    end
  end
```

There’s one more thing to tie up and that is the mapping of a Scrivener attribute to an HTML tag. We’ll update `tag_mapping`.

```diff
  def tag_mapping
    {
      name: "input",
      email: "input",
-     message: "textarea"
+     message: "textarea",
+     enquiry: "select"
    }
  end
```

Should we now try to render the form from this Scrivener object it… breaks. We forgot to add a whitelist of attributes for a `select` to the `Template::ATTRS` constant. Let’s remedy that, and while we’re at it, do the same for `option`.

```
ATTRS = {
  "input" => %i(type name id value),
  "label" => %i(for),
  "textarea" => %i(id name rows cols),
  "select" => %i(id name),
  "option" => %i(value)
}
```

Remember we’re just relying on the bare minimum for now. We can update these lists to match the HTML5 specification at a later date.

And would you believe it, if we run the script now, save for a little bit of jiggery pokery with string and symbol keys in our field definitions, we now have the full form rendered with our select and our options.

```
<form action="/contact" method="post">
  <label for="email_field">
    Email
  </label>
  <input type="email" name="email" id="email_field" value="" />
  <label for="name_field">
    Name
  </label>
  <input type="text" name="name" id="name_field" value="" />
  <label for="message_field">
    Message
  </label>
  <textarea id="message_field" name="message">
    Hello, World!
  </textarea>
  <label for="enquiry_field">
    Enquiry
  </label>
  <select id="enquiry_field" name="enquiry">
    <option value="uninterested">
      I’m just having a look around.
    </option>
    <option value="interested">
      I’d really like to buy something
    </option>
  </select>
  <input type="submit" value="send" />
</form>
```

We’re getting there. It’s a little slow and laborious, but we’re getting there. Moving in the right direction.

At this stage we could tidy this up and confirm whether or not we have indeed catered to all types of form ‘fields’ in HTML. We could add definitive lists of valid attributes. Another thing that strikes me when I view the [full file (Gist)][ffg] is that a lot of this doesn’t feel like it belongs in a Scrivener object any more. Or maybe it does. There are a few routes there too: like creating a new “form” object wrapping the Scrivener object and delegating when necessary; or working ‘above’ the Scrivener level and dynamically generating the Scrivener object as a by-product of a higher level data definition which includes both attributes and their desired representation in an HTML form; and there may yet be others.

All interesting avenues for discovery that I’m leaving for another day.

—*Saturday 17th April 2021*.

[frd1]: https://www.crossingtheruby.com/2021/04/15/form-representation-as-data.html
[frd2]: https://www.crossingtheruby.com/2021/04/16/form-representation-as-data-2.html
[ffg]: https://gist.github.com/pootsbook/cb02dce23509ec4044e43095fb5f6806
