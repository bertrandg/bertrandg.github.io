
sequence actions depending on another


Imagine you have these effects:
When ACTION_X --> callApiX() --> ACTION_X_SUCCESS or ACTION_X_FAIL
When ACTION_Y --> callApiY() --> ACTION_Y_SUCCESS or ACTION_Y_FAIL

Nothing special here: An action > an HTTP call > another action depending on success or not.
In case of success, results are stored inside state using reducers.

Code:

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



Now imagine that, before calling callApiY(), you need to be sure that callApiX() has been called successfully.
And if it hasn't, call it first, then call callApiY() and finish with ACTION_Y_SUCCESS if both calls succeed else ACTION_Y_FAIL.

Here is a simple workaround to solve it elegantly:

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
    .withLatestFrom(this.store.select(state => state.someBoolean))
    .switchMap(([payload, someBoolean]) => {

        const callHttpY = v => {
            return this.api.callApiY(v)
                .map(data => ({type: 'ACTION_Y_SUCCESS', payload: data}))
                .catch(err => Observable.of({type: 'ACTION_Y_FAIL', payload: err}));
        }
    
        if(someBoolean) {
            return callHttpY(payload);
        }

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