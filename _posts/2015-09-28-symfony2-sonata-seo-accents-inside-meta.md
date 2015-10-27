---
layout: post
title: "Symfony2: Accents inside SonataSeoBundle meta tags"
description: ""
modified: 2015-09-28
tags: [symfony2, seo]
image:
  feature: abstract-10.jpg
comments: true
share: true  
---

### Problem

When adding meta tags using [SonataSeoBundle](https://github.com/sonata-project/SonataSeoBundle) directly from your `config.yml`:

{% highlight ruby %}
sonata_seo:
    page:
        metas:
            name:
                keywords:    'été, plage'
                description: 'Un été à la plage!''
{% endhighlight %}

Or in your `controller` for a specific route:

{% highlight ruby %}
$this->container->get('sonata.seo.page')->addMeta('name', 'keywords', "été, plage");
$this->container->get('sonata.seo.page')->addMeta('name', 'description', "Un été à la plage!");
{% endhighlight %}

Accented characters outputs html special characters due to `htmlentities` call inside the twig extension:

{% highlight html %}
<meta name="keywords" content="&eacute;t&eacute;, plage" />
<meta name="description" content="Un &eacute;t&eacute; &agrave; la plage!" />
{% endhighlight %}

### Solution

To prevent that, you have to override `SeoExtension` twig extension with your own like this:

{% highlight ruby %}
sonata.seo.twig.extension:
    class: MyCompany\Bundle\AppBundle\Twig\Extension\SeoExtension
    arguments: ["@sonata.seo.page", "UTF-8"]
    public: false
    tags:
        - { name: twig.extension }
{% endhighlight %}

{% highlight php %}
<?php
namespace MyCompany\Bundle\AppBundle\Twig\Extension;

use Sonata\SeoBundle\Seo\SeoPageInterface;
use Sonata\SeoBundle\Twig\Extension\SeoExtension as BaseSeoExtension;

class SeoExtension extends BaseSeoExtension
{
    public function getMetadatas()
    {
        $html = '';
        foreach ($this->page->getMetas() as $type => $metas) {
            foreach ((array) $metas as $name => $meta) {
                list($content, $extras) = $meta;

                if (!empty($content)) {
                    $html .= sprintf("<meta %s=\"%s\" content=\"%s\" />\n",
                        $type,
                        $this->normalize($name),
                        $this->normalize($content)
                    );
                } else {
                    $html .= sprintf("<meta %s=\"%s\" />\n",
                        $type,
                        $this->normalize($name)
                    );
                }
            }
        }

        return $html;
    }

    private function normalize($string)
    {
    	return str_replace( '"', "'", strip_tags($string) );
    }
}
{% endhighlight %}

<br>
Note:<br>
Keywords meta tag is useless nowadays [https://chrisedwards.me/seo/keyword-meta-tag-google/](https://chrisedwards.me/seo/keyword-meta-tag-google/)
