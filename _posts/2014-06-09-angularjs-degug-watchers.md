---
layout: post
title: "AngularJS debug: Track your watchers and expressions"
description: ""
modified: 2014-06-09
tags: [angularjs, debug]
image:
  feature: abstract-3.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
comments: true
share: true
---

Here is a quick way to know **how many** **watchers** are running and which **expressions** are parsed in a **specific part** and at **any moment** of your angular app lifecycle.

You can test it **before** and **after** an action to view the **difference**.


## <i class="icon icon-asterisk"></i> How it works ##

### Paste this code inside your console with your angular app running ###
<script src="https://gist.github.com/bertrandg/6516282749ea7116f610.js"></script>

### Call it from the console ###

{% highlight js %}
debugWatchers(selector, showExp)
{% endhighlight %}

`selector` could be a DOM element, a jQuery object or a css selector (String).

`showExp` is a boolean to tell if you want to output expressions foud or not.

> Warning, if your selector is a jQuery object, it should contain only one element. It is true for your css selector too!

> Warning, if you have too much watchers to be listed in the console, you can extend the log limit of Firebug (with Firefox):<br>
> Type url `about:config` and change this var: `extensions.firebug.console.logLimit = 1200`


## <i class="icon icon-asterisk"></i> Examples: ##

{% highlight js %}
debugWatchers($0, true)
{% endhighlight %}

Return watchers number and expressions inside the current selected DOM element in the HTML Firebug panel.<br>
In fact, this is the best way to use it. I always use it like that, switching between Console panel and HTML panel to know which parts of the app are gourmand.

{% highlight js %}
debugWatchers('[ng-controller="MenuCtrl"]')
{% endhighlight %}

Return watchers number inside MenuCtrl, be careful, it will works only if the controller was include with an `ng-controller`, not if a directive included it.

{% highlight js %}
debugWatchers('my-custom-directive:nth-child(0)', true)
{% endhighlight %}

Return watchers number and all expressions inside the first instance of `myCustomDirective`, it will works only if there is not a `replace: true` inside it.

{% highlight js %}
debugWatchers('html body.ng-scope div#global div div.subContent div.ng-scope div#panel.ng-scope div.table-container table.ng-scope tbody tr.ng-scope:nth-child(2)', true)
{% endhighlight %}


Idem, but this case I used the html panel of Firebug to select the element I want and `Right-click` > `Copy CSS path`. Sometimes a `nth-child()` is needed to complete the `selector`.

## To do: ##

* This is a 
* Remove jQuery dependency..