# Iterators/Generators

### ADT

```typescript
interface Iterator<T> {
    next(value?: any): IteratorResult<T>;
    return?(value?: any): IteratorResult<T>;
    throw?(e?: any): IteratorResult<T>;
}

interface IteratorResult<T> {
    done: boolean;
    value: T;
}
```

### Usage

* Allows for lazy execution/enumeration
* Can create infinite sequences without sending program into infinite loop

### Generators

```typescript
function * infiniteSequence() {
    var i = 0;
    while (true) {
        yield i++;
    }
}

var iterator = infiniteSequence();
while (true) {
    console.log(iterator.next())
```

