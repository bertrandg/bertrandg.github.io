---
layout: post
title: "HTML5 DnD: disable drop on input and textarea"
description: ""
modified: 2014-08-21
tags: [directive, angularjs, plunker, drag and drop, html5]
image:
  feature: abstract-10.jpg
comments: true
share: true  
---

Add this simple directive on input/textarea in your angular app and users won't be able to drop draggable html5 elements on it.

{% highlight javascript %}
    app.directive('dropDisable', function() {
    return {
        restrict: 'A',
        link: function(scope, element, attrs) {
            var handler = function(event) {
                event.preventDefault();
                return false;
            }
            element.on('dragenter', handler);
            element.on('dragover', handler);
            element.on('drop', handler);
        }
    };
});
{% endhighlight %}

View it in action:

<iframe style="border: 1px solid #bbb;width: 100%; height: 700px" src="https://embed.plnkr.co/8B0Qbl/?t=run" frameborder="0" allowfullscreen="allowfullscreen">Loading plunk...</iframe>
