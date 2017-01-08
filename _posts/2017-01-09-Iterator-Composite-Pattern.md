---
layout: post
title: 'Iterator & Composite Pattern'
author: yongmaroo.kim
date: 2017-01-09 02:00:00
tags: [design pattern,java]
---

## Iterator Pattern

### Definition

The iterator pattern is a design pattern in which an iterator is used to traverse a container and access the container's elements. The iterator pattern decouples algorithms from containers; in some cases, algorithms are necessarily container-specific and thus cannot be decoupled.

![Iterator Pattern Class Diagram](/files/design-pattern/iterator-composite-pattern/iterator_pattern.png)

### Implementation

#### Base object

```java
public interface Menu {
	public Iterator<MenuItem> createIterator();
}
```

```java
public class MenuItem {
	String name;
	String description;
	boolean vegetarian;
	double price;

	public MenuItem(String name, String description, boolean vegetarian, double price) {
		this.name = name;
		this.description = description;
		this.vegetarian = vegetarian;
		this.price = price;
	}

	public String getName() {
		return name;
	}

	public String getDescription() {
		return description;
	}

	public boolean isVegetarian() {
		return vegetarian;
	}

	public double getPrice() {
		return price;
	}

	@Override
	public String toString() {
		return "MenuItem{" +
				"name='" + name + '\'' +
				", description='" + description + '\'' +
				", vegetarian=" + vegetarian +
				", price=" + price +
				'}';
	}
}
```

#### Concrete Class

```java
public class PancakeHouseMenu implements Menu {
	ArrayList<MenuItem> menuItems;

	public PancakeHouseMenu() {
		menuItems = new ArrayList<>();

		addItem("K&B Pancake set",
				"scrambled egg & toast pancake", true, 2.99);
		addItem("Blueberry Pancake",
				"Blueberry Pancake", true, 3.49);
	}

	public void addItem(String name, String description, boolean vegetarian, double price) {
		menuItems.add(
				new MenuItem(name, description, vegetarian, price)
		);
	}

	public Iterator<MenuItem> createIterator() {
		return menuItems.iterator();
	}
}
```

```java
public class DinerMenu implements Menu {
	static final int MAX_ITEMS = 6;
	int numberOfItems = 0;
	MenuItem[] menuItems;

	public DinerMenu() {
		menuItems = new MenuItem[MAX_ITEMS];

		addItem("Vegetarian BLT",
				"Vegetarian BLT", true, 2.99);
		addItem("BLT",
				"BLT", false, 2.99);
		addItem("Today's soup",
				"Soup with potato salad", false, 3.29);
		addItem("Hotdog",
				"Hotdog with cheese", false, 3.29);
	}

	public void addItem(String name, String description, boolean vegetarian, double price) {
		MenuItem menuItem = new MenuItem(name, description, vegetarian, price);

		if (MAX_ITEMS <= numberOfItems) {
			System.out.println("Sorry, menu is full. It cannot add new item");
		} else {
			menuItems[numberOfItems] = menuItem;
			numberOfItems += 1;
		}
	}

	public Iterator<MenuItem> createIterator() {
		return new DinerMenuIterator(menuItems);
	}
}
```

```java
public class CafeMenu implements Menu {
	Hashtable<String, MenuItem> menuItems = new Hashtable<>();

	public CafeMenu() {
		addItem("Burger & Fries",
				"Burger & potato fries", true, 3.99);
		addItem("Today's soup",
				"Soup with salad", false, 3.69);
	}

	public void addItem(String name, String description, boolean vegetarian, double price) {
		MenuItem menuItem = new MenuItem(name, description, vegetarian, price);
		menuItems.put(menuItem.getName(), menuItem);
	}

	@Override
	public Iterator<MenuItem> createIterator() {
		return menuItems.values().iterator();
	}
}
```

#### Concrete Iterator

```java
public class DinerMenuIterator implements Iterator<MenuItem> {
	MenuItem[] items;
	int position = 0;

	public DinerMenuIterator(MenuItem[] items) {
		this.items = items;
	}

	@Override
	public boolean hasNext() {
		if (items.length <= position || items[position] == null) {
			return false;
		} else {
			return true;
		}
	}

	@Override
	public MenuItem next() {
		MenuItem menuItem = items[position];
		position += 1;
		return menuItem;
	}
}
```

#### Client

```java
public class Waitress {
	List<Menu> menus;

	public Waitress(List<Menu> menus) {
		this.menus = menus;
	}

	public void printMenu() {
		System.out.println("Menus : ");

		menus.forEach(menu -> {
			System.out.println("===");
			printMenu(menu.createIterator());
		});
	}

	private void printMenu(Iterator<MenuItem> iterator) {
		while (iterator.hasNext()) {
			System.out.println(iterator.next());
		}
	}
}
```

#### Test

```java
public class MenuTest {
	public static void main(String[] args) {
		List<Menu> menus = Arrays.asList(new PancakeHouseMenu(), new DinerMenu(), new CafeMenu());
		Waitress waitress = new Waitress(menus);

		waitress.printMenu();
	}
}
```


## Composite Pattern

### Definition

The composite pattern is a partitioning design pattern. The composite pattern describes that a group of objects is to be treated in the same way as a single instance of an object. The intent of a composite is to "compose" objects into tree structures to represent part-whole hierarchies. Implementing the composite pattern lets clients treat individual objects and compositions uniformly.[

![Composite Pattern Class Diagram](/files/design-pattern/iterator-composite-pattern/composite-pattern.png)

### Implementation

#### Component

```java
public abstract class MenuComponent {
	public void add(MenuComponent menuComponent) {
		throw new UnsupportedOperationException();
	}

	public void remove(MenuComponent menuComponent) {
		throw new UnsupportedOperationException();
	}

	public MenuComponent getChild(int i) {
		throw new UnsupportedOperationException();
	}

	public String getName() {
		throw new UnsupportedOperationException();
	}

	public String getDescription() {
		throw new UnsupportedOperationException();
	}

	public boolean isVegetarian() {
		throw new UnsupportedOperationException();
	}

	public double getPrice() {
		throw new UnsupportedOperationException();
	}

	public void print() {
		throw new UnsupportedOperationException();
	}

	public Iterator<MenuComponent> createIterator() {
		throw new UnsupportedOperationException();
	}
}
```

#### Composite 

```java
public class Menu extends MenuComponent {
	List<MenuComponent> menuComponents = new ArrayList<>();
	String name;
	String description;

	public Menu(String name, String description) {
		this.name = name;
		this.description = description;
	}

	public void add(MenuComponent menuComponent) {
		menuComponents.add(menuComponent);
	}

	public void remove(MenuComponent menuComponent) {
		menuComponents.remove(menuComponent);
	}

	public MenuComponent getChild(int i) {
		return menuComponents.get(i);
	}

	public String getName() {
		return name;
	}

	public String getDescription() {
		return description;
	}

	public void print() {
		System.out.print("\n" + getName());
		System.out.println(", " + getDescription());
		System.out.println("--------------------");

		menuComponents.forEach(MenuComponent::print);
	}

	public Iterator<MenuComponent> createIterator() {
		return new CompositeIterator(menuComponents.iterator());
	}
}
```

#### Leaf

```java
public class MenuItem extends MenuComponent {
	String name;
	String description;
	boolean vegetarian;
	double price;

	public MenuItem(String name, String description, boolean vegetarian, double price) {
		this.name = name;
		this.description = description;
		this.vegetarian = vegetarian;
		this.price = price;
	}

	public String getName() {
		return name;
	}

	public String getDescription() {
		return description;
	}

	public boolean isVegetarian() {
		return vegetarian;
	}

	public double getPrice() {
		return price;
	}

	public void print() {
		System.out.print(" " + getName());
		if (isVegetarian()) {
			System.out.print("(v)");
		}
		System.out.println(", " + getPrice());
		System.out.println("    -- " + getDescription());
	}

	public Iterator<MenuComponent> createIterator() {
		return new NullIterator();
	}
}
```

#### Composite with Iterator

```java
public class CompositeIterator implements Iterator<MenuComponent> {
	Stack<Iterator> stack = new Stack();

	public CompositeIterator(Iterator<MenuComponent> iterator) {
		stack.push(iterator);
	}

	@Override
	public boolean hasNext() {
		if (stack.empty()) {
			return false;
		} else {
			Iterator iterator = stack.peek();
			if (!iterator.hasNext()) {
				stack.pop();
				return hasNext();
			} else {
				return true;
			}
		}
	}

	@Override
	public MenuComponent next() {
		if (hasNext()) {
			Iterator<MenuComponent> iterator = stack.peek();
			MenuComponent component = iterator.next();

			if (component instanceof Menu) {
				stack.push(((Menu) component).createIterator());
			}
			return component;
		} else {
			return null;
		}
	}
}
```

```java
public class NullIterator implements Iterator<MenuComponent> {
	@Override
	public boolean hasNext() {
		return false;
	}

	@Override
	public MenuComponent next() {
		return null;
	}
}
```

#### Client

```java
public class Waitress {
	MenuComponent allMenus;

	public Waitress(MenuComponent allMenus) {
		this.allMenus = allMenus;
	}

	public void print() {
		allMenus.print();
	}

	public void printVegetarianMenu() {
		Iterator iterator = allMenus.createIterator();
		System.out.println("\nVegetarian Menu");

		while (iterator.hasNext()) {
			MenuComponent menuComponent = (MenuComponent) iterator.next();

			try {
				if (menuComponent.isVegetarian()) {
					menuComponent.print();
				}
			} catch (UnsupportedOperationException e) {}
		}
	}
}
```

#### Test

```java
public class MenuTest {
	public static void main(String[] args) {
		MenuComponent pancakeHouseMenu = new Menu("Pancake house menu", "breakfast menu");
		MenuComponent dinerMenu = new Menu("ObjectTown menu", "lunch menu");
		MenuComponent cafeMenu = new Menu("cafe menu", "dinner menu");
		MenuComponent dessertMenu = new Menu("dessert menu", "enjoy dessert!!");

		MenuComponent allMenus = new Menu("all menus", "all menus");

		allMenus.add(pancakeHouseMenu);
		allMenus.add(dinerMenu);
		allMenus.add(cafeMenu);

		dinerMenu.add(new MenuItem("pasta", "pasta with bread", true, 3.89));
		dinerMenu.add(dessertMenu);

		dessertMenu.add(new MenuItem("apple pie", "apple pie with vanilla icecream", true, 1.59));

		Waitress waitress = new Waitress(allMenus);
		waitress.print();
		waitress.printVegetarianMenu();
	}
}
```