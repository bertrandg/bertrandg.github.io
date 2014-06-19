---
layout: post
title: Syntax Highlighting Post
description: "Demo post displaying the various ways of highlighting code in Markdown."
modified: 2013-08-20
tags: [sample post, code, highlighting]
image:
  feature: abstract-10.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
comments: true
share: true  
---

[Syntax highlighting](http://en.wikipedia.org/wiki/Syntax_highlighting) is a feature that displays source code, in different colors and fonts according to the category of terms. This feature facilitates writing in a structured language such as a programming language or a markup language as both structures and syntax errors are visually distinct. Highlighting does not affect the meaning of the text itself; it is intended only for human readers.

### Code


{% highlight ruby %}
parameters:
    routes_to_convert_html: ["api_datapoint_get_datapointsX", "spa_path_js"]
	
services:
    acme.listener.kernel.convert_html:
        class: ADTM\DatasourceBundle\Listener\ConvertToHtmlResponseListener
        arguments: ['%routes_to_convert_html%']
        tags:
            - { name: kernel.event_listener, event: kernel.response }
{% endhighlight %}

{% highlight php %}
<?php
namespace ADTM\DatasourceBundle\Listener;

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

		// $request->getMethod()
		// $request->getRequestUri()
		// var_dump($request->attributes->get('_route'));die;

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


Or you can simply use the sf profiler history by clicking on "View last 10" button.

And to test your POST/PUT/DELETE requests, [Postman](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm) is a must-have tool.


