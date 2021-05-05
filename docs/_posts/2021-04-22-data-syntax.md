---
layout:     single
title:      "Data Syntax"
date:       2021-04-22 01:45:19 -0500
categories: jekyll update
---

Elder splits it's syntax standard and language implementation.
Much like S-expressions are the syntax of choice for many LISPs but are a separate standard.

## Overview

During the design process, many different syntax options were considered and generally fall into a few categories:
* A very specific syntax which only provides the minimum set of syntax constructs used by the language (the most common choice)
* A DSL/language-creator syntax which allows for custom syntax and semantics much like Racket 
* A mostly homoiconic syntax which uses it's data for it's AST like LISP or Rebol

The first options is too constrained. Elder is meant to be used from HTML to system programming and hard coding each construct needed isn't feasible. It also seems unlikely that we can account for all the types of constructs that developers would find useful. For example, imagine adding ObjectCapabilities to a language like Java with compile time feedback.

The second option has the opposite problem; it is too flexible. Learning new syntax, defining lexer/grammar rules, and running code analysis across multiple languages/DSL becomes more difficult. From my limited experience, Racket has tools to assist with much of these however there is still often a disconnect between the AST that's used and the syntax of the language effectively making multiple (somtimes conflicting) constructs.

Ultimately the third option was chosen as it seems to balance the flexibility of creating DSLs without allowing the burden of entirely new syntax.
Since we require the AST to also be defined using Elder syntax the barrier to entry is lowered as developers will know the one syntax which is similar throughout.
This makes it somewhat easier to reason on your task as it's a single set of rules and code patterns for different DSLs, metaprogramming, compile time, and others.
It also makes it easier to combine multiple tools and techniques together as it's a single set of syntax and code patterns.

## Benefits

This choice has other benefits:
* Multiple paradigms are handled with a syntax. For example, Elder can be used for declarative, logic, markdown, imperative, and other paradigms.
* Since the AST is made of the data of the language it make compile time programming, static analysis, and metaprogramming easier to understand as there's only one syntax.
* DSLs are easier to create the main task is defining semantics to the existing syntax instead of introducing new syntax.
* Elders list of reserved symbols and words is very small. Instead, developers are free to define their own often under some function
  * For example, `for` loops have a `it` keyword signifying an item of the iteration
* In a constrained, hard-coded language it's hard to know when you've added enough keywords, constructs, patterns, etc. and adding more in often becomes a cludge
  * For example, see how the syntax for generics have been added to Java. They function, but they're certainly not meant to jive w/ the existing syntax and semantics of the language. Now imagine extending the syntax futher to accept additional concepts like ObjectCapabilities or Linear Types. One would have to do some sort of metaprogramming likely paired with annotations and even then it wouldn't be very clear to the user with helpful compiler insight.
* Writing tools (for verification, code analysis, metaprogramming, codegen, optimizers, etc.) are easier as everything is defined in the existing syntax w/o an extra layers of abstraction for the AST. The AST is aligned with the syntax.
* Syntax without semantics is allowed which provides a richer way to name, model, and structure code
  * For example, instead of using a prefix to identify globals (like `_x = 0` or `$y = 1`) you can use pure syntax (like `global/(x, y) = 0, 1`) without computational cost.
* Developers will be able to express themselves instead of conforming only to the tools provided by the language. As more external libraries, tools, code analysis, etc. are added a simple data-oriented syntax which doesn't rely on keywords or compiler-constrainted structure becomes more useful.

## Drawbacks

Like most design decision, it also has drawbacks:
* It can be useful to heavily constrain a language into a terse, cohesive, and composable whole. This is often more feasible with DSLs and not general purpose languages.
  * PugJS, SASS, SQL are a good examples of mostly well-designed, cohesive, and fit-to-purpose language which maps very well to their domains
  * This becomes more difficult as the domains and use-cases become wider and general purpose. Elder attempts to get 90% of the terseness of most languages while still maintaining a general syntax.
* Only allowing a single, or few, ways to model a problem can be useful as it removes certain choices the developers must make.
* Since Elder syntax is defined in terms of it's data, it's often possible to define syntax that isn't meaningful for a domain
  * To combat this Elder allows domains to specify arbitrary rules that can run at comptime or runtime to constrain the syntax.
  * Sometimes defining orthogonal concepts to a domain can be useful as well. For example, logging is a cross-cutting concern across domains which must be handled everywhere. If we only allowed the syntax of a domain we could lose out on cross-cutting as domains become more complex. To constrain this, Elder provides tools to isolate syntax to specific domains and functionality (much like `private` in other languages but travels with data across the stack.

## Summary

The choice to use a homoiconic language is one of the most impactful in the language.
It affects how developers, tooling, and compiler work and fit together to form a more cohesive whole.
