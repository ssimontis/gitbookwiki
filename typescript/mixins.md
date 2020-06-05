---
description: Building classes from partials
---

# Mixins

* Instead of class A extending class B to add functionality, function B takes class A and returns a new class with added functionality.
* Mixins are functions that
  * take a constructor
  * Create a class that extends that constructor with new functionality
  * returns the new class

```typescript
type Constructor<T = {}> = new (...args: any[]) => T;

function Timestamped<TBase extends Constructor>(Base: TBase) {
    return class extends Base {
        timestamp = Date.now();
    };
}

function Activatable<TBase extends Constructor>(Base: TBase) {
    return class extends Base {
        isActivated = false;
        
        activate () {
            this.isActivated = true;
        }
        
        deactivate() {
            this.isActivated = false;
        }
    };
}

class User {
    name = '';
}

const TimestampedUser = Timestamped(User);
const TimestampedActivatableUser = Timestamped(Activatable(User));
```



