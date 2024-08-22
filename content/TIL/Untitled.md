---
draft: false
tags:
  - TIL
description: 
aliases:
---
Injecting with an Angular constructor vs `Inject()`, the constructor lets you deal with type generics.
Inject takes care of what the constructor would do, just newer syntax. With move to just using functions instead of classes, it simplifies things

(dependency injection - how to get one thing to communicate with another)
99% of the time it will be more than suficient to provide at root, so that it's available everywhere, and angular make it happen when needed
if you do it at the app level, you can guarantee that it loads.



//

abstract class keyword, 

\\
a service is something that is dependency injectable, could even be a component

3 reasons to use services.
1 it's on the root
2 you need to migrate code
3. need to communicate between 2 thingies.