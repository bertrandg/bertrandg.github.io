---
layout: post
title: "Angular2: Infinite loop route component"
description: ""
modified: 2015-11-07
tags: [angular2, plunker]
image:
  feature: abstract-10.jpg
comments: true
share: true  
---

If you have followed Angular 2 genesis, you maybe remember a video showing a component router early version demo with a component loading itself infinitely.

## I reproduce it there:

<iframe style="border: 1px solid #bbb;width: 100%; height: 400px" src="https://embed.plnkr.co/SqZ8Z2/?t=run" frameborder="0" allowfullscreen="allowfullscreen">Loading plunk...</iframe>

This is useless but funny and it helps to understand the power of this new router.

In few times, we will see open source librairies coming with their own routes to plug where we want in our application.<br>
That's a big step forward!<br>

It will be possible to have `async route` too for lazy loading.
An other killer feature of this new router are `auxiliary routes`. It will be possible to have parallel routes with their own history (back / forward).
Note that `aux routes` are not fully usable currently in alpha 45.

## Here is the specific code:

{% highlight javascript %}
import {Component, View} from 'angular2/angular2';
import {RouteConfig, ROUTER_DIRECTIVES} from 'angular2/router';
import {LoopHome} from './LoopHome';


@RouteConfig([
    { path: '/', component: LoopHome, as: 'LooperHome' },
    { path: '/looper/...', component: LoopRoute, as: 'Looper' }
])
@Component({
  selector: 'loop-route'
})
@View({
  directives: [ROUTER_DIRECTIVES],
  template: `
    <div style="border: 1px solid #000; margin: 5px; padding: 5px;">
      <button [router-link]="['./Looper/LooperHome']">GO DEEPER</button>
      <button [router-link]="['./LooperHome']">GO LOOP HOME</button>
      <router-outlet></router-outlet>
    </div>
  `
})
export class LoopRoute {}
{% endhighlight %}

## Can't wait for the first beta!

I'm playing a lot with ng2 these times and it's <strong>REALLY AWESOME</strong>!

Combining this new version and all last tool and specs like ES6, TypeScript, RxJS, SystemJS,.. bring Javascript development to the next level.

I will post others experiments soon.

Note:<br>
You can now directly play with ng2 alpha inside Plunker following this:
`New` > `AngularJS` > `2.0.X` (`TS` / `ES6` / `ES5`)

<center>
  <img style="max-width: 300px;" src="{{site.baseurl}}/images/ng2_infinite_loop/plunker-ng2.jpg">
</center>