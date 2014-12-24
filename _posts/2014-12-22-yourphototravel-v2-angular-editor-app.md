---
layout: post
title: "YourPhotoTravel, version 2 launched"
description: ""
modified: 2014-12-22
tags: [yourphototravel, html5, angularjs, symfony2, works]
image:
  feature: abstract-10.jpg
comments: true
share: true  
---

[YourPhotoTravel](http://www.yourphototravel.com/) is a social network about travel experiences, we launched it first 2 years ago.

<a href="http://www.yourphototravel.com/" target="_blank"><img src="{{site.baseurl}}/images/ypt_version2/home.jpg"></a>

We worked hard with [Jerôme](http://fr.linkedin.com/in/jeromefath) and [Yoann](http://fr.linkedin.com/in/ylupe) to release this new version. Members can now create and share their Etins more simply than ever! *(An Etin is the name we choosed to speak about a trip, trek, cruise,.. on the service.)*

The service is only in French but English is coming soon..

This second version rocks for several reasons:

* A new design 100% responsive, you can use it on tablets and phones (All Etin editor features included).
* An easier navigation with less and clearer pages.
* New features like the ability to build a real book based on your Etin, visualize it with an online flipbook and order it (only France delivery currently)!
* Use of all last frameworks version (Symfony2, AngularJS,..) which improves performances and functionnalities.

## The new Etin editor, an SPA using AngularJS:

I'm gonna talk more about the new Etin editor I coded.
The last one used backboneJS, the widest used frontend framework at this time.
And Flash was still alive and even needed for some fonctionnalities like image resizing on client side, photo editing, and upload progress.

Now, 2 years and a half later, web development changed a lot.<br>
Browsers are almost all evergreen, more and more html5 apis are usable (geoloc, history, canvas, drag&drop,..) and AngularJS has emerged and became the new standart frontend framework.

I coded all in Javascript with the (big) help of AngularJS and we have an editor fully usable on tablets/mobiles.<br>Perfect for travellers!

### Development workflow:

* Firstly, I coded all the Rest api with [FosRestBundle](https://github.com/FriendsOfSymfony/FOSRestBundle) (the whole service uses [Symfony2](http://symfony.com/)) and a simple html page to test it (AngularJS is wonderfull to quickly code a basic interface). Do it first is a best practise or simply logic.

* [Bower](http://bower.io/) is a good friend to manage your frontend libraries and dependencies, like [Composer](https://getcomposer.org/) for PHP.

* For the style, SASS is needed, impossible to work without nowadays. Compass was not essential. Note this lib [sass-css-importer](https://github.com/chriseppstein/sass-css-importer) to import CSS files inside SCSS (don't know why not a default feature, LESS does it..).

* [Grunt](http://gruntjs.com/) is a tasks runner with many many plugins for everythings. I build 3 tasks (`ypt-dev` / `ypt-watch` / `ypt-prod`) to suit my needs. JS files are [ngAnnotate](https://github.com/mzgol/grunt-ng-annotate), concat and uglify, my templates are added to `$templateCache` with [ngtemplates](https://github.com/ericclemmons/grunt-angular-templates) and CSS are minified.

### Key features:

* Use AngularJS 1.3 with latest features like `$compileProvider.debugInfoEnabled(false)`, one time binding, `as` inside ngRepeat,..
* Images resized on client side, sended with upload progress, and added to the interface.
* Image edition with canvas and KineticJs. You can crop it and apply filters.
* Native HTML5 drag and drop at several places.
* Mobiles/tablets version (small resolution) with CSS media queries and special features (avoid native DnD).
* Nested routing with ui-router using clean HTML5 urls (no more # inside).
* Add your places on the map with autocomplete or by dragging markers. Edit your path or import your tracking files (KML and GPX support).
* Working with 100% REST api but use the classic Symfony2 authentification system (no token), you can't access the webapp if you are not logged (of course all rest are secured too). 
* Translations: I choosed to keep the Symfony2 yml file (because my app is inside a dedicated bundle) and use grunt tasks to convert it to json with [grunt-convert](https://github.com/assemble/grunt-convert) and then to js with [grunt-json](https://github.com/wilsonpage/grunt-json).
Finally, [angular-translate](http://angular-translate.github.io/) brings it gracefully in the application.


### All libraries used:

Here are all frontend vendors used (`bower list --offline`).

The goal wasn't to use as much as possible but it's a fact that with angular ecosystem, more and more libs are well-tested and works really well together.

{% highlight php %}
├─┬ AngularJS-Toaster#0.4.8
│ ├── angular#1.3.2
│ └─┬ angular-animate#1.3.2
│   └── angular#1.3.2
├── angular#1.3.2
├── angular-animate#1.3.2
├─┬ angular-bootstrap#0.11.2
│ └── angular#1.3.2
├─┬ angular-google-maps#2.0.7
│ ├── angular#1.3.2
│ └── lodash#2.4.1
├── angular-i18n#1.3.1
├── angular-translate#2.4.2
├─┬ angular-ui-router#0.2.12
│ └── angular#1.3.2
├── animate.css#3.2.0
├── blueimp-canvas-to-blob#2.1.1
├── blueimp-load-image#1.13.0
├── bootstrap-sass-official#3.1.1+2
├── jquery#2.1.1
├── kineticjs#5.1.0
├─┬ ladda#0.9.4
│ └── spin.js#2.0.1
├── lodash#2.4.1
├── ng-file-upload#1.6.12
├── nprogress#0.1.6
└─┬ restangular#1.4.0
  ├── angular#1.3.2
  └── lodash#2.4.1
{% endhighlight %}

### Libraries feedbacks:

* **angular-ui-router**: A must-have to build nested routes in your app.
* **lodash**: Similar to underscore, it avoids a lot of loops.
* **animate.css**: A small CSS3 animation lib who works nice with **angularjs**.
* **blueimp-load-image**: Resize JPG images on client side.
* **angular-google-maps**: Good but use carefully, I used polylines with it and performances sucked.. Finally, I get the map instance and add it "manually".
* **kineticjs**: An old but heavy library to work with canvas, documentation not really good and up to date.. I don't think I'm gonna use it for my next project.
* **angular-bootstrap**: Usefull to work with **bootstrap-sass-official**, `$modal` service wich return a promise is really cool.
* **restangular**: A must-have for your api calls.
* **ng-file-upload**: Sadly, **restangular** doesn't handle progress event on upload request because it uses `$http`, [issue here](https://github.com/angular/angular.js/issues/1934). This lib handles it.

### Screenshots:

<figure class="half">
    <a href="{{site.baseurl}}/images/ypt_version2/screen1.jpg" target="_blank"><img src="{{site.baseurl}}/images/ypt_version2/screen1.jpg" alt=""></a>
    <a href="{{site.baseurl}}/images/ypt_version2/screen2.jpg" target="_blank"><img src="{{site.baseurl}}/images/ypt_version2/screen2.jpg" alt=""></a>
</figure>
<figure class="half">
    <a href="{{site.baseurl}}/images/ypt_version2/screen3.jpg" target="_blank"><img src="{{site.baseurl}}/images/ypt_version2/screen3.jpg" alt=""></a>
    <a href="{{site.baseurl}}/images/ypt_version2/screen4.jpg" target="_blank"><img src="{{site.baseurl}}/images/ypt_version2/screen4.jpg" alt=""></a>
</figure>
<figure class="half">
    <a href="{{site.baseurl}}/images/ypt_version2/screen5.jpg" target="_blank"><img src="{{site.baseurl}}/images/ypt_version2/screen5.jpg" alt=""></a>
    <a href="{{site.baseurl}}/images/ypt_version2/screen6.jpg" target="_blank"><img src="{{site.baseurl}}/images/ypt_version2/screen6.jpg" alt=""></a>
</figure>

