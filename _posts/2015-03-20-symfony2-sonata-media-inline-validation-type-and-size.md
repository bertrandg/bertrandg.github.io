---
layout: post
title: "SonataMediaBundle: Add mimeType and size validation on Media file"
description: ""
modified: 2015-03-20
tags: [symfony2, sonata]
image:
  feature: abstract-10.jpg
comments: true
share: true  
---


If you have errors like **"No media linked to the object"** (Field error) and **"The file could not be found."** (form error) in your SonataAdmin form or Symfony2 form when you are trying to add File constraints validation to SonataMedia, you are probably in the right place!


## What I wanted


I needed to have a job application form containing 2 file fields (a CV and a motivation letter) on a Symfony2 website.
These 2 files should be PDF or DOC lesser than 4MB.

All job applications should be listed on the backoffice and files downloadable.
I already use the great Sonata bundles suite (Admin + Media + Formatter) and wanted to manage them with it.


## First try with errors


So I first write my JobApply entity properties like these:
{% highlight php%}
<?php # Sapiens\Bundle\JobBundle\Entity\JobApply

/**
 * @Assert\NotBlank()
 * @Assert\File(
*     maxSize = "5m",
*     mimeTypes = {"application/pdf", "application/x-pdf", "application/msword"},
* )
 * @ORM\ManyToOne(targetEntity="Application\Sonata\MediaBundle\Entity\Media", cascade={"remove", "persist"}, fetch="LAZY")
 */
protected $cv;

/**
 * @Assert\NotBlank()
 * @Assert\File(
*     maxSize = "5m",
*     mimeTypes = {"application/pdf", "application/x-pdf", "application/msword"},
* )
 * @ORM\ManyToOne(targetEntity="Application\Sonata\MediaBundle\Entity\Media", cascade={"remove", "persist"}, fetch="LAZY")
 */
protected $letter;
{% endhighlight %}

With this form:
{% highlight php%}
<?php # Sapiens\Bundle\JobBundle\Form\Type\JobApplyType

public function buildForm(FormBuilderInterface $builder, array $options)
{
    $builder
    ->add('cv', 'sonata_media_type', array(
            'label'  => 'CV* (doc/pdf)',
            'required'  => true,
            'provider' => 'sonata.media.provider.file',
            'context'  => 'apply'
    ))
    ->add('letter', 'sonata_media_type', array(
            'label'  => 'Lettre de motivation* (doc/pdf)',
            'required'  => true,
            'provider' => 'sonata.media.provider.file',
            'context'  => 'apply'
    ))
    ->add('save', 'submit', array(
            'label'  => 'Envoyer',
    ));
}
{% endhighlight %}
But it can't works like that, errors **"No media linked to the object"** and **"The file could not be found."** appears because I tried to add File validation to a Sonata media entity!


## Solution with Sonata InlineConstraint


After navigating throught Sonata CoreBundle/AdminBundle/MediaBundle documentation and the few stackoverflow/blog ressources availables I came to this solution: Using Sonata InlineConstraint


### 1. Create a service which will be called to validate your entity:
{% highlight yaml %}
# Sapiens/Bundle/JobBundle/Resources/config/services.yml

services:
    sapiens_job.validation.job_apply:
        class: Sapiens\Bundle\JobBundle\Form\Type\JobApplyType
{% endhighlight %}


### 2. Add the InlineConstraint class constraint to your bundle’s validation configuration file:

{% highlight yaml %}
# Sapiens/Bundle/JobBundle/Resources/config/validation.yml

Sapiens\Bundle\JobBundle\Entity\JobApply:
    constraints:
        - Sonata\AdminBundle\Validator\Constraints\InlineConstraint:
            service: sapiens_job.validation.job_apply
            method: validate        
{% endhighlight %}
You can see that I call the `validate` function on the service created previously.


### 3. Add the entity media properties like this:

{% highlight php%}
<?php # Sapiens\Bundle\JobBundle\Entity\JobApply

/**
 * @var \Application\Sonata\MediaBundle\Entity\Media
 * @ORM\ManyToOne(targetEntity="Application\Sonata\MediaBundle\Entity\Media", cascade={"remove", "persist"}, fetch="LAZY")
 */
protected $cv;

/**
 * @var \Application\Sonata\MediaBundle\Entity\Media
 * @ORM\ManyToOne(targetEntity="Application\Sonata\MediaBundle\Entity\Media", cascade={"remove", "persist"}, fetch="LAZY")
 */
protected $letter;
{% endhighlight %}


### 4. Build your validate function:

{% highlight php%}
<?php # Sapiens\Bundle\JobBundle\Form\Type\JobApplyType

public function validate(ErrorElement $errorElement, JobApply $jobApply)
{
    $errorElement
        ->with('cv.binaryContent')
            ->assertNotNull(array())
            ->assertNotBlank()
            ->assertFile(array('maxSize' => '3000000', 'mimeTypes' => array("application/pdf", "application/x-pdf", "application/msword")))
        ->end()
        ->with('letter.binaryContent')
            ->assertNotNull(array())
            ->assertNotBlank()
            ->assertFile(array('maxSize' => '3000000', 'mimeTypes' => array("application/pdf", "application/x-pdf", "application/msword")))
        ->end();
}
{% endhighlight %}
*Note 1*:<br>
You can use `$errorElement->addConstraint(new NotBlank())` syntax instead of `$errorElement->assertNotBlank()` for all constraints.
Validator component will be used in all cases.

*Note 2*:<br>
You can see that my validation service class is my form type class.
It's not a best practise but like that, I have my `validate` function where my form is build..


## Result:


<figure class="full image-popup">
    <!---<a href="{{site.baseurl}}/images/sonata_validation/states.jpg" target="_blank">-->
    <img src="{{site.baseurl}}/images/sonata_validation/states.jpg" alt="">
    <!---</a>-->
</figure>


*Note*:<br>
I used [Jasny Bootstrap](http://jasny.github.io/bootstrap/) to skin file input. It was not simple and maybe the subject of a next post.

*Sonata Documentation*:<br>
[http://sonata-project.org/bundles/admin/master/doc/reference/conditional_validation.html](http://sonata-project.org/bundles/admin/master/doc/reference/conditional_validation.html)<br>
[http://sonata-project.org/bundles/doctrine-orm-admin/master/doc/reference/form_field_definition.html](http://sonata-project.org/bundles/doctrine-orm-admin/master/doc/reference/form_field_definition.html)




