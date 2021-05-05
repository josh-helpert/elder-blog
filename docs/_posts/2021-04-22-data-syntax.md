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

The first option is too constrained.
Elder is meant to be used from HTML to systems programming.
Prescriptively deciding each syntax construct needed isn't feasible especially as more tools, static analyzers, theorem provers, and others are added.
Even further, we can't account for all the types of syntax constructs that developers would find useful.
For example, imagine adding ObjectCapabilities to a language like Java.
At best, it would be a cludge.
At worst, a mess of syntax and nonsensical compiler errors.

The second option has the opposite problem; it is too flexible.
Learning new syntax, defining lexer/grammar rules, and running code analysis across multiple languages and DSLs becomes more difficult.
From my limited experience, Racket has tools to assist with much of these issues.
Regardless there is still a disconnect between the AST that is generated and the syntax of the DSL.

Ultimately the third option was chosen.
Using a homoiconic syntax allows Elder to unify different aspects of development and provides a solid basis for tools outside of itself.

## Benefits

This choice has other benefits:
* Multiple paradigms are handled with a single syntax. For example, Elder can be used for declarative, logic, markdown, imperative, and other paradigms.
* Since the AST uses the data of the language it makes compile time programming, static analysis, and metaprogramming easier to understand.
* DSLs are easier to create because the main task is defining semantics and reusing the Elder data syntax.
* Elders list of reserved symbols and words is very small. Instead, developers are free to define their own often under some function
  * For example, `for` loops have a `it` keyword signifying an item of the iteration
* In a constrained, hard-coded language it's hard to know when you've added enough keywords, constructs, patterns, etc. and adding more in often becomes a cludge
  * For example, see how the syntax for generics have been added to Java. They function, but they're certainly not meant to jive with the existing syntax and semantics of the language. Now imagine extending the syntax futher to accept additional concepts like ObjectCapabilities or Linear Types.
* Writing tools (for verification, code analysis, metaprogramming, codegen, optimizers, etc.) are easier as everything is defined in the existing syntax without an extra layers of abstraction for the AST. The AST is aligned with the syntax.
* Syntax without semantics is allowed. This can provide a richer way to name, model, and structure code with less visual noise.
  * For example, instead of using a prefix to identify globals (like `_x = 0` or `$y = 1`) you can use pure syntax (like `global/(x, y) = 0, 1`) without computational cost.
* Developers will be able to express themselves instead of conforming only to the tools provided by the language. As more external libraries, tools, code analysis, etc. are added a simple data-oriented syntax which doesn't rely on keywords or compiler-constrainted structure becomes more useful.
* Backwards compatibility becomes much less of an issue. Much like Racket the syntax is versioned. Since the syntax it data, it can be easier to migrate and convert between versions.

## Drawbacks

Like most design decision, it also has drawbacks:
* It can be useful to heavily constrain a language into a terse, cohesive, and composable whole. This is often more feasible with DSLs and not general purpose languages.
  * PugJS, SASS, SQL are a good examples of mostly well-designed, cohesive, and fit-to-purpose language which maps very well to their domains
  * This becomes more difficult as the domains and use-cases become wider and general purpose. Elder attempts to get 90% of the terseness of most languages while still maintaining a general syntax.
* Only allowing a single, or few, ways to model a problem can be useful as it removes certain choices the developers must make.
    * Zig and Odin both constrain their syntax and semantics with the goal of providing only what is necessary for writing low-level, general purpose programs. They sacrifice some terseness for the sake of safety.
* Since Elder syntax is defined in terms of it's data, it's often possible to define syntax that isn't meaningful to a domain.
  * To combat this Elder allows domains to specify arbitrary rules that can run at comptime or runtime to constrain the syntax (effectively a form of static analysis).
  * Sometimes defining orthogonal concepts to a domain can be useful as well.
    * For example, logging is a cross-cutting concern across domains which must be handled everywhere. If we only allowed the syntax of a domain we could lose some richness across domains.

## Summary

The choice to use a homoiconic language is one of the most impactful in the language.
It affects how developers, tooling, and compiler work and fit together to form a more cohesive whole.
