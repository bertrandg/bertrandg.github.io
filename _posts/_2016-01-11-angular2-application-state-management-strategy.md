---
layout: post
title: "Angular2: Select an application state management strategy"
description: ""
modified: 2016-01-11
tags: [angular2, redux, rxjs, observables]
image:
  feature: abstract-10.jpg
comments: true
share: true  
---

Last times, I documented me a lot about JS concepts (functional programming, composition, reactive programming) and new librairies (Flux, Redux, RxJS). All of this is really vast and passionnate.
Angular2 allows us more freedom for application state management and lots of tools are here to help us: Redux, Immutable.js, RxJS,..

# Starting point

I'm gonna start a new job in few months and I will be in charge of a new big webapp. There will be a lot of data to take care and interact with. Angular is the defacto choice because this is the historical front framework company (and because it's a really good one). The development will take several months so Angular2 will be used and I'm really excited about it.
But I had some pains on previous AngularJS apps. When they grow to medium/big sizes, difficulties appears dues to data dispatched in lots of places and managed from actions coming from everywhere.
Results is an app : hard to maintain, debug and test.

### Centralize data & unidirectionnal data flow

This is why I came to the admited fact that application state should be only at one (or maybe 2) places only inside the whole app and follow an unidirectionnal data flow.

This post reflects my exploration about different known ways of solving this.


# Let's talk about immutability

Working with immutables objects means you always create a new one when you want to modify it. If you want to add an item to an array, you create a new one containing all items plus the new one. You want to modify a boolean property on an object, you create a new one with all same properties and the modified one.
Immutability leads to better performances for tracking changes, just have to look the object references (`objA !== objB`), if it didn't change, nothing to do, no need to check deeper:

[Introduction to immutability in javascript](http://www.sitepoint.com/immutability-javascript/)

### How use it
We can do it with vanilla JS but we have to be carefull and always keep it in mind. Or there are librairies made for it and [Immutable.js](https://facebook.github.io/immutable-js/) is most popular implementations of immutable data structures.

[Using immutable data structures in javaScript with Immutable.js](http://jlongster.com/Using-Immutable-Data-Structures-in-JavaScript)

### Angular2 and Immutability
Angular2 change detection allows us more possibilities than its predecessor (thanks to [ChangeDetectionStrategy](https://angular.io/docs/js/latest/api/core/ChangeDetectionStrategy-enum.html)) and we can know tell ng2 to rerender some components only when input objects change, so they play well with immutable objects!

[Change detection in Angular2](http://victorsavkin.com/post/110170125256/change-detection-in-angular-2)

[Immutability and encapsulation in Angular2](http://victorsavkin.com/post/133936129316/angular-immutability-and-encapsulation)

Build a minesweeper with Angular2 and Immutable.js: [Part1](http://www.jvandemo.com/how-to-build-minesweeper-using-angular-2-and-immutable-js/) & [part2](http://www.jvandemo.com/how-i-optimized-minesweeper-using-angular-2-and-immutable-js-to-make-it-insanely-fast/)


# Two main solutions

I see 2 main ways to manage data properly: Use a Flux architecture like Redux or do it 'manually' with RxJS observables.
Both plays well with immutable data.

# Solutions 1 : Let Redux takes care <small>(or another Flux architecture)</small>

Redux is a tiny (2kb) 'state container' library implementing this idea with PREDICTABILITY as the master word. It has three main principles:

* State of the whole application is stored in an object tree (single source of truth).
* Application state is read only.
* State changes are written as pure functions.

And it works with immutable datas that we just view upstairs (because of the third principle!).

### Redux history

Facebook firstly introduced Flux and then a lot of implementations appears: Reflux, Flummox, Fluxible, Fluxxor, Fynx, Fluxify,.. and Redux won the battle.

### Learn Redux smoothly

Go in this order:
[A great Flux cartoon explanations without code, just concepts](https://code-cartoons.com/a-cartoon-guide-to-flux-6157355ab207#.otzw12wgi)
[Another cartoon about Redux and differences with Flux](https://code-cartoons.com/a-cartoon-intro-to-redux-3afb775501a6#.ub8gstdmo)
[Best explanations about Redux from his author, Dan ABRAMOV, in 30 small live-coding videos: priceless and free content! (Learn main ES6 features before watching it.)](https://egghead.io/series/getting-started-with-redux )
[Read the official doc](http://redux.js.org/)
And then you can go deeper about asynchronous actions, using middlewares like [redux-thunk](https://github.com/gaearon/redux-thunk) and [redux-saga](https://github.com/yelouafi/redux-saga).

### Redux with Angular

Redux is historically combined with React to render web pages but you can use it to manage your data with any others front frameworks like Angular 1&2, Ember, Aurelia,..

First, you need to understand how this architecture could be implemented with angular, [this post shows you how switch a Todo app using angular1 to a Redux-like state management](https://medium.com/@riadbenguella/understand-unidirectional-data-flow-by-practice-rewrite-an-angularjs-application-55cb5d8cb620#.iy8e20x6v)

These one makes the glue between Redux and the render frameworks but it's not really needed, you can directly get the state from the store and use it inside your Angular2 components like describe in this article:
[Build Angular2 application with Reflux and Immutable.js](http://blog.jhades.org/angular-2-application-architecture-building-flux-like-apps-using-redux-and-immutable-js-js/)
[How to use Redux in Angular 2 Applications](https://medium.com/google-developer-experts/angular-2-introduction-to-redux-1cf18af27e6e#.37etee7to)

You have angular bindings library like [react-redux](https://github.com/rackt/react-redux) for React (totally optional):
[ng-redux for Angular 1.X](https://github.com/wbuchwalter/ng-redux)
[ng2-redux for Angular 2.X](https://github.com/wbuchwalter/ng2-redux)


# Solution 2 : Do it manually

If you impose to yourself (and your team) strict rules about state management in your app, you can build 0000

### UNIQUE FACADE
Build your own convention to manage your data properly using a single façade [following this article](https://medium.com/@bojzi/anatomy-of-a-large-angular-application-f098e5e36994#.8syee5imx)

### RXJS AND OBSERVABLES - USE OBSERVABLES (RXJS) STORED IN DIFFERENT SERVICES WITH DEPENDENCIES BETWEEN THOSE SERVICES

#### Observable streams
Observables are data streams, a very powerfull mix between arrays and promises. We can subscribe/unsubscribe to them.
[See this FunFunFunction episode about streams](https://www.youtube.com/watch?v=UD2dZw9iHCc)

You can use Observables in javascript by installing RxJS (or Bacon.js). It will be maybe part of ES7/ES2016 (currently TC39 part 1)
[introduction to RxJS](https://medium.com/@andrestaltz/2-minute-introduction-to-rx-24c8ca793877#.sqrb8l4jk)

#### RxJS with Angular2
I see more and more RxJS Observables (which is embed inside Angular2) documentations and posts. It seems a good way to build a Flux-like architecture inside Angular2 application:
[Angular2 Observable data services](http://coryrylan.com/blog/angular-2-observable-data-services)
[Managing state in Angular 2 using RxJs](https://medium.com/front-end-developers/managing-state-in-angular-2-using-rxjs-b849d6bbd5a5#.w82rdllt5)

# Solution bonus: Mixing both

# CONCLUSION

I'm not sure if Angular2 has enought mecanisms with Observables and Immutables data to avoid using Redux...
I haven't made my choice for the moment and things are moving fast but the main idea is to really
Never stop learning and nice time to be a JS developer!

### BOTH WORKS WELL AND VIABLE

### REDUX GOT MIDDLEWARES AND DEVTOOLS
