---
layout: post
title: 'Singleton Pattern'
author: yongmaroo.kim
date: 2016-12-12 02:14
tags: [design pattern,java]
---

### Definition

#### Singleton Pattern

The singleton pattern is a design pattern that restricts the instantiation of a class to one object. This is useful when exactly one object is needed to coordinate actions across the system. The concept is sometimes generalized to systems that operate more efficiently when only one object exists, or that restrict the instantiation to a certain number of objects. The term comes from the mathematical concept of a singleton.

![Singleton Pattern Class Diagram](/files/design-pattern/singleton-pattern/singleton-pattern.png)


### Implementation

- Classical Example

This use-case is really bad. Because getInstance method will be called many times. And when the method is called, it is going to be synchronized. Usually, method execution with synchronization is slower 100 times than normal execution.

```java
public class ClassicalSingleton {
	private static ClassicalSingleton classicalSingleton;

	private ClassicalSingleton() {
	}

	public static synchronized ClassicalSingleton getInstance() {
		if (classicalSingleton == null) {
			classicalSingleton = new ClassicalSingleton();
		}
		return classicalSingleton;
	}
}
```

- Initialize instance during class-loading.

I prefer below code normally. The instance is going to be initialized while class-loading. It looks like a waste of memory. But i think it will be used often. If this singleton is used often, then i think below way is not bad.

```java
public class InitializedSingleton {
	private static InitializedSingleton initializedSingleton = new InitializedSingleton();

	private InitializedSingleton() {
	}

	public static synchronized InitializedSingleton getInstance() {
		return initializedSingleton;
	}
}
```

- Double Checking Locking

I think this way is most widely used. This singleton instance is going to be initialized when it needed, and it is safe, and it is also faster than synchronization on all over the method.

```java
public class DoubleCheckingLockingSingleton {
	private volatile static DoubleCheckingLockingSingleton doubleCheckingLockingSingleton;

	private DoubleCheckingLockingSingleton() {
	}

	public static DoubleCheckingLockingSingleton getInstance() {
		if (doubleCheckingLockingSingleton == null) {
			synchronized (DoubleCheckingLockingSingleton.class) {
				if (doubleCheckingLockingSingleton == null) {
					doubleCheckingLockingSingleton = new DoubleCheckingLockingSingleton();
				}
			}
		}

		return doubleCheckingLockingSingleton;
	}
}
```

- Java Volatile keyword

The Java volatile keyword is used to mark a Java variable as "being stored in main memory". More precisely that means, that every read of a volatile variable will be read from the computer's main memory, and not from the CPU cache, and that every write to a volatile variable will be written to main memory, and not just to the CPU cache.
Actually, since Java 5 the volatile keyword guarantees more than just that volatile variables are written to and read from main memory. 
Since Java 5 the volatile keyword guarantees also
  
  + If Thread A writes to a volatile variable and Thread B subsequently reads the same volatile variable, then all variables visible to Thread A before writing the volatile variable, will also be visible to Thread B after it has read the volatile variable. 
  + The reading and writing instructions of volatile variables cannot be reordered by the JVM (the JVM may reorder instructions for performance reasons as long as the JVM detects no change in program behaviour from the reordering). Instructions before and after can be reordered, but the volatile read or write cannot be mixed with these instructions. Whatever instructions follow a read or write of a volatile variable are guaranteed to happen after the read or write.

![volatile](/files/design-pattern/singleton-pattern/java-volatile.png)

### Example

[Source Code Repository](https://github.com/kymr/design-pattern/tree/master/src/main/java/design/pattern/singleton)


