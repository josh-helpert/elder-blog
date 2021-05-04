---
layout:     single
title:      "Data Syntax"
date:       2021-04-22 01:45:19 -0500
categories: jekyll update
---

Elder splits it's syntax standard and language implementation. Much like S-expressions are the syntax of choice for many LISPs but are a separate standard.

During the design process, many different syntax options were considered and generally fall into a few categories:
* A very specific syntax which only provides the minimum set of syntax constructs used by the language (the most common choice)
* A DSL/language creator syntax which allows for custom syntax and semantics much like Racket 
* A mostly homoiconic syntax which uses it's data for it's AST like LISP or Rebol

The first options is too constrained. Elder is meant to be used from HTML to system programming and hard coding each construct needed isn't feasible. It also seems unlikely that we can account for all the types of constructs that developers would find useful.

The second option has the opposite problem. It is too flexible as learning new syntax, defining lexer/grammar rules, and running code analysis across multiple languages/DSL becomes more difficult.

Ultimately the third option was chosen as it seems to balance the flexibility of creating DSLs without allowing the burden of entirely new syntax. Thus making it easier for both developers and analysis tools to work across multiple DSLs.

This choice has other benefits:
* Multiple paradigms are handled with a single set of syntax constructs like declarative, logic, markdown, imperative, and others
* Since the AST is made of the data of the language it make compile time programming and metaprogramming easier to understand as there's only one syntax
* DSLs are easy to create as it mainly adds custom semantics to the existing syntax instead of introducing different syntax
* In a constrained, hard-coded language it's hard to know when you've added enough keywords, constructs, patterns, etc. and adding more in often becomes a cludge
  * For example, see how the syntax for generics shave been added to Java. Now imagine extending the syntax futher to accept additional concepts like ObjectCapabilities or Linear Types.
* Writing tools (for verification, code analysis, metaprogramming, codegen, optimizers, etc.) are easier as everything is defined in the existing syntax w/o an extra layers of abstraction for the AST
* Syntax as just syntax is allowed which provides a richer way to name, model, and structure code
  * For example, instead of using a prefix to identify globals (like `_x = 0` and `_y = 1`) can use pure syntax (like `global/(x, y) = 0, 1`) without computational cost
* Developers will be able to express themselves instead of conforming only to the tools provided by the language. As more external libraries, tools, code analysis, etc. are added a simple data-oriented syntax which doesn't rely on keywords or compiler-constrainted structure becomes more useful.

It also has some drawbacks:
* It can be useful to heavily constrain a language into a terse, cohesive, and composable whole.
  * PugJS is a good example of a well design, cohesive, and fit-to-purpose language which maps very well to it's domain
  * This becomes more difficult as the domains and use-cases become wider and general purpose. Elder attempts to get 90% of the terseness of most languages while still maintaining a general syntax.
* Only allowing a single, or few, ways to model a problem can be useful as it removes certain choices the developers must make.
* Languages that are designed for a specific purpose can model their domain more tersly than Elder can
* Since Elder syntax is defined in terms of it's data, it's often possible to define syntax that isn't meaningful for a domain
  * Elder has tools to combat this mainly by allowing domains to specify arbitrary rules that can run at comptime or runtime


