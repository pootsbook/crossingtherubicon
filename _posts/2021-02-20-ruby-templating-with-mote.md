---
layout: post
title: Ruby Templating with Mote
---

[Mote][mote] is another delightfully simple and succinct library from [Michel Martens][mm], the author of Syro and Seg. Mote is a templating library in Ruby for **m**inimal, **o**perational **te**mplates. I often use it when building applications with Syro and have also integrated it with Rails for email templating in the past.

It is very simple and fast. So simple in fact that comes in at 58 significant lines of code. Fast, well, because it’s running more of your code than its own. It uses some interesting tricks to arrive at such concision.

The main principle, like most templating engines, is the parsing of a source file, determining certain terms that should be evaluated, and then evaluating them in a given context (provided by you) with some parameters (also provided by you). This is an example of a `mote` function from one of my Syro decks:

```
def mote(file, params = {}, context = self)
  template = File.read(file)
  Mote.parse(template, context, params.keys, file).call(params)
end
```

What Mote is doing under the hood is transforming the template into executable Ruby. Essentially taking a string template and making it code. This involves a reversal of normal string content to a Ruby string and transforming the strings indicated for dynamic evaluation to Ruby. These are then bundled together and evaluated using `instance_eval` which evaluates a string containing Ruby source code within the context of the receiver, in this case, the provided context. It’s mind-bogglingly simple.

It uses three markers or delimiters for indicating dynamic content:


```
% …
<? … ?>
{% raw %}{{ … }}{% endraw %}
```

Anything on a line prefixed by `%` will be evaluated as Ruby code, anything between a `<?` `?>` pair will be evaluated as Ruby code, and anything between a `{% raw %}{{` `}}`{% endraw %} pair will get evaluated as Ruby code _and_ printed to the template. Anything else in the template gets printed as is.

So taking these three types of block in a template like this:

```
% if true
  True dat.
% end

<? 
  sum = 7 + 8
?>

Sum: {% raw %}{{ sum }}{% endraw %}
```

It will be transformed into a Proc containing the string of _code_ to be evaluated which looks like this:

```
Proc.new do |params, __o| params ||= {}; __o ||= '';__o << \"\\n\"\nif true\n__o << \"  True dat.\\n\"\nend\n__o << \"\\n\"\nsum = 7 + 8\n__o << \"\\n\\nSum: \"\n__o << ( sum ).to_s\n__o << \"\\n\"\n__o; end
```

To make it a little more readable, I’ve formatted the construction of the `__o` variable which accumulates the output (here, the Ruby string to be executed):

```
__o ||= '';
__o << \"\\n\"\nif true\n
__o << \"  True dat.\\n\"\nend\n
__o << \"\\n\"\nsum = 7 + 8\n
__o << \"\\n\\nSum: \"\n
__o << ( sum ).to_s\n
__o << \"\\n\"\n
__o;
```

When executed it will look something like this:

```
if true
"  True dat.\n"
end
"\n"
sum = 7 + 8
"\n\nSum: "
( sum ).to_s
"\n"
```

As you can see this is largely the inverse of the template we wrote. Static strings from the template are now Ruby strings, and dynamic strings from the template are now Ruby code.

When the Proc gets evaluated, it spits out the final result, complete with the results of the evaluation:

```

  True dat.



Sum: 15
```

That’s it. Simple, powerful, minimal, operational.

—*Saturday 20th February 2021.*

[mote]: https://github.com/soveran/mote
[mm]: https://soveran.com/about.html
