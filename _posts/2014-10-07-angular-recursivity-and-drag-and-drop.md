---
layout: post
title: "Advanced Drag and Drop on recursive table representation"
description: ""
modified: 2014-08-27
tags: [drag and drop, html5, directive, angularjs, plunker, recursive]
image:
  feature: abstract-10.jpg
comments: true
share: true  
---

### [This post follow and upgrade this older: [Recursivity exercice with AngularJS](http://bertrandg.github.io/angular-recursivity-exercice/)]

#### <i class="icon icon-asterisk"></i> Drag & drop:
I used the native html5 drag & drop api.<br>
So it doesn't work on mobile/tablet devices: [http://caniuse.com/#feat=dragndrop](http://caniuse.com/#feat=dragndrop)

#### <i class="icon icon-asterisk"></i> Animations:
I used the nice css library [Animate.css](http://daneden.github.io/animate.css/)<br>
It is very easy to use it with angular and ngAnimate, see that (SASS syntax):
{% highlight scss %}
.li-condition {
    &.ng-enter {
        @include animation(pulse 0.4s);
    }
    &.ng-leave {
        @include animation(zoomOut 0.4s);
    }
    &.ng-move {
        @include animation(zoomIn 0.4s);
    }
}

.li-group {
    &.ng-enter {
        @include animation(flipInX 0.4s);
    }
    &.ng-leave {
        @include animation(zoomOut 0.4s);
    }
    &.ng-move {
        @include animation(zoomIn 0.4s);
    }
}
{% endhighlight %}

#### <i class="icon icon-asterisk"></i> Angularjs 1.3:
Note that the new 1.3 ngRepeat `as` syntax is used there:<br>
{% highlight html %}
<li ng-repeat="element in data.elements | orderBy:'position' as filteredData track by element.id">...</li>
{% endhighlight %}
Like this, I can use the `filteredData` variable inside the ngRepeat node without recalculating it.


### <i class="icon icon-asterisk"></i> Try it:

<iframe style="border: 1px solid #bbb;width: 100%; height: 900px" src="http://embed.plnkr.co/S1wCzx/?t=run" frameborder="0" allowfullscreen="allowfullscreen">Loading plunk...</iframe>

### <i class="icon icon-asterisk"></i> There is a graphical bug/weird behavior linked to ngMove:

You can reproduce it by move an element down on same level, animations occurs not on the moved element but on all others between old and new position..

I found this [issue](https://github.com/angular/angular.js/issues/5160) on github with this example who explains it very well:<br>
[http://plnkr.co/edit/4yRkLWbsU57YxrYOrWUQ?p=preview](http://plnkr.co/edit/4yRkLWbsU57YxrYOrWUQ?p=preview)<br>
But there is no solution yet.
