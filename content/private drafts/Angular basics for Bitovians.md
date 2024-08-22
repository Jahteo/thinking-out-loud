---
draft: true
tags:
  - TIL
  - Angular
description: 
aliases:
---
Link: https://bitovi.atlassian.net/wiki/spaces/NG/pages/399015940/Learn+the+Basics+of+Angular

## Copy from [[2024-08-21]]
Learning Angular can be a long adventure with many twists and turns. To survive, you’ll need a grasp of:

1. [RxJS Basics](https://bitovi.atlassian.net/wiki/spaces/NG/pages/399015940/Learn+the+Basics+of+Angular#RxJS-Basics%3A "https://bitovi.atlassian.net/wiki/spaces/NG/pages/399015940/Learn+the+Basics+of+Angular#RxJS-Basics%3A")
    
2. [Core Fundamentals of Angular](https://bitovi.atlassian.net/wiki/spaces/NG/pages/399015940/Learn+the+Basics+of+Angular#Core-Fundamentals-of-Angular "https://bitovi.atlassian.net/wiki/spaces/NG/pages/399015940/Learn+the+Basics+of+Angular#Core-Fundamentals-of-Angular")
    
3. [Basics to Debugging Angular Applications](https://bitovi.atlassian.net/wiki/spaces/NG/pages/399015940/Learn+the+Basics+of+Angular#Basics-to-Debugging-Angular-Applications "https://bitovi.atlassian.net/wiki/spaces/NG/pages/399015940/Learn+the+Basics+of+Angular#Basics-to-Debugging-Angular-Applications")
    

Below is a shortest-path guide to faking it until you make it to being an Angular expert. The first two steps are interchangeable. You can learn RxJS or the core fundamentals of Angular first. Just know that you’ll run into RxJS when completing [Bitovi's RxJS academy course](https://www.bitovi.com/academy/learn-rxjs.html "https://www.bitovi.com/academy/learn-rxjs.html"). Good luck and reach out to the Angular channel for any questions ![:slight_smile:](https://pf-emoji-service--cdn.us-east-1.prod.public.atl-paas.net/standard/caa27a19-fc09-4452-b2b4-a301552fd69c/64x64/1f642.png)

# RxJS Basics

For a deeper context on what these operators do, you should read [RxJS’s official documentation on that operator](https://rxjs.dev/api "https://rxjs.dev/api").

### Operators

Basics - your bread and butter:

1. `map` - transform data
    
    - When you use `map`, only include logic involved with the value you are “transforming”. Anything else (side effects) should go in a `tap` operator instead.
        
2. `tap` - for side effects (things that don't affect data).
    
    - Helpful in opening modals, setting non-Observable properties, or `console.log` (debugging).
        

Dealing with multiple Observables:

1. `combineLatest`/`forkJoin` - joining multiple Observable values together. Like `Promise.all`.
    
    - "Creation" operator: you don't use `pipe` with this operator. You start with this operator instead:
        
        `combineLatest([obs1$, obs2$, obs3$]).pipe( map([value1, value2, value3]) => { /* Use the latest values of the Observables */ } );`
        
    - They function differently from each other, but typically you can use them interchangeably.
        
2. `switchMap` - When one Observable depends on the value of another Observable(s).
    
    - Also known as a ==flattening== operator. A.K.A dealing with "higher-order Observables."
        
    - Basically `mergeMap`, `concatMap`, `exhaustMap`. They are helpful for different things. That being said, just use `switchMap`. Here's a showcase of how they differ: [![](https://m-thompson-code.github.io/rxjs-maps/favicon.ico)RxjsMaps](https://m-thompson-code.github.io/rxjs-maps/).
        
3. `withLatestFrom` - need a snapshot of the latest value of another ==Observable==(s).
    
    - Similar to `switchMap`, but it doesn't wait for a value, it just gets the latest snapshot.
        

Does your Observable stream need to reference its **previous value**? Use the "reducer" ==pattern==:

1. `merge` - Treats multiple Observables as if they're a single stream.
    
    - "Creation" operator: you don't use `pipe` with this operator. You start with this operator instead:
        
        `merge([obs1$, obs2$, obs3$]).pipe( map(valueFrom1or2or3) => { /* valueFrom1or2or3 is a value from any of the Observable */ } )`
        
    - Unlike `combineLatest`/`forkJoin`, there's no distinction of which Observable the value came from in the stream.
        
2. `scan` - Get reference to previous value of the stream and create next value. Kinda like `Array.reduce`
    
    - Commonly paired with `merge` where each observable value can be distinguished from each other so they can be handled in a specific way with the previous value. A _simple_ example is the “stop watch exercise” [![](https://c.staticblitz.com/assets/favicon_sb-861fe1b85c0dc928750c62de15fed96fc75e57ee366bd937bad17a3938917b3f.svg)stop watch example - StackBlitz](https://stackblitz.com/edit/angular-ivy-c1bpkh?file=src%2Fapp%2Fapp.component.ts%3AL23) .
        

Do you only need one value?

1. `take(1)` - Take limits how many times an Observable emits. There are other alternatives such as `first()`, but ignore these and always use `take(1)`.
    
    - Not always necessary. Sometimes Observables are designed to only emit once.
        

Do you need to skip certain incoming values?

1. `filter` - Allows you to ignore incoming values based on a condition.
    
    - Although uncommon in general, it is typically used to ==avoid nullish incoming values==.
        
    - It can be important to know how to use [type narrowing (type predicates)](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#using-type-predicates "https://www.typescriptlang.org/docs/handbook/2/narrowing.html#using-type-predicates") when using `filter` operator:
        
        ``myObs$.pipe( // If you have a stream where you want to remove // `undefined` as a possible value. // Functionally, using `filter` will ignore them, but TypeScript // will still believe `undefined` is a possible value // Use type narrowing to avoid this issue filter((x: MyType | undefined): x is MyType => x !== undefined), // Now your types know to ignore undefined too map(x: MyType => {/* ... */}) );``
        
    
    `skip` - ==Allows you to skip== the first `n` values where `n` is the argument passed to `skip`
    
    - Using `skip` for troubleshooting is considered a bad practice and should always be avoided. It’s arguably worse than relying on `setTimeout` to resolve asynchronous code issues.
        

Did none of the above help solve a problem? Don’t believe you have the tools needed? Check the [RxJS operator decision tree](https://rxjs.dev/operator-decision-tree "https://rxjs.dev/operator-decision-tree").

### Subscriptions

When subscribing to an Observable, we always attempt to use `==async==` ==pipe when we can==. You know you should use the `async` pipe when that Observable is involved in the template somehow.

If it's not involved in the component’s template, then you will have to subscribe in the component class. When you do, you should use the `takeUntil` pattern. ==There is an alternative pattern known as the== `==SubSink==` ==pattern that we don't like==. You should know both of these and know the difference.

### Bitovi Academy

[Bitovi's RxJS academy course](https://www.bitovi.com/academy/learn-rxjs.html "https://www.bitovi.com/academy/learn-rxjs.html") is a good way to learn advanced usage of RxJS, but the walkthrough isn't always one-to-one to code you'll see on client projects, so take it with a grain of salt. The key takeaways ==are== to be familiar with the operators and to learn the mindset on when to use one over the other.

# Core Fundamentals of Angular

There are 3 main approaches to learning the basics of Angular, and that is to:

1. Complete tutorials / walkthroughs
    
2. Read ==documentation==
    
3. Watch videos and presentations
    

There are more ways to improve your Angular knowledge, but to start off, these are the key ways. I recommend ultimately **completing the tutorials**. But I'd also recommend reading enough of the documentation so that when you need to look something up, you have an idea where to look. Once you’ve completed all of that, find videos to watch to understand more advanced concepts.

### Tutorials / Walkthroughs

Bitovi's academy is a good first walkthrough on what a realistic client project would be. You can use it as a starting point to understand the basics of Angular, but ==the concepts== may go over your head on what you're supposed to learn. I would recommend completing some shorter or simpler tutorials first:

(for more in-depth information, go to [Angular's quick-start guide](https://angular.io/quick-start) "https://angular.io/quick-start)") )

1. Introduction video on Angular [![](https://www.youtube.com/s/desktop/bf8c00d7/img/favicon_32x32.png)Introduction to Angular](https://www.youtube.com/watch?v=qxchrt04bTA&feature=youtu.be) (Optional)
    
    - Feel free to skip since you'll likely read this content during the tutorials
        
2. Getting started tutorial: [https://angular.io/start](https://angular.io/start "https://angular.io/start")
    
3. Tour of the Heros: [https://angular.io/tutorial](https://angular.io/tutorial "https://angular.io/tutorial")
    

Once you've completed those tutorials, check out [Bitovi's Angular academy course](https://www.bitovi.com/academy/learn-angular.html "https://www.bitovi.com/academy/learn-angular.html") for a more realistic feel on what an Angular project will look like.

### Read the Documentation (Optional)

It's hard to call this _optional_ since knowing the documentation helps a lot in the long run and is something you'll ultimately have to interact with when debugging. That being said, if you only had time for tutorials or reading documentation, I'd choose completing the tutorials. Here are the sections you’ll want to know of first:

1. Go to [https://angular.io/docs](https://angular.io/docs "https://angular.io/docs")
    
2. Click the "Developer guides" tab (and open in another tab)
    
    - Read the following sections and their subsections: Change detection, Routing and navigation, Forms, HTTP client. You don’t know ==how to master== these topics now, but just know where to look when implementing a feature or ==debugging an== issue.
        
    - Any time you run into a term you don't understand or know of, look it up by going to the "Understanding Angular" section and try to find that term. If you cannot find it, you can search for the term in the upper right corner of their site or look through the [Angular's glossary](https://angular.io/guide/glossary "https://angular.io/guide/glossary"). If you can’t find what you’re looking for, fallback to google or ping the Angular channel.
        
3. Repeat for the following topics:  
    [property-binding-best-practices](https://angular.io/guide/property-binding-best-practices "https://angular.io/guide/property-binding-best-practices")  
    [lazy-loading-ngmodules](https://angular.io/guide/lazy-loading-ngmodules "https://angular.io/guide/lazy-loading-ngmodules") (don’t get too caught up on this one, just know this is what routing looks like in Angular)
    
4. Repeat for all of the sections and subsections for the "Understanding Angular" tab
    

# Basics to Debugging Angular Applications

There’s a lot of moving parts in Angular. Because of this, typical debugging tools for JavaScript can be more complex than usual. It’s nice to know how to use what Angular provides to make this experience easier:

1. [Angular](https://angular.io/guide/devtools#debug-your-application) - Install the browser plugin and read up on [debug-your-application](https://angular.io/guide/devtools#debug-your-application "https://angular.io/guide/devtools#debug-your-application") section.
    
2. Learn about [==debugging functions on window==](https://angular.io/api/core/global "https://angular.io/api/core/global")==.== The (only) important one is `ng.getComponent` since it shows you the state of a component in your browser **without needing to use the browser plugin**.
    
3. Watch our internal ==presentation== on debugging [![](https://developers.google.com/drive/images/drive_icon.png)Angular Team Code & Tell (2023-04-21 11:35 GMT-5) | Debugging Angular beyond console.log](https://drive.google.com/file/d/1b8g_OgCIpmJbTgKiv3Sp2GMKGrJ2zca-/view) _(note this is only for internal Bitovi developers as it contains private information)_
    
4. If you run into an Error that starts with “NG”, you can look up [information about that error and read through the tips on how to resolve it](https://angular.io/extended-diagnostics "https://angular.io/extended-diagnostics").
    
    1. example: [NG8101 -](https://angular.io/extended-diagnostics/NG8101 "https://angular.io/extended-diagnostics/NG8101") `invalidBananaInBox` (yes this is a real Angular error. “Banana in a box” is a technical term that you should know ![:stuck_out_tongue:](https://pf-emoji-service--cdn.us-east-1.prod.public.atl-paas.net/standard/caa27a19-fc09-4452-b2b4-a301552fd69c/64x64/1f61b.png) )