# LLD 
1. System Design Overview
    1. HLD (High-Level Design): Focuses on system architecture, components, and their interactions.
    2. LLD (Low-Level Design): Focuses on class diagrams, methods, relationships, and design patterns.
2. OOP’s and Why OOP’s?
    1. Pillars of OOP:
        1. Abstraction: Hides complexity, exposes only essentials.
            1. Example: Abstract class `Car` in `Week 1/Lec2/Abstraction.cpp`.
        2. Encapsulation: Bundles data and methods, restricts direct access.
            1. Example: Private members and setters/getters in `SportsCar` (`Week 1/Lec2/Encapsulation.cpp`).
        3. Inheritance: Enables code reuse via parent-child relationships.
            1. Example: `ManualCar` and `ElectricCar` inherit from `Car` (`Week 1/Lec3/Inheritance.cpp`).
        4. Polymorphism: Allows different behaviors via method overriding/overloading.
            1. Example: Overloaded `accelerate()` in `Week 1/Lec3/StaticDynamic.cpp`.
3. Association, Aggregation, and Composition
    1. Association: General relationship (e.g., `User` and `Group` in `Week 7/Lec1/main.cpp`).
    2. Aggregation: Whole-part, but parts can exist independently (e.g., `Folder` and `File` in `Week 4/Lec4/main.cpp`).
    3. Composition: Stronger form, parts cannot exist independently (e.g., `Folder` owns its `FileSystemItem` children).
4. Sequence Diagram
    1. Visualizes object interactions over time for a use case.
    2. (Draw as needed for your projects.)
5. SOLID Principles
    1. Single Responsibility Principle (SRP): One reason to change.
        1. Example: `ShoppingCart` vs `ShoppingCartPrinter` (`Week 2/Lec1/LID.cpp`).
    2. Open/Closed Principle (OCP): Open for extension, closed for modification.
        1. Example: `InvoiceFormatter` and its subclasses (`Week 2/Lec1/LID.cpp`).
    3. Liskov Substitution Principle (LSP): Subtypes must be substitutable for base types.
        1. Example: `SavingAccount` and `FixedTermAccount` (`Week 2/Lec1/LID.cpp`).
    4. Interface Segregation Principle (ISP): Prefer small, specific interfaces.
        1. Example: `DepositOnlyAccount` and `WithdrawableAccount`.
    5. Dependency Inversion Principle (DIP): Depend on abstractions, not concretions.
        1. Example: `CartPersistence` interface (`Week 2/Lec1/LID.cpp`).
6. Design Patterns (with Code Examples)
    1. Foundational Patterns
        1. Strategy: `SplitStrategy` (`Week 7/Lec1/main.cpp`), `WalkableRobot` (`Week 2/Lec3/Main.cpp`).
        2. Factory: `SimpleBurgerFactory`, `BurgerFactory`, `MealFactory` (`Week 2/Lec4/Main.cpp`).
        3. Singleton: `LazySingleton`, `EagerSingleton` (`Week 2/Lec5/main.cpp`).
        4. Observer: `Observer` (`Week 7/Lec1/main.cpp`), `Channel`/`Subscriber` (`Week 3/Lec2/main.cpp`).
        5. Decorator: `CharacterDecorator` (`Week 3/Lec3/main.cpp`).
        6. Command: `Command` and `RemoteController` (`Week 3/Lec5/main.cpp`).
        7. Adapter: `XmlDataProviderAdapter` (`Week 4/Lec1/main.cpp`).
        8. Facade: `ComputerFacade` (`Week 4/Lec2/main.cpp`).
        9. State: `VendingState` and `VendingMachine` (`Week 7/Lec2/main.cpp`).
        10. Mediator: `ChatRoom` and `User` (`Week 7/Lec5/main.cpp`).
        11. Prototype: `Cloneable` and `NPC` (`Week 8/Lec1/main.cpp`).
        12. Builder: `HttpRequestBuilder` (`Week 6/Lec3/main.cpp`).
        13. Iterator: `Iterator` and `Iterable` (`Week 6/Lec4/main.cpp`).
        14. Flyweight: `AsteroidTypeFactory` (`Week 6/Lec5/main.cpp`).
        15. Chain of Responsibility: `MoneyHandler` (`Week 5/Lec2/main.cpp`).
        16. Memento: `DatabaseMemento` and `TransactionManager` (`Week 8/Lec4/main.cpp`).
    2. Problem Specific Patterns
        1. Composite: `Folder` and `File` (`Week 4/Lec4/main.cpp`).
        2. Template: `ModelTrainer` (`Week 4/Lec5/main.cpp`).
        3. Proxy: `DocumentProxy` (`Week 5/Lec1/main.cpp`).
7. LLD (Low-Level Design) Workflow (How to Approach Any LLD Problem)
    1. Identify Entities and Responsibilities
        1. List main classes/objects and their core responsibilities.
            1. Example: `Car`, `ManualCar`, `ElectricCar` (`Week 1/Lec3/Inheritance.cpp`).
            2. Example: `User`, `Group`, `ExpenseManager` (`Week 7/Lec1/main.cpp`).
    2. Define Attributes and Methods
        1. For each class, specify its data (attributes) and actions (methods).
            1. Example: `SportsCar` (`Week 1/Lec2/Encapsulation.cpp`).
            2. Example: `Product`, `ShoppingCart` (`Week 2/Lec1/LID.cpp`).
    3. Apply OOP Principles (see section 2)
    4. Model Relationships (see section 3)
    5. Apply SOLID Principles (see section 5)
    6. Use Design Patterns Where Needed (see section 6)
    7. Draw Sequence Diagrams (Optional)
    8. Implement and Test
        1. Write code, create objects, and simulate real scenarios.
            1. Example: Using `ManualCar` and `ElectricCar` (`Week 1/Lec3/Inheritance.cpp`).
            2. Example: Testing vending machine states (`Week 7/Lec2/main.cpp`).
    9. Review and Refactor
        1. Check for clarity, simplicity, and adherence to principles.
8. Quick Reference Table (Where to Find Examples)
    1. OOP Pillars: `Week 1/Lec2/Abstraction.cpp`, `Week 1/Lec2/Encapsulation.cpp`, `Week 1/Lec3/Inheritance.cpp`, `Week 1/Lec3/StaticDynamic.cpp`
    2. SOLID: `Week 2/Lec1/LID.cpp`, `Week 1/Lec5/SOL.cpp`
    3. Design Patterns: See section 6 for file references
    4. Composite: `Week 4/Lec4/main.cpp`
    5. State: `Week 7/Lec2/main.cpp`
    6. Mediator: `Week 7/Lec5/main.cpp`
    7. Builder: `Week 6/Lec3/main.cpp`
    8. Memento: `Week 8/Lec4/main.cpp`
    9. Chain of Responsibility: `Week 5/Lec2/main.cpp`
    10. Flyweight: `Week 6/Lec5/main.cpp`
    11. Iterator: `Week 6/Lec4/main.cpp`
    12. Proxy: `Week 5/Lec1/main.cpp`
    13. Template: `Week 4/Lec5/main.cpp`
    14. Facade: `Week 4/Lec2/main.cpp`
    15. Adapter: `Week 4/Lec1/main.cpp`
    16. Decorator: `Week 3/Lec3/main.cpp`
    17. Observer: `Week 7/Lec1/main.cpp`, `Week 3/Lec2/main.cpp`
    18. Command: `Week 3/Lec5/main.cpp`
    19. Factory: `Week 2/Lec4/Main.cpp`
    20. Singleton: `Week 2/Lec5/main.cpp`
    21. Strategy: `Week 7/Lec1/main.cpp`, `Week 2/Lec3/Main.cpp`
    22. Prototype: `Week 8/Lec1/main.cpp`
