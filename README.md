# Design Patterns — Tam və Ətraflı Bələdçi

## Mündəricat

1. [Giriş](#giriş)
2. [Design Patterns Nədir?](#design-patterns-nədir)
3. [SOLID Prinsipləri](#solid-prinsipləri)
4. [Creational Patterns](#creational-patterns)
   - [Singleton](#singleton)
   - [Factory Method](#factory-method)
   - [Abstract Factory](#abstract-factory)
   - [Builder](#builder)
   - [Prototype](#prototype)
5. [Structural Patterns](#structural-patterns)
   - [Adapter](#adapter)
   - [Bridge](#bridge)
   - [Composite](#composite)
   - [Decorator](#decorator)
   - [Facade](#facade)
   - [Flyweight](#flyweight)
   - [Proxy](#proxy)
6. [Behavioral Patterns](#behavioral-patterns)
   - [Chain of Responsibility](#chain-of-responsibility)
   - [Command](#command)
   - [Iterator](#iterator)
   - [Mediator](#mediator)
   - [Memento](#memento)
   - [Observer](#observer)
   - [State](#state)
   - [Strategy](#strategy)
   - [Template Method](#template-method)
   - [Visitor](#visitor)
7. [Anti-Patterns](#anti-patterns)
8. [Pattern Selection Guide](#pattern-selection-guide)

---

## Giriş

Design Patterns — proqramlaşdırmada tez-tez rast gəlinən problemlərə ümumi, təkrar istifadə oluna bilən həllərdir. Bu, kod şablonları deyil, problemləri necə həll etmək üçün konseptual yanaşmalardır.

### Tarixçə

Design Patterns konsepsiyası **Gang of Four (GoF)** — Erich Gamma, Richard Helm, Ralph Johnson və John Vlissides tərəfindən 1994-cü ildə **"Design Patterns: Elements of Reusable Object-Oriented Software"** kitabında təqdim edilmişdir.

---

## Design Patterns Nədir?

**Design Pattern** — yazılım dizaynında tez-tez rast gəlinən problemlərə sınaqdan keçmiş həllərdir.

### Niyə Lazımdır?

✅ **Kodun təkrar istifadəsi** (Reusability)  
✅ **Oxunaqlılıq** (Readability)  
✅ **Genişlənmə imkanı** (Extensibility)  
✅ **Ümumi dil** (Proqramçılar arasında)  
✅ **Best practices**

### Pattern-lərin Kateqoriyaları

```
Design Patterns
├── Creational (Yaradıcı)
│   └── Obyekt yaratma mexanizmləri
├── Structural (Struktural)
│   └── Obyekt kompozisiyası
└── Behavioral (Davranışsal)
    └── Obyektlər arası əlaqə
```

---

## SOLID Prinsipləri

Design Pattern-ləri başa düşməzdən əvvəl SOLID prinsiplərini bilmək lazımdır.

### S — Single Responsibility Principle (SRP)

**Prinsip:** Hər class yalnız bir məsuliyyətə malik olmalıdır.

#### ❌ Pis Nümunə:

```java
// Bir class çox məsuliyyət daşıyır
class Employee {
    private String name;
    private double salary;
    
    // Məsuliyyət 1: Employee məlumatları
    public void calculateSalary() { }
    
    // Məsuliyyət 2: Database əməliyyatları
    public void saveToDatabase() { }
    
    // Məsuliyyət 3: Report yaratmaq
    public void generateReport() { }
}
```

#### ✅ Yaxşı Nümunə:

```java
// Hər class bir məsuliyyət
class Employee {
    private String name;
    private double salary;
    
    public void calculateSalary() { }
    
    // Getters/Setters
}

class EmployeeRepository {
    public void save(Employee employee) { }
}

class EmployeeReportGenerator {
    public void generateReport(Employee employee) { }
}
```

---

### O — Open/Closed Principle (OCP)

**Prinsip:** Class-lar genişlənməyə açıq, dəyişikliyə qapalı olmalıdır.

#### ❌ Pis Nümunə:

```java
class AreaCalculator {
    public double calculateArea(Object shape) {
        if (shape instanceof Circle) {
            Circle circle = (Circle) shape;
            return Math.PI * circle.radius * circle.radius;
        } else if (shape instanceof Rectangle) {
            Rectangle rect = (Rectangle) shape;
            return rect.width * rect.height;
        }
        // Yeni shape əlavə etmək üçün bu class-ı dəyişdirməlisən! ❌
        return 0;
    }
}
```

#### ✅ Yaxşı Nümunə:

```java
interface Shape {
    double calculateArea();
}

class Circle implements Shape {
    private double radius;
    
    @Override
    public double calculateArea() {
        return Math.PI * radius * radius;
    }
}

class Rectangle implements Shape {
    private double width;
    private double height;
    
    @Override
    public double calculateArea() {
        return width * height;
    }
}

// Yeni shape əlavə etmək üçün sadəcə yeni class yarat ✅
class Triangle implements Shape {
    private double base;
    private double height;
    
    @Override
    public double calculateArea() {
        return 0.5 * base * height;
    }
}
```

---

### L — Liskov Substitution Principle (LSP)

**Prinsip:** Alt class-lar öz əsas class-larını əvəz edə bilməlidir.

#### ❌ Pis Nümunə:

```java
class Bird {
    public void fly() {
        System.out.println("Flying...");
    }
}

class Penguin extends Bird {
    @Override
    public void fly() {
        throw new UnsupportedOperationException("Penguins can't fly!");
        // LSP pozulur! Penguin Bird-i əvəz edə bilmir
    }
}
```

#### ✅ Yaxşı Nümunə:

```java
interface Bird {
    void eat();
}

interface FlyingBird extends Bird {
    void fly();
}

class Sparrow implements FlyingBird {
    public void eat() { }
    public void fly() { }
}

class Penguin implements Bird {
    public void eat() { }
    // fly() metodu yoxdur ✅
}
```

---

### I — Interface Segregation Principle (ISP)

**Prinsip:** Client lazım olmayan interface-lərdən asılı olmamalıdır.

#### ❌ Pis Nümunə:

```java
interface Worker {
    void work();
    void eat();
    void sleep();
}

class HumanWorker implements Worker {
    public void work() { }
    public void eat() { }
    public void sleep() { }
}

class RobotWorker implements Worker {
    public void work() { }
    
    public void eat() { 
        // Robot yemək yeyə bilməz! ❌
        throw new UnsupportedOperationException();
    }
    
    public void sleep() { 
        // Robot yata bilməz! ❌
        throw new UnsupportedOperationException();
    }
}
```

#### ✅ Yaxşı Nümunə:

```java
interface Workable {
    void work();
}

interface Eatable {
    void eat();
}

interface Sleepable {
    void sleep();
}

class HumanWorker implements Workable, Eatable, Sleepable {
    public void work() { }
    public void eat() { }
    public void sleep() { }
}

class RobotWorker implements Workable {
    public void work() { }
    // Yalnız lazım olan interface ✅
}
```

---

### D — Dependency Inversion Principle (DIP)

**Prinsip:** High-level modullar low-level modullara asılı olmamalı. Hər ikisi abstraksiyalara asılı olmalıdır.

#### ❌ Pis Nümunə:

```java
// Low-level class
class MySQLDatabase {
    public void save(String data) {
        System.out.println("Saving to MySQL: " + data);
    }
}

// High-level class
class UserService {
    private MySQLDatabase database;  // Konkret class-a asılıdır! ❌
    
    public UserService() {
        this.database = new MySQLDatabase();
    }
    
    public void saveUser(String user) {
        database.save(user);
    }
}
```

#### ✅ Yaxşı Nümunə:

```java
// Abstraction
interface Database {
    void save(String data);
}

// Low-level implementations
class MySQLDatabase implements Database {
    public void save(String data) {
        System.out.println("Saving to MySQL: " + data);
    }
}

class MongoDatabase implements Database {
    public void save(String data) {
        System.out.println("Saving to MongoDB: " + data);
    }
}

// High-level class
class UserService {
    private Database database;  // Abstraction-a asılıdır ✅
    
    public UserService(Database database) {
        this.database = database;
    }
    
    public void saveUser(String user) {
        database.save(user);
    }
}

// İstifadə
Database mysql = new MySQLDatabase();
UserService service1 = new UserService(mysql);

Database mongo = new MongoDatabase();
UserService service2 = new UserService(mongo);
```

---

# Creational Patterns

Creational Pattern-lər obyekt yaratma mexanizmlərini təmin edir.

---

## Singleton

### Singleton Nədir?

**Singleton** — class-dan yalnız **bir dənə instance** yaradılmasını təmin edən pattern.

### Hansı Problemi Həll Edir?

✅ Bütün proqram üçün yalnız bir obyekt lazımdırsa  
✅ Global giriş nöqtəsi təmin etmək  
✅ Resource-ların səmərəli istifadəsi

### Real-World Nümunələr

- Database Connection Pool
- Logger
- Configuration Manager
- Cache
- Thread Pool
- Application Settings

---

### Classic Singleton (Lazy Initialization)

```java
public class Singleton {
    // 1. Private static instance
    private static Singleton instance;
    
    // 2. Private constructor
    private Singleton() {
        // Xaricdan yaradılmasının qarşısını alır
    }
    
    // 3. Public static getInstance() metodu
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}

// İstifadə
Singleton obj1 = Singleton.getInstance();
Singleton obj2 = Singleton.getInstance();

System.out.println(obj1 == obj2);  // true - eyni obyekt!
```

#### Problem: Thread-Safe Deyil! ❌

```java
// İki thread eyni anda getInstance() çağırırsa:
Thread 1: if (instance == null)  // true
Thread 2: if (instance == null)  // true
Thread 1: instance = new Singleton()
Thread 2: instance = new Singleton()  // İki obyekt yaradıldı! ❌
```

---

### Thread-Safe Singleton (Synchronized)

```java
public class Singleton {
    private static Singleton instance;
    
    private Singleton() { }
    
    // synchronized keyword - thread-safe ✅
    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

#### Problem: Performance! ❌

```java
// Hər çağırışda lock alır (yavaş!)
public static synchronized Singleton getInstance() {
    // Lock alır
    if (instance == null) {
        instance = new Singleton();
    }
    // Lock buraxır
}
```

---

### Double-Checked Locking

```java
public class Singleton {
    // volatile keyword - memory visibility təmin edir
    private static volatile Singleton instance;
    
    private Singleton() { }
    
    public static Singleton getInstance() {
        // Birinci yoxlama - synchronized olmadan (sürətli)
        if (instance == null) {
            synchronized (Singleton.class) {
                // İkinci yoxlama - lock daxilində
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

#### volatile Keyword İzahı:

**volatile** — dəyişənin hər thread tərəfindən dərhal görünməsini təmin edir.

```java
// volatile olmadan:
Thread 1: instance = new Singleton()  // Write
Thread 2: if (instance == null)       // Hələ null görə bilər! ❌

// volatile ilə:
private static volatile Singleton instance;
Thread 1: instance = new Singleton()  // Write
Thread 2: if (instance == null)       // Dərhal yeni dəyəri görür ✅
```

---

### Eager Initialization

```java
public class Singleton {
    // Class load olduqda yaradılır
    private static final Singleton instance = new Singleton();
    
    private Singleton() { }
    
    public static Singleton getInstance() {
        return instance;
    }
}
```

**Üstünlük:** Thread-safe ✅  
**Problem:** İstifadə olunmasa belə yaradılır ❌

---

### Bill Pugh Singleton (Best Practice!)

```java
public class Singleton {
    
    private Singleton() { }
    
    // Static inner class
    private static class SingletonHelper {
        private static final Singleton INSTANCE = new Singleton();
    }
    
    public static Singleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
}
```

**Üstünlükləri:**
✅ Lazy initialization  
✅ Thread-safe (classloader tərəfindən)  
✅ Synchronized overhead yoxdur  
✅ Ən yaxşı performans

**Necə İşləyir?**

```
1. Singleton class load olur
2. SingletonHelper hələ load olunmur (lazy)
3. getInstance() çağırılır
4. SingletonHelper load olur
5. INSTANCE yaradılır (thread-safe, classloader tərəfindən)
```

---

### Enum Singleton (Joshua Bloch tövsiyyəsi)

```java
public enum Singleton {
    INSTANCE;
    
    public void doSomething() {
        System.out.println("Singleton method");
    }
}

// İstifadə
Singleton.INSTANCE.doSomething();
```

**Üstünlükləri:**
✅ Thread-safe  
✅ Serialization-safe  
✅ Reflection-safe  
✅ Ən sadə implementasiya

---

### Real-World Nümunə: Database Connection

```java
public class DatabaseConnection {
    private static volatile DatabaseConnection instance;
    private Connection connection;
    
    private DatabaseConnection() {
        try {
            // Database connection yaradılır
            connection = DriverManager.getConnection(
                "jdbc:mysql://localhost:3306/mydb", 
                "user", 
                "password"
            );
        } catch (SQLException e) {
            throw new RuntimeException("Connection failed!", e);
        }
    }
    
    public static DatabaseConnection getInstance() {
        if (instance == null) {
            synchronized (DatabaseConnection.class) {
                if (instance == null) {
                    instance = new DatabaseConnection();
                }
            }
        }
        return instance;
    }
    
    public Connection getConnection() {
        return connection;
    }
}

// İstifadə
DatabaseConnection db = DatabaseConnection.getInstance();
Connection conn = db.getConnection();
```

---

### Singleton Problemləri və Həlləri

#### Problem 1: Reflection Attack

```java
// Reflection ilə private constructor-a giriş
Constructor<Singleton> constructor = Singleton.class.getDeclaredConstructor();
constructor.setAccessible(true);
Singleton instance1 = constructor.newInstance();  // İkinci obyekt! ❌
```

**Həll:**

```java
public class Singleton {
    private static volatile Singleton instance;
    
    private Singleton() {
        // Reflection qarşısını al
        if (instance != null) {
            throw new RuntimeException("Use getInstance() method!");
        }
    }
    
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

---

#### Problem 2: Serialization

```java
// Singleton serialize və deserialize edirsən
ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("singleton.ser"));
out.writeObject(Singleton.getInstance());

ObjectInputStream in = new ObjectInputStream(new FileInputStream("singleton.ser"));
Singleton instance2 = (Singleton) in.readObject();  // Yeni obyekt! ❌
```

**Həll:**

```java
public class Singleton implements Serializable {
    private static volatile Singleton instance;
    
    private Singleton() { }
    
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
    
    // readResolve metodu - serialization zamanı eyni obyekti qaytarır
    protected Object readResolve() {
        return getInstance();
    }
}
```

---

#### Problem 3: Cloning

```java
Singleton instance1 = Singleton.getInstance();
Singleton instance2 = (Singleton) instance1.clone();  // Yeni obyekt! ❌
```

**Həll:**

```java
public class Singleton implements Cloneable {
    private static volatile Singleton instance;
    
    private Singleton() { }
    
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
    
    // clone()-u override et və exception at
    @Override
    protected Object clone() throws CloneNotSupportedException {
        throw new CloneNotSupportedException("Singleton cannot be cloned!");
    }
}
```

---

### Singleton Nə Zaman İstifadə Etmək?

#### ✅ İstifadə Et:

- Logger
- Configuration
- Database connection pool
- Cache
- Thread pool
- Device drivers

#### ❌ İstifadə Etmə:

- Əgər bir obyekt kifayət etmirsə
- Testing çətindirsə (mocking)
- Global state lazım deyilsə

---

## Factory Method

### Factory Method Nədir?

**Factory Method** — obyekt yaratmaq üçün interface təmin edir, amma hansı class-ın yaradılacağına qərar alt class-lara verir.

### Hansı Problemi Həll Edir?

✅ Obyekt yaratma məntiqini enkapsulasiya edir  
✅ Loose coupling (kodlar arasında asılılığı azaldır)  
✅ Open/Closed Principle-a uyğunlaşır

---

### Problem: Obyekt Yaratma Birbaşa (Tight Coupling)

```java
// Client kodu
public class Application {
    public void createDocument(String type) {
        Document doc;
        
        if (type.equals("PDF")) {
            doc = new PDFDocument();  // Tight coupling! ❌
        } else if (type.equals("Word")) {
            doc = new WordDocument();  // Tight coupling! ❌
        } else {
            doc = new ExcelDocument();
        }
        
        doc.open();
    }
}
```

**Problem:** Yeni document növü əlavə etmək üçün bu kodu dəyişdirməlisən!

---

### Factory Method Pattern İmplementasiyası

```java
// 1. Product Interface
interface Document {
    void open();
    void save();
    void close();
}

// 2. Concrete Products
class PDFDocument implements Document {
    @Override
    public void open() {
        System.out.println("Opening PDF document");
    }
    
    @Override
    public void save() {
        System.out.println("Saving PDF document");
    }
    
    @Override
    public void close() {
        System.out.println("Closing PDF document");
    }
}

class WordDocument implements Document {
    @Override
    public void open() {
        System.out.println("Opening Word document");
    }
    
    @Override
    public void save() {
        System.out.println("Saving Word document");
    }
    
    @Override
    public void close() {
        System.out.println("Closing Word document");
    }
}

// 3. Creator Abstract Class (Factory)
abstract class DocumentCreator {
    // Factory Method
    public abstract Document createDocument();
    
    // Template method - factory method istifadə edir
    public void newDocument() {
        Document doc = createDocument();
        doc.open();
    }
}

// 4. Concrete Creators
class PDFDocumentCreator extends DocumentCreator {
    @Override
    public Document createDocument() {
        return new PDFDocument();
    }
}

class WordDocumentCreator extends DocumentCreator {
    @Override
    public Document createDocument() {
        return new WordDocument();
    }
}

// 5. Client
public class Application {
    public static void main(String[] args) {
        DocumentCreator creator;
        
        // Runtime-da qərar verilir
        String type = "PDF";
        
        if (type.equals("PDF")) {
            creator = new PDFDocumentCreator();
        } else {
            creator = new WordDocumentCreator();
        }
        
        creator.newDocument();
    }
}
```

---

### Real-World Nümunə: Pizza Factory

```java
// Product
abstract class Pizza {
    protected String name;
    protected String dough;
    protected String sauce;
    protected List<String> toppings = new ArrayList<>();
    
    public void prepare() {
        System.out.println("Preparing " + name);
        System.out.println("Tossing dough: " + dough);
        System.out.println("Adding sauce: " + sauce);
        System.out.println("Adding toppings:");
        toppings.forEach(topping -> System.out.println("  " + topping));
    }
    
    public void bake() {
        System.out.println("Baking for 25 minutes at 350");
    }
    
    public void cut() {
        System.out.println("Cutting pizza into diagonal slices");
    }
    
    public void box() {
        System.out.println("Placing pizza in official box");
    }
    
    public String getName() {
        return name;
    }
}

// Concrete Products
class NYStyleCheesePizza extends Pizza {
    public NYStyleCheesePizza() {
        name = "NY Style Sauce and Cheese Pizza";
        dough = "Thin Crust Dough";
        sauce = "Marinara Sauce";
        
        toppings.add("Grated Reggiano Cheese");
    }
}

class ChicagoStyleCheesePizza extends Pizza {
    public ChicagoStyleCheesePizza() {
        name = "Chicago Style Deep Dish Cheese Pizza";
        dough = "Extra Thick Crust Dough";
        sauce = "Plum Tomato Sauce";
        
        toppings.add("Shredded Mozzarella Cheese");
    }
    
    @Override
    public void cut() {
        System.out.println("Cutting pizza into square slices");
    }
}

// Creator
abstract class PizzaStore {
    // Factory Method
    protected abstract Pizza createPizza(String type);
    
    // Template method
    public Pizza orderPizza(String type) {
        Pizza pizza = createPizza(type);
        
        pizza.prepare();
        pizza.bake();
        pizza.cut();
        pizza.box();
        
        return pizza;
    }
}

// Concrete Creators
class NYPizzaStore extends PizzaStore {
    @Override
    protected Pizza createPizza(String type) {
        if (type.equals("cheese")) {
            return new NYStyleCheesePizza();
        } else if (type.equals("pepperoni")) {
            return new NYStylePepperoniPizza();
        }
        return null;
    }
}

class ChicagoPizzaStore extends PizzaStore {
    @Override
    protected Pizza createPizza(String type) {
        if (type.equals("cheese")) {
            return new ChicagoStyleCheesePizza();
        } else if (type.equals("pepperoni")) {
            return new ChicagoStylePepperoniPizza();
        }
        return null;
    }
}

// Client
public class PizzaTestDrive {
    public static void main(String[] args) {
        PizzaStore nyStore = new NYPizzaStore();
        PizzaStore chicagoStore = new ChicagoPizzaStore();
        
        Pizza pizza = nyStore.orderPizza("cheese");
        System.out.println("Ordered: " + pizza.getName());
        
        pizza = chicagoStore.orderPizza("cheese");
        System.out.println("Ordered: " + pizza.getName());
    }
}
```

---

### Factory Method vs Simple Factory

#### Simple Factory (Static Factory):

```java
class PizzaFactory {
    public static Pizza createPizza(String type) {
        if (type.equals("cheese")) {
            return new CheesePizza();
        } else if (type.equals("pepperoni")) {
            return new PepperoniPizza();
        }
        return null;
    }
}

// İstifadə
Pizza pizza = PizzaFactory.createPizza("cheese");
```

**Fərq:**
- Simple Factory: Sadə static metod
- Factory Method: Inheritance və polymorphism istifadə edir

---

## Abstract Factory

### Abstract Factory Nədir?

**Abstract Factory** — bir-birinə əlaqəli obyektlər ailəsini yaratmaq üçün interface təmin edir.

### Hansı Problemi Həll Edir?

✅ Əlaqəli obyektlər qrupunu yaratmaq  
✅ Platform-müstəqil kod  
✅ Dependency Inversion Principle

---

### Real-World Nümunə: UI Components

```java
// Abstract Products
interface Button {
    void paint();
}

interface Checkbox {
    void paint();
}

// Concrete Products - Windows
class WindowsButton implements Button {
    @Override
    public void paint() {
        System.out.println("Rendering Windows button");
    }
}

class WindowsCheckbox implements Checkbox {
    @Override
    public void paint() {
        System.out.println("Rendering Windows checkbox");
    }
}

// Concrete Products - Mac
class MacButton implements Button {
    @Override
    public void paint() {
        System.out.println("Rendering Mac button");
    }
}

class MacCheckbox implements Checkbox {
    @Override
    public void paint() {
        System.out.println("Rendering Mac checkbox");
    }
}

// Abstract Factory
interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}

// Concrete Factories
class WindowsFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new WindowsButton();
    }
    
    @Override
    public Checkbox createCheckbox() {
        return new WindowsCheckbox();
    }
}

class MacFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new MacButton();
    }
    
    @Override
    public Checkbox createCheckbox() {
        return new MacCheckbox();
    }
}

// Client
class Application {
    private Button button;
    private Checkbox checkbox;
    
    public Application(GUIFactory factory) {
        button = factory.createButton();
        checkbox = factory.createCheckbox();
    }
    
    public void paint() {
        button.paint();
        checkbox.paint();
    }
}

// Main
public class Demo {
    public static void main(String[] args) {
        GUIFactory factory;
        String osName = System.getProperty("os.name").toLowerCase();
        
        if (osName.contains("mac")) {
            factory = new MacFactory();
        } else {
            factory = new WindowsFactory();
        }
        
        Application app = new Application(factory);
        app.paint();
    }
}
```

---

### Real-World Nümunə: Database Factory

```java
// Abstract Products
interface Connection {
    void connect();
}

interface Command {
    void execute(String sql);
}

// MySQL Products
class MySQLConnection implements Connection {
    @Override
    public void connect() {
        System.out.println("Connecting to MySQL");
    }
}

class MySQLCommand implements Command {
    @Override
    public void execute(String sql) {
        System.out.println("Executing MySQL: " + sql);
    }
}

// PostgreSQL Products
class PostgreSQLConnection implements Connection {
    @Override
    public void connect() {
        System.out.println("Connecting to PostgreSQL");
    }
}

class PostgreSQLCommand implements Command {
    @Override
    public void execute(String sql) {
        System.out.println("Executing PostgreSQL: " + sql);
    }
}

// Abstract Factory
interface DatabaseFactory {
    Connection createConnection();
    Command createCommand();
}

// Concrete Factories
class MySQLFactory implements DatabaseFactory {
    @Override
    public Connection createConnection() {
        return new MySQLConnection();
    }
    
    @Override
    public Command createCommand() {
        return new MySQLCommand();
    }
}

class PostgreSQLFactory implements DatabaseFactory {
    @Override
    public Connection createConnection() {
        return new PostgreSQLConnection();
    }
    
    @Override
    public Command createCommand() {
        return new PostgreSQLCommand();
    }
}

// Client
class DatabaseClient {
    private Connection connection;
    private Command command;
    
    public DatabaseClient(DatabaseFactory factory) {
        connection = factory.createConnection();
        command = factory.createCommand();
    }
    
    public void executeQuery(String sql) {
        connection.connect();
        command.execute(sql);
    }
}

// Main
public class Main {
    public static void main(String[] args) {
        // Configuration-dan oxumaq
        String dbType = "MySQL";  // və ya "PostgreSQL"
        
        DatabaseFactory factory;
        if (dbType.equals("MySQL")) {
            factory = new MySQLFactory();
        } else {
            factory = new PostgreSQLFactory();
        }
        
        DatabaseClient client = new DatabaseClient(factory);
        client.executeQuery("SELECT * FROM users");
    }
}
```

---

## Builder

### Builder Nədir?

**Builder** — mürəkkəb obyektləri addım-addım yaratmaq üçün pattern.

### Hansı Problemi Həll Edir?

✅ Telescoping constructor anti-pattern-dən xilas edir  
✅ Oxunaqlı obyekt yaratma  
✅ Immutable obyektlər yaratmaq

---

### Problem: Telescoping Constructor

```java
class Pizza {
    private int size;
    private boolean cheese;
    private boolean pepperoni;
    private boolean bacon;
    private boolean mushrooms;
    
    // Telescoping constructors ❌
    public Pizza(int size) {
        this(size, false, false, false, false);
    }
    
    public Pizza(int size, boolean cheese) {
        this(size, cheese, false, false, false);
    }
    
    public Pizza(int size, boolean cheese, boolean pepperoni) {
        this(size, cheese, pepperoni, false, false);
    }
    
    public Pizza(int size, boolean cheese, boolean pepperoni, boolean bacon) {
        this(size, cheese, pepperoni, bacon, false);
    }
    
    public Pizza(int size, boolean cheese, boolean pepperoni, boolean bacon, boolean mushrooms) {
        this.size = size;
        this.cheese = cheese;
        this.pepperoni = pepperoni;
        this.bacon = bacon;
        this.mushrooms = mushrooms;
    }
}

// İstifadə - Oxunması çətin! ❌
Pizza pizza = new Pizza(12, true, false, true, false);
// Hansı parameter nədir? 🤔
```

---

### Builder Pattern İmplementasiyası

```java
class Pizza {
    private final int size;
    private final boolean cheese;
    private final boolean pepperoni;
    private final boolean bacon;
    private final boolean mushrooms;
    
    // Private constructor
    private Pizza(Builder builder) {
        this.size = builder.size;
        this.cheese = builder.cheese;
        this.pepperoni = builder.pepperoni;
        this.bacon = builder.bacon;
        this.mushrooms = builder.mushrooms;
    }
    
    // Static nested Builder class
    public static class Builder {
        // Required parameters
        private final int size;
        
        // Optional parameters - default values
        private boolean cheese = false;
        private boolean pepperoni = false;
        private boolean bacon = false;
        private boolean mushrooms = false;
        
        public Builder(int size) {
            this.size = size;
        }
        
        public Builder cheese(boolean value) {
            cheese = value;
            return this;  // Method chaining
        }
        
        public Builder pepperoni(boolean value) {
            pepperoni = value;
            return this;
        }
        
        public Builder bacon(boolean value) {
            bacon = value;
            return this;
        }
        
        public Builder mushrooms(boolean value) {
            mushrooms = value;
            return this;
        }
        
        public Pizza build() {
            return new Pizza(this);
        }
    }
    
    @Override
    public String toString() {
        return "Pizza{" +
                "size=" + size +
                ", cheese=" + cheese +
                ", pepperoni=" + pepperoni +
                ", bacon=" + bacon +
                ", mushrooms=" + mushrooms +
                '}';
    }
}

// İstifadə - Oxunaqlı! ✅
Pizza pizza = new Pizza.Builder(12)
                    .cheese(true)
                    .pepperoni(true)
                    .bacon(true)
                    .build();

System.out.println(pizza);
// Pizza{size=12, cheese=true, pepperoni=true, bacon=true, mushrooms=false}
```

---

### Method Chaining İzahı

**Method Chaining** — hər metod `this` qaytarır və ardıcıl çağırış mümkün olur.

```java
public Builder cheese(boolean value) {
    this.cheese = value;
    return this;  // Eyni Builder obyektini qaytarır
}

// Beləliklə:
builder.cheese(true).pepperoni(true).bacon(true);

// Ekvivalentdir:
builder.cheese(true);
builder.pepperoni(true);
builder.bacon(true);
```

---

### Real-World Nümunə: HTTP Request Builder

```java
class HttpRequest {
    private final String url;
    private final String method;
    private final Map<String, String> headers;
    private final Map<String, String> parameters;
    private final String body;
    
    private HttpRequest(Builder builder) {
        this.url = builder.url;
        this.method = builder.method;
        this.headers = builder.headers;
        this.parameters = builder.parameters;
        this.body = builder.body;
    }
    
    public static class Builder {
        // Required
        private final String url;
        
        // Optional
        private String method = "GET";
        private Map<String, String> headers = new HashMap<>();
        private Map<String, String> parameters = new HashMap<>();
        private String body = "";
        
        public Builder(String url) {
            this.url = url;
        }
        
        public Builder method(String method) {
            this.method = method;
            return this;
        }
        
        public Builder header(String key, String value) {
            headers.put(key, value);
            return this;
        }
        
        public Builder parameter(String key, String value) {
            parameters.put(key, value);
            return this;
        }
        
        public Builder body(String body) {
            this.body = body;
            return this;
        }
        
        public HttpRequest build() {
            // Validation
            if (url == null || url.isEmpty()) {
                throw new IllegalStateException("URL cannot be empty");
            }
            return new HttpRequest(this);
        }
    }
    
    public void send() {
        System.out.println("Sending " + method + " request to " + url);
        System.out.println("Headers: " + headers);
        System.out.println("Parameters: " + parameters);
        System.out.println("Body: " + body);
    }
}

// İstifadə
HttpRequest request = new HttpRequest.Builder("https://api.example.com/users")
                            .method("POST")
                            .header("Content-Type", "application/json")
                            .header("Authorization", "Bearer token123")
                            .parameter("page", "1")
                            .parameter("limit", "10")
                            .body("{\"name\": \"John\"}")
                            .build();

request.send();
```

---

### Real-World Nümunə: SQL Query Builder

```java
class SQLQuery {
    private final String table;
    private final List<String> columns;
    private final String whereClause;
    private final String orderBy;
    private final Integer limit;
    
    private SQLQuery(Builder builder) {
        this.table = builder.table;
        this.columns = builder.columns;
        this.whereClause = builder.whereClause;
        this.orderBy = builder.orderBy;
        this.limit = builder.limit;
    }
    
    public static class Builder {
        private final String table;
        private List<String> columns = new ArrayList<>();
        private String whereClause = "";
        private String orderBy = "";
        private Integer limit;
        
        public Builder(String table) {
            this.table = table;
        }
        
        public Builder select(String... columns) {
            this.columns = Arrays.asList(columns);
            return this;
        }
        
        public Builder where(String condition) {
            this.whereClause = condition;
            return this;
        }
        
        public Builder orderBy(String column) {
            this.orderBy = column;
            return this;
        }
        
        public Builder limit(int limit) {
            this.limit = limit;
            return this;
        }
        
        public SQLQuery build() {
            return new SQLQuery(this);
        }
    }
    
    public String toSQL() {
        StringBuilder sql = new StringBuilder("SELECT ");
        
        if (columns.isEmpty()) {
            sql.append("*");
        } else {
            sql.append(String.join(", ", columns));
        }
        
        sql.append(" FROM ").append(table);
        
        if (!whereClause.isEmpty()) {
            sql.append(" WHERE ").append(whereClause);
        }
        
        if (!orderBy.isEmpty()) {
            sql.append(" ORDER BY ").append(orderBy);
        }
        
        if (limit != null) {
            sql.append(" LIMIT ").append(limit);
        }
        
        return sql.toString();
    }
}

// İstifadə
SQLQuery query = new SQLQuery.Builder("users")
                        .select("id", "name", "email")
                        .where("age > 18")
                        .orderBy("name ASC")
                        .limit(10)
                        .build();

System.out.println(query.toSQL());
// SELECT id, name, email FROM users WHERE age > 18 ORDER BY name ASC LIMIT 10
```

---

### Lombok @Builder

```java
import lombok.Builder;
import lombok.ToString;

@Builder
@ToString
class User {
    private String name;
    private int age;
    private String email;
    private String phone;
}

// İstifadə - Lombok avtomatik Builder yaradır
User user = User.builder()
                .name("John")
                .age(30)
                .email("john@example.com")
                .phone("555-1234")
                .build();

System.out.println(user);
```

---

## Prototype

### Prototype Nədir?

**Prototype** — mövcud obyekti klonlayaraq yeni obyekt yaratmaq pattern-i.

### Hansı Problemi Həll Edir?

✅ Obyekt yaratma baha olarsa (DB-dən oxumaq)  
✅ Obyekt yaratma mürəkkəbdirsə  
✅ Runtime-da dinamik obyektlər lazımdırsa

---

### Prototype Pattern İmplementasiyası

```java
// Prototype interface
interface Prototype extends Cloneable {
    Prototype clone();
}

// Concrete Prototype
class Document implements Prototype {
    private String title;
    private String content;
    private List<String> images;
    
    public Document(String title, String content) {
        this.title = title;
        this.content = content;
        this.images = new ArrayList<>();
        
        // Expensive operation
        loadImagesFromDatabase();
    }
    
    private void loadImagesFromDatabase() {
        System.out.println("Loading images from database... (expensive!)");
        try {
            Thread.sleep(2000);  // Simulate DB call
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        images.add("image1.jpg");
        images.add("image2.jpg");
    }
    
    // Copy constructor
    private Document(Document document) {
        this.title = document.title;
        this.content = document.content;
        this.images = new ArrayList<>(document.images);  // Shallow copy
    }
    
    @Override
    public Prototype clone() {
        return new Document(this);
    }
    
    public void setTitle(String title) {
        this.title = title;
    }
    
    @Override
    public String toString() {
        return "Document{title='" + title + "', content='" + content + 
               "', images=" + images + "}";
    }
}

// Client
public class PrototypeDemo {
    public static void main(String[] args) {
        // İlk obyekt - baha yaradılır
        System.out.println("Creating first document...");
        Document original = new Document("Template", "Default content");
        System.out.println(original);
        
        // Klonlama - sürətli!
        System.out.println("\nCloning document...");
        Document clone1 = (Document) original.clone();
        clone1.setTitle("Copy 1");
        System.out.println(clone1);
        
        Document clone2 = (Document) original.clone();
        clone2.setTitle("Copy 2");
        System.out.println(clone2);
    }
}
```

---

### Shallow Copy vs Deep Copy

#### Shallow Copy:

```java
class Person implements Cloneable {
    private String name;
    private Address address;  // Reference type
    
    @Override
    public Person clone() {
        try {
            return (Person) super.clone();  // Shallow copy
        } catch (CloneNotSupportedException e) {
            throw new RuntimeException(e);
        }
    }
}

// Problem
Person person1 = new Person("John", new Address("Street 1"));
Person person2 = person1.clone();

person2.getAddress().setStreet("Street 2");
System.out.println(person1.getAddress());  // "Street 2" ❌ (dəyişdi!)
```

#### Deep Copy:

```java
class Person implements Cloneable {
    private String name;
    private Address address;
    
    @Override
    public Person clone() {
        try {
            Person cloned = (Person) super.clone();
            cloned.address = this.address.clone();  // Deep copy!
            return cloned;
        } catch (CloneNotSupportedException e) {
            throw new RuntimeException(e);
        }
    }
}

class Address implements Cloneable {
    private String street;
    
    @Override
    public Address clone() {
        try {
            return (Address) super.clone();
        } catch (CloneNotSupportedException e) {
            throw new RuntimeException(e);
        }
    }
}

// İndi düzgün işləyir
Person person1 = new Person("John", new Address("Street 1"));
Person person2 = person1.clone();

person2.getAddress().setStreet("Street 2");
System.out.println(person1.getAddress());  // "Street 1" ✅
System.out.println(person2.getAddress());  // "Street 2" ✅
```

---

### Real-World Nümunə: Prototype Registry

```java
// Prototype
abstract class Shape implements Cloneable {
    private String id;
    protected String type;
    
    abstract void draw();
    
    @Override
    public Shape clone() {
        try {
            return (Shape) super.clone();
        } catch (CloneNotSupportedException e) {
            throw new RuntimeException(e);
        }
    }
    
    // Getters/Setters
    public String getId() { return id; }
    public void setId(String id) { this.id = id; }
    public String getType() { return type; }
}

// Concrete Prototypes
class Circle extends Shape {
    public Circle() {
        type = "Circle";
    }
    
    @Override
    void draw() {
        System.out.println("Drawing a Circle");
    }
}

class Rectangle extends Shape {
    public Rectangle() {
        type = "Rectangle";
    }
    
    @Override
    void draw() {
        System.out.println("Drawing a Rectangle");
    }
}

// Prototype Registry (Cache)
class ShapeCache {
    private static Map<String, Shape> shapeMap = new HashMap<>();
    
    public static Shape getShape(String shapeId) {
        Shape cachedShape = shapeMap.get(shapeId);
        return cachedShape.clone();
    }
    
    public static void loadCache() {
        Circle circle = new Circle();
        circle.setId("1");
        shapeMap.put(circle.getId(), circle);
        
        Rectangle rectangle = new Rectangle();
        rectangle.setId("2");
        shapeMap.put(rectangle.getId(), rectangle);
    }
}

// Client
public class PrototypePatternDemo {
    public static void main(String[] args) {
        ShapeCache.loadCache();
        
        Shape clonedShape1 = ShapeCache.getShape("1");
        System.out.println("Shape: " + clonedShape1.getType());
        clonedShape1.draw();
        
        Shape clonedShape2 = ShapeCache.getShape("2");
        System.out.println("Shape: " + clonedShape2.getType());
        clonedShape2.draw();
    }
}
```

---

# Structural Patterns

Structural Pattern-lər class və obyektlərin kompozisiyası ilə bağlıdır.

---

## Adapter

### Adapter Nədir?

**Adapter** — uyğun olmayan interface-ləri birləşdirən pattern.

### Hansı Problemi Həll Edir?

✅ Legacy kod ilə yeni kod birləşdirmək  
✅ 3rd party library-ləri inteqrasiya etmək  
✅ Interface uyğunsuzluğunu həll etmək

---

### Real-World Analoq

Elektrik adapter:
```
Avropa Plugin (2 pin) → Adapter → Amerika Socket (3 pin)
```

---

### Problem: Interface Uyğunsuzluğu

```java
// Legacy class - dəyişdirə bilmirsən
class LegacyRectangle {
    public void draw(int x1, int y1, int x2, int y2) {
        System.out.println("Drawing rectangle: (" + x1 + "," + y1 + 
                          ") to (" + x2 + "," + y2 + ")");
    }
}

// Yeni interface
interface Shape {
    void draw(int x, int y, int width, int height);
}

// Problem: LegacyRectangle Shape interface-inə uyğun deyil! ❌
```

---

### Adapter Pattern İmplementasiyası

```java
// Target Interface
interface MediaPlayer {
    void play(String audioType, String fileName);
}

// Adaptee - uyğunlaşdırılacaq class
class AdvancedMediaPlayer {
    public void playVlc(String fileName) {
        System.out.println("Playing vlc file: " + fileName);
    }
    
    public void playMp4(String fileName) {
        System.out.println("Playing mp4 file: " + fileName);
    }
}

// Adapter
class MediaAdapter implements MediaPlayer {
    private AdvancedMediaPlayer advancedPlayer;
    
    public MediaAdapter(String audioType) {
        if (audioType.equalsIgnoreCase("vlc")) {
            advancedPlayer = new AdvancedMediaPlayer();
        } else if (audioType.equalsIgnoreCase("mp4")) {
            advancedPlayer = new AdvancedMediaPlayer();
        }
    }
    
    @Override
    public void play(String audioType, String fileName) {
        if (audioType.equalsIgnoreCase("vlc")) {
            advancedPlayer.playVlc(fileName);
        } else if (audioType.equalsIgnoreCase("mp4")) {
            advancedPlayer.playMp4(fileName);
        }
    }
}

// Client
class AudioPlayer implements MediaPlayer {
    private MediaAdapter mediaAdapter;
    
    @Override
    public void play(String audioType, String fileName) {
        // Built-in support for mp3
        if (audioType.equalsIgnoreCase("mp3")) {
            System.out.println("Playing mp3 file: " + fileName);
        }
        // Adapter for other formats
        else if (audioType.equalsIgnoreCase("vlc") || 
                 audioType.equalsIgnoreCase("mp4")) {
            mediaAdapter = new MediaAdapter(audioType);
            mediaAdapter.play(audioType, fileName);
        } else {
            System.out.println("Invalid format: " + audioType);
        }
    }
}

// Main
public class AdapterPatternDemo {
    public static void main(String[] args) {
        AudioPlayer audioPlayer = new AudioPlayer();
        
        audioPlayer.play("mp3", "song.mp3");
        audioPlayer.play("mp4", "video.mp4");
        audioPlayer.play("vlc", "movie.vlc");
        audioPlayer.play("avi", "video.avi");
    }
}
```

---

### Real-World Nümunə: XML to JSON Adapter

```java
// Target Interface
interface JSONConverter {
    String convertToJSON(String data);
}

// Adaptee - Legacy XML library
class XMLProcessor {
    public String processXML(String xml) {
        System.out.println("Processing XML: " + xml);
        // Complex XML processing...
        return "<root><data>" + xml + "</data></root>";
    }
}

// Adapter
class XMLToJSONAdapter implements JSONConverter {
    private XMLProcessor xmlProcessor;
    
    public XMLToJSONAdapter() {
        this.xmlProcessor = new XMLProcessor();
    }
    
    @Override
    public String convertToJSON(String data) {
        String xml = xmlProcessor.processXML(data);
        
        // Convert XML to JSON
        String json = xmlToJson(xml);
        return json;
    }
    
    private String xmlToJson(String xml) {
        // Actual conversion logic
        return "{\"data\": \"" + xml + "\"}";
    }
}

// Client
public class Main {
    public static void main(String[] args) {
        JSONConverter converter = new XMLToJSONAdapter();
        String result = converter.convertToJSON("test data");
        System.out.println("JSON: " + result);
    }
}
```

---

## Decorator

### Decorator Nədir?

**Decorator** — obyektə dinamik olaraq yeni funksionallıq əlavə edən pattern.

### Hansı Problemi Həll Edir?

✅ Inheritance-dən istifadə etmədən funksionallıq əlavə etmək  
✅ Runtime-da davranış dəyişdirmək  
✅ Single Responsibility Principle

---

### Problem: Explosion of Subclasses

```java
// Base class
class Coffee {
    public double cost() {
        return 5.0;
    }
}

// Subclasses explosion! ❌
class CoffeeWithMilk extends Coffee {
    public double cost() {
        return super.cost() + 1.5;
    }
}

class CoffeeWithSugar extends Coffee {
    public double cost() {
        return super.cost() + 0.5;
    }
}

class CoffeeWithMilkAndSugar extends Coffee {
    public double cost() {
        return super.cost() + 2.0;
    }
}

class CoffeeWithMilkAndSugarAndWhippedCream extends Coffee {
    public double cost() {
        return super.cost() + 3.0;
    }
}

// Kombinasiyaların sayı = 2^n (n = əlavələr)
// 10 əlavə = 1024 class! ❌❌❌
```

---

### Decorator Pattern İmplementasiyası

```java
// Component Interface
interface Coffee {
    double cost();
    String description();
}

// Concrete Component
class SimpleCoffee implements Coffee {
    @Override
    public double cost() {
        return 5.0;
    }
    
    @Override
    public String description() {
        return "Simple Coffee";
    }
}

// Decorator Abstract Class
abstract class CoffeeDecorator implements Coffee {
    protected Coffee decoratedCoffee;
    
    public CoffeeDecorator(Coffee coffee) {
        this.decoratedCoffee = coffee;
    }
    
    @Override
    public double cost() {
        return decoratedCoffee.cost();
    }
    
    @Override
    public String description() {
        return decoratedCoffee.description();
    }
}

// Concrete Decorators
class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee coffee) {
        super(coffee);
    }
    
    @Override
    public double cost() {
        return super.cost() + 1.5;
    }
    
    @Override
    public String description() {
        return super.description() + ", Milk";
    }
}

class SugarDecorator extends CoffeeDecorator {
    public SugarDecorator(Coffee coffee) {
        super(coffee);
    }
    
    @Override
    public double cost() {
        return super.cost() + 0.5;
    }
    
    @Override
    public String description() {
        return super.description() + ", Sugar";
    }
}

class WhippedCreamDecorator extends CoffeeDecorator {
    public WhippedCreamDecorator(Coffee coffee) {
        super(coffee);
    }
    
    @Override
    public double cost() {
        return super.cost() + 1.0;
    }
    
    @Override
    public String description() {
        return super.description() + ", Whipped Cream";
    }
}

// Client
public class DecoratorDemo {
    public static void main(String[] args) {
        // Simple coffee
        Coffee coffee = new SimpleCoffee();
        System.out.println(coffee.description() + " = $" + coffee.cost());
        
        // Coffee with milk
        coffee = new MilkDecorator(new SimpleCoffee());
        System.out.println(coffee.description() + " = $" + coffee.cost());
        
        // Coffee with milk and sugar
        coffee = new SugarDecorator(new MilkDecorator(new SimpleCoffee()));
        System.out.println(coffee.description() + " = $" + coffee.cost());
        
        // Coffee with everything!
        coffee = new WhippedCreamDecorator(
                    new SugarDecorator(
                        new MilkDecorator(
                            new SimpleCoffee())));
        System.out.println(coffee.description() + " = $" + coffee.cost());
    }
}

// Output:
// Simple Coffee = $5.0
// Simple Coffee, Milk = $6.5
// Simple Coffee, Milk, Sugar = $7.0
// Simple Coffee, Milk, Sugar, Whipped Cream = $8.0
```

---

### Real-World Nümunə: Java I/O Streams

Java-da I/O Stream-lər Decorator pattern istifadə edir:

```java
// InputStream hierarchy-si Decorator pattern-dir!

// Component
InputStream inputStream = new FileInputStream("file.txt");

// Decorated with BufferedInputStream
inputStream = new BufferedInputStream(inputStream);

// Decorated with DataInputStream
DataInputStream dataInputStream = new DataInputStream(inputStream);

// İstifadə
int data = dataInputStream.read();
```

**Vizual:**

```
DataInputStream
    └── BufferedInputStream
            └── FileInputStream
                    └── file.txt
```

---

## Observer

### Observer Nədir?

**Observer** — bir obyektin vəziyyəti dəyişdikdə, asılı olan bütün obyektləri avtomatik xəbərdar edən pattern.

### Hansı Problemi Həll Edir?

✅ One-to-many əlaqə  
✅ Loose coupling  
✅ Event-driven proqramlaşdırma

---

### Real-World Analoq

```
YouTube kanalı (Subject)
    │
    ├── Abunəçi 1 (Observer) → Bildiriş alır
    ├── Abunəçi 2 (Observer) → Bildiriş alır
    └── Abunəçi 3 (Observer) → Bildiriş alır

Yeni video yüklənir → Hamıya bildiriş
```

---

### Observer Pattern İmplementasiyası

```java
// Observer Interface
interface Observer {
    void update(String message);
}

// Subject Interface
interface Subject {
    void attach(Observer observer);
    void detach(Observer observer);
    void notifyObservers();
}

// Concrete Subject
class YouTubeChannel implements Subject {
    private List<Observer> subscribers = new ArrayList<>();
    private String latestVideo;
    
    @Override
    public void attach(Observer observer) {
        subscribers.add(observer);
    }
    
    @Override
    public void detach(Observer observer) {
        subscribers.remove(observer);
    }
    
    @Override
    public void notifyObservers() {
        for (Observer subscriber : subscribers) {
            subscriber.update(latestVideo);
        }
    }
    
    public void uploadVideo(String title) {
        this.latestVideo = title;
        System.out.println("Channel uploaded: " + title);
        notifyObservers();
    }
}

// Concrete Observers
class Subscriber implements Observer {
    private String name;
    
    public Subscriber(String name) {
        this.name = name;
    }
    
    @Override
    public void update(String message) {
        System.out.println(name + " received notification: " + message);
    }
}

// Client
public class ObserverDemo {
    public static void main(String[] args) {
        YouTubeChannel channel = new YouTubeChannel();
        
        Observer subscriber1 = new Subscriber("Ali");
        Observer subscriber2 = new Subscriber("Vusal");
        Observer subscriber3 = new Subscriber("Cavid");
        
        channel.attach(subscriber1);
        channel.attach(subscriber2);
        channel.attach(subscriber3);
        
        channel.uploadVideo("Design Patterns Tutorial");
        
        System.out.println("\n--- Subscriber 2 unsubscribed ---\n");
        channel.detach(subscriber2);
        
        channel.uploadVideo("Java Concurrency Tutorial");
    }
}

// Output:
// Channel uploaded: Design Patterns Tutorial
// Ali received notification: Design Patterns Tutorial
// Vusal received notification: Design Patterns Tutorial
// Cavid received notification: Design Patterns Tutorial
//
// --- Subscriber 2 unsubscribed ---
//
// Channel uploaded: Java Concurrency Tutorial
// Ali received notification: Java Concurrency Tutorial
// Cavid received notification: Java Concurrency Tutorial
```

---

### Real-World Nümunə: Weather Station

```java
// Observer
interface WeatherObserver {
    void update(float temperature, float humidity, float pressure);
}

// Subject
interface WeatherSubject {
    void registerObserver(WeatherObserver observer);
    void removeObserver(WeatherObserver observer);
    void notifyObservers();
}

// Concrete Subject
class WeatherStation implements WeatherSubject {
    private List<WeatherObserver> observers;
    private float temperature;
    private float humidity;
    private float pressure;
    
    public WeatherStation() {
        observers = new ArrayList<>();
    }
    
    @Override
    public void registerObserver(WeatherObserver observer) {
        observers.add(observer);
    }
    
    @Override
    public void removeObserver(WeatherObserver observer) {
        observers.remove(observer);
    }
    
    @Override
    public void notifyObservers() {
        for (WeatherObserver observer : observers) {
            observer.update(temperature, humidity, pressure);
        }
    }
    
    public void setMeasurements(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        this.pressure = pressure;
        notifyObservers();
    }
}

// Concrete Observers
class CurrentConditionsDisplay implements WeatherObserver {
    @Override
    public void update(float temperature, float humidity, float pressure) {
        System.out.println("Current conditions: " + temperature + 
                          "°C and " + humidity + "% humidity");
    }
}

class StatisticsDisplay implements WeatherObserver {
    private List<Float> temperatures = new ArrayList<>();
    
    @Override
    public void update(float temperature, float humidity, float pressure) {
        temperatures.add(temperature);
        float avg = (float) temperatures.stream()
                                       .mapToDouble(Float::doubleValue)
                                       .average()
                                       .orElse(0.0);
        System.out.println("Avg/Max/Min temperature: " + avg + "/" + 
                          Collections.max(temperatures) + "/" + 
                          Collections.min(temperatures));
    }
}

// Client
public class WeatherStationDemo {
    public static void main(String[] args) {
        WeatherStation weatherStation = new WeatherStation();
        
        CurrentConditionsDisplay currentDisplay = new CurrentConditionsDisplay();
        StatisticsDisplay statisticsDisplay = new StatisticsDisplay();
        
        weatherStation.registerObserver(currentDisplay);
        weatherStation.registerObserver(statisticsDisplay);
        
        weatherStation.setMeasurements(25.5f, 65f, 1013f);
        System.out.println();
        weatherStation.setMeasurements(27.2f, 70f, 1012f);
        System.out.println();
        weatherStation.setMeasurements(23.8f, 60f, 1014f);
    }
}
```

---

### Java-da Observer: PropertyChangeListener

Java-da built-in Observer pattern var:

```java
import java.beans.PropertyChangeListener;
import java.beans.PropertyChangeSupport;

class Product {
    private String name;
    private double price;
    private PropertyChangeSupport support;
    
    public Product() {
        support = new PropertyChangeSupport(this);
    }
    
    public void addPropertyChangeListener(PropertyChangeListener listener) {
        support.addPropertyChangeListener(listener);
    }
    
    public void removePropertyChangeListener(PropertyChangeListener listener) {
        support.removePropertyChangeListener(listener);
    }
    
    public void setPrice(double price) {
        double oldPrice = this.price;
        this.price = price;
        support.firePropertyChange("price", oldPrice, price);
    }
    
    public double getPrice() {
        return price;
    }
}

// Client
public class PropertyChangeDemo {
    public static void main(String[] args) {
        Product product = new Product();
        
        product.addPropertyChangeListener(evt -> {
            System.out.println("Price changed from " + evt.getOldValue() + 
                             " to " + evt.getNewValue());
        });
        
        product.setPrice(100.0);
        product.setPrice(150.0);
    }
}
```

---

## Strategy

### Strategy Nədir?

**Strategy** — alqoritmləri enkapsulasiya edib onları dəyişdirilə bilən edən pattern.

### Hansı Problemi Həll Edir?

✅ Runtime-da alqoritm dəyişdirmək  
✅ If-else zəncirindən xilas olmaq  
✅ Open/Closed Principle

---

### Problem: İf-Else Zənciri

```java
class PaymentService {
    public void pay(String paymentType, double amount) {
        if (paymentType.equals("CREDIT_CARD")) {
            System.out.println("Processing credit card payment: $" + amount);
            // Credit card logic...
        } else if (paymentType.equals("PAYPAL")) {
            System.out.println("Processing PayPal payment: $" + amount);
            // PayPal logic...
        } else if (paymentType.equals("BITCOIN")) {
            System.out.println("Processing Bitcoin payment: $" + amount);
            // Bitcoin logic...
        }
        // Yeni payment method əlavə etmək üçün bu kodu dəyişdirməlisən! ❌
    }
}
```

---

### Strategy Pattern İmplementasiyası

```java
// Strategy Interface
interface PaymentStrategy {
    void pay(double amount);
}

// Concrete Strategies
class CreditCardStrategy implements PaymentStrategy {
    private String cardNumber;
    private String cvv;
    
    public CreditCardStrategy(String cardNumber, String cvv) {
        this.cardNumber = cardNumber;
        this.cvv = cvv;
    }
    
    @Override
    public void pay(double amount) {
        System.out.println("Paid $" + amount + " using Credit Card: " + cardNumber);
    }
}

class PayPalStrategy implements PaymentStrategy {
    private String email;
    
    public PayPalStrategy(String email) {
        this.email = email;
    }
    
    @Override
    public void pay(double amount) {
        System.out.println("Paid $" + amount + " using PayPal: " + email);
    }
}

class BitcoinStrategy implements PaymentStrategy {
    private String walletAddress;
    
    public BitcoinStrategy(String walletAddress) {
        this.walletAddress = walletAddress;
    }
    
    @Override
    public void pay(double amount) {
        System.out.println("Paid $" + amount + " using Bitcoin: " + walletAddress);
    }
}

// Context
class ShoppingCart {
    private PaymentStrategy paymentStrategy;
    
    public void setPaymentStrategy(PaymentStrategy strategy) {
        this.paymentStrategy = strategy;
    }
    
    public void checkout(double amount) {
        paymentStrategy.pay(amount);
    }
}

// Client
public class StrategyDemo {
    public static void main(String[] args) {
        ShoppingCart cart = new ShoppingCart();
        
        // Pay with credit card
        cart.setPaymentStrategy(new CreditCardStrategy("1234-5678-9012-3456", "123"));
        cart.checkout(100.0);
        
        // Pay with PayPal
        cart.setPaymentStrategy(new PayPalStrategy("user@example.com"));
        cart.checkout(50.0);
        
        // Pay with Bitcoin
        cart.setPaymentStrategy(new BitcoinStrategy("1A2B3C4D5E6F"));
        cart.checkout(200.0);
    }
}
```

---

### Real-World Nümunə: Sorting Strategy

```java
// Strategy
interface SortStrategy {
    void sort(int[] array);
}

// Concrete Strategies
class BubbleSortStrategy implements SortStrategy {
    @Override
    public void sort(int[] array) {
        System.out.println("Sorting using Bubble Sort");
        // Bubble sort implementation
        int n = array.length;
        for (int i = 0; i < n - 1; i++) {
            for (int j = 0; j < n - i - 1; j++) {
                if (array[j] > array[j + 1]) {
                    int temp = array[j];
                    array[j] = array[j + 1];
                    array[j + 1] = temp;
                }
            }
        }
    }
}

class QuickSortStrategy implements SortStrategy {
    @Override
    public void sort(int[] array) {
        System.out.println("Sorting using Quick Sort");
        quickSort(array, 0, array.length - 1);
    }
    
    private void quickSort(int[] arr, int low, int high) {
        if (low < high) {
            int pi = partition(arr, low, high);
            quickSort(arr, low, pi - 1);
            quickSort(arr, pi + 1, high);
        }
    }
    
    private int partition(int[] arr, int low, int high) {
        int pivot = arr[high];
        int i = low - 1;
        for (int j = low; j < high; j++) {
            if (arr[j] < pivot) {
                i++;
                int temp = arr[i];
                arr[i] = arr[j];
                arr[j] = temp;
            }
        }
        int temp = arr[i + 1];
        arr[i + 1] = arr[high];
        arr[high] = temp;
        return i + 1;
    }
}

class MergeSortStrategy implements SortStrategy {
    @Override
    public void sort(int[] array) {
        System.out.println("Sorting using Merge Sort");
        mergeSort(array, 0, array.length - 1);
    }
    
    private void mergeSort(int[] arr, int left, int right) {
        if (left < right) {
            int mid = (left + right) / 2;
            mergeSort(arr, left, mid);
            mergeSort(arr, mid + 1, right);
            merge(arr, left, mid, right);
        }
    }
    
    private void merge(int[] arr, int left, int mid, int right) {
        // Merge implementation...
    }
}

// Context
class Sorter {
    private SortStrategy strategy;
    
    public void setStrategy(SortStrategy strategy) {
        this.strategy = strategy;
    }
    
    public void sortArray(int[] array) {
        strategy.sort(array);
    }
}

// Client
public class SortingDemo {
    public static void main(String[] args) {
        Sorter sorter = new Sorter();
        int[] data = {64, 34, 25, 12, 22, 11, 90};
        
        // Use different algorithms based on data size
        if (data.length < 10) {
            sorter.setStrategy(new BubbleSortStrategy());
        } else if (data.length < 100) {
            sorter.setStrategy(new QuickSortStrategy());
        } else {
            sorter.setStrategy(new MergeSortStrategy());
        }
        
        sorter.sortArray(data);
        System.out.println(Arrays.toString(data));
    }
}
```

---

## Strategy vs State Pattern

| Xüsusiyyət | Strategy | State |
|-----------|----------|-------|
| **Məqsəd** | Alqoritm seçmək | Davranış vəziyyətə görə dəyişir |
| **Dəyişiklik** | Client dəyişdirir | Obyekt özü dəyişir |
| **Context** | Strategy bilir | State bilməyə bilər |

---

### State Pattern Nümunəsi

```java
// State interface
interface State {
    void handleRequest();
}

// Concrete States
class OnState implements State {
    @Override
    public void handleRequest() {
        System.out.println("Light is ON. Turning OFF...");
    }
}

class OffState implements State {
    @Override
    public void handleRequest() {
        System.out.println("Light is OFF. Turning ON...");
    }
}

// Context
class LightSwitch {
    private State state;
    
    public LightSwitch() {
        this.state = new OffState();  // Initial state
    }
    
    public void setState(State state) {
        this.state = state;
    }
    
    public void press() {
        state.handleRequest();
        
        // State transition
        if (state instanceof OffState) {
            setState(new OnState());
        } else {
            setState(new OffState());
        }
    }
}

// Client
public class StateDemo {
    public static void main(String[] args) {
        LightSwitch lightSwitch = new LightSwitch();
        
        lightSwitch.press();  // OFF → ON
        lightSwitch.press();  // ON → OFF
        lightSwitch.press();  // OFF → ON
    }
}
```

---

## Özünü Yoxla

### Sual 1: Hansı pattern-i istifadə edərsən?

```
Bir obyekt lazımdır və bütün proqram bu obyekti istifadə edir?
```

<details>
<summary>Cavab</summary>

**Singleton** - Yalnız bir instance yaratmaq üçün
</details>

---

### Sual 2: Hansı pattern-i istifadə edərsən?

```
Obyekt yaratma baha əməliyyatdır və oxşar obyektlər lazımdır?
```

<details>
<summary>Cavab</summary>

**Prototype** - Clone edərək yeni obyekt yaratmaq
</details>

---

### Sual 3: Hansı pattern-i istifadə edərsən?

```
Runtime-da alqoritm dəyişdirmək lazımdır?
```

<details>
<summary>Cavab</summary>

**Strategy** - Alqoritmləri enkapsulasiya edib dəyişdirmək
</details>

---

## Xülasə

### Creational Patterns
- **Singleton:** Yalnız bir instance
- **Factory Method:** Obyekt yaratma subclass-lara
- **Abstract Factory:** Əlaqəli obyekt ailəsi
- **Builder:** Mürəkkəb obyekt yaratma
- **Prototype:** Clone ilə yaratma

### Structural Patterns
- **Adapter:** Interface uyğunlaşdırma
- **Decorator:** Dinamik funksionallıq
- **Observer:** One-to-many notification

### Behavioral Patterns
- **Strategy:** Alqoritm dəyişdirmək
- **State:** Vəziyyətə görə davranış

---

## Pattern Seçim Qısa Yol

```
Obyekt yaratma problemi?
├─ Yalnız 1 instance? → Singleton
├─ Mürəkkəb obyekt? → Builder
├─ Baha yaratma? → Prototype
└─ Obyekt ailəsi? → Abstract Factory

Struktural problem?
├─ Interface uyğun deyil? → Adapter
└─ Dinamik funksionallıq? → Decorator

Davranış problemi?
├─ Alqoritm dəyişir? → Strategy
├─ Notification lazım? → Observer
└─ Vəziyyətə görə davranış? → State
```

---

