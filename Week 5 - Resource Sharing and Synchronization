# Week 5: Resource Sharing and Synchronization

## 1. Deep Dive into Synchronization:

Definition:
Synchronization ensures that multiple threads do not concurrently execute some particular program segments known as critical sections.

Need for Synchronization:
- Atomicity: Ensuring a sequence of operations is atomic.
- Visibility: Making sure changes made by one thread are visible to others.
- Ordered Operations: Ensuring the order of execution.

Example:

```java
public synchronized void synchronizedMethod() {
    // critical section
}
```

## 2. Object and Class-level Locking:

Object-level Locking: Using the intrinsic lock of an object to ensure mutual exclusion.

Class-level Locking: Synchronizing on the class's Class object.

```java
// Object-level Locking
public class Counter {
    private int count = 0;
    private final Object lock = new Object();

    public void increment() {
        synchronized(lock) {
            count++;
        }
    }
}

// Class-level Locking
public class SharedResource {
    private static int data = 0;

    public static synchronized void modifyData() {
        data++;
    }
}
```

## 3. Synchronized Methods and Blocks:

- Synchronized Methods: Using the synchronized keyword with methods.
- Synchronized Blocks: Creating synchronized blocks within methods for finer-grained control.

Examples:

```java
// Synchronized Method
public synchronized void syncMethod() {
    // critical section
}

// Synchronized Block
public void method() {
    synchronized(this) {
        // critical section
    }
}
```
