---
layout: post
title: "Angular2: Recursivity exercice"
description: ""
modified: 2015-12-09
tags: [angular2, typescript, plunker]
image:
  feature: abstract-10.jpg
comments: true
share: true  
---

I reproduced a feature I've coded in a previous job. I did it to experiment ng2 and I’m too lazy to explain what it is for. Just notice that there are 3 differents types (group, segment, criterion) and groups can contain all types. 
You can add, remove and drag & drop all types at any groups/positions.

When I coded it first time few years ago, it was with ES5 and Angular1.<br>
And with this new version using Typescript and Angular2, I see the giant gap, benefits are amazing:

- Debugging before runtime (TS compilation)
- Readability (ES6 modules, events and properties bindings syntax)
- Performance (change detection following a tree structure)
- Compatibility (no more $apply, thanks zone)
- Maintainability (types helps a lot for refactor)
- ..

## Try it

<iframe style="border: 1px solid #bbb;width: 100%; height: 930px" src="https://embed.plnkr.co/jgUUaP/?t=run" frameborder="0" allowfullscreen="allowfullscreen">Loading plunk...</iframe>

I didn’t use immutable/observable data here but I’m gonna try it in a future post.

## Note:

I used this syntax:
{% highlight html %}
<div *ng-for="#m of group.children; #i = index">
  <div *ng-if="m.type == 'criterion'">..</div>
  <div *ng-if="m.type == 'segment'">..</div>
</div>
{% endhighlight %}

Instead of this one which avoid an unnecessary div node:
{% highlight html %}
<template ng-for #m [ng-for-of]="group.children" #i="index">
  <div *ng-if="m.type == 'criterion'">..</div>
  <div *ng-if="m.type == 'segment'">..</div>
</template>
{% endhighlight %}

Due to this [bug](https://github.com/angular/angular/issues/3442) which is gonna be solved in the beta normally.

## Beta is coming!

Currently angular2 is in alpha 50 and beta is finished at 78% (47 issues left).

This year I asked father christmas an angular2 beta, hope magic will operate! :)
