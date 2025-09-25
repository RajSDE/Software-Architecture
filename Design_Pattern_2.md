# Design Patterns: Complete Developer's Guide

## What Are Design Patterns?

Design patterns are **proven, reusable solutions to commonly occurring problems** in software design. They serve as templates or blueprints that provide standardized approaches to solve recurring design challenges without being rigid code structures. Think of them as architectural blueprints - you can see the structure and features, but the exact implementation details are up to you.

### Key Benefits
- **Proven Solutions**: Tested, reliable approaches that prevent "reinventing the wheel"
- **Common Language**: Standardized terminology for better developer communication
- **Code Quality**: Promote cleaner, more maintainable, and scalable code
- **Faster Development**: Established solutions speed up the development process

## [The Gang of Four (GoF) Foundation](Design_Pattern_GOF_Pattern.md)

The **Gang of Four** (Erich Gamma, Richard Helm, Ralph Johnson, and John Vlissides) documented **23 classic design patterns** in their 1994 book "Design Patterns: Elements of Reusable Object-Oriented Software". This book became the cornerstone of object-oriented software development and continues to be vital in every software engineer's toolki

## The Three Categories of Design Patterns

All design patterns fall into three main categories based on their purpose

---

# CREATIONAL PATTERNS
**Focus**: Object creation mechanisms - how objects are instantiat

## 1. Singleton Pattern

**Problem**: Need exactly one instance of a class (database connections, configuration settings)

**Solution**: Ensures a class has only one instance and provides global access

**Java Implementation**:
```java
public class DatabaseConnection {
    private static DatabaseConnection instance;
    private static final Object lock = new Object();
    
    private DatabaseConnection() {
        System.out.println("Connecting to database...");
    }
    
    public static DatabaseConnection getInstance() {
        if (instance == null) {
            synchronized (lock) {
                if (instance == null) {
                    instance = new DatabaseConnection();
                }
            }
        }
        return instance;
    }
}
```

**When to Use**: Configuration managers, logging services, database pools, cache managers

**Spring Boot**: Default scope for Spring beans

## 2. Factory Method Pattern

**Problem**: Need to create different types of objects without specifying exact classes

**Solution**: Defines interface for creating objects, letting subclasses decide which class to instantiat

**Java Implementation**:
```java
// Product interface
interface Vehicle {
    void start();
    void stop();
}

// Concrete products
class Car implements Vehicle {
    public void start() { System.out.println("Car started"); }
    public void stop() { System.out.println("Car stopped"); }
}

class Motorcycle implements Vehicle {
    public void start() { System.out.println("Motorcycle started"); }
    public void stop() { System.out.println("Motorcycle stopped"); }
}

// Factory
abstract class VehicleFactory {
    public abstract Vehicle createVehicle();
    
    public Vehicle getVehicle() {
        Vehicle vehicle = createVehicle();
        vehicle.start();
        return vehicle;
    }
}

class CarFactory extends VehicleFactory {
    public Vehicle createVehicle() {
        return new Car();
    }
}
```

**When to Use**: Complex object creation logic, flexible object types

**Spring Boot**: Bean creation in ApplicationContext

## 3. Builder Pattern

**Problem**: Creating objects with many optional parameters without constructor explosion

**Solution**: Constructs complex objects step by step

**Java Implementation**:
```java
public class User {
    private final String firstName;
    private final String lastName;
    private final String email;
    private final String phone;
    
    private User(UserBuilder builder) {
        this.firstName = builder.firstName;
        this.lastName = builder.lastName;
        this.email = builder.email;
        this.phone = builder.phone;
    }
    
    public static class UserBuilder {
        private String firstName;
        private String lastName;
        private String email;
        private String phone;
        
        public UserBuilder(String firstName, String lastName) {
            this.firstName = firstName;
            this.lastName = lastName;
        }
        
        public UserBuilder email(String email) {
            this.email = email;
            return this;
        }
        
        public UserBuilder phone(String phone) {
            this.phone = phone;
            return this;
        }
        
        public User build() {
            return new User(this);
        }
    }
}

// Usage
User user = new User.UserBuilder("John", "Doe")
        .email("john@email.com")
        .phone("123-456-7890")
        .build();
```

**When to Use**: Many optional parameters, immutable objects, complex construction

**Spring Boot**: Configuration builders, query builders

## 4. Abstract Factory Pattern

**Problem**: Need to create families of related objects

**Solution**: Provides interface for creating families of related objects without specifying concrete classes

**When to Use**: Multiple product families, platform-independent code

## 5. Prototype Pattern  

**Problem**: Creating objects is expensive, need copies of existing objects

**Solution**: Creates new objects by copying existing ones

**When to Use**: Expensive object creation, similar objects needed

***

# STRUCTURAL PATTERNS
**Focus**: Object composition - how classes and objects combine to form larger structures

## 6. Adapter Pattern

**Problem**: Incompatible interfaces need to work together

**Solution**: Converts interface of a class into another interface clients expect

**Java Implementation**:
```java
// Target interface (what client expects)
interface MediaPlayer {
    void play(String audioType, String fileName);
}

// Adaptee (existing incompatible interface)  
interface AdvancedMediaPlayer {
    void playVlc(String fileName);
    void playMp4(String fileName);
}

class VlcPlayer implements AdvancedMediaPlayer {
    public void playVlc(String fileName) {
        System.out.println("Playing vlc file: " + fileName);
    }
    public void playMp4(String fileName) { /* Not supported */ }
}

// Adapter
class MediaAdapter implements MediaPlayer {
    private AdvancedMediaPlayer advancedPlayer;
    
    public MediaAdapter(String audioType) {
        if (audioType.equalsIgnoreCase("vlc")) {
            advancedPlayer = new VlcPlayer();
        }
    }
    
    public void play(String audioType, String fileName) {
        if (audioType.equalsIgnoreCase("vlc")) {
            advancedPlayer.playVlc(fileName);
        }
    }
}
```

**When to Use**: Third-party library integration, legacy system integration

**Spring Boot**: Data access abstraction, REST template adapters

## 7. Facade Pattern

**Problem**: Complex subsystem needs simplified interface

**Solution**: Provides unified interface to complex subsystem

**Java Implementation**:
```java
// Complex subsystem classes
class CPU {
    public void freeze() { System.out.println("CPU: Freezing"); }
    public void jump(long position) { System.out.println("CPU: Jumping"); }
    public void execute() { System.out.println("CPU: Executing"); }
}

class Memory {
    public void load(long position, byte[] data) {
        System.out.println("Memory: Loading data");
    }
}

class HardDrive {
    public byte[] read(long lba, int size) {
        System.out.println("HardDrive: Reading data");
        return new byte[size];
    }
}

// Facade
class ComputerFacade {
    private CPU processor;
    private Memory ram;
    private HardDrive hd;
    
    public ComputerFacade() {
        this.processor = new CPU();
        this.ram = new Memory();
        this.hd = new HardDrive();
    }
    
    public void start() {
        System.out.println("Starting computer...");
        processor.freeze();
        ram.load(0, hd.read(0, 1024));
        processor.jump(0);
        processor.execute();
        System.out.println("Computer started!");
    }
}
```

**When to Use**: Complex subsystems, API wrappers, simplified interfaces

**Spring Boot**: Spring Boot itself is a facade over Spring framework

## 8. Proxy Pattern

**Problem**: Need to control access to an object

**Solution**: Provides placeholder/surrogate for another object

**Types**: Virtual proxy (lazy loading), Protection proxy (access control), Remote proxy

**When to Use**: Lazy loading, access control, caching, logging

**Spring Boot**: AOP proxies, transaction management

## Other Structural Patterns

- **Decorator**: Adds behavior to objects dynamically
- **Composite**: Treats individual and composite objects uniformly
- **Bridge**: Decouples abstraction from implementation
- **Flyweight**: Shares common data to minimize memory usage

***

# BEHAVIORAL PATTERNS  
**Focus**: Communication between objects and responsibility assignment

## 9. Observer Pattern

**Problem**: Multiple objects need to stay synchronized when one changes

**Solution**: Defines one-to-many dependency for automatic notifications

**Java Implementation**:
```java
import java.util.*;

interface Subject {
    void registerObserver(Observer observer);
    void removeObserver(Observer observer);
    void notifyObservers();
}

interface Observer {
    void update(String message);
}

class NewsAgency implements Subject {
    private List<Observer> observers;
    private String news;
    
    public NewsAgency() {
        this.observers = new ArrayList<>();
    }
    
    public void registerObserver(Observer observer) {
        observers.add(observer);
    }
    
    public void removeObserver(Observer observer) {
        observers.remove(observer);
    }
    
    public void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(news);
        }
    }
    
    public void setNews(String news) {
        this.news = news;
        notifyObservers();
    }
}

class NewsChannel implements Observer {
    private String name;
    
    public NewsChannel(String name) {
        this.name = name;
    }
    
    public void update(String news) {
        System.out.println(name + " received: " + news);
    }
}
```

**When to Use**: Event handling, model-view architectures, pub-sub systems

**Spring Boot**: Event publishing with `@EventListener`

## 10. Strategy Pattern

**Problem**: Need to choose algorithms at runtime

**Solution**: Defines family of algorithms, makes them interchangeable

**Java Implementation**:
```java
interface PaymentStrategy {
    void pay(double amount);
}

class CreditCardPayment implements PaymentStrategy {
    private String cardNumber;
    
    public CreditCardPayment(String cardNumber) {
        this.cardNumber = cardNumber;
    }
    
    public void pay(double amount) {
        System.out.println("Paid $" + amount + " using Credit Card");
    }
}

class PayPalPayment implements PaymentStrategy {
    private String email;
    
    public PayPalPayment(String email) {
        this.email = email;
    }
    
    public void pay(double amount) {
        System.out.println("Paid $" + amount + " using PayPal");
    }
}

class ShoppingCart {
    private PaymentStrategy paymentStrategy;
    
    public void setPaymentStrategy(PaymentStrategy strategy) {
        this.paymentStrategy = strategy;
    }
    
    public void checkout(double amount) {
        paymentStrategy.pay(amount);
    }
}
```

**When to Use**: Multiple algorithms, avoiding conditionals, runtime selection

**Spring Boot**: Security authentication strategies

## 11. Command Pattern

**Problem**: Need to decouple request invokers from request handlers

**Solution**: Encapsulates requests as objects

**Java Implementation**:
```java
interface Command {
    void execute();
    void undo();
}

class Light {
    public void turnOn() { System.out.println("Light ON"); }
    public void turnOff() { System.out.println("Light OFF"); }
}

class LightOnCommand implements Command {
    private Light light;
    
    public LightOnCommand(Light light) {
        this.light = light;
    }
    
    public void execute() { light.turnOn(); }
    public void undo() { light.turnOff(); }
}

class RemoteControl {
    private Command[] commands;
    private Command lastCommand;
    
    public RemoteControl() {
        commands = new Command[7];
    }
    
    public void setCommand(int slot, Command command) {
        commands[slot] = command;
    }
    
    public void pressButton(int slot) {
        if (commands[slot] != null) {
            commands[slot].execute();
            lastCommand = commands[slot];
        }
    }
    
    public void pressUndo() {
        if (lastCommand != null) {
            lastCommand.undo();
        }
    }
}
```

**When to Use**: Undo operations, macro recording, queuing operations

**Spring Boot**: Transaction management, batch processing

## Other Behavioral Patterns

- **Template Method**: Defines algorithm skeleton, subclasses fill in steps
- **State**: Changes object behavior based on internal state
- **Chain of Responsibility**: Passes requests along chain of handlers
- **Mediator**: Centralizes complex communications between objects
- **Visitor**: Defines new operations without changing object classes
- **Iterator**: Provides sequential access to collection elements
- **Memento**: Saves/restores object states for undo functionality
- **Interpreter**: Defines language grammar and interpreter

***

# Design Patterns in Spring Boot

Spring Boot extensively uses design patterns :

## Commonly Used Patterns
- **Dependency Injection**: Core IoC pattern for loose coupling
- **Singleton**: Default scope for Spring beans  
- **Factory Method**: Bean creation in ApplicationContext
- **Proxy**: AOP and transaction management
- **Template Method**: JdbcTemplate, RestTemplate, TransactionTemplate
- **Observer**: Event publishing with @EventListener

## Pattern Usage Examples
```java
// Singleton (Spring Bean)
@Service
public class UserService { }

// Factory Method (Configuration)
@Configuration
public class DatabaseConfig {
    @Bean
    public DataSource dataSource() {
        return new HikariDataSource();
    }
}

// Observer (Event Publishing)
@EventListener
public void handleUserCreated(UserCreatedEvent event) {
    // Handle event
}

// Template Method (JdbcTemplate)
@Autowired
private JdbcTemplate jdbcTemplate;

public User findById(Long id) {
    return jdbcTemplate.queryForObject(
        "SELECT * FROM users WHERE id = ?",
        new BeanPropertyRowMapper<>(User.class),
        id
    );
}
```

***

# Best Practices and Guidelines

## When to Use Design Patterns
1. **Solve Actual Problems**: Don't use patterns just to use them
2. **Keep It Simple**: Avoid over-engineering with unnecessary patterns
3. **Understand Trade-offs**: Patterns add complexity
4. **Use Framework Implementations**: Leverage Spring's built-in patterns
5. **Document Usage**: Help team understand pattern choices

## Common Anti-Patterns to Avoid
- **Pattern Abuse**: Using patterns where simple solutions work
- **Golden Hammer**: Using same pattern for every problem
- **Premature Optimization**: Adding patterns before they're needed
- **Copy-Paste Programming**: Not understanding the pattern's purpose

## Learning Path
1. **Master OOP Principles**: Encapsulation, inheritance, polymorphism
2. **Start with Common Patterns**: Singleton, Factory, Observer, Strategy
3. **Practice with Real Problems**: Don't just memorize examples
4. **Study Framework Usage**: See patterns in Spring, Java APIs
5. **Refactor Existing Code**: Apply patterns to improve design

## Pattern Selection Criteria
- **Problem Complexity**: Simple problems don't need complex patterns
- **Team Experience**: Consider team's pattern knowledge
- **Maintainability**: Will this pattern help long-term maintenance?
- **Performance**: Some patterns add overhead
- **Testability**: Patterns should improve, not hinder testing

***

Design patterns are powerful tools that, when used appropriately, create more maintainable, scalable, and robust applications. They represent decades of collective wisdom from the software engineering community and provide a common vocabulary for discussing design solutions. The key is understanding not just how to implement them, but when and why to use each pattern.
