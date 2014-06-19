---
layout: post
title: Get Symfony2 Profiler when testing Get REST api
description: ""
modified: 2013-08-20
tags: [sample post, code, highlighting]
image:
  feature: abstract-10.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
comments: true
share: true  
---

Sometimes you want to know in live, how many Doctrine queries, memory usage, or others things while testing your Get REST api, here is a simple way to have your profiler.

The condition to have the profiler added is to output HTML, so we output HTML.

### Code


{% highlight ruby %}
parameters:
    routes_to_convert_html: ["api_datasource_get_source", "api_datasource_get_customer", "api_datasource_get_history"]
	
services:
    dtm.datasource.listener.kernel.convert_html:
        class: CTM\DatasourceBundle\Listener\ConvertToHtmlResponseListener
        arguments: ['%routes_to_convert_html%']
        tags:
            - { name: kernel.event_listener, event: kernel.response }
{% endhighlight %}

{% highlight php %}
<?php
namespace CTM\DatasourceBundle\Listener;

use Symfony\Component\HttpKernel\Event\FilterResponseEvent;

class ConvertToHtmlResponseListener {
    protected $routes;

    public function __construct(Array $routes)
    {
        $this->routes = $routes;
    }
    
	public function onKernelResponse(FilterResponseEvent $event) {
		
		if (!$event->isMasterRequest()) {
			return;
		}
		
		$request = $event->getRequest();
		
		if (!in_array($request->attributes->get('_route'), $this->routes)) {
			return;
		}
		
		if (!$request->headers->has('Accept') || (false === strpos($request->headers->get('Accept'), 'text/html'))) {
			return;
		}

		$response = $event->getResponse();
		switch ($request->getRequestFormat()) {
			case 'json':
				$prettyprint_lang = 'js';
				$content = json_encode(json_decode($response->getContent()), JSON_PRETTY_PRINT);
				break;

			case 'xml':
				$prettyprint_lang = 'xml';
				$content = $response->getContent();
				break;

			default:
				return;
		}

		$response->setContent(
			'<html><body>' .
				'<pre class="prettyprint lang-' . $prettyprint_lang . '">' .
					htmlspecialchars($content) .
				'</pre>' .
				'<script src="https://google-code-prettify.googlecode.com/svn/loader/run_prettify.js"></script>' .
			'</body></html>'
		);
		
		$response->headers->set('Content-Type', 'text/html; charset=UTF-8');
		$request->setRequestFormat('html');
		$event->setResponse($response);
	}
}
{% endhighlight %}

Solution found here: [Paul Ferrett - Stack Overflow](http://stackoverflow.com/questions/21555697/how-to-display-the-symfony-profiler-for-api-request-made-in-the-browser)

To check your `POST` `PUT` `DELETE` requests, use [Chrome Postman extension](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm) (a must-have tool), and then use the symfony2 Profiler history by clicking on "View last 10" button.

