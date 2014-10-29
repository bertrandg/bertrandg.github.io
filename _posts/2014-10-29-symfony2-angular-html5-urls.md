---
layout: post
title: "Symfony2 & AngularJS: Pretty HTML5 urls (without #)"
description: ""
modified: 2014-10-29
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


