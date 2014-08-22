---
layout: post
title: "AngularJS: Disable a button during an promise (like an http request)"
description: ""
modified: 2014-08-21
tags: [disabled, directive, angularjs, plunker, promise]
image:
  feature: abstract-10.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
comments: true
share: true  
---

In my angular app, I wanted to disable a button during a REST api request, my first solution was to add an ngDisabled directive bind to a boolean I set manually.

It worked well but I found a better way to do that with a custom directive:

{% highlight javascript %}
app.directive('disableOnPromise', function ($parse) {
    return {
        restrict: 'A',
        compile: function($element, attr) {
            var fn = $parse(attr.disableOnPromise);
            return function clickHandler(scope, element, attrs) {
                element.on('click', function(event) {
                    attrs.$set('disabled', true);
                    scope.$apply(function() {
                        fn(scope, {$event:event}).finally(function() {
                            attrs.$set('disabled', false);
                        });
                    });
                });
            };
        }
    };
});
{% endhighlight %}

The second solution is better because you don't pollute your controller with a boolean only used for the view and it's one watcher less for each time you use it.

However, to use it, a promise have to be returned in your save function. `Restangular` does by default or you can create one with `$q`.

View it in action:

<iframe style="border: 1px solid #bbb;width: 100%; height: 400px" src="http://embed.plnkr.co/PNQ6Ih/?t=run" frameborder="0" allowfullscreen="allowfullscreen">Loading plunk...</iframe>
