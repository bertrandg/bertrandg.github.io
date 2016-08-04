---
layout: post
title: "Angular2: Custom validation using multiple form fields"
description: ""
modified: 2016-07-25
tags: [angular2, forms, validation]
image:
  feature: abstract-10.jpg
comments: true
share: true  
---

**[Post using Angular2 RC4 + @angular/forms module]**

Imagine you want to validate a field using another one with the last angular2 form API.
In fact you can't do that directly in a custom field validator but a really simple way to do it exists and this is what i'm gonna show you there.

## ➜ The form API

Here are the three differents form elements in Angular2:

- `FormControl`: A form entry, could be an `input[type=text]`, `textarea`, `select`,..
- `FormGroup`: Composed of `FormControl` and `FormGroup`/`FormArray`, each item contained got a string identifier. It could be an entire form or a subform.
- `FormArray`: Same as `FormGroup` but acts like an array (you can `push`, `insert`, or `removeAt`), useful for dynamic length (add a list of urls for example).

Each of these elements inherits from [`AbstractControl`](https://github.com/angular/angular/blob/2.0.0-rc.4/modules/%40angular/common/src/forms-deprecated/model.ts#L56-L258). And if you go check this abstract class, you will see it means all got these following properties:

- `pristine` / `dirty` / `touched` / `untouched` / `pending` / `valid` > Boolean showing state of the control/group/array.
- `valueChanges` / `statusChanges` > Observables to catch whatever changed in the control/group/array.
- `errors` > A key/value object with all errors (if errors it is) on the control/group/array.
- `value` > Typed `any` because it is an object litteral for `FormGroup`, an array for `FormArray` and anything for a `FormControl` (string for a `input[type=text]` for example).
- `validator` / `asyncValidator` > list of sync and async validators.

This module is awesome and allow to code complex things properly.
Like you can see, `FormGroup` have a `validator` property and this is the one to use to solve our problem.

## ➜ Slice our form

So create a subform containing only fields needed for your validation and add your custom validator as parameter like this:
{% highlight js %}
// Note: formBuilder.group(...) === new FormGroup(...)
this.rangeForm = formBuilder.group({
  'range':          new FormControl('', Validators.required),
  'specificValue':  new FormControl(null)
}, {
  validator: this.specificValueInsideRange.bind(this)
});
{% endhighlight %}

Then create your main form including it:
{% highlight js %}
this.mainForm = formBuilder.group({
  'rangeForm': this.rangeForm,
  // others controls
});
{% endhighlight %}

Th HTML markup looks like this:
{% highlight html %}
<form [formGroup]="mainForm" (ngSubmit)="send()" novalidate>
	<div [formGroup]="rangeForm">
		<select formControlName="range">
			<option value="">Select a range</option> 
			<option *ngFor="let r of listRanges" [value]="r.id">{{ r.label }}</option> 
		</select>
		<input formControlName="specificValue" type="number"/>
	</div>
	<!-- Others controls -->
	<button type="submit" [disabled]="!mainForm.valid">SEND</button>
</form>
{% endhighlight %}

And finally, here is our custom validator accessing our 2 `FormControl` value to check if it fit well:
{% highlight js %}
specificValueInsideRange(group: ControlGroup) {
  const range = this.listRanges.find(r => r.id === Number(group.value.range));
  if(range && (group.value.specificValue < range.min || group.value.specificValue > range.max)) {
    return {
      outsideRange: true
    };
  }
}
{% endhighlight %}

Here it is, we got our custom fields validation, the `specificValueInsideRange` validator function will be called anytime something change inside `rangeForm`.

## ➜ Demo

You can check this example in the following plunker:
<iframe style="border: 1px solid #bbb;width: 100%; height: 600px" src="https://embed.plnkr.co/b6ddFy/?t=run" frameborder="0" allowfullscreen="allowfullscreen">Loading plunk...</iframe>
