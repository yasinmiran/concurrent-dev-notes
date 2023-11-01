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

## 4. Reentrant Locks and Conditions:

- Introduction to ReentrantLock: An explicit locking mechanism that offers more flexibility than intrinsic locks.
- Conditions: Using Condition objects to control thread access and coordination.

Examples:

```java
// ReentrantLock
ReentrantLock lock = new ReentrantLock();
lock.lock();
try {
    // critical section
} finally {
    lock.unlock();
}

// Conditions
ReentrantLock lock = new ReentrantLock();
Condition condition = lock.newCondition();

lock.lock();
try {
    while (/* condition not met */) {
        condition.await();
    }
    // critical section
    condition.signalAll();
} finally {
    lock.unlock();
}
```

## 5. Strategies for Deadlock Prevention and Avoidance:

- Understanding Deadlocks: The four necessary conditions for a deadlock.
- Prevention: Breaking the conditions required for a deadlock.

Example:

```java
Object resource1 = new Object();
Object resource2 = new Object();

Thread t1 = new Thread(() -> {
    synchronized(resource1) {
        // ...
        synchronized(resource2) {
            // ...
        }
    }
});

Thread t2 = new Thread(() -> {
    synchronized(resource2) {
        // ...
        synchronized(resource1) {
            // ...
        }
    }
});

t1.start();
t2.start();
```

## 6. Case Studies: Problems When Sharing Resources:

- Shared Resource Conflicts: Real-world scenarios where multiple threads access shared resources leading to issues.

Example:

```java
public class SharedResource {
    private int value = 0;

    public synchronized void increment() {
        value++;
    }

    public synchronized int getValue() {
        return value;
    }
}
```

# Dining Philosophers

![Dining Philosophers](https://upload.wikimedia.org/wikipedia/commons/7/7b/An_illustration_of_the_dining_philosophers_problem.png)


