# Static factory methods instead of constructors


A while ago I started reading a highly recommended book for Java developers - Effective Java(2nd edition) by Joshua Bloch.
This book has a wealth of knowledge about how to write effective java code and not make common mistakes that affect the performance and in some cases, correctness of your code. Over the course of next few months, I intend to write more of my learnings and summarize the items from the book.

**Item 1: Consider static factory methods instead of constructors.**

Here the author discusses one of the ways one can allow clients of a particular class to create instances without using a constructor, the ***static factory method.***

> Static factory method : A simple static method that returns an instance of the class.

Example:
```
public static Boolean valueOf(boolean b) {
	return b ? Boolean.TRUE : Boolean.FALSE;
}
```

**Advantages**

***Unlike constructors, factory methods have names. This has many advantages.***

A well chosen name is easier to use and the client code easier to read. A class can have only a single constructor with a given signature. Often developers will change the order of the parameters and create new constructors. This would result in the client to remember the order of the arguments to pass to the constructor. This is really bad design. Using factory methods, the developer can give a logical name to different methods resulting in better code readability.

***Unlike constructors, they are not required to create a new object every time it is invoked.***

With static factory methods, you as a developer of the class has an option to limit the number of instances of the class. These classes are called *instance-controlled* classes. This is very useful when the creating a new object of the class is an expensive operation. An example given in the book is about the `valueOf` method in the Boolean class(example above). Here each time the method is invoked, one of the 2 values are returned. This way of creating and caching objects is also used in extensively in the lazy initialization of singleton objects. One added benefit of this is, you can check for objects being equal by using the `==` operator rather than the `equals` method.

***Unlike constructors, they can return an object of any subtype of their return type.***

This gives you the flexibility in choosing the class of the returned object. This also gives you the flexibility of making your API very thin by not making the implementation classes public.
Consider the `List` interface in Java. For one to define a static factory method is not an option as interfaces cannot have static methods. Hence we have a non-instantiable class `Collections` which has a bunch of static factory methods that return implementations of various collection interfaces. Many of these implementations are not made public. Hence with future versions of the release of Java, the authors can choose to add/change/replace these implementations without affecting the client code.
One example of adding new implementation is the **Service provider framework**. Example: JDBC. In the service provider framework, there are 3 main interfaces:

- The **service interface** which providers implement.
- The **provider registration API** which the system uses to register the provider implementations.
- The **service access API** which the clients use to obtain an instance of the service.
- The **service provider interface** which providers implement to create instances of their service implementation.

With reference to JDBC:

- `Connection` = service interface.
- `DriverManager.registerDriver` = provider registration API.
- `DriverManager.getConnection` = service access API.
- `Driver` = service provider interface.


**Disadvantages**

***Providing only static factory methods is that class without public or protected constructors cannot be subclassed.***

***Static factory methods are not easily distinguished from other static methods.***

This is mainly the result of the current javadoc tool as it does not have a separate section like constructors. To avoid this, naming static factory methods in a consistent way would help. Some common names mentioned in the book are `valueOf`, `of`, `getInstance`, `newInstance`, `getType` and `newType`.
