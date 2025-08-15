# 📘 Angular Deep Dive — Q&A Style Guide

A **cleaned-up, structured transcript** of Angular concepts, rewritten in a **Question and Answer format**. Topics include directives, lifecycle hooks, dependency injection, observables, performance optimization, and more.

---

## 🧩 Angular Directives

**Q: What are directives in Angular and what types exist?**  
A: Angular has two main types of directives:
- **Structural Directives**: Modify the structure of the DOM (e.g., `*ngIf`, `*ngFor`).
- **Attribute Directives (non-structural)**: Modify the appearance or behavior of an element (e.g., `ngClass`, `ngStyle`).

**Q: How can you distinguish between structural and attribute directives in the template?**  
A: Structural directives use an asterisk `*` before their name (e.g., `*ngIf`). This is syntactic sugar for `<ng-template>`.

**Q: Is the asterisk mandatory for structural directives?**  
A: No, you can use `<ng-template>` directly instead of the asterisk notation.

---

## ⚙️ Components & Lifecycle Hooks

**Q: Why is a component considered a directive in Angular?**  
A: A component is essentially a directive with its own template.

**Q: Can you list and briefly describe Angular's lifecycle hooks?**

- `ngOnChanges`: Called when input properties change.
- `ngOnInit`: Called once after inputs are initialized.
- `ngDoCheck`: Custom change detection.
- `ngAfterContentInit`: After content (`ng-content`) is projected.
- `ngAfterContentChecked`: After projected content is checked.
- `ngAfterViewInit`: After component’s view (and child views) are initialized.
- `ngAfterViewChecked`: After the component’s view is checked.
- `ngOnDestroy`: Cleanup, like unsubscribing from observables.

**Q: Are input properties available in the constructor?**  
A: No. They're only available in `ngOnInit` or `ngOnChanges`.

**Q: What is the difference between `ngOnInit` and `ngOnChanges`?**  
A: `ngOnChanges` is called first whenever inputs change. `ngOnInit` is called once after the first change is processed.

**Q: Why should we avoid logic in the constructor?**  
A: Angular may reuse component instances. Constructor logic can run prematurely or inconsistently.

---

## 🔁 Change Detection

**Q: What are the different change detection strategies?**  
A: Two main strategies:
- **Default**: Checks the entire component tree.
- **OnPush**: Checks only when input references change or an event is triggered.

**Q: When should we use OnPush?**  
A: When inputs are immutable or controlled precisely — great for performance.

**Q: What is the difference between `markForCheck` and `detectChanges`?**

- `markForCheck`: Marks component to be checked during the next change detection run.
- `detectChanges`: Immediately triggers change detection.

---

## 🌐 View Encapsulation

**Q: What is ViewEncapsulation in Angular and its modes?**

- `None`: Styles are global.
- `Emulated` (default): Styles scoped using generated attributes.
- `ShadowDom`: Uses native shadow DOM.

**Q: Why is ShadowDom not commonly used?**  
A: Emulated mode provides good isolation without browser limitations.

**Q: What does `::ng-deep` do?**  
A: Pierces style encapsulation — not recommended for frequent use.

---

## 🔗 Dependency Injection (DI)

**Q: How does DI work in Angular?**  
A: Angular uses a hierarchical injector tree:
- Platform injector
- Root injector (`providedIn: 'root'`)
- Module injector
- Component-level injector

**Q: What is `providedIn: any`?**  
A: Creates a new instance for each lazy-loaded module.

**Q: Can we customize DI behavior?**  
A: Yes, using:
- `@Optional()`
- `@Self()`, `@SkipSelf()`, `@Host()`

**Q: Who throws if a required dependency is missing?**  
A: The `NullInjector`.

---

## 💬 Component Communication

**Q: What are the built-in ways for components to communicate?**

- `@Input()` and `@Output()`
- Shared services
- `ViewChild`, `ContentChild`, `ViewChildren`, `ContentChildren`

---

## 🛠️ HTTP Client

**Q: Why use Angular’s `HttpClient` over `fetch()` or `XMLHttpRequest`?**  
A: `HttpClient`:
- Integrates with Angular’s zones.
- Supports observables.
- Enables interceptors.

**Q: What is an HTTP Interceptor and how does it work?**  
A: Middleware for HTTP requests/responses. Interceptors are called in the order they’re provided.

**Q: How to bypass an interceptor?**  
A: Override the `HttpClient` provider at a lower level without the interceptor.

---

## 🔄 RxJS & Observables

**Q: What is RxJS and its role in Angular?**  
A: RxJS provides observables for async data/event handling.

**Q: What are some RxJS types?**

- `Observable`
- `Subject`
- `BehaviorSubject`
- `ReplaySubject`
- `AsyncSubject`

**Q: Hot vs Cold Observables?**

- **Cold**: Starts on subscription.
- **Hot**: Emits regardless of subscriptions.

**Q: How to make a cold observable hot?**  
A: Use `share()`, `shareReplay()`, or `publish()` + `connect()`.

**Q: Do we always need to unsubscribe?**  
A: Not for auto-completing streams (e.g., HTTP), but use `takeUntil` or `AsyncPipe` for safety.

---

## ⚙️ Pipes & Performance

**Q: Difference between pure and impure pipes?**

- **Pure Pipe**: Executes only on input change.
- **Impure Pipe**: Runs every change detection cycle.

**Q: Is it good to call methods in templates?**  
A: No — they run every cycle. Use memoization.

**Q: What is memoization?**  
A: Caching function results to avoid recomputation.

---

## ⚡ Performance Optimization Techniques

**Q: What optimization techniques have you used in Angular?**

- OnPush change detection
- `trackBy` in `*ngFor`
- Lazy loading
- Caching with `shareReplay`
- Service Workers
- Memoization
- Avoid re-rendering heavy components

---

## 🧠 NgRx & State Management

**Q: What is the NgRx lifecycle?**

1. Action is dispatched.
2. Reducer updates the state.
3. Effects handle async logic.
4. New action may be dispatched.

**Q: When should we use state management libraries like NgRx?**  
A: For managing complex app states with interconnected entities.

---


## 1. How does HTTP interceptor work under the hood in Angular?

HTTP Interceptors in Angular act like a pipeline that every HTTP request and response travels through. They allow you to:
- Modify requests before they are sent (e.g., add headers).
- Log or transform responses.
- Handle errors globally.

Flow:
1. Request leaves your component/service.
2. Passes through each interceptor in sequence.
3. The last handler sends it to the backend via `fetch` or `XMLHttpRequest`.
4. Response passes back through interceptors in reverse order.

Visual:
```
Your Code → Interceptor 1 → Interceptor 2 → BackendHandler → (server)
    ↑                                                      ↓
   Response ← Interceptor 2 ← Interceptor 1 ← BackendHandler
```

---

## 2. What is `forwardRef` in Angular?

`forwardRef` is used to reference classes before they are defined, often to resolve circular dependencies.

Example:
```ts
constructor(@Inject(forwardRef(() => ChildService)) private child: ChildService) {}
```
It delays resolution until runtime, breaking circular dependency chains.

---

## 3. How does `forwardRef` pair up with the injector?

Angular's injector always runs `resolveForwardRef()` before dependency resolution:
- If the token is a normal class → use it directly.
- If the token is a `forwardRef` wrapper → execute its function to get the real class.

This ensures classes can be referenced before they are defined.

---

## 4. What is `APP_INITIALIZER` in Angular?

`APP_INITIALIZER` is a special Angular injection token for registering functions to run **before** the app bootstraps. Angular waits for these (and any returned Promises) to resolve before loading the root component.

Example use case: Load configuration from the server before rendering.

---

## 5. Signals in Angular

Signals are reactive values introduced in Angular 16+:
- Hold data and notify Angular when they change.
- Used with `computed` for derived values and `effect` for side effects.
- Replace some uses of Observables for local state.

Example:
```ts
count = signal(0);
double = computed(() => count() * 2);
effect(() => console.log(count()));
```

---

## 6. How Angular tracks dependencies between signals

Angular internally uses a reactivity graph:
- Nodes: signals, computed values, effects.
- Edges: "depends on" relationships.
When a signal is read inside a consumer (computed, effect, template binding), Angular records the dependency. When the signal changes, Angular re-runs only affected consumers.

---

## 7. Reactive Forms in Angular

Reactive Forms are a model-driven approach to handling form inputs.
- `FormControl`: Single input.
- `FormGroup`: Group of named controls.
- `FormArray`: List of controls.

They are fully defined in the component class and emit observable streams for value and status changes.

---

## 8. Updating values in Reactive Forms

- `setValue()` → Requires all fields.
- `patchValue()` → Updates specific fields.
- `.updateValueAndValidity()` → Forces validation after an update.

---

## 9. Difference between `FormControl`, `FormGroup`, `FormArray`

| Feature       | FormControl | FormGroup         | FormArray        |
|---------------|-------------|-------------------|------------------|
| Holds         | Single value| Object of controls| Array of controls|
| Use case      | Single input| Related inputs    | Dynamic list     |
| Access by     | `.value`    | `.get(name)`      | `.at(index)`     |
| Shape         | Primitive   | Object            | Array            |
| Dynamic size? | No          | No                | Yes              |
