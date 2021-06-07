---
layout: post
title: "Form Formation: Notes—Encoding"
---

The encoding of the presentation element of a form, the HTML, is perhaps the least controversial part of the puzzle. As Mr. Martens [notes][mmn]:

> it does not matter where the directives are encoded.

The directives being the transformation of a given set of data or encoding into the HTML itself.

This is done largely on an ad hoc basis in most frameworks. The patterns are imperative, loosely abstracted on the basis of presentation concerns. Simple Form can be configured to producing Bootstrap flavoured HTML.

Some libraries like Formtastic also go a step further and automate the whole field generation based on the “contract” of different data provided: value, validation error(s), hint, label, placeholder, etc.

This leads me to believe that perhaps a sensible approach would be to focus on a clear field definition which carries an HTML representation, so that the definition is taken out of the HTML and into the application logic.

The mapping of a field definition to a form object’s attributes would be a separate step. Or we should shift the “bundling” of concerns away from the model and towards to field representation. That is, combine the filter and the HTML representation given their tight interconnectedness.

I feel like it’s almost time to start looking at concrete examples to weigh up the trade-offs involved.

—*Monday 7th June 2021.*

[mmn]: https://www.crossingtheruby.com/2021/06/04/form-formation-notes.html
