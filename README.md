# bookish-funicular

An experimental syntax for HTML components that supports being compiled to
multiple languages and frameworks.

HTML components refers to HTML snippets that are reused within a given website
to produce a consistent look and feel and for the purpose of code reuse. HTML
components are often the code version of a UI component in a tool like Figma.
Components may be small like a button. Components can contain other components,
and may therefore be large such as an entire page.

Modern websites often need the HTML components to work on the client. This may
be to add interactivity to the page, or to show new content that wasn't in the
original HTML such as a dialog. Javascript is the programming lanaguage of the
client side web, which necessitates HTML components needing to be rendered with
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

[mitosis](https://mitosis.builder.io/) has fairly similar goals, but there are
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
