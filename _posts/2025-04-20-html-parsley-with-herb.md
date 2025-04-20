---
title: HTML Parsley with Herb
layout: post
---

Herb, the new HTML-aware ERB parser library by Marco Roth, parses ERB templates into an abstract syntax tree. The tree has two main branches:

1. HTML
2. Embedded Ruby

In the real world these are extricably (thanks Herb!) intertwined but for now I’d like to concentrate on the HTML tree structure that the Herb AST uses.

## Herb AST Node Types

The docs on Herb’s Parser are quite, _ahem_, sparse, so I had to do some introspection to arrive at a list of nodes in the tree:

```
require "herb"

Herb.version
#=> "herb gem v0.1.0, libprism v1.4.0, libherb v0.1.0 (Ruby C native extension)"

constants = Herb::AST.constants.map do |const|
  klass = Herb::AST.const_get(const)
  next unless klass.is_a?(Class)
  klass.name
end.compact.each do |klass|
  puts klass
end; constants.count
```

This produces a list of `Herb::AST::Node`s, which after a touch of light editing to order and group the twenty-eight nodes, looks like this:

```
Herb::AST::Node
Herb::AST::DocumentNode

Herb::AST::HTMLDoctypeNode
Herb::AST::HTMLCommentNode
Herb::AST::HTMLElementNode
Herb::AST::HTMLOpenTagNode
Herb::AST::HTMLAttributeNode
Herb::AST::HTMLAttributeNameNode
Herb::AST::HTMLAttributeValueNode
Herb::AST::LiteralNode
Herb::AST::HTMLTextNode
Herb::AST::HTMLCloseTagNode
Herb::AST::HTMLSelfCloseTagNode

Herb::AST::WhitespaceNode

Herb::AST::ERBContentNode
Herb::AST::ERBIfNode
Herb::AST::ERBUnlessNode
Herb::AST::ERBElseNode
Herb::AST::ERBBlockNode
Herb::AST::ERBCaseNode
Herb::AST::ERBWhenNode
Herb::AST::ERBForNode
Herb::AST::ERBWhileNode
Herb::AST::ERBUntilNode
Herb::AST::ERBBeginNode
Herb::AST::ERBRescueNode
Herb::AST::ERBEnsureNode
Herb::AST::ERBEndNode
```

The two groups are distinguished here, with the exception of the mutual parents (`DocumentNode`—parent (or root) of the syntax tree, `Node`—parent of the class hierarchy) and the shared `WhitespaceNode` rather appropriately surrounded by, em, whitespace.

## Parsing HTML

### `Herb::AST::DocumentNode`

The ever-present root.

```
Herb.parse("").value
=> 
@ DocumentNode (location: (1:0)-(1:0))
└── children: []
```

Contains an array of child nodes in `DocumentNode#children`.

### `Herb::AST::HTMLDoctypeNode`

The singular doctype declaration. Also note the `Herb::AST::LiteralNode`, a node representing some literal text (as distinct from HTML text) usually inside an HTML doctype, HTML comment, or HTML attribute value.

```
Herb.parse("<!doctype html>").value
=> 
@ DocumentNode (location: (1:0)-(1:15))
└── children: (1 item)
    └── @ HTMLDoctypeNode (location: (1:0)-(1:15))
        ├── tag_opening: "<!doctype" (location: (1:0)-(1:9))
        ├── children: (1 item)
        │   └── @ LiteralNode (location: (1:9)-(1:14))
        │       └── content: " html"
        │
        └── tag_closing: ">" (location: (1:14)-(1:15))
```

### `Herb::AST::HTMLCommentNode`

The humble HTML comment.

```
Herb.parse("<!-- comment -->").value
=> 
@ DocumentNode (location: (1:0)-(1:16))
└── children: (1 item)
    └── @ HTMLCommentNode (location: (1:0)-(1:16))
        ├── comment_start: "<!--" (location: (1:0)-(1:4))
        ├── children: (1 item)
        │   └── @ LiteralNode (location: (1:4)-(1:13))
        │       └── content: " comment "
        │
        └── comment_end: "-->" (location: (1:13)-(1:16))
```

### `Herb::AST::HTMLElementNode`

The `HTMLElementNode` captures the full element from opening tag to closing tag, including the contents (`body`). Each of these are represented by a dedicated node:

- `HTMLElementNode#open_tag  => HTMLOpenTagNode`
- `HTMLElementNode#body      => [HTMLTextNode, …]` (array of child nodes)
- `HTMLElementNode#close_tag => HTMLCloseTagNode`

```
Herb.parse("<em>emphasis</em>").value
=>
@ DocumentNode (location: (1:0)-(1:17))
└── children: (1 item)
    └── @ HTMLElementNode (location: (1:0)-(1:17))
        ├── open_tag:
        │   └── @ HTMLOpenTagNode (location: (1:0)-(1:4))
        │       ├── tag_opening: "<" (location: (1:0)-(1:1))
        │       ├── tag_name: "em" (location: (1:1)-(1:3))
        │       ├── tag_closing: ">" (location: (1:3)-(1:4))
        │       ├── children: []
        │       └── is_void: false
        │
        ├── tag_name: "em" (location: (1:1)-(1:3))
        ├── body: (1 item)
        │   └── @ HTMLTextNode (location: (1:4)-(1:12))
        │       └── content: "emphasis"
        │
        ├── close_tag:
        │   └── @ HTMLCloseTagNode (location: (1:12)-(1:17))
        │       ├── tag_opening: "</" (location: (1:12)-(1:14))
        │       ├── tag_name: "em" (location: (1:14)-(1:16))
        │       └── tag_closing: ">" (location: (1:16)-(1:17))
        │
        └── is_void: false
```

### `Herb::AST::HTMLAttributeNode`, `Herb::AST::HTMLAttributeNameNode`, `Herb::AST::HTMLAttributeValueNode`

The `HTMLOpenTagNode#children` can also contain an array of HTML attributes, represented by the `HTMLAttributeNode`. The `HTMLAttributeNode` has a `#name` pointing to an `HTMLAttributeNameNode` and a `#value` pointing to the `HTMLAttributeValueNode`. The `HTMLAttributeValueNode#children` points to an array of `LiteralNode`s.

```
Herb.parse('<span class="alert">Watch out!</span>').value
=>
@ DocumentNode (location: (1:0)-(1:37))
└── children: (1 item)
    └── @ HTMLElementNode (location: (1:0)-(1:37))
        ├── open_tag:
        │   └── @ HTMLOpenTagNode (location: (1:0)-(1:20))
        │       ├── tag_opening: "<" (location: (1:0)-(1:1))
        │       ├── tag_name: "span" (location: (1:1)-(1:5))
        │       ├── tag_closing: ">" (location: (1:19)-(1:20))
        │       ├── children: (1 item)
        │       │   └── @ HTMLAttributeNode (location: (1:6)-(1:19))
        │       │       ├── name:
        │       │       │   └── @ HTMLAttributeNameNode (location: (1:6)-(1:11))
        │       │       │       └── name: "class" (location: (1:6)-(1:11))
        │       │       │
        │       │       ├── equals: "=" (location: (1:11)-(1:12))
        │       │       └── value:
        │       │           └── @ HTMLAttributeValueNode (location: (1:12)-(1:19))
        │       │               ├── open_quote: """ (location: (1:12)-(1:13))
        │       │               ├── children: (1 item)
        │       │               │   └── @ LiteralNode (location: (1:13)-(1:18))
        │       │               │       └── content: "alert"
        │       │               │
        │       │               ├── close_quote: """ (location: (1:18)-(1:19))
        │       │               └── quoted: true
        │       │
        │       │
        │       └── is_void: false
        │
        ├── tag_name: "span" (location: (1:1)-(1:5))
        ├── body: (1 item)
        │   └── @ HTMLTextNode (location: (1:20)-(1:30))
        │       └── content: "Watch out!"
        │
        ├── close_tag:
        │   └── @ HTMLCloseTagNode (location: (1:30)-(1:37))
        │       ├── tag_opening: "</" (location: (1:30)-(1:32))
        │       ├── tag_name: "span" (location: (1:32)-(1:36))
        │       └── tag_closing: ">" (location: (1:36)-(1:37))
        │
        └── is_void: false
```

### `Herb::AST::HTMLSelfCloseTagNode`

I would expect a void HTML node to result in an `HTMLSelfCloseTagNode`, however it seems this is instead handled by a `HTMLOpenTagNode#void == true`.

```
Herb.parse('<br />').value
@ DocumentNode (location: (1:0)-(1:6))
└── children: (1 item)
    └── @ HTMLElementNode (location: (1:0)-(1:6))
        ├── open_tag:
        │   └── @ HTMLOpenTagNode (location: (1:0)-(1:6))
        │       ├── tag_opening: "<" (location: (1:0)-(1:1))
        │       ├── tag_name: "br" (location: (1:1)-(1:3))
        │       ├── tag_closing: "/>" (location: (1:4)-(1:6))
        │       ├── children: []
        │       └── is_void: true
        │
        ├── tag_name: "br" (location: (1:1)-(1:3))
        ├── body: []
        ├── close_tag: ∅
        └── is_void: true
```

## Tree Nest

The following tree is nested according to the levels above for an quick overview. The only thing to note is that where the `HTMLElementNode#body` in this case contains an “only-child” of `HTMLTextNode` for simplicity, it could of course contain many children, of different types, and be nested several layers deep depending on the structure of the HTML.

```
Herb::AST::DocumentNode
  :children #=> Herb::AST::HTMLElementNode

    Herb::AST::HTMLElementNode
      :open_tag  #=> Herb::AST::HTMLOpenTagNode
      :tag_name
      :body      #=> [Herb::AST::HTMLTextNode, …]
      :close_tag #=> Herb::AST::HTMLCloseTagNode
      :is_void

        Herb::AST::HTMLOpenTagNode
          :tag_opening
          :tag_name
          :tag_closing
          :children #=> Herb::AST::HTMLAttributeNode
          :is_void

            Herb::AST::HTMLAttributeNode
              :name   #=> Herb::AST::HTMLAttributeNameNode
              :equals
              :value  #=> Herb::AST::HTMLAttributeValueNode

                Herb::AST::HTMLAttributeNameNode
                  :name

                Herb::AST::HTMLAttributeValueNode
                  :open_quote
                  :children #=> Herb::AST::LiteralNode
                  :close_quote
                  :quoted

                    Herb::AST::LiteralNode
                      :content

        Herb::AST::HTMLTextNode
          :content

        Herb::AST::HTMLCloseTagNode
          :tag_opening
          :tag_name
          :tag_closing
```

There are a few more observations to make. The `HTMLElementNode` consists of three parts, the `#open_tag` (`<span class="alert">`, `#body` (`Watch out!`) and `#close_tag` (`</span>`). It speaks of a `#body` rather than `#children` (in other node types) but these seem for all intents and purposes identical in function. The tag nodes (`HTMLOpenTagNode` and `HTMLCloseTagNode`) have `#tag_opening` (`<`, and `</` respectively), `#tag_name` (`span`) and `#tag_closing` (`>`) methods. The `HTMLOpenTagNode` has support for HTML attributes via `#children` and supports void elements (i.e., self-closing tags like `<input />`) with `is_void`. `HTMLAttributeNode`s are themselves a compound of `#name` and `#value` where the `HTMLAttributeValueNode` has itself a `#children` collection of `Herb::AST::LiteralNode`s.  The `LiteralNode` and the `HTMLTextNode` have `#content`.

—*1819, Sunday 20th April 2025.*
