
```ts
count = signal(0);
doubleCount = computed(() => this.count() * 2);
doubleDoubleCount = computed(() => this.doubleCount() * 2);


count$ = new BehaviorSubject(0);
doubleCount$ = this.count$.pipe(map((count) => count * 2));
doubleDoubleCount$ = this.doubleCount$.pipe(map((doubleCount) => doubleCount * 2));
```
- signal is not observable; you can simply retrieve the value directly
- observable requires using async pipe in template to pull values out
- signals are for synchronous reactivity, not async; observable is for async
	- signal is like inputting a form and seeing ui update
	- observable is for hitting an api and handling race conditions like potentially out of order requests (eg search terms where 2nd key lands before 1st)
- Signals will be able to be upgraded to an observable with Angular API methods
```ts
// signal
searchTerm = signal('');

// observable
employees$ = fromSignal(this.searchTerm).pipe(
	debounceTime(300),
	distinctUntilChanged(),
	switchMap((searchTerm) => this.employeeService.search(searchTerm)),
	retryCount(5)
)

// signal (allows hooking back into signal change detection mechanism)
employees = fromObservable(this.employees$)
```