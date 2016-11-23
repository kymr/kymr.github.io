---
layout: post
title: 'Dynamic Extension with Intersection Type & Lambda'
author: yongmaroo.kim
date: 2016-11-24 03:00
tags: [java,intersection]
---

#### Generics

This is based on Toby Lee's LiveCoding tv. https://www.youtube.com/watch?v=PQ58n0hk7DI




#### FunctionalInterface

Interface that has only one abstract method.

```java
	private static void hello1(Function p0) {
		
	}
	
	public static void main(String[] args) {
		hello1(s -> s);
	}
```

Old way, it normally created with anonymous class. At java8, lambda changes a lot of things!

```java
	public static void anonymousClassExample() {
		Function<String, String> f = new Function<String, String>() {
			@Override
			public String apply(String s) {
				return null;
			}
		};
		hello1(f);
	}
```

#### Intersection Type

Function should has single abstract method.

```java
	public static void intersect() {	
		hello1((Function & Serializable & Cloneable) s -> s);	// intersect with marker interface, still has only one method.
	}
```

Because Marker Interface has no method, after intersection those interfaces it still has only one method. So it's fine, it works.
	
#### Can we extends features dynamically with Interface Intersection?

Default method! It is not abstract method, below lambda expression has only apply method.

```java
	interface Hello {
		default void hello() {
			System.out.println("Hello");
		}
	}

	interface Hi {
		default void hi() {
			System.out.println("Hi");
		}
	}

	public static void intersect() {
		intersectHello((Function & Hello & Hi) s -> s);
	}
	
	private static <T extends Function & Hello & Hi> void intersectHello(T t) {
		t.hello();
		t.hi();
	}
```

Consumer is another functional interface that has a parameter but no return value.

```java
	public static void intersect() {
		run((Function & Hello & Hi) s -> s, o -> {
			o.hello();
			o.hi();
		});
	}

	private static <T extends Function> void run(T t, Consumer<T> consumer) {
		consumer.accept(t);;
	}
```

Let's define new interface DelegateTo that has no paramter, but has return value

```java
	interface DelegateTo<T> {
		T delegate();
	}
```

And then, define new interfaces that extends DelegateTo and implements its own feature with default method.
	
```java
	interface Hello extends DelegateTo<String> {
		default void hello() {
			System.out.println("Hello " + delegate());
		}
	}

	interface UpperCase extends DelegateTo<String> {
		default void upperCase() {
			System.out.println(delegate().toUpperCase());
		}
	}
```

Then it can be used like below.

```java
	public static void intersect() {
		run2((DelegateTo<String> & Hello & UpperCase)() -> "Yongmaroo Kim", o -> {		// () -> "Yongmaroo Kim", this is implementation of delegate method
			o.hello();
			o.delegate();
		});
	}

	private static <T extends DelegateTo<S>, S> void run2(T t, Consumer<T> consumer) {
		consumer.accept(t);
	}
```


#### Extension with Forwarding

Let's define interface Pair

```java
	interface Pair<T> {
		T getFirst();
		T getSecond();
		void setFirst(T first);
		void setSecond(T second);
	}
```

This interface has 4 methods. So it cannot be used as a functional interface. But we have default methods!

```java
	interface ForwardingPair<T> extends DelegateTo<Pair<T>>, Pair<T> {
		default T getFirst() { return delegate().getFirst(); }
		default T getSecond() { return delegate().getSecond(); }
		default void setFirst(T first) { delegate().setFirst(first); }
		default void setSecond(T second) { delegate().setSecond(second); }
	}
```

Then this interface has only one method delegate.
Let's define class Name as an example.

```java
	static class Name implements Pair<String> {
		String firstName;
		String lastName;

		public Name(String firstName, String lastName) {
			this.firstName = firstName;
			this.lastName = lastName;
		}

		@Override
		public String getFirst() {
			return this.firstName;
		}

		@Override
		public String getSecond() {
			return this.lastName;
		}

		@Override
		public void setFirst(String first) {
			this.firstName = first;
		}

		@Override
		public void setSecond(String second) {
			this.lastName = second;
		}
	}
```

Then new feature can be extends dynamically.

```java
	interface Convertable<T> extends DelegateTo<Pair<T>> {
		default void convert(Function<T, T> mapper) {
			Pair<T> pair = delegate();
			pair.setFirst(mapper.apply(pair.getFirst()));
			pair.setSecond(mapper.apply(pair.getSecond()));
		}
	}

	interface Printable<T> extends DelegateTo<Pair<T>> {
		default void print() {
			System.out.println(delegate().getFirst() + " " + delegate().getSecond());
		}
	}

	public static void intersect() {
		Pair<String> name = new Name("Yongmaroo", "Kim");
		run2((ForwardingPair<String> & Convertable<String> & Printable<String>)() -> name, o -> {
			o.print();
			o.convert(s -> s.toUpperCase());
			o.print();
			o.convert(s -> s.substring(0, 2));
			o.print();
		});
	}
```