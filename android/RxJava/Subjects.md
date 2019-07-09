### Behaviour vs Publish

* Behaviour remembers the last emitted item, but puslish starts with empty and starts emitting
  * Publish subject: Starts empty and only emits new elements to subscribers. 
   There is a possibility that one or more items may be lost between the time the Subject is created and the observer 
   subscribes to it because PublishSubject starts emitting elements immediately upon creation.
  * BehaviorSubject: It needs an initial value and replays it or the latest element to new subscribers. 
  As BehaviorSubject always emits the latest element, you can’t create one without giving a default initial value. 
  BehaviorSubject is helpful for depicting "values over time". For example, an event stream of birthdays is a Subject, 
  but the stream of a person's age would be a BehaviorSubject.