---
layout: post
title: Angular degugging: Track your watchers
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

[DRAFT]

Here is a quick way to know at any moment of your angular app lifecycle how many watchers are running and which expressions are parsed, you can test it before and after an action to view the difference.


## How it works ##

- Paste this code inside your console with your angular app running:
<script src="https://gist.github.com/bertrandg/6516282749ea7116f610.js"></script>

- Call `debugWatchers(selector, showExp)` inside the console
The first parameter is a `css selector` to target a DOM element on which you want to know how many watchers inside him.
The second parameter is a `boolean` to tell if you want to output `expressions` found.

> Warning, your selector have to return only one element!

> Maybe you have too much watchers to be listed in the console, you can extend le log limit of Firebug with Firefox:
> New tab > `about:config` > change this var: `extensions.firebug.console.logLimit = 1200`


## Example: ##

- `debugWatchers('[ng-controller="MenuCtrl"]')`

Return watchers number inside MenuCtrl, be careful, it will works only if the controller was include with an `ng-controller`, not if a directive included it.

- `debugWatchers('my-custom-directive:nth-child(0)', true)`

Return watchers number and all expressions inside the first instance of `myCustomDirective`, it will works only if there is not a `replace: true` inside it.

- `debugWatchers('html body.ng-scope div#global div div.subContent div.ng-scope div#panel.ng-scope div.table-container table.ng-scope tbody tr.ng-scope:nth-child(2)', true)`


Idem, but this case I used the html panel of Firebug to select the element I want and `Right-click` > `Copy CSS path`. Sometimes a `nth-child()` is needed to complete the `selector`.

## To do: ##

* Remove jQuery dependency..