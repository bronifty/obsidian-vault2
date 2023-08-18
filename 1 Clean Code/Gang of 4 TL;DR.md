summary: construction is the construction of objects from classes. structure is the composition of those constructed objects from classes. behavior is the behavior mechanisms of those composed classes in a program.

Creation
1. Abstract Factory what
2. Builder concrete impl with extra steps
3. Factory Method function extraction
4. Prototype built into js
5. Singleton makes the constructor private with a public method to access the unique instance (see image 1)

Structure (composition)
1. Adapter inject dependency of one class implementation of an interface into another class which implements another interface 
2. Bridge garbage for inheritance; use strategy
3. Composite same as strat but immutable methods in task class (see image 2)
4. Decorator a multistep form of dependency injection
5. Facade combines multiple concrete implementations and composes (a) method(s) comprising extracted class methods
6. Flyweight a garbage version of strategy (see image 3)
7. Proxy proxy is a wrapper around the class which adds members (properties and methods)

### Behavioral Patterns:
1. Chain of Responsibility strategy with extra steps that works less efficiently to do the same thing as the actor model (comms between objects)
2. Command strategy with extra steps
3. Interpreter another strategy pattern
4. **Iterator**: Sequentially access the elements of a collection.
5. **Mediator**: Defines simplified communication between classes.
6. **Memento**: Capture and restore an object's internal state.
7. **Observer**: A way of notifying change to a number of classes.
8. **State**: Alter an object's behavior when its state changes.
9. **Strategy**: Encapsulates an algorithm inside a class.
10. **Template Method**: Defer the exact steps of an algorithm to a subclass.
11. **Visitor**: Defines a new operation to a class without change.