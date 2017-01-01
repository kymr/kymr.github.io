---
layout: post
title: 'Template Method Pattern'
author: yongmaroo.kim
date: 2017-01-01 23:00:00
tags: [design pattern,java]
---

## Template Method Pattern

### Definition

The template method pattern is a behavioral design pattern that defines the program skeleton of an algorithm in an operation, deferring some steps to subclasses. It lets one redefine certain steps of an algorithm without changing the algorithm's structure.

![Template Method Pattern Class Diagram](/files/design-pattern/templatemethod-pattern/templatemethod_pattern.png)

Template method's abstract class may also define hook methods that may be overridden by subclasses.

### Implementation

#### Template Method Abstract Class

```java
public abstract class CaffeineBeverage {

	final void prepareRecipe() {
		boilWater();
		brew();
		pourInCup();
		addCondiments();
	}

	abstract void brew();
	abstract void addCondiments();

	void boilWater() {
		System.out.println("boiling water..");
	}

	void pourInCup() {
		System.out.println("pouring in cup");
	}
}
```

#### Concrete Class Coffee & Tea

```java
public class Coffee extends CaffeineBeverage {
	@Override
	void brew() {
		System.out.println("brewing coffee with filter");
	}

	@Override
	void addCondiments() {
		System.out.println("adding sugar");
	}
}
```

```java
public class Tea extends CaffeineBeverage {

	@Override
	void brew() {
		System.out.println("brewing tea");
	}

	@Override
	void addCondiments() {
		System.out.println("adding lemon");
	}
}
```


#### Demo

```java
public class Demo {
	public static void main(String[] args) {
		Arrays.asList(new Tea(), new Coffee())
				.forEach((CaffeineBeverage beverage) -> {
					System.out.println("This is " + beverage.getClass().getSimpleName());
					beverage.prepareRecipe();
				});
	}
}
```


#### Arrays Sort Example

```java
public class Duck {
	String name;
	int weight;

	public Duck(String name, int weight) {
		this.name = name;
		this.weight = weight;
	}

	@Override
	public String toString() {
		return "Duck{" +
				"name='" + name + '\'' +
				", weight=" + weight +
				'}';
	}
}
```

```java
public class Demo {
	public static void main(String[] args) {
		System.out.println("\n\nDuck Sort Example");

		Duck[] ducks = {
			new Duck("Daffy", 8),
			new Duck("Dewey", 2),
			new Duck("Howard", 7),
			new Duck("Louie", 2),
			new Duck("Donald", 10),
			new Duck("Huey", 2)
		};

		System.out.println("Before:");
		Stream.of(ducks).forEach(System.out::println);

		System.out.println("After");
		Arrays.sort(ducks, (Duck o1, Duck o2) -> o1.weight - o2.weight);
		Stream.of(ducks).forEach(System.out::println);
	}
}
```

Sorting Arrays uses Comparator Interface at sort method internally. It just need to be declared how to compare that object, not how we rearrange the array.