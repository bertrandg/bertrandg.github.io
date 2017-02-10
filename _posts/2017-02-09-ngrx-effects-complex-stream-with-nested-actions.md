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


### ➜ Simple need to start:

Imagine you have these needs:

- When ACTION_X --> callApiX() --> ACTION_X_SUCCESS or ACTION_X_FAIL
- When ACTION_Y --> callApiY() --> ACTION_Y_SUCCESS or ACTION_Y_FAIL

Nothing special here:

An `action` > an HTTP request > another `action` depending on success or not.
In case of success, results are stored inside state using `reducers`.

This corresponds to these `@effects` code:

{% highlight javascript %}
@Effect() actionX$ = this.updates$
    .ofType('ACTION_X')
    .map(toPayload)
    .switchMap(payload => this.api.callApiX(payload)
        .map(data => ({type: 'ACTION_X_SUCCESS', payload: data}))
        .catch(err => Observable.of({type: 'ACTION_X_FAIL', payload: err}))

@Effect() actionY$ = this.updates$
    .ofType('ACTION_Y')
    .map(toPayload)
    .switchMap(payload => this.api.callApiY(payload)
        .map(data => ({type: 'ACTION_Y_SUCCESS', payload: data}))
        .catch(err => Observable.of({type: 'ACTION_Y_FAIL', payload: err}))
{% endhighlight %}


### ➜ Let's complexify the need:

Now imagine that, before calling callApiY(), you need to be sure that callApiX() has been called successfully.
And if it hasn't, call it first, then call callApiY() and finish with ACTION_Y_SUCCESS if both calls succeed (else ACTION_Y_FAIL).

Here is a simple workaround to solve it elegantly:

{% highlight javascript %}

// Nothing changed here, works as previously.
@Effect() actionX$ = this.updates$
    .ofType('ACTION_X')
    .map(toPayload)
    .switchMap(payload => this.api.callApiX(payload)
        .map(data => ({type: 'ACTION_X_SUCCESS', payload: data}))
        .catch(err => Observable.of({type: 'ACTION_X_FAIL', payload: err}))


// Here is the magic.
@Effect() actionY$ = this.updates$
    .ofType('ACTION_Y')
    .map(toPayload)
    // Retrieve part of the current state.
    .withLatestFrom(this.store.select(state => state.someBoolean))
    .switchMap(([payload, someBoolean]) => {
        // Function calling callApiY() and acting accordingly.
        const callHttpY = v => {
            return this.api.callApiY(v)
                .map(data => ({type: 'ACTION_Y_SUCCESS', payload: data}))
                .catch(err => Observable.of({type: 'ACTION_Y_FAIL', payload: err}));
        }
        
        // If data from store indicates that callApiX() has already been called with success
        // Then directly call callApiY().
        if(someBoolean) {
            return callHttpY(payload);
        }

        // Otherwise emit action triggering callApiX()
        // Then wait for first response action (success or fail)
        // And act accordingly.
        return Observable.of({type: 'ACTION_X', payload})
            .merge(
                this.updates$
                    .ofType('ACTION_X_SUCCESS', 'ACTION_X_FAIL')
                    .first()
                    .switchMap(action => {
                        if(action.type === 'ACTION_X_FAIL') {
                            return Observable.of({type: 'ACTION_Y_FAIL', payload: 'Because ACTION_X failed.'});
                        }
                        return callHttpY(payload);
                    })
            );
    });
{% endhighlight %}
