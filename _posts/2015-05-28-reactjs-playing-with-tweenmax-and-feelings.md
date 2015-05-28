---
layout: post
title: "ReactJS: Playing with TweenMax and first feelings"
description: ""
modified: 2015-05-28
tags: [reactjs, tweenmax, plunker]
image:
  feature: abstract-10.jpg
comments: true
share: true  
---


I am working to port my [Angular1 training app](http://bertrandg.github.io/angular-training-plunker-exercice/) to Angular2 but some parts are still missing.<br>
It's a bit too early to really play with [Angular2](https://angular.io/download/).

So, having time to discover new javascript frameworks, I decided to give a try to [ReactJS](https://facebook.github.io/react/) in combinaison with the best animation framework named [TweenMax](https://greensock.com/tweenmax).<br>
It reminds me my actionscript developer background. (do not tell anyone!)

## Here is the result

<iframe style="border: 1px solid #bbb;width: 100%; height: 700px" src="http://embed.plnkr.co/ZgDO9d/?t=run" frameborder="0" allowfullscreen="allowfullscreen">Loading plunk...</iframe>

Note: I used this [helpfull service](http://beta.json-generator.com/GrTI49Y) to generate my fake data.


## Feelings about ReactJS

I really liked it!

It is not comparable to Angular because it takes only care about UI but we can compare it to Angular directives.

It’s interesting to note that Angular2 will have real similarities to ReactJS:

* Everything is a component (Component model which is a key point of Web's future)
* Unidirectionnal binding (It forces proper data flow architecture and better performance with long list for example)
* [Isomorphic](http://isomorphic.net/) framework (Serve side render really good for performance and SEO)

On the bad side, I don’t like to code HTML inside my JS file, I feel like doing something wrong...<br>
I definitely prefer to have my HTML template files and this is more “webdesigner friendly”.


## Resources to start

* [Nice slides for developers with Angular background](https://speakerdeck.com/pedronauck/reactjs-keep-simple-everything-can-be-a-component)
* [A 7 minutes presentation video from Egghead](https://egghead.io/lessons/react-react-in-7-minutes)
* [ReactJS official documentation](https://facebook.github.io/react/docs/getting-started.html)



