### Blogs

* [About Subjects](https://tech.instacart.com/how-to-think-about-subjects-in-rxjava-part-1-ca509b981020)
* [ - State propogation with subjects](https://proandroiddev.com/state-propagation-in-android-with-rxjava-subjects-81db49a0dd8e)
* [ - About Publish processor](https://medium.com/@ar.xa.vasquez/how-to-rxjava2-series-intro-and-publishprocessor-79c3fea17921)
* [ - hot observables with test cases](https://github.com/politrons/reactive/blob/master/src/test/java/rx/observables/connectable/HotObservable.java)


### Behaviour vs Publish

* Behaviour remembers the last emitted item, but puslish starts with empty and starts emitting
  * Publish subject: Starts empty and only emits new elements to subscribers. 
   There is a possibility that one or more items may be lost between the time the Subject is created and the observer 
   subscribes to it because PublishSubject starts emitting elements immediately upon creation.
  * BehaviorSubject: It needs an initial value and replays it or the latest element to new subscribers. 
  As BehaviorSubject always emits the latest element, you can’t create one without giving a default initial value. 
  BehaviorSubject is helpful for depicting "values over time". For example, an event stream of birthdays is a Subject, 
  but the stream of a person's age would be a BehaviorSubject.
  
### PublishSubject vs PublishProcessor

* The main difference is their base class, therefore the way that these two react to onNext event is different.
* PublishProcessor is subclassed from Flowables so you can use a BackPressure Strategy when you make use of them. PublishSubject's superclass is Observable so at very least there is no BackPressure Strategy
