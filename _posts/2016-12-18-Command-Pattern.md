---
layout: post
title: 'Command Pattern'
author: yongmaroo.kim
date: 2016-12-12 02:14
tags: [design pattern,java]
---

### Definition

#### Command Pattern

The command pattern is a behavioral design pattern in which an object is used to encapsulate all information needed to perform an action or trigger an event at a later time. This information includes the method name, the object that owns the method and values for the method parameters.

![Command Pattern Class Diagram](/files/design-pattern/command-pattern/command-pattern.jpg)

1. Command
A command object knows about receiver and invokes a method of the receiver. Values for parameters of the receiver method are stored in the command. 

2. Invoker
An invoker object knows how to execute a command, and optionally does bookkeeping about the command execution. The invoker does not know anything about a concrete command, it knows only about command interface. Both an invoker object and several command objects are held by a client object
 
3. Receiver
The receiver then does the work

4. client
The client decides which commands to execute at which points. To execute a command, it passes the command object to the invoker object.


### Implementation

#### RemoteContol

First, Command interface.

```java
public interface Command {
	public void execute();
}
```

And, concrete commands need to be implemented.

```java
public class LightOnCommand implements Command {
	Light light;

	public LightOnCommand(Light light) {
		this.light = light;
	}

	@Override
	public void execute() {
		light.on();
	}
}
```

```java
public class LightOffCommand implements Command {
	Light light;

	public LightOffCommand(Light light) {
		this.light = light;
	}

	@Override
	public void execute() {
		light.off();
	}
}
```

This is Receiver that work 

```java
public class Light {
	public void on() {
		System.out.println("light on");
	}

	public void off() {
		System.out.println("light off");
	}
}
```

RemoteControl is an invoker. And main method is an implementation of client.

```java
public class SimpleRemoteControl {
	Command slot;

	public SimpleRemoteControl() {

	}

	public void setCommand(Command command) {
		slot = command;
	}

	public void buttonWasPressed() {
		slot.execute();
	}

	public static void main(String[] args) {
		SimpleRemoteControl simpleRemoteControl = new SimpleRemoteControl();
		Light light = new Light();
		simpleRemoteControl.setCommand(new LightOnCommand(light));
		simpleRemoteControl.buttonWasPressed();
	}
}
```

If you want to execute multi-comamnds, then you can easily implement MacroCommand that has multi commands as a variable.

```java
public class MacroCommand implements Command {
	Command[] commands;

	public MacroCommand(Command[] coomands) {
		this.commands = coomands;
	}

	@Override
	public void execute() {
		Arrays.stream(commands).forEach(Command::execute);
	}

	public static void main(String[] args) {
		Light light = new Light();

		LightOnCommand lightOnCommand = new LightOnCommand(light);
		LightOffCommand lightOffCommand = new LightOffCommand(light);

		Command[] commands = {lightOnCommand, lightOffCommand};
		MacroCommand macroCommand = new MacroCommand(commands);

		SimpleRemoteControl simpleRemoteControl = new SimpleRemoteControl();
		simpleRemoteControl.setCommand(macroCommand);

		simpleRemoteControl.buttonWasPressed();
	}
}
```


#### Java 8 Lambda

Command interface is same. But this Command interface has single abstract method. So this is FunctionalInterface.

```java
public interface Command {
	public void execute();
}
```

Now, you can easily implement RemoteControl like below with lambda expression.

```java
public class RemoteControl {
	private HashMap<Integer, Command> commands = new HashMap<>();

	public void register(int slot, Command command) {
		commands.put(slot, command);
	}

	public void buttonPressed(int slot) {
		if (commands.containsKey(slot) == false) {
			System.out.println("This button is not registed yet.");
		}

		commands.get(slot).execute();
	}

	public static void main(String[] args) {
		Light light = new Light();

		RemoteControl remoteControl = new RemoteControl();
		remoteControl.register(1, () -> light.on());
		remoteControl.register(2, () -> light.off());
		remoteControl.register(3, () -> {
			light.on();
			light.off();
		});

		remoteControl.buttonPressed(1);
		remoteControl.buttonPressed(2);
		remoteControl.buttonPressed(3);
	}
}
```