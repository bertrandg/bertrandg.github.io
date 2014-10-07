---
layout: post
title: "UI-Router: Memorize nested routes inside tab"
description: ""
modified: 2014-08-21
tags: [angularjs, plunker, ui-router, cacheFactory]
image:
  feature: abstract-10.jpg
comments: true
share: true  
---

Imagine an AngularJS app with 3 tabs.
On the second one, users can select a car brand and then a car model.

Each tabs, brands and models have a state/route like this:

| <b>STATES</b> | <b>URLS</b> |
| `app.tab1` | `#/tab1` |
| `app.tab2` | `#/tab2` |
| `app.tab2.brand` | `#/tab2/brand/:idBrand` |
| `app.tab2.brand.model` | `#/tab2/brand/:idBrand/model/:idModel` |
| `app.tab3` | `#/tab3` |

With the default behavior, when we click on tab2, we arrive on tab2 but no brand is selected.
Then when we click on a brand, no model is selected.

In my case this was really not optimal, I wanted to have directly:

 - when I click on tab2 the first brand and the first model of it or last viewed.
 - when I click on a brand the first model or the last viewed.

Solution came from catching `$stateChangeStart` and redirect to another route/state.

View it in action:

<iframe style="border: 1px solid #bbb;width: 100%; height: 700px" src="http://embed.plnkr.co/FHA4QR/?t=run" frameborder="0" allowfullscreen="allowfullscreen">Loading plunk...</iframe>

To test this example in real conditions with browser `back` and `forward` buttons [click here](http://plnkr.co/edit/FHA4QR?p=preview) and then, in the preview panel, click on the icon 'enlarge' to view the url changing in the address bar.

More about this: [https://github.com/angular-ui/ui-router/issues/178](https://github.com/angular-ui/ui-router/issues/178)