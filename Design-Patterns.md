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

---

## 🎯 Design Patterns Interview Questions — Critical Scenarios

---

### 🏗️ Pattern Selection & Trade-offs

**Q1. When does the Singleton pattern become an anti-pattern?**

**Answer:**

Singleton is often overused. It's appropriate for genuinely single-resource scenarios (logger, config loader) but becomes an anti-pattern when:

**Problems with Singleton:**
- **Hidden global state:** Any code can access the singleton, creating invisible dependencies
- **Untestable:** Can't inject mock implementations (hard to test in isolation)
- **Concurrency issues:** Shared mutable state across threads/requests without synchronization
- **Violation of SRP:** Classes access singleton directly instead of through abstraction
- **Lifecycle issues:** In long-running processes (Laravel Octane), singletons persist across requests — state bleeds between users

```php
// ❌ Anti-pattern: Singleton accessed globally
class OrderService {
    public function process(Order $order): void {
        // Hidden dependency on singleton — can't mock in tests
        $db = Database::getInstance();
        $logger = Logger::getInstance();
        $db->save($order);
    }
}

// ✅ Better: Dependency injection (testable, explicit)
class OrderService {
    public function __construct(
        private readonly DatabaseInterface $db,
        private readonly LoggerInterface $logger,
    ) {}

    public function process(Order $order): void {
        $this->db->save($order);
    }
}
// Register as singleton in DI container — same instance, but injectable/mockable
```

**When Singleton IS appropriate:**
- Database connection pool (one shared pool per process)
- Configuration loaded once from env
- Logger (writing to single output stream)
- Service Container itself

**Laravel Octane warning:**
```php
// ❌ Singleton holding request-specific data in Octane
$this->app->singleton(CurrentUser::class, fn() => auth()->user());
// First request's user leaked to second request!

// ✅ Use scoped() — reset per request
$this->app->scoped(CurrentUser::class, fn() => auth()->user());
```

---

**Q2. Explain the difference between Strategy, State, and Command patterns with real-world examples.**

**Answer:**

All three encapsulate behavior — the key difference is **what drives the behavior change**.

**Strategy — selectable algorithm:**
```php
// Context selects algorithm at construction/call time
interface PaymentStrategy {
    public function pay(float $amount): Receipt;
}

class StripeStrategy implements PaymentStrategy { ... }
class PayPalStrategy implements PaymentStrategy { ... }

class Checkout {
    public function __construct(private PaymentStrategy $payment) {}
    public function complete(float $amount): Receipt {
        return $this->payment->pay($amount);  // Strategy chosen by caller
    }
}

// Usage
$checkout = new Checkout(new StripeStrategy());
$checkout->complete(99.99);
```

**State — behavior changes based on internal state:**
```php
// Object changes its own behavior as state transitions occur
interface OrderState {
    public function ship(Order $order): void;
    public function cancel(Order $order): void;
}

class PendingState implements OrderState {
    public function ship(Order $order): void {
        $order->setState(new ShippedState());
    }
    public function cancel(Order $order): void {
        $order->setState(new CancelledState());
    }
}

class ShippedState implements OrderState {
    public function ship(Order $order): void {
        throw new Exception("Already shipped");
    }
    public function cancel(Order $order): void {
        // Initiate return process
    }
}
```

**Command — encapsulate operation as object (supports undo/queue):**
```php
interface Command {
    public function execute(): void;
    public function undo(): void;
}

class TransferMoneyCommand implements Command {
    public function __construct(
        private Account $from,
        private Account $to,
        private float $amount
    ) {}

    public function execute(): void {
        $this->from->debit($this->amount);
        $this->to->credit($this->amount);
    }

    public function undo(): void {
        $this->to->debit($this->amount);
        $this->from->credit($this->amount);
    }
}

// Macro command — batch operations
class MacroCommand implements Command {
    private array $commands = [];
    public function add(Command $cmd): void { $this->commands[] = $cmd; }
    public function execute(): void { foreach ($this->commands as $c) $c->execute(); }
    public function undo(): void { foreach (array_reverse($this->commands) as $c) $c->undo(); }
}
```

| Pattern | Driven by | Undo support | Key use |
|---------|----------|-------------|---------|
| **Strategy** | Caller's choice | ❌ | Swap algorithms (payment, sorting) |
| **State** | Internal state machine | ❌ | Order lifecycle, connection states |
| **Command** | Encapsulated operation | ✅ | Undo/redo, queuing, transactions |

---

**Q3. When should you use Observer vs Event Bus vs Pub/Sub?**

**Answer:**

**Observer (direct, synchronous, same process):**
```php
// Subject directly calls observer methods
interface Observer {
    public function update(Event $event): void;
}

class OrderCreated {
    private array $observers = [];

    public function attach(Observer $observer): void {
        $this->observers[] = $observer;
    }

    public function notify(): void {
        foreach ($this->observers as $observer) {
            $observer->update($this);  // Synchronous, direct call
        }
    }
}
```

**Event Bus (decoupled, same process, framework-managed):**
```php
// Laravel Events — still same process, but decoupled via dispatcher
event(new OrderPlaced($order));  // Fire and forget

class SendConfirmationEmail implements ShouldQueue {
    public function handle(OrderPlaced $event): void { ... }
}
// Listener registered in EventServiceProvider
// Can run sync or async (via queue)
```

**Pub/Sub (different processes, across services):**
```
OrderService → publishes "order.created" to Kafka/SQS
EmailService → subscribes to "order.created" from Kafka
SMSService   → subscribes to "order.created" from Kafka
(Services are completely decoupled, different codebases, different servers)
```

| Aspect | Observer | Event Bus | Pub/Sub |
|--------|---------|----------|--------|
| **Coupling** | Tight (knows observers) | Medium (knows event class) | Loose (string topic) |
| **Scope** | Same object/class | Same process | Cross-process, cross-service |
| **Async** | ❌ | Optional (queue) | ✅ Always |
| **Delivery guarantee** | At-most-once | At-most-once | Configurable (at-least-once, exactly-once) |
| **Use** | UI updates, model notifications | Domain events, Laravel events | Microservice integration |

---

**Q4. What is the Decorator pattern and how does it differ from inheritance?**

**Answer:**

Decorator adds behavior **at runtime** by wrapping objects. Inheritance adds behavior **at compile time** by extending classes.

**Problem with inheritance:**
```php
// Combinatorial explosion with inheritance
class Logger {}
class TimestampLogger extends Logger {}
class FileLogger extends Logger {}
class TimestampFileLogger extends TimestampLogger {}  // Multiple inheritance needed!
class TimestampFileEncryptedLogger extends ...?       // Gets out of hand
```

**Decorator solution:**
```php
interface Logger {
    public function log(string $message): void;
}

class ConsoleLogger implements Logger {
    public function log(string $message): void {
        echo $message . PHP_EOL;
    }
}

class TimestampDecorator implements Logger {
    public function __construct(private Logger $logger) {}
    public function log(string $message): void {
        $this->logger->log('[' . date('Y-m-d H:i:s') . '] ' . $message);
    }
}

class EncryptionDecorator implements Logger {
    public function __construct(private Logger $logger) {}
    public function log(string $message): void {
        $this->logger->log(encrypt($message));
    }
}

// Compose at runtime:
$logger = new TimestampDecorator(
    new EncryptionDecorator(
        new ConsoleLogger()
    )
);
$logger->log("User logged in");
// Outputs: [2024-01-15 10:30:00] <encrypted>User logged in</encrypted>
```

**Real-world decorators:**
- **HTTP middleware** (Laravel/PSR-15): Each middleware wraps the next
- **PHP Streams:** `fopen()` → `zlib.deflate` → `GnuPG` → file
- **Laravel Cache:** `FileStore` decorated by `TaggedCache`
- **Guzzle HandlerStack:** HTTP client middleware chain

---

**Q5. Explain the Repository pattern and when it justifies the added abstraction.**

**Answer:**

Repository mediates between domain model and data mapping layer, providing collection-like interface for accessing domain objects.

```php
// Without Repository — Eloquent queries scattered everywhere
class OrderController {
    public function index() {
        // Business logic mixed with DB query details everywhere
        $orders = Order::where('status', 'pending')
            ->where('created_at', '>', now()->subDays(7))
            ->with('customer', 'items')
            ->orderBy('created_at', 'desc')
            ->get();
    }
}

// With Repository
interface OrderRepository {
    public function findPendingOrdersFromLastWeek(): Collection;
    public function findById(int $id): ?Order;
    public function save(Order $order): void;
}

class EloquentOrderRepository implements OrderRepository {
    public function findPendingOrdersFromLastWeek(): Collection {
        return Order::where('status', 'pending')
            ->where('created_at', '>', now()->subDays(7))
            ->with('customer', 'items')
            ->orderBy('created_at', 'desc')
            ->get();
    }
}

// Controller is now clean and DB-agnostic
class OrderController {
    public function __construct(private OrderRepository $orders) {}
    public function index() {
        return $this->orders->findPendingOrdersFromLastWeek();
    }
}
```

**Justified when:**
- You need to swap data sources (DB → API, MySQL → MongoDB)
- Complex queries appear in multiple places (DRY)
- Testing requires different data backends (memory, SQLite)
- Domain logic should not know about DB schema

**Over-engineering when:**
- Simple CRUD with one DB backend (Laravel Eloquent is good enough)
- Small project with 1-2 developers
- Just wrapping Eloquent methods 1:1 with no added value
