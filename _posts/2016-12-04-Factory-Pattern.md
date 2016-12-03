---
layout: post
title: 'Factory Pattern'
author: yongmaroo.kim
date: 2016-11-27 23:55
tags: [design pattern,java]
---

### Definition

#### Factory Method Pattern

The factory method pattern is a creational pattern that uses factory methods to deal with the problem of creating objects without having to specify the exact class of the object that will be created. This is done by creating objects by calling a factory method—either specified in an interface and implemented by child classes, or implemented in a base class and optionally overridden by derived classes—rather than by calling a constructor.

![Factory Method Pattern Class Diagram](/files/design-pattern/factory-pattern/factory-method.jpg)
![Factory Method Example](/files/design-pattern/factory-pattern/factory-method-example.png)

#### Abstract Factory Pattern

The abstract factory pattern provides a way to encapsulate a group of individual factories that have a common theme without specifying their concrete classes.

![Abstract Factory Pattern Class Diagram](/files/design-pattern/factory-pattern/abstract-factory.jpg)
![Abstract Factory Example](/files/design-pattern/factory-pattern/abstract-factory-example.png)


### Dependency Inversion Principle

he dependency inversion principle refers to a specific form of decoupling software modules. When following this principle, the conventional dependency relationships established from high-level, policy-setting modules to low-level, dependency modules are reversed, thus rendering high-level modules independent of the low-level module implementation details.

- High-level modules should not depend on low-level modules. Both should depend on abstractions.
- Abstractions should not depend on details. Details should depend on abstractions.


### Example

[Source Code Repository](https://github.com/kymr/design-pattern/tree/master/src/main/java/design/pattern/factory)

This example is based on Head-First example which uses Pizza & PizzaStore

![Simple Factory](/files/design-pattern/factory-pattern/simple-factory.jpg)

There are a lot of kinds of Pizza. So you can use factory to create pizza object.

![Factory Method](/files/design-pattern/factory-pattern/factory-method-pizza.jpg)

Each location has its own style to create pizza. Because of that PizzaStore declares as a abstract class, and each chain stores inherent it. The important thing is create-method is declared as a abstract which is need to be implemented by each concrete classes. The result of which, it can encapsulate the instance creation logic. And instance creation is executed by each concrete classes, it can be easily changed. This pattern is called as 'Factory-Method-Pattern'

But, There are a lot of objects. And PizzaStore has dependency with all the concrete Pizza objects. Decoupling is important. So in this case we need to focus how to decouple between PizzaStore and Pizza.
The biggest problem is PizzaStore depends on each style concrete pizza. Even though each branch store has its own style, we don't need to create all the pizza objects for each store's style if we abstract the pizza style.
The difference among each chain stores is ingredients. So if we apply Abstract Factory to create pizza ingredient objects for each chain's style all over the pizza, then we don't need to declare each concrete styles of pizza object.

![Abstract Factory](/files/design-pattern/factory-pattern/abstract-factory-pizza.jpg)

