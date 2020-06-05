# Clean Code Guidelines

### Error Handling

#### Use Errors to Throw or Reject

* Promises may be rejected using any data type; we can reject a promise by passing a string. This will make debugging more difficult down the road. Similarly, any value can be thrown. Use of **Error** or a subclass makes it clear something is wrong and lets `catch` statements respond to rejected promises. 

```typescript
function notImplemented() {
    throw new Error('Not implemented yet.');
}

function get(): Promise<Item[]> {
    return Promise.reject(new Error('Not implemented.'));
}

async function get(): Promise<Item[]> {
    throw new Error('Not implemented.');
}
```

### Immutable Objects

#### Arrays

* `ReadonlyArray` describes an `Array` that can only be read from.

  * Elements cannot be added, removed, or replaced.
  * Can also declare typed array `readonly T[]` for same effect

#### Tuples

* Prefix a tule type with `readonly` keyword to disallow mutations
* Slots can only be read from, not written to
* `readonly [string, string]`
* Read-only tuple extends from `ReadonlyArray<T1, T2, ... TN>`

