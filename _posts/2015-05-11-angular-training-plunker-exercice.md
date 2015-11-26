---
layout: post
title: "AngularJS Training: Full Plunker exercice"
description: ""
modified: 2015-05-11
tags: [angularjs, plunker, beginner]
image:
  feature: abstract-10.jpg
comments: true
share: true  
---

I recently provided a 5 days AngularJS training in a web agency ([slides availables here in French](https://github.com/bertrandg/slides)) and here is the result of one of the exercices.

This is a simple CRUD application to manage an entity, the server is faked thanks to `$httpBackend` (and this [post](http://www.jeremyzerr.com/angularjs-backend-less-development-using-httpbackend-mock)).
You can list, add and delete, `ui-router` takes care of the routes and `ngAnimate` mixed with [animate.css](http://daneden.github.io/animate.css/) makes things move.
<br><br>

<iframe style="border: 1px solid #bbb;width: 100%; height: 700px" src="https://embed.plnkr.co/Q05wdG/?t=run" frameborder="0" allowfullscreen="allowfullscreen">Loading plunk...</iframe>


I share it there because it covers multiple side of the framework and could be a good first exercice:

Here is what it covers:

- Split application in modules
- Routing with `ui-router` using `resolve` property
- Animate easily with `ngAnimate` module
- Ajax requests with `$http`
- Ajax requests simulation with `$httpBackend` (fake server file was provided)
- Forms validation with `ngMessages` module
- Simple directives to add custom form field validation
- Complex directives with isolate scope and 3 different binding types
- `$modal` service from `ui-bootstrap` library
- Use promises multiple times with `$q` and others services


Next step is gonna build the same small CRUD application with Angular2 which is coming quickly!
