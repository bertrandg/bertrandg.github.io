---
layout: post
title: "Angular2: Select a state management strategy"
description: ""
modified: 2016-01-16
tags: [angular2, redux, immutables, rxjs, observables]
image:
  feature: abstract-10.jpg
comments: true
share: true  
---

<img style="float: right; max-width: 180px; margin: 0 0 10px 10px;" src="{{site.baseurl}}/images/ng2_state/angular2.png" />

This is admitted for developers that state management is one of the toughest part to make an application growing safe and stay maintainable over time.

Angular1 doesn't promote specific way to take care about that except to add it inside services (instead of scopes/components) but change detection system makes useless options like immutability or observables.

Angular2 corrects it and allows us much more possibilities. We can now use at full power libraries like Flux/Redux, Immutable.js, RxJS,..<br>
Lately, I documented me a lot about these Javascript libraries and concepts like **functional programming** & **reactive programming**.
All of this is really vast and exciting!


## ➜ Starting point
I will start a job in few months and will be in charge of a new big webapp.
There will be a lot of data to take care and interact with.
Angular is the de facto choice because of his historical front framework company position and as a full stack solution.
Angular2 will be used and I'm really happy about it but I want to avoid some pains I had on previous applications.


## ➜ Centralize data & unidirectional data flow
Difficulties appears dues to data dispatched in lots of places and managed from everywhere. The result is an application hard to update, debug and test.
This is why it is fundamental that **application state** stays at **one place only** inside the whole application and follows an **unidirectional data flow**.

This post **reflects my exploration** about different known and emerging ways of solving this.


<hr style="position: relative; top: 15px; margin-top: 30px;">
# <center>Let's talk about immutability</center>
Working with immutables objects means you always create a new one when you want to modify it.

You can't mutate an object. If you want to add an item to an array, you create a new one containing all items plus the new one. You want to modify a boolean property on an object, you create a new one with all same properties and the modified boolean.

Immutability leads to better performances for tracking changes: just look at the object references (`objA !== objB`), if it didn't change, nothing to do, no need to check deeper.

[↪ Introduction to immutability in Javascript](http://www.sitepoint.com/immutability-javascript/)


## ➜ How use it
We can do it with vanilla JS but there is no default type for it (contrary to Haskell, Scala,..) so we have to be careful and always keep it in mind.
Otherwise there are libraries made for it and [↪Immutable.js](https://facebook.github.io/immutable-js/) is the most popular implementations of immutable data structures.

[↪ Using immutable data structures in Javacript with Immutable.js](http://jlongster.com/Using-Immutable-Data-Structures-in-JavaScript)

<center>
  <br><img style="max-width: 450px;" src="{{site.baseurl}}/images/ng2_state/immutablejs.png" /><br>
</center>

## ➜ Angular2 and Immutability
Thanks to [↪ChangeDetectionStrategy](https://angular.io/docs/js/latest/api/core/ChangeDetectionStrategy-enum.html), we can know tell Angular2 to rerender a components only when his `inputs` objects change, so it plays well with immutable objects!

[↪ Change detection in Angular2](http://victorsavkin.com/post/110170125256/change-detection-in-angular-2)<br>
[↪ Immutability and encapsulation in Angular2](http://victorsavkin.com/post/133936129316/angular-immutability-and-encapsulation)<br>
↪ Build a minesweeper with Angular2 and Immutable.js: [↪Part1](http://www.jvandemo.com/how-to-build-minesweeper-using-angular-2-and-immutable-js/) & [↪part2](http://www.jvandemo.com/how-i-optimized-minesweeper-using-angular-2-and-immutable-js-to-make-it-insanely-fast/)<br>
[↪ Angular2 with Immutable.js](http://blog.scottlogic.com/2016/01/05/angular2-with-immutablejs.html)


<hr style="position: relative; top: 15px; margin-top: 30px;">
# <center>Three main solutions</center>
I identified three main ways to manage data properly: Use a Flux architecture like Redux, do it 'manually' with RxJS observables, or use the young Ngrx.

All play well with immutables, Redux makes it a duty and it's as you want for others.


<hr style="position: relative; top: 15px; margin-top: 30px;">
# <center>Solutions 1 : Let Redux takes care</center>
[↪Redux](https://github.com/rackt/redux) is a tiny (2kb) **state container library** implementing an unidirectional data flow with **predictability** as the watchword. It has three main principles:

* **State of the whole application is stored in an object tree** (single source of truth).
* **Application state is read only** (unidirectional data flow).
* **State changes are written as pure functions** (why it works with immutable data).


<center><blockquote class="twitter-tweet" lang="en"><p lang="en" dir="ltr">I’m surprised Redux achieved semi-mainstream popularity (9k stars) while asking users to learn immutability.</p>&mdash; Dan Abramov (@dan_abramov) <a href="https://twitter.com/dan_abramov/status/665137077818933248">November 13, 2015</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script></center>



## ➜ Redux history
Facebook firstly introduced Flux and then a lot of implementations appears: Reflux, Flummox, Fluxible, Fluxxor, Fynx, Fluxify,.. and Redux won the battle.

<center>
  <br><img style="max-width: 450px;" src="{{site.baseurl}}/images/ng2_state/redux.png" /><br>
</center>

A big advantage to use Redux is his amazing DevTools with features like time travel.


## ➜ Learn Redux smoothly
Go in this order:<br>
[↪ A nice Flux cartoon explanations without code, concepts only.](https://code-cartoons.com/a-cartoon-guide-to-flux-6157355ab207#.otzw12wgi)<br>
[↪ Another cartoon about Redux and his differences with Flux.](https://code-cartoons.com/a-cartoon-intro-to-redux-3afb775501a6#.ub8gstdmo)<br>
[↪ Best explanations about Redux from his author, Dan ABRAMOV, in 30 small live coding videos ★](https://egghead.io/series/getting-started-with-redux )<br>
[↪ RDFM: Read the official documentation.](http://redux.js.org/)<br>
[↪ Understand what is a Redux Middleware.](https://medium.com/@meagle/understanding-87566abcfb7a#.hrv12sczw)

And then you can go deeper about asynchronous actions, using Middlewares like:<br>
[↪ Redux-thunk](https://github.com/gaearon/redux-thunk)<br>
[↪ Redux-saga](https://github.com/yelouafi/redux-saga)


## ➜ Redux with Angular
Redux is historically combined with React to render web application but you can use it to manage your data with any other front framework like Angular 1&2, Ember, Aurelia,..

First, you need to understand how this architecture could be implemented with angular, [↪this post shows you how switch a Todo application using Angular1 to a Redux-like state management.](https://medium.com/@riadbenguella/understand-unidirectional-data-flow-by-practice-rewrite-an-angularjs-application-55cb5d8cb620#.iy8e20x6v)

Then read these:<br>
[↪ Managing state with Redux and Angular.](http://blog.rangle.io/managing-state-redux-angular/)<br>
[↪ Build Angular2 application with Reflux and Immutable.js](http://blog.jhades.org/angular-2-application-architecture-building-flux-like-apps-using-redux-and-immutable-js-js/)<br>
[↪ How to use Redux in Angular2 Applications.](https://medium.com/google-developer-experts/angular-2-introduction-to-redux-1cf18af27e6e#.37etee7to)

You have angular bindings library similar to [react-redux](https://github.com/rackt/react-redux) for React (totally optional):<br>
[↪ Ng-redux for Angular 1.X](https://github.com/wbuchwalter/ng-redux)<br>
[↪ Ng2-redux for Angular 2.X](https://github.com/wbuchwalter/ng2-redux)


<hr style="position: relative; top: 15px; margin-top: 30px;">
# <center>Solution 2 : Do it manually with RxJS</center>
If you impose to yourself (and your team) strict rules about state management in your application, you can build well maintainable application.
Here are few tools and resources to help you achieve it. Immutable data are not mandatory.


## ➜ A unique facade
Build your own convention to manage your data properly using a single façade following this article:<br>
[↪ Anatomy of a large angular application](https://medium.com/@bojzi/anatomy-of-a-large-angular-application-f098e5e36994#.8syee5imx)

This facade abstract the data management part from components. It reduces dependencies and allows more flexibility in developing the system.
Under the hood, she can call multiple services.

## ➜ Observable streams
<img style="float: right; margin: 0 0 10px 10px;" src="{{site.baseurl}}/images/ng2_state/rxjs.png" />
Observables are data streams, a very powerful mix between arrays and promises. We can subscribe and unsubscribe to them.
The observer Pattern helps us to keep the unidirectional data flow.

You can use observables in Javascript by installing RxJS, it will be part of ES7/ES2016 (currently TC39 part 1).

[↪ FunFunFunction episode about streams](https://www.youtube.com/watch?v=UD2dZw9iHCc)<br>
[↪ Introduction to RxJS](https://medium.com/@andrestaltz/2-minute-introduction-to-rx-24c8ca793877#.sqrb8l4jk)


<center><blockquote class="twitter-tweet" lang="en"><p lang="und" dir="ltr"><a href="https://t.co/aIQTdbFPrA">pic.twitter.com/aIQTdbFPrA</a></p>&mdash; Todd Motto (@toddmotto) <a href="https://twitter.com/toddmotto/status/687104621421158400">January 13, 2016</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script></center>


## ➜ RxJS with Angular2
We can use RxJS observables inside Angular2 services. It allows your components to subscribe and be informed when data changes.

Angular2 is really linked to RxJS (which is in version 5 beta 1) and promotes it.
You can use RxJS observables to build stores-like inside a Angular2 application.

[↪ Angular2 Observable data services.](http://coryrylan.com/blog/angular-2-observable-data-services)<br>
[↪ Managing state in Angular 2 using RxJs.](https://medium.com/front-end-developers/managing-state-in-angular-2-using-rxjs-b849d6bbd5a5#.w82rdllt5)<br>
[↪ Reactive Data Flow in Angular 2](http://blog.lambda-it.ch/reactive-data-flow-in-angular-2/)<br>
[↪ Managing state in Angular2 using RxJS in a Redux-like way ★](http://victorsavkin.com/post/137821436516/managing-state-in-angular-2-applications)

<hr style="position: relative; top: 15px; margin-top: 30px;">
# <center>Solution 3 : NgRx Everything is an Observable</center>
<img style="float: right; max-width: 180px; margin: 0 0 10px 10px;" src="{{site.baseurl}}/images/ng2_state/ngrx.png" />
[↪ Ngrx: RxJS powered state management for Angular2 apps, inspired by Redux.](https://github.com/ngrx/store)

This library goes a step further and make every state part an observable, then components can subscribe to.
This approach is really promising and seems to be the next 'big thing' in the angular community as the following tweet from @egghead mentions.

Use this library with the included `async` pipe and `ChangeDetectionStrategy.OnPush` and you will have a massive performance boost in large ng2 applications.

[↪ Read more explanations from the author @robwormald.](https://github.com/ngrx/store/issues/16#issuecomment-172027797)<br>
[↪ Build a Better Angular 2 Application with Redux and ngrx. ★](http://onehungrymind.com/build-better-angular-2-application-redux-ngrx/)


<center><blockquote class="twitter-tweet" data-cards="hidden" lang="en"><p lang="en" dir="ltr">ngrx/store is fantastic. Check the example app. Clean Redux inspired reactive Angular 2. <a href="https://t.co/0SM1ONwefi">https://t.co/0SM1ONwefi</a> <a href="https://t.co/uq0DDLEP5m">pic.twitter.com/uq0DDLEP5m</a></p>&mdash; egghead.io (@eggheadio) <a href="https://twitter.com/eggheadio/status/688023660431556609">January 15, 2016</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script></center>



<hr style="position: relative; top: 15px; margin-top: 30px;">
# <center>Conclusion</center>
I haven't made my choice actually. Redux is really attractive but **things are moving fast** (we are used to with javascript!).

Keep an eye on **NG-NL**, the next big Angular conference in Amsterdam, many talks about these subjects:

<center>
<a href="http://ng-nl.org/"><img style="max-width: 450px;" src="{{site.baseurl}}/images/ng2_state/ngnl.png" /></a>
</center>

Hope this helps some of you. Tells me about your thoughts about it and if I made mistakes.
