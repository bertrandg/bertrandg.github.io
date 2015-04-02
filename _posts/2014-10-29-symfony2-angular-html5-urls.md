---
layout: post
title: "Symfony2 and AngularJS: Pretty HTML5 urls (without #)"
description: ""
modified: 2015-04-02
tags: [html5, angularjs, symfony2, history api, ui-router]
image:
  feature: abstract-10.jpg
comments: true
share: true  
---

In a current project, I have an Angular application inside a big Symfony2 website.

So the SPA (Single Page Application) homepage is a Symfony route which lead to a Twig template.


### <i class="icon icon-asterisk"></i> Why use HTML5 urls?

I used the great [UI-Router](https://github.com/angular-ui/ui-router) in my application and initially let the `#` inside my routes..

But when I discovered how it's simple to upgrade to HTML5 History API with Angular, I changed it immediatly and you should do the same! :)

Imagine these routes in a website (parts after `#` are SPA urls):

* http://www.mywebsite.com/editor/projects/51
* http://www.mywebsite.com/editor/projects/51/#/stories/16
* http://www.mywebsite.com/editor/projects/51/#/stories/16/tasks/82

Now with HTML5 History API:

* http://www.mywebsite.com/editor/projects/51
* http://www.mywebsite.com/editor/projects/51/stories/16
* http://www.mywebsite.com/editor/projects/51/stories/16/tasks/82

Users can directly access any of these but **don't care** and **don't have to know** when they **really change "pages"** or **when a change is done inside the SPA** (In others words when it's Symfony2 routing or Angular routing which is involved).

They can always copy/paste urls and use next/prev browser buttons.


### <i class="icon icon-asterisk"></i> What on Angular side?

Not a lot of things, just tell Angular you want HTML5 urls and add `base` node in the `header` to let him know where the SPA url begin inside the full url (because `#` isn't there anymore to do the separation).

{% highlight js %}
appProject.config(function($locationProvider) {
    $locationProvider.html5Mode(true);
});
{% endhighlight %}

{% highlight html %}
<head>
    <meta charset="utf-8">
    <base href="{ { path('editor_edit', {id: project_id}) } }">
</head>
{% endhighlight %}


### <i class="icon icon-asterisk"></i> What on Symfony2 side?

We create two Symfony2 routes leading to the same controller/action.

The first is the SPA homepage and the second is used for SPA direct access routes.

{% highlight yaml %}
editor_edit:
    path: /editor/projects/{id}/
    defaults: { _controller: BibzEditorBundle:Editor:edit }
    requirements:
        id: \d+
        
editor_edit_angular:
    path: /editor/projects/{id}/{whatever}
    defaults: { _controller: BibzEditorBundle:Editor:edit }
    requirements:
        id: \d+
        whatever: .+ # Require to accept character "/" inside
{% endhighlight %}

The controller is really simple here:

{% highlight php %}
<?php
class EditorController extends Controller
{
    /**
     * @Template()
     * @Secure(roles="ROLE_USER")
     */
    public function editAction($id)
    {
        return array(
            'project_id' => $id
        );
    }
}
{% endhighlight %}


### <i class="icon icon-asterisk"></i> What about browsers support?

Don't worry for (few) people with old browsers not compatibles with HTML5 History API ([http://caniuse.com/#feat=history](http://caniuse.com/#feat=history)),
Angular manage it really well going back with `#` when not supported!



### <i class="icon icon-asterisk"></i> [Update] What about links inside the app?

Like Paul said in the comments, there was a missing part in this tutorial, links inside the app.<br>
[UI-Router](https://github.com/angular-ui/ui-router) with `uiSref` directive automatically generates the good `href` property depending on HTML5 mode activation or not.

Here is what happens in details:

- Imagine an angular application on this page:
{% highlight yaml %}
http://etin.yourphototravel.com/fr/editor/etin/6
{% endhighlight %}

- So the html base tag is:
{% highlight html %}
<base href="/fr/editor/etin/6/">
{% endhighlight %}

- We use UI-Router directive to directly point to a state:
{% highlight html %}
<a ui-sref="app.tab2.step({ idStep: step.id })">link</a>
{% endhighlight %}

- The directive generates this common SPA url in classic mode:
{% highlight html %}
<a href="#/chapter/7">link</a>
{% endhighlight %}

- And it builds this `href` for HTML5 urls mode :
{% highlight html %}
<a href="/fr/editor/etin/6/chapter/7">link</a>
{% endhighlight %}
Note that the **base tag href property is added** to have a full route from the domain.


