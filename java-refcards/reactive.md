### Reactive manifesto
- Responsive
- Resilient
- Elastic (scalable)
- Message driven (event driven)

### RxJava patterns
#### Observer
Observers subscribe to *observable* subject\
`Observable` ---[events]---> `Observers`

`Observable<T>`, where `T` - class type for events.

Observers are completely passive.

Events are delivered sync or async, it is all incapsulated in the `Scheduler`.\
:exclamation: RxJava uses a single threaded scheduler by default.\
:exclamation: Observable does not emit events until some Observer subscribes. This does not apply to "Hot Observables".

![Observable](reactive_files/Observable.png)

Observable example:
```
    Action createToDo = (HttpServletRequest request, HttpServletResponse response) -> {
        Observable.just(request)
                .map(req -> (ToDoDto) getDataFromJsonBodyRequest(req, ToDoDto.class))
                .flatMap(toDoDto -> toDoDao.create(toDoDto))
                .subscribe(toDo -> toJsonResponse(request, response, new ResponseDto(200, toDo)));
    };
```

Observable example #2 with callbacks defined for Observable, not for Observer:
```
String[] result = {""};
Single<String> single = Observable.just("Hello")
  .toSingle()
  .doOnSuccess(i -> result[0] += i)
  .doOnError(error -> {
      throw new RuntimeException(error.getMessage());
  });
single.subscribe();
  
assertTrue(result[0].equals("Hello"))
```
#### Hot vs Cold Observables
Cold Observable is providing items in a lazy way.
The Observer is taking elements only when it is ready to process that item, and items do not need to be buffered in an Observable because they are requested in a **pull** fashion.

A hot Observable begins generating items and emits them immediately when they are created (**push** model). 
Hot Observable emits items at its own pace, and it is up to its observers to keep up.
When the Observer is not able to consume items as quickly as they are produced by an Observable they need to be buffered or handled in some other way, as they will fill up the memory, finally causing `OutOfMemoryException`.

#### Observer methods
- `onNext` is called each time a new event is published
- `onCompleted` is called when the sequence of events is complete, indicating that we should not expect any more `onNext` calls
- `onError` is called when an unhandled exception is thrown during the RxJava framework code or our event handling code

#### Types of observables
1. `ConnectableObservable` doesn't begin emitting items when it is subscribed to, but only when the `connect()` operator is applied to it.
2. `Single` emits only one value or an error.
3. `Subject` is simultaneously two elements, a subscriber and an observable. Subject can observe several observables at one time
4. `Flowable` - backpressure-aware type of Observable. It makes extra request to pull new events at a required pace.

![Subject](reactive_files/Subject.png)

Types of subjects:
- `PublishSubject` - does not replay past messages
```
PublishSubject<Integer> subject = PublishSubject.create(); 
subject.subscribe(getFirstObserver()); 
```
- `BehaviorSubject` - replays the last message and all future messages
- `ReplaySubject` - replays all past messages
- `AsyncSubject` - emits only the las message

#### Dealing with Backpressure
1. Buffering (default RxJava behavior)
```
PublishSubject<Integer> source = PublishSubject.<Integer>create();
         
source.buffer(1024)
  .observeOn(Schedulers.computation())
  .subscribe(ComputeFunction::compute, Throwable::printStackTrace);
```

2. Batching
```
PublishSubject<Integer> source = PublishSubject.<Integer>create();
 
source.window(500)
  .observeOn(Schedulers.computation())
  .subscribe(ComputeFunction::compute, Throwable::printStackTrace);
```

3. Skipping Elements
```
PublishSubject<Integer> source = PublishSubject.<Integer>create();
 
source.sample(100, TimeUnit.MILLISECONDS)
  .observeOn(Schedulers.computation())
  .subscribe(ComputeFunction::compute, Throwable::printStackTrace);
```

4. Dropping 
```
Observable.range(1, 1_000_000)
  .onBackpressureDrop()
  .observeOn(Schedulers.computation())
  .doOnNext(ComputeFunction::compute)
  .subscribe(v -> {}, Throwable::printStackTrace);
```

And more...