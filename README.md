# ECMAScript proposal: `Promise.any`

## Status

This proposal is at stage 0 of [the TC39 process](https://tc39.github.io/process-document/).

## Motivation

There are four main combinators in the `Promise` landscape.

| name                 | description                                     |                                                                             |
| -------------------- | ----------------------------------------------- | --------------------------------------------------------------------------- |
| `Promise.allSettled` | does not short-circuit                          | [separate proposal](https://github.com/tc39/proposal-promise-allSettled) 🔜 |
| `Promise.all`        | short-circuits when an input value is rejected  | added in ES2015 ✅                                                          |
| `Promise.race`       | short-circuits when an input value is settled   | added in ES2015 ✅                                                          |
| `Promise.any`        | short-circuits when an input value is fulfilled | this proposal 🆕                                                            |

These are all commonly available in userland promise libraries, and they’re all independently useful, each one serving different use cases.

## Proposed solution

`Promise.any` accepts an array of promises and returns a promise that is fulfilled by the first given promise to be fulfilled, or rejected with an array of rejection reasons if all of the given promises are rejected.

## High-level API

```js
try {
  const first = await Promise.any(promises);
  // Any of the promises was fulfilled.
} catch (error) {
  // All of the promises were rejected.
}
```

Or, without `async`/`await`:

```js
Promise.any(promises).then(
  (first) => {
    // Any of the promises was fulfilled.
  },
  (error) => {
    // All of the promises were rejected.
  }
);
```

In the above examples, `error` is an `AggregateError`, a new `Error` subclass that groups together individual errors. Every `AggregateError` instance contains a pointer to an array of exceptions.

### FAQ

#### Why choose the name `any`?

It clearly describes what it does, and there’s precedent for the name `any` in userland libraries offering this functionality:

- https://github.com/kriskowal/q#combination
- http://bluebirdjs.com/docs/api/promise.any.html
- https://github.com/m0ppers/promise-any
- https://github.com/cujojs/when/blob/master/docs/api.md#whenany
- https://github.com/sindresorhus/p-any

#### Why throw an `AggregateError` instead of an array?

The prevailing practice within the ECMAScript language is to only throw exception types. Existing code in the ecosystem likely relies on the fact that currently, all native exceptions are `Error` subclass instances. Adding a new language feature that can throw a plain `array` would break that invariant, and could be a web compatibility issue.

## Illustrative examples

This snippet checks which endpoint responds the fastest, and then logs it.

```js
Promise.any([
  fetch('https://v8.dev/').then(() => 'home'),
  fetch('https://v8.dev/blog').then(() => 'blog'),
  fetch('https://v8.dev/docs').then(() => 'docs')
]).then((first) => {
  // Any of the promises was fulfilled.
  console.log(first);
  // → 'home'
}).catch((error) => {
  // All of the promises were rejected.
  console.log(error);
});
```

## TC39 meeting notes

- TODO

## Specification

- [Ecmarkup source](https://github.com/tc39/proposal-promise-any/blob/master/spec.html)
- [HTML version](https://tc39.github.io/proposal-promise-any/)

## Implementations

- none yet
