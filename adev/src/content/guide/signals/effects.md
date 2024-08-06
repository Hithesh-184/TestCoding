# Effects

Signals track values and their changes, but this isn't very useful without the ability to react to those values changing. When signals are used in a template, for example, Angular reacts when they change by updating the UI, but sometimes you'll want to react to signals changing in other ways: logging values, updating a cache, sending network requests, etc. For these cases, you can create _effects_.

Effects are created with the `effect()` API, which accepts a function that performs an action which depends on the value of one or more signals. Angular will execute that action, and re-execute it whenever its signal dependencies have new values.

A common example is an effect that logs the value of a signal:

```ts
effect(() => console.log(currentUser()));
```

As a slightly more advanced example, you can use an effect to keep the value of a signal in sync with `localStorage`:

```ts
const CACHE_KEY = 'name';
@Injectable({providedIn: 'root'})
export class NameService {
  readonly name: WritableSignal<number>;

  constructor() {
    // Read the initial name from local storage:
    this.name = signal(window.localStorage.getItem(CACHE_KEY) ?? '');

    // Create an effect to update local storage every time the name changes.
    effect(() => {
      window.localStorage.setItem(CACHE_KEY, this.name());
    });
  }
}
```

<docs-callout helpful title="Effect Style">
Both of these effects have something in common: the reaction function is written to perform an action that uses the value of one or more signals. Because Angular tracks which signals are used as part of that action, it knows to only re-run the action _if it could produce a different outcome_.
</docs-callout>

## When do effects run?

Effects are never synchronous - they don't run immediately when one of their signal dependencies changes. Instead, their executions are scheduled by the framework to run at an optimal time in the future. Ideally, you should not need to consider exactly when your effects will run, and leave this up to Angular.

Generally, there are two different kinds of effects, which have different timings:

- Component effects, which are associated with a specific component or location in the UI.
- Root effects, which aren't tied to any specific component.

Where you call `effect()` determines which kind of effect is created. When used within a component, directive, or a service provided within a component or directive, `effect()` creates a component effect associated with that part of the UI. When called outside of that context (for example, from a service created by the application injector), `effect()` creates a root effect.

When a component effect is dirty, it runs

## Testing of effects

## Advanced Topics

### Why are effects not synchronous?
