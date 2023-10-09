
# SOLID Principle
https://www.freecodecamp.org/news/solid-principles-single-responsibility-principle-explained/

## Single Responsibility
- A class should have only one reason to change. 
- Class should have only 1 purpose - **Applies to class**
- Exa: class should not be doing hashing as well as save to file operation

https://en.wikipedia.org/wiki/Single-responsibility_principle

## Open close Principle
- The open-closed principle states that software entities should be open for extension, but closed for modification.  **Applies to class**
- If we have a password hasher and adding new pass hasher algo, then it should be such that adding new algo do not change existing class as well as adding new algo should be easily expandable such than min no of code needs to be written
- Feel free to **extend** our classes with any new behavior you like. If your needs or requirements change (and we know they will), just go ahead and make your own extensions.
- Sorry, we’re **closed**. That’s right, we spent a lot of time getting this code correct and bug free, so we can’t let you alter the existing code. It must remain closed to modification. If you don’t like it, you can speak to the manager.
- Our goal is to allow classes to be easily extended to incorporate new behavior without modifying existing code. What do we get if we accomplish this? Designs that are resilient to change and flexible enough to take on new functionality to meet changing requirements.
- **Decorator pattern** is an example of  this principle


## Liskov substitution principle
- **Applies on subclass - inheritance**
- Each subclass should be substitutable for its parent class. It should only extend its functionality rather than overriding it…
- Like Square should not be subclass of Rectangle since it does not have properties called width and height. For square both are same. So rather it should be a parallel class not inherited one.
- The Liskov substitution principle simply implies that when an instance of a class is passed/extended to another class, the inheriting class should have a use case for all the properties and behavior of the inherited class.

## Interface segregation Principle
- segregate interfaces as much as possible. **Applies to interfaces**
- It should not force the class to implement things which are not required.
- The interface segregation principle states that the interface of a program **should be split** in a way that the user/client would only have access to the necessary methods related to their needs.

## Dependency inversion principle
- **Woks for Composition**
- Components should be dependent on abstraction(Interfaces) rather than concrete classes. i.e, Class should not load concrete class and then call its method. Rather it should have variable like IHasher which is interface/abstract class and then call IHasher.hashPw() so that - IHasher can have multiple hashing algorithms and adding/ deleting algorithm do not require our class to change implementations
One of the way to achieve this is Factory pattern


# Overview
1. In LLD Interview…
- First identity entities… like players, board, snack, ladder
- Separate out Models ← data and Services ← logical part
- Don't directly jump to code.
- Think about the behaviors of entities
- Checking our common behaviors and specific behaviors also helps identifying Interfaces and inheritance
- See Inheritance vs Composition

2. Total Design Patterns
- Creation
Factory: creates and returns a new obj based on argument
Abstract Factory Pattern: factory of factory
Singleton: Single object of a class. 
Builder: Builds complex object 
Prototype: to create duplicate object
Structural → How more than one class/objects are structured
Adapter
Bridge
Composite
Decorator
Facade
Flyweight
Proxy
Behavioral → How more than one objects communicate with each other
Observer
State : When system can have multiple states
Strategy
Interpreter
Template
Chain of responsibility
Command
Iterator
Mediator
Memento

J2EE patterns
Strategy Pattern
Deciding behavior class based on input
A strategy pattern is an operational pattern.
Observer Pattern
One data source object notifies all observers regarding data change. Observers can be added and removed at any time
Here subject and observers are loosely coupled.
Decorator Pattern
Attaches additional responsibility to object at run time
When to use: some set of concrete classes can use a combination of additional features or functionality which can be added at run time.
It wraps concrete class and becomes wrapper to it by adding its own functionality like 
Pizza pizza = new CheeseToppings(new Margarita())
Cons: Adds lot more small sub classes
Example:

Factory pattern
A factory pattern is a creational pattern. Put another way, a factory pattern is used to create objects of a specific type. A strategy pattern is use to perform an operation (or set of operations) in a particular manner
Abstract Factory pattern 
Factory of factory


Builder pattern
If you find yourself in a situation where you keep on adding new parameters to a constructor, resulting in code that becomes error-prone and hard to read, perhaps it's a good time to take a step back and consider refactoring your code to use a Builder.
Proxy pattern:
Useful to wrap out main method from client and put explicit sanity checks/ validations
Prototype design pattern
Creational design pattern
In the prototype pattern, cloning is delegated to the actual objects being cloned.
Basically concrete class implements Cloneable interface and overrides clone method.
Clients need not worry when references object using interface/abstract class which actual object being copied. 
Command Design Pattern
TODO
State Design Pattern
Can use this when your system have multiple states and multiple events on same state
Can create interface of State with possible functionalities/event at each state
Avoids multiple if-else conditions
Coupling : The knowledge or dependency each class has about other classes. This should be minimum. 
Cohesion : Degree of similarities
Cohesion refers to the level of a component which performs a single well-defined task
The java.io package is a highly cohesive package because it has I/O related classes and interface. However, the java.util package is a weakly cohesive package because it has unrelated classes and interfaces.
Association : Association represents the relationship between the objects.
1:1
1:many
many:1
many:many
Aggregation : Aggregation is a way to achieve Association. Aggregation represents the relationship where one object contains other objects as a part of its state. 
It represents the weak relationship between objects.
It is also termed as a has-a relationship in Java. 
Like, inheritance represents the is-a relationship. It is another way to reuse objects.
Composition
Way to achieve association
Strong relationship between objects
Child object can not exist without a parent object.
Has-a relationship. Board has pieces
Inheritance
Is-a relationship
Engineer is-a employee.
Object based programming language
Object-based programming language follows all the features of OOPs except Inheritance. JavaScript and VBScript are examples of object-based programming languages.
When the type of the object is determined at compile time(by the compiler), it is known as static binding.
When the type of the object is determined at run-time, it is known as dynamic binding.
Think about concurrency in LLD interview




Head first Design patterns 


Snaps of design principles











Symbols used in class diagrams



References
LLD primer: https://github.com/prasadgujar/low-level-design-primer/blob/master/solutions.md
Command Design Pattern : https://medium.com/@learncsdesign/learn-the-command-design-pattern-60cad5b85e34
https://www.tutorialspoint.com/design_pattern/




