---
layout: post
title: "AngularJS debug: Track your watchers and expressions"
description: ""
modified: 2014-06-09
tags: [angularjs, debug]
image:
  feature: abstract-10.jpg
comments: true
share: true
---


## \[Edit\] Don't return 100% watchers in several cases.. :( <br>Use [Chrome Batarang extension](https://chrome.google.com/webstore/detail/angularjs-batarang/ighdmehidhipcmcojjgiloacoafjmpfk) to do that better.

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

Count watchers and output expressions inside the current selected DOM element in the HTML Firebug panel (Elements panel inside Chrome). In fact, I always use it like that.<br>
Switching between Console panel and HTML/Elements panel is the best way to know which parts of your app are gourmand and it's an interesting way to 'look under the hood' of famous angular app too! :) 

{% highlight js %}
debugWatchers('[ng-controller="MenuCtrl"]')
{% endhighlight %}

Count watchers inside MenuCtrl, be careful, it will works only if the controller was include with an `ng-controller`, not if a directive included it.

{% highlight js %}
debugWatchers('my-custom-directive:nth-child(0)', true)
{% endhighlight %}

Count watchers and output expressions inside the first instance of `myCustomDirective`, it will works only if there is not a `replace: true` inside it.

{% highlight js %}
debugWatchers('html body.ng-scope div#global div div.subContent div.ng-scope div#panel.ng-scope div.table-container table.ng-scope tbody tr.ng-scope:nth-child(2)', true)
{% endhighlight %}

Idem, but this case I used the HTML panel of Firebug (Elements panel inside Chrome) to select the element I want and `Right-click` > `Copy CSS path`. Sometimes a `nth-child()` is needed to complete the `selector`.

<!---
## <i class="icon icon-asterisk"></i> Results: ##

1. Select your target in HTML panel.
2. Execute `debugWatchers($0, true)` in Console panel.

<figure class="half">
    <a href="/images/debug_watchers/screen_firebug_1.png"><img src="/images/debug_watchers/screen_firebug_1.png" alt=""></a>
    <a href="/images/debug_watchers/screen_firebug_2.jpg"><img src="/images/debug_watchers/screen_firebug_2.jpg" alt=""></a>
</figure>
-->

This code haven't been tested in all situations, tell me if you have problems!
