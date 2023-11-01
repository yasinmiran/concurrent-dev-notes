# Week 5: Resource Sharing and Synchronization

## 1. Deep Dive into Synchronization:

## Definition:

Synchronization is the coordination or control of multiple threads to ensure they work in a predictable and reliable manner when accessing shared resources. It's a mechanism that ensures that two or more concurrent processes or threads do not simultaneously execute some particular program segment known as a critical section.

## Why is Synchronization Needed?

- Data Inconsistency: In a multi-threaded environment, when multiple threads try to access and modify the same shared data concurrently, it can lead to data inconsistency.
- Atomicity: Some operations may need to be atomic, meaning they need to be executed fully without being interrupted, paused, or stopped in the middle. Synchronization ensures atomicity for critical sections.
- Visibility: Changes made by one thread to shared data may not immediately be visible to other threads. Synchronization ensures that a change made by one thread becomes visible to all other threads.

## Concurrency vs Parallelism:

- Concurrency: Deals with managing access to a shared resource (like a database or a variable) from multiple threads and ensuring data integrity.
- Parallelism: Focuses on executing multiple tasks or processes simultaneously, often to improve performance.

## Critical Section:

A critical section is a piece of code that accesses shared resources and must not be concurrently executed by more than one thread. The primary goal of synchronization is to protect the critical section.

## Locks:

A lock is a synchronization primitive that is used to enforce mutual exclusion in concurrent systems. When a thread acquires a lock, no other thread can acquire the same lock until the first thread releases it.

## Deadlocks, Livelocks, and Starvation:

- Deadlock: A situation where two or more threads are unable to proceed with their execution because each is waiting for the other to release a resource.
- Livelock: A situation where two or more threads continuously change their state in response to the state of the other threads, preventing progress.
- Starvation: A situation where a thread is perpetually denied access to resources and thus is unable to proceed with its work.

## Performance Implications:

While synchronization ensures thread safety, it can also introduce overhead and can potentially degrade performance. Over-synchronization, where unnecessary locks are used, can lead to contention where multiple threads are waiting for a lock, leading to performance bottlenecks.

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

Five silent philosophers sit at a round table with bowls of spaghetti. Forks are placed between each pair of adjacent philosophers. Each philosopher must alternately think and eat. However, a philosopher can only eat spaghetti when they have both left and right forks. A fork can be held by only one philosopher and so a philosopher can use the fork only if it's not being used by another philosopher. After an individual philosopher finishes eating, they need to put down both forks so that the forks become available to others. A philosopher can take the fork on their right or the one on their left as they become available, but can't start eating before getting both forks.

![Dining Philosophers](https://upload.wikimedia.org/wikipedia/commons/7/7b/An_illustration_of_the_dining_philosophers_problem.png)

The challenge is to design a protocol that allows the philosophers to eat without any deadlock (a state where everyone is waiting and no one is eating).

### Potential Issues:

1. Deadlock: All philosophers pick up their left fork at the same time. No one can pick up a right fork, so no one eats.
2. Starvation: Philosophers could be polite and always give up their forks for others, leading to a situation where one or more philosophers never get to eat.

```java
class Philosopher extends Thread {
    private final Object leftFork;
    private final Object rightFork;

    Philosopher(Object leftFork, Object rightFork) {
        this.leftFork = leftFork;
        this.rightFork = rightFork;
    }

    void eat() {
        synchronized (leftFork) {
            synchronized (rightFork) {
                System.out.println(Thread.currentThread().getName() + " is eating...");
            }
        }
    }

    @Override
    public void run() {
        while (true) {
            eat();
        }
    }
}

public class DiningPhilosophers {
    public static void main(String[] args) {
        final Object fork1 = new Object();
        final Object fork2 = new Object();
        final Object fork3 = new Object();
        final Object fork4 = new Object();
        final Object fork5 = new Object();

        Philosopher p1 = new Philosopher(fork1, fork2);
        Philosopher p2 = new Philosopher(fork2, fork3);
        Philosopher p3 = new Philosopher(fork3, fork4);
        Philosopher p4 = new Philosopher(fork4, fork5);
        Philosopher p5 = new Philosopher(fork5, fork1);

        p1.start();
        p2.start();
        p3.start();
        p4.start();
        p5.start();
    }
}
```

### The solution to Avoid Deadlock:

One common solution to avoid deadlock is to introduce some asymmetry. For instance:

1. Odd-Even Solution: Philosophers are numbered from 1 to 5. Odd-numbered philosophers pick up their left fork first, then their right, while even-numbered philosophers pick up their right fork first, then their left. This ensures that at least one philosopher (the one with both an odd and even neighbour) can eat.

2. Waiter Solution: Introduce a waiter at the table. Philosophers must ask the waiter's permission before picking up their forks. The waiter gives permission to only one philosopher at a time, ensuring that a maximum of two philosophers sitting next to each other can eat at the same time.

3. Resource Hierarchy Solution: Number the forks and always pick up the lower-numbered fork first. If a philosopher can't pick up both forks, they release any fork they have picked up. This ensures that a philosopher can't prevent their neighbours from eating.


