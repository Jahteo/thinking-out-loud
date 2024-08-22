---
draft: false
tags:
  - TIL
  - NeedMoreInfo
description: 
aliases:
---
Common: generic types for function arguments.

Badass: using them to constrain types
1. using [[type predicates, convince me]]
	1. great for when you want to add a bunch of constrains and want to safely typenarrow lo
			[[Weird JS Bug - type of null is object]]. 
1. using `extends` keyword
	1. using`extends` both lets it be shorter than a type predicate, & is great for only adding the constraints needed by the function using it

#NeedMoreInfo  type predicates 
#NeedMoreInfo  example codeblocks of using both