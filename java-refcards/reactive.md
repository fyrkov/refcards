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