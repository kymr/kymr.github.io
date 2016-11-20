---
layout: post
title: 'Observer Pattern'
author: yongmaroo.kim
date: 2016-11-20 03:00
tags: [design pattern,java]
---

### Definition

The Observer pattern is a software design pattern in which an object, called the Subject, maintains a list of its dependent object, called Observers, and notifies them automatically of any state changes, usually by calling one of their methods. It is mainly used to implement distributed event handling systems.

![Observer Pattern Class Diagram](/files/design-pattern/observer-pattern/observer-pattern.png)


### Loose Coupling

Loose Coupling means that they interact each other, but they don't know each other well.
It is loosely coupled between Subject and Observer.

- Subject only knows about Observer that it implements observer interface method `update`, so Subject doesn't need to know what observer actually does.
- Subject easily attach & detach Observers at any time.
- When new type of Observer need to be attached, Subject is not dependent on it.
- Subject and Observer is reusable independently.
- If Observer implements its interface method, then change doesn't affect each other.


### Example

[Source Repository](https://github.com/kymr/design-pattern/tree/master/src/main/java/design/pattern/observer)

##### Subject.java

This is a Subject Interface.

```java
public interface Subject {
	void addObserver(Observer observer);
	void deleteObserver(Observer observer);
	void notifyObservers();
}
```

##### News.java

This is a concrete class of Subject.

```java
public class News implements Subject {
	private List<Observer> observers;
	private String title;
	private String contents;

	public News() {
		this.observers = new ArrayList<>();
	}

	@Override
	public void addObserver(Observer observer) {
		observers.add(observer);
	}

	@Override
	public void deleteObserver(Observer observer) {
		observers.remove(observer);
	}

	@Override
	public void notifyObservers() {
		observers.forEach(observer -> observer.update(this));
	}

	public void setNews(String title, String contents) {
		System.out.println("\n\nNews arrived!!");
		this.title = title;
		this.contents = contents;
		notifyObservers();
	}

	public String getTitle() {
		return title;
	}

	public String getContents() {
		return contents;
	}
}
```

##### Observer.java

This is a Observer interface.

```java
public interface Observer {
    void update(Subject subject);
}
```

##### SimpleDisplay.java

This is one of the concrete class of Observer.

```java
public class SimpleDisplay implements Observer {
	private Subject subject;
	private String title;
	private String contents;

	public SimpleDisplay(Subject subject) {
		this.subject = subject;
		subject.addObserver(this);
	}

	@Override
	public void update(Subject subject) {
		if (subject instanceof News) {
			News news = (News) subject;
			this.title = news.getTitle();
			this.contents = news.getContents();

			display();
		}
	}

	public void display() {
		System.out.println("[title] : " + title);
	}
}
```

##### DetailDisplay.java

This is another concrete class of Observer.

```java
public class DetailDisplay implements Observer {
	private Subject subject;
	private String title;
	private String contents;

	public DetailDisplay(Subject subject) {
		this.subject = subject;
		subject.addObserver(this);
	}

	@Override
	public void update(Subject subject) {
		if (subject instanceof News) {
			News news = (News) subject;
			this.title = news.getTitle();
			this.contents = news.getContents();

			display();
		}
	}

	public void display() {
		System.out.println("[title] : " + title + ", [contents] : " + contents);
	}
}
```

##### NewsDemo.java

This is a demo class.

```java
public class NewsDemo {
	public static void main(String[] args) {
		News news = new News();

		SimpleDisplay simpleDisplay = new SimpleDisplay(news);
		news.setNews("Earthquake!!", "Today, Earthquake happens!!");

		DetailDisplay detailDisplay  = new DetailDisplay(news);
		news.setNews("Flood!!", "Today, Flood happens!!");

		news.deleteObserver(simpleDisplay);
		news.setNews("Warm", "It is warm today.");
	}
}
```


### Pull vs Push

Observer pattern can be implemented both way push and pull model. In Push model, Subject pushes the state directly to each observers at every change. Some of the Observers doesn't want to receive the state at every change. Maybe each observers interests the state of Subject with different interval. At Observer, Taking Action with state can be separated from Receiving the state. Maybe Observer doesn't want to know all of the state. In this case pull model can be used. But, in pull model, Observer doesn't receive the state directly when change occurs. Because of this, synchronization problem can happens, so you need to deal with this.


### Observer & Observable in java.util package

There is already implemented code that support Observer Pattern in java.util packages. Subject need to inherit java.util.Observable, and Observer need to implement java.util.Observer.

![Observer & Observable in java.util package](/files/design-pattern/observer-pattern/java-util-observer.png)

- It only notifies to Observers when changed is set.

```java
setChanged() {
	changed = true;
}

notifyObservers(Object arg) {
	if (changed) {
		for (Observer observer : Observers) {
			update(this, arg);
		}
		changed = false;
	}
}

notifyObservers() {
	notifyObservers(null);
}
```

- Cons
  - Observable is a class. If Subject already inherit other class, then you cannot use it.
  - There is no Observable interface, and java.util.Observer interface uses Observable as a parameter, so you cannot implement own Observable that is feasible with java.util.Observer.

### Observer Pattern Implementation with Java8 Function Feature.

This is an another implementation with Java8 features.
There is no Observer interface, because function can be passed to Subject as a parameter instead of Observer.
In this case, Consumer can be used instead of Function, because it has no return value.

##### Subject.java

```java
public interface Subject {
	void addFunction(Function<Subject, Void> funtion);		// or Consumer<Subject>
	void deleteFunction(Function<Subject, Void> funtion);	// or Consumer<Subject>
	void notifyObservers();
}
```

##### News.java

```java
public class News implements Subject {
	private List<Function<Subject, Void>> functions;		// or Consumer<Subject>
	private String title;
	private String contents;

	public News() {
		this.functions = new ArrayList<>();
	}

	@Override
	public void addFunction(Function<Subject, Void> funtion) { 	// or Consumer<Subject>
		this.functions.add(funtion);
	}

	@Override
	public void deleteFunction(Function<Subject, Void> funtion) {		// or Consumer<Subject>
		this.functions.remove(funtion);
	}

	@Override
	public void notifyObservers() {
		functions.forEach(function -> function.apply(this));
	}

	public void setNews(String title, String contents) {
		System.out.println("\n\nNews arrived!!");
		this.title = title;
		this.contents = contents;
		notifyObservers();
	}

	public String getTitle() {
		return title;
	}

	public String getContents() {
		return contents;
	}
}
```

##### SimpleDisplay.java

```java
public class SimpleDisplay {
	private Subject subject;

	public SimpleDisplay(Subject subject) {
		this.subject = subject;
		subject.addFunction(this.update);
	}

	Function<Subject, Void> update = (subject) -> {
		if (subject instanceof News) {
			News news = (News) subject;
			System.out.println("[title] : " + news.getTitle());
		}

		return null;
	};
}
```

##### DetailDisplay.java

```java
public class DetailDisplay {
	private Subject subject;

	public DetailDisplay(Subject subject) {
		this.subject = subject;
		subject.addFunction(this.update);
	}

	Function<Subject, Void> update = (subject) -> {
		if (subject instanceof News) {
			News news = (News) subject;
			System.out.println("[title] : " + news.getTitle() + ", [contents] : " + news.getContents());
		}

		return null;
	};
}
```

##### NewsDemo.java

```java
public class NewsDemo {
	public static void main(String[] args) {
		News news = new News();

		SimpleDisplay simpleDisplay = new SimpleDisplay(news);
		news.setNews("Earthquake!!", "Today, Earthquake happens!!");

		DetailDisplay detailDisplay  = new DetailDisplay(news);
		news.setNews("Flood!!", "Today, Flood happens!!");

		news.deleteFunction(simpleDisplay.update);
		news.setNews("Warm", "It is warm today.");
	}
}
```



