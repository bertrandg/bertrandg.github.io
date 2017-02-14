---
layout: post
title: "Ngrx/effects: Complex stream with nested actions depending on another"
description: ""
modified: 2017-02-10
tags: [angular, ngrx, rxjs]
image:
  feature: abstract-10.jpg
comments: true
share: true  
---


Here is a case I faced using the great [ngrx/store](https://github.com/ngrx/store) & [effects](https://github.com/ngrx/effects) libraries to take care of my data.

Working only with streams to manage all side effects is hard at first but once you got the declic and start to tame more and more `rxjs` operators, you are the king of the world!
<br>


### ➜ Simple need to start:

Imagine you have these needs:

- When **ACTION_X** --> `callApiX()` --> **ACTION_X_SUCCESS** / **ACTION_X_FAIL**
- When **ACTION_Y** --> `callApiY()` --> **ACTION_Y_SUCCESS** / **ACTION_Y_FAIL**

Nothing special here:

An `action` > an HTTP request > another `action` depending on success or not.

In case of success, results are stored inside state using `reducers`.


This corresponds to these `@effects` code:

<script src="https://gist.github.com/bertrandg/6207c52d8d58d4ca6f2ed924131a65c9.js"></script>
<br>


### ➜ Let's complexify the need:

Now imagine that, before calling `callApiY()`, you need to be sure that `callApiX()` has been called successfully.
And if it hasn't, call it first, then call `callApiY()` and finish with **ACTION_Y_SUCCESS** if both calls succeed (else **ACTION_Y_FAIL**).

Here is a simple workaround to solve it elegantly:

<script src="https://gist.github.com/bertrandg/7efbc924d60164d05daaa7d0b4f89267.js"></script>

