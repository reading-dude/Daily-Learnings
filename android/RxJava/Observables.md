## Disposables
  
  * UseCase: If user is making a n/w call and presses back button to cancel it, we need something like disposable to dispose the n/w call 
  * clear() vs dispose() 
	 compositeDisposable().clear() - it cancels all the tasks, but can add more disposables and should be called in onstop only 
	  e.g if user goes to another app and comes back
	 compositeDisposable.dispose() - it cancels all the tasks, but cannot add more disposables abd should be called only in ondestory 
	 e.g if the user does not return
   
 ## Observables
 
  * Single - emits only single data so has only onSuccess, onSubscribe(), onError()
  * Completeable - tells whether a task is completed does not return obersavle return result e.g file download is completed
		onSubscribe(), onError(), onComplete()  
  * Maybe - might or might not give results, not much used in android
  * Flowable - In case observable emitting a lot of values which cannot be handled then its 
  throws `MissingBackpressureException`, flowable similar observable but it does not have `MissingBackpressureException` 
  so we use flowable 
    * (About Flowable buffer)[https://stackoverflow.com/questions/43934143/difference-between-backpressurestrategy-buffer-and-                  onbackpressurebuffer-operator]
    * With `BackpressureStrategy` it will ignore the items to handle the exception based on strategy provided
    * (Flowables) [https://android.jlelse.eu/rxjava-flowables-what-when-and-how-to-use-it-9f674eb3ecb5]
    
 ## Observable to livedata
 
 * dependency implementation "android.arch.lifecycle:reactivestreams:1.1.1" 
   Observable.frompublisher or Observable.toPublisher
   
 ## Creating Observable different methods 
 
 * `Observable.create()` - will be able to emit multiple items.
 * `Observable.just()` - only 10 items as it takes (Array[]) of size 10
 * `Observable.repeat()` repeats the emitted items based on mentioned number 
 * `Observable.range()` - can be replaced instead of looping along with other operators
 * fromArray(), fromIterable() and fromCallable() only emit items when subscribed by the observers.
 * `Observable.fromCallable()` - returns object from db transaction a callable object
