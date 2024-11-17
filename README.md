# bookish-funicular

An experimental syntax for HTML components that supports being compiled to
multiple languages and frameworks.

HTML components refers to HTML snippets that are reused within a given website
to produce a consistent look and feel and for the purpose of code reuse. HTML
components are often the code version of a UI component in a tool like Figma.
Components may be small like a button. Components can contain other components,
and may therefore be large such as an entire page. Components are usually a
function over a collection of data, or "props".

Modern websites often need the HTML components to work on the client. This may
be to add interactivity to the page, or to show new content that wasn't in the
original HTML such as a dialog. Javascript is the programming lanaguage of the
client side web, which necessitates HTML components needing to be useable with
Javascript. Usually a higher level Javascript framework is used such as React or
Lit.

Websites should also render on the server, as it is a faster experience for the
user and better for search engines. Reusing HTML components in the client code
and the server traditionally meant sharing programming languages and frameworks
between the client and the server. As Javascript is the language of the client,
this meant the server needs to run Javascript. The term for this is "Isomorphic
Javascript" or "Universal Javascript".

Javascript is not a bad choice for writing web servers. But depending on the
circumstances of each project, it may not be the best choice. But the better
choice comes with the downside of not reusing HTML components.

I propose to create tooling that allows writing code that can be compiled to
code for various Javascript frameworks (React, Vue, Angular, Lit, etc) and code
in various server side languages (Rust, Go, Swift, Ruby ERB, etc) that in all
cases output the same HTML component.

## Prior Art

### Mitosis

[Mitosis](https://mitosis.builder.io/) has fairly similar goals, but there are
differences.

> Mitosis is an open-source tool that transforms JSX components into fully
> functional components for frameworks like Angular, React, Qwik, Vue, Svelte,
> Solid, and React Native.

Mitosis has some level of support for non-Javascript outputs such as Swift and
HTML. These largely do not feature in the documentation and didn't work for me
when I tried them. I believe aspects of the design make non-Javascript output
second-tier in Mitosis. I however want non-Javascript output to be a primary
goal.

Mitosis supports interactive components, with state, and lifecycle hooks. This
necessitates the components support scripting. I however will be focussed closer
on templates, that transform props to HTML or DOM. Interactivity will be
available to the developer by wrapping the output component with an outer
component that controls the props; as interactivity is a client side concern
this does not need sharing with the server.

### Mustache

https://mustache.github.io/

> Logic-less templates

Mustache is intersting as it has acheived many of my goals. It was once very
popular. It is important that I understand why it is no longer popular so I
don't fall for the same mistakes.

Mustache is rarely used as a building block for design systems. In examples I've
seen, mustache templates don't concern themselves with styling, which may be the
reason for this.

Mustache templates can contain conditional expressions and loops, but are
limited beyond the basic. I am unsure how limiting this is for components- the
initial release of my project will not support advanced transformations. I will
need to see if there any any existing solutions that can compile simple scripts
into a range of different languages.

The typical implementation of Mustache is to write interpreters for various
different programming languages. I have not seen generators, which are currently
my favoured solution.

Mustache takes untyped input.

## Open Questions

What does the source syntax look like?

How will the CSS associated with an HTML component be attached? Assuming we give
options, what options do we give?

How well does writing a website using this tooling work? Are there other things
that are duplicated, and how costly is the duplication?

Many components are quite simplistic in structure. For example a button may just
be a `<button`, `<input type="submit"`, `<a href` or `<div` element with styling
attached and callbacks (on the client). That is, the design of components like
buttons, tags or labels is just a class or classnames that can be applied to any
element with a single slot for children. Should we support something simpler
than full components that are effectively just `class` or `style` attributes?

## Typing

We should support prop typing. We should support:

- strings- or if the language supports it more efficient types like Rust's
  `Display` trait.
- Enums (eg `confirmation | subtle | danger`) usually for applying different
  styles to a component. It would be good to support tagged unions, but:
  - Would this be incompatible with some of the languages we wish to target?
    Maybe we support it, with the understanding that it's the developer's choice
    to reduce their list of supported languages.
  - Would tagged unions be useful when building components? Potentially- for
    example the danger variant might allow for selecting a choice of danger
    icons.
- Numbers, for repeating an inner component?
- Numbers, so that they can be displayed with a format string?
- Lists generic over other types, that can be mapped over to repeat inner
  components. In languages like Rust this could be the Iterator trait rather
  than a concrete array.
- Structs or records generic over other types.
- Other components which may in turn take props. Passing in components will be
  one way to combine components- it should also be possible to directly
  reference another component within a function.
- Style functions for simplistic components- these may in turn take props.
- Functions that can return any of the above?
- Async or promised data? If we support it, on the server it would have to be
  awaited, and on the client we'd need to support multiple scenarios (loading
  fallbacks at multiple levels, hydration behaviour).

As we're not targetting a single language, we need to support types in a cross
language way. If possible, we should reuse existing solutions for writing types
in a cross language manner and compiling them to multiple languages.

### Existing Typing cross language typing solutions

#### [typeshare](https://github.com/1Password/typeshare)

> Typeshare is the ultimate tool for synchronizing your type definitions between
> Rust and other languages for seamless FFI.

The input is Rust code with annotated structs and enums. While we don't want the
people using this tool to be writing the components in Rust, perhaps we can have
the source types in Rust.

#### [JSON Schema](https://json-schema.org/)

> While JSON is probably the most popular format for exchanging data, JSON
> Schema is the vocabulary that enables JSON data consistency, validity, and
> interoperability at scale.

JSON being a data serialization format doesn't support function references or
references to other components.

## First Slice of Value

Styling is very important to components, and in some cases is basically all the
component is. I could make something that allows us to take a collection of
style properties and return a class name or class names. This would be
configurable to various approaches:

- Wether to group the collection of CSS properties to a single class, whether to
  have a 1-to-1 mapping of class names to CSS properties or some combination in
  the middle.
- Support inlining just used CSS, referencing just used CSS or having one large
  external style sheet.
- Support restricting referenced variables to a predefined list.
- Support inlining certain variables.

This is likely to be needed to support full components.
