# 🎨 Design Patterns

> Essential software design patterns every developer should know.

---

## 📚 Table of Contents

1. [What Are Design Patterns?](#-what-are-design-patterns)
2. [Creational Patterns](#-creational-patterns)
3. [Structural Patterns](#-structural-patterns)
4. [Behavioral Patterns](#-behavioral-patterns)
5. [SOLID Principles](#-solid-principles)
6. [Anti-Patterns](#-anti-patterns)
7. [Resources](#-resources)

---

## 🎯 What Are Design Patterns?

**Design Patterns** are reusable solutions to commonly occurring problems in software design. They are **templates** for solving problems that can be used in many different situations.

### Why Use Design Patterns?

| Benefit | Description |
|---------|-------------|
| **Proven Solutions** | Battle-tested approaches |
| **Common Vocabulary** | Communicate design intent clearly |
| **Avoid Reinventing** | Don't solve same problems repeatedly |
| **Best Practices** | Follow industry standards |
| **Maintainability** | Easier to understand and modify |

---

## 🏗️ Creational Patterns

Patterns for **object creation** mechanisms.

---

### 1. **Singleton Pattern**

**Purpose:** Ensure a class has only **one instance** and provide global access to it.

**When to Use:**
- Database connection
- Logger
- Configuration manager
- Cache

#### Implementation (PHP)

```php
class Database {
    private static $instance = null;
    private $connection;
    
    // Private constructor prevents direct instantiation
    private function __construct() {
        $this->connection = new PDO(/* ... */);
    }
    
    // Prevent cloning
    private function __clone() {}
    
    // Prevent unserialization
    private function __wakeup() {}
    
    public static function getInstance() {
        if (self::$instance === null) {
            self::$instance = new self();
        }
        return self::$instance;
    }
    
    public function getConnection() {
        return $this->connection;
    }
}

// Usage
$db1 = Database::getInstance();
$db2 = Database::getInstance();
// $db1 === $db2 (same instance)
```

#### Implementation (JavaScript)

```javascript
class Database {
    constructor() {
        if (Database.instance) {
            return Database.instance;
        }
        this.connection = this.connect();
        Database.instance = this;
    }
    
    connect() {
        // Connection logic
        return 'Connected';
    }
}

const db1 = new Database();
const db2 = new Database();
// db1 === db2
```

---

### 2. **Factory Pattern**

**Purpose:** Create objects without specifying the exact class.

**When to Use:**
- Object creation is complex
- Need to decouple object creation from usage
- Conditional object creation

#### Implementation

```php
interface Vehicle {
    public function drive();
}

class Car implements Vehicle {
    public function drive() {
        return "Driving a car";
    }
}

class Bike implements Vehicle {
    public function drive() {
        return "Riding a bike";
    }
}

class VehicleFactory {
    public static function create($type) {
        switch ($type) {
            case 'car':
                return new Car();
            case 'bike':
                return new Bike();
            default:
                throw new Exception("Unknown vehicle type");
        }
    }
}

// Usage
$vehicle = VehicleFactory::create('car');
echo $vehicle->drive(); // "Driving a car"
```

---

### 3. **Abstract Factory Pattern**

**Purpose:** Create families of related objects without specifying their concrete classes.

```php
interface Button {
    public function render();
}

interface Checkbox {
    public function render();
}

class WindowsButton implements Button {
    public function render() { return "Windows Button"; }
}

class WindowsCheckbox implements Checkbox {
    public function render() { return "Windows Checkbox"; }
}

class MacButton implements Button {
    public function render() { return "Mac Button"; }
}

class MacCheckbox implements Checkbox {
    public function render() { return "Mac Checkbox"; }
}

interface GUIFactory {
    public function createButton(): Button;
    public function createCheckbox(): Checkbox;
}

class WindowsFactory implements GUIFactory {
    public function createButton(): Button {
        return new WindowsButton();
    }
    
    public function createCheckbox(): Checkbox {
        return new WindowsCheckbox();
    }
}

class MacFactory implements GUIFactory {
    public function createButton(): Button {
        return new MacButton();
    }
    
    public function createCheckbox(): Checkbox {
        return new MacCheckbox();
    }
}

// Usage
$factory = new WindowsFactory();
$button = $factory->createButton();
$checkbox = $factory->createCheckbox();
```

---

### 4. **Builder Pattern**

**Purpose:** Construct complex objects step by step.

**When to Use:**
- Object has many optional parameters
- Need immutable objects
- Want to avoid telescoping constructors

```php
class Pizza {
    private $size;
    private $cheese = false;
    private $pepperoni = false;
    private $bacon = false;
    
    public function setSize($size) { $this->size = $size; }
    public function setCheese($cheese) { $this->cheese = $cheese; }
    public function setPepperoni($pepperoni) { $this->pepperoni = $pepperoni; }
    public function setBacon($bacon) { $this->bacon = $bacon; }
}

class PizzaBuilder {
    private $pizza;
    
    public function __construct() {
        $this->pizza = new Pizza();
    }
    
    public function size($size) {
        $this->pizza->setSize($size);
        return $this; // Method chaining
    }
    
    public function cheese() {
        $this->pizza->setCheese(true);
        return $this;
    }
    
    public function pepperoni() {
        $this->pizza->setPepperoni(true);
        return $this;
    }
    
    public function bacon() {
        $this->pizza->setBacon(true);
        return $this;
    }
    
    public function build() {
        return $this->pizza;
    }
}

// Usage
$pizza = (new PizzaBuilder())
    ->size('large')
    ->cheese()
    ->pepperoni()
    ->build();
```

---

### 5. **Prototype Pattern**

**Purpose:** Clone existing objects instead of creating new ones.

```php
class Sheep {
    public $name;
    public $category;
    
    public function __construct($name, $category = 'Mountain Sheep') {
        $this->name = $name;
        $this->category = $category;
    }
    
    public function __clone() {
        // Deep cloning if needed
    }
}

// Usage
$original = new Sheep('Jolly');
$cloned = clone $original;
$cloned->name = 'Dolly';
```

---

## 🧱 Structural Patterns

Patterns for **composing classes and objects**.

---

### 1. **Adapter Pattern**

**Purpose:** Make incompatible interfaces work together.

```php
// Target interface
interface MediaPlayer {
    public function play($audioType, $fileName);
}

// Adaptee (incompatible interface)
class VlcPlayer {
    public function playVlc($fileName) {
        echo "Playing vlc file: $fileName\n";
    }
}

// Adapter
class MediaAdapter implements MediaPlayer {
    private $advancedPlayer;
    
    public function __construct($audioType) {
        if ($audioType == 'vlc') {
            $this->advancedPlayer = new VlcPlayer();
        }
    }
    
    public function play($audioType, $fileName) {
        if ($audioType == 'vlc') {
            $this->advancedPlayer->playVlc($fileName);
        }
    }
}

// Client
class AudioPlayer implements MediaPlayer {
    private $mediaAdapter;
    
    public function play($audioType, $fileName) {
        if ($audioType == 'mp3') {
            echo "Playing mp3 file: $fileName\n";
        } elseif ($audioType == 'vlc') {
            $this->mediaAdapter = new MediaAdapter($audioType);
            $this->mediaAdapter->play($audioType, $fileName);
        }
    }
}
```

---

### 2. **Decorator Pattern**

**Purpose:** Add new functionality to objects dynamically.

```php
interface Coffee {
    public function cost();
    public function description();
}

class SimpleCoffee implements Coffee {
    public function cost() {
        return 10;
    }
    
    public function description() {
        return "Simple coffee";
    }
}

class MilkDecorator implements Coffee {
    private $coffee;
    
    public function __construct(Coffee $coffee) {
        $this->coffee = $coffee;
    }
    
    public function cost() {
        return $this->coffee->cost() + 2;
    }
    
    public function description() {
        return $this->coffee->description() . ", milk";
    }
}

class SugarDecorator implements Coffee {
    private $coffee;
    
    public function __construct(Coffee $coffee) {
        $this->coffee = $coffee;
    }
    
    public function cost() {
        return $this->coffee->cost() + 1;
    }
    
    public function description() {
        return $this->coffee->description() . ", sugar";
    }
}

// Usage
$coffee = new SimpleCoffee();
$coffee = new MilkDecorator($coffee);
$coffee = new SugarDecorator($coffee);
echo $coffee->description(); // "Simple coffee, milk, sugar"
echo $coffee->cost(); // 13
```

---

### 3. **Facade Pattern**

**Purpose:** Provide a simplified interface to a complex subsystem.

```php
class CPU {
    public function freeze() { echo "CPU: Freeze\n"; }
    public function jump() { echo "CPU: Jump\n"; }
    public function execute() { echo "CPU: Execute\n"; }
}

class Memory {
    public function load() { echo "Memory: Load\n"; }
}

class HardDrive {
    public function read() { echo "HardDrive: Read\n"; }
}

// Facade
class Computer {
    private $cpu;
    private $memory;
    private $hardDrive;
    
    public function __construct() {
        $this->cpu = new CPU();
        $this->memory = new Memory();
        $this->hardDrive = new HardDrive();
    }
    
    public function start() {
        $this->cpu->freeze();
        $this->memory->load();
        $this->hardDrive->read();
        $this->cpu->jump();
        $this->cpu->execute();
    }
}

// Usage
$computer = new Computer();
$computer->start(); // Simple interface to complex operations
```

---

### 4. **Proxy Pattern**

**Purpose:** Control access to an object.

**Types:**
- **Virtual Proxy**: Lazy initialization
- **Protection Proxy**: Access control
- **Remote Proxy**: Remote object representation
- **Caching Proxy**: Cache results

```php
interface Image {
    public function display();
}

class RealImage implements Image {
    private $filename;
    
    public function __construct($filename) {
        $this->filename = $filename;
        $this->loadFromDisk();
    }
    
    private function loadFromDisk() {
        echo "Loading image: {$this->filename}\n";
    }
    
    public function display() {
        echo "Displaying image: {$this->filename}\n";
    }
}

class ProxyImage implements Image {
    private $realImage;
    private $filename;
    
    public function __construct($filename) {
        $this->filename = $filename;
    }
    
    public function display() {
        if ($this->realImage === null) {
            $this->realImage = new RealImage($this->filename);
        }
        $this->realImage->display();
    }
}

// Usage
$image = new ProxyImage('photo.jpg');
// Image not loaded yet
$image->display(); // Loads and displays
$image->display(); // Just displays (already loaded)
```

---

## 🎭 Behavioral Patterns

Patterns for **communication between objects**.

---

### 1. **Strategy Pattern**

**Purpose:** Define a family of algorithms and make them interchangeable.

```php
interface PaymentStrategy {
    public function pay($amount);
}

class CreditCardPayment implements PaymentStrategy {
    public function pay($amount) {
        echo "Paid $$amount via Credit Card\n";
    }
}

class PayPalPayment implements PaymentStrategy {
    public function pay($amount) {
        echo "Paid $$amount via PayPal\n";
    }
}

class BitcoinPayment implements PaymentStrategy {
    public function pay($amount) {
        echo "Paid $$amount via Bitcoin\n";
    }
}

class ShoppingCart {
    private $paymentStrategy;
    
    public function setPaymentStrategy(PaymentStrategy $strategy) {
        $this->paymentStrategy = $strategy;
    }
    
    public function checkout($amount) {
        $this->paymentStrategy->pay($amount);
    }
}

// Usage
$cart = new ShoppingCart();
$cart->setPaymentStrategy(new CreditCardPayment());
$cart->checkout(100);

$cart->setPaymentStrategy(new PayPalPayment());
$cart->checkout(200);
```

---

### 2. **Observer Pattern**

**Purpose:** Define a one-to-many dependency so when one object changes state, all dependents are notified.

```php
interface Observer {
    public function update($subject);
}

class Subject {
    private $observers = [];
    private $state;
    
    public function attach(Observer $observer) {
        $this->observers[] = $observer;
    }
    
    public function setState($state) {
        $this->state = $state;
        $this->notify();
    }
    
    public function getState() {
        return $this->state;
    }
    
    private function notify() {
        foreach ($this->observers as $observer) {
            $observer->update($this);
        }
    }
}

class ConcreteObserver implements Observer {
    private $name;
    
    public function __construct($name) {
        $this->name = $name;
    }
    
    public function update($subject) {
        echo "{$this->name} received update: {$subject->getState()}\n";
    }
}

// Usage
$subject = new Subject();
$observer1 = new ConcreteObserver("Observer 1");
$observer2 = new ConcreteObserver("Observer 2");

$subject->attach($observer1);
$subject->attach($observer2);

$subject->setState("State changed!"); // Both observers notified
```

---

### 3. **Command Pattern**

**Purpose:** Encapsulate a request as an object.

```php
interface Command {
    public function execute();
}

class Light {
    public function turnOn() {
        echo "Light is ON\n";
    }
    
    public function turnOff() {
        echo "Light is OFF\n";
    }
}

class LightOnCommand implements Command {
    private $light;
    
    public function __construct(Light $light) {
        $this->light = $light;
    }
    
    public function execute() {
        $this->light->turnOn();
    }
}

class LightOffCommand implements Command {
    private $light;
    
    public function __construct(Light $light) {
        $this->light = $light;
    }
    
    public function execute() {
        $this->light->turnOff();
    }
}

class RemoteControl {
    private $command;
    
    public function setCommand(Command $command) {
        $this->command = $command;
    }
    
    public function pressButton() {
        $this->command->execute();
    }
}

// Usage
$light = new Light();
$remote = new RemoteControl();

$remote->setCommand(new LightOnCommand($light));
$remote->pressButton(); // Light is ON

$remote->setCommand(new LightOffCommand($light));
$remote->pressButton(); // Light is OFF
```

---

### 4. **Template Method Pattern**

**Purpose:** Define skeleton of algorithm, let subclasses override specific steps.

```php
abstract class Game {
    // Template method
    final public function play() {
        $this->initialize();
        $this->startPlay();
        $this->endPlay();
    }
    
    abstract protected function initialize();
    abstract protected function startPlay();
    abstract protected function endPlay();
}

class Cricket extends Game {
    protected function initialize() {
        echo "Cricket Game Initialized\n";
    }
    
    protected function startPlay() {
        echo "Cricket Game Started\n";
    }
    
    protected function endPlay() {
        echo "Cricket Game Finished\n";
    }
}

class Football extends Game {
    protected function initialize() {
        echo "Football Game Initialized\n";
    }
    
    protected function startPlay() {
        echo "Football Game Started\n";
    }
    
    protected function endPlay() {
        echo "Football Game Finished\n";
    }
}

// Usage
$game = new Cricket();
$game->play();
```

---

## 🎯 SOLID Principles

### 1. **S**ingle Responsibility Principle (SRP)

A class should have **one reason to change**.

❌ **Bad:**
```php
class User {
    public function save() { /* DB logic */ }
    public function sendEmail() { /* Email logic */ }
}
```

✅ **Good:**
```php
class User {
    // Only user data
}

class UserRepository {
    public function save(User $user) { /* DB logic */ }
}

class EmailService {
    public function sendEmail(User $user) { /* Email logic */ }
}
```

---

### 2. **O**pen/Closed Principle (OCP)

Open for extension, closed for modification.

✅ **Example:**
```php
interface Shape {
    public function area();
}

class Circle implements Shape {
    private $radius;
    
    public function area() {
        return pi() * $this->radius ** 2;
    }
}

class Square implements Shape {
    private $side;
    
    public function area() {
        return $this->side ** 2;
    }
}

// Adding new shapes doesn't modify existing code
```

---

### 3. **L**iskov Substitution Principle (LSP)

Subclasses should be substitutable for their base classes.

---

### 4. **I**nterface Segregation Principle (ISP)

Don't force clients to depend on interfaces they don't use.

---

### 5. **D**ependency Inversion Principle (DIP)

Depend on abstractions, not concretions.

```php
// Depend on interface, not concrete class
public function __construct(LoggerInterface $logger) {
    $this->logger = $logger;
}
```

---

## 🚫 Anti-Patterns

**Common bad practices to avoid:**

1. **God Object** - Class that knows/does too much
2. **Spaghetti Code** - Tangled, hard-to-follow code
3. **Golden Hammer** - Using same solution for everything
4. **Premature Optimization** - Optimizing before needed
5. **Copy-Paste Programming** - Duplicating code instead of reusing

---

## 📚 Resources

### Books
- **"Design Patterns: Elements of Reusable Object-Oriented Software"** (Gang of Four)
- **"Head First Design Patterns"** by Freeman & Freeman
- **"Refactoring: Improving the Design of Existing Code"** by Martin Fowler

### Websites
- [Refactoring.Guru](https://refactoring.guru/design-patterns)
- [SourceMaking](https://sourcemaking.com/design_patterns)

---

**Remember:** Design patterns are tools, not rules. Use them when appropriate, don't force them!
