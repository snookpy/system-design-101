# SOLID

Guide for helping developers create better-structured, more manageable object-oriented programming (OOP) code.

- **S -- Single Responsibility Principle (SRP)** a class should have only one reason to change.
- **O -- The Open/Closed Principle (OCP)** a class or method open for extension but closed for modification
- **L -- The Liskov Substitution Principle (LSP)** objects of a subclass should be able to replace objects of the superclass without affecting the correctness of the program
- **I -- The Interface Segregation Principle (ISP)** concrete classes should not be forced to depend on interfaces they do not use.
- **D -- The Dependency Inversion Principle (DIP)** high-level modules should not depend on low-level modules. Both should depend on abstractions. It helps create loosely coupled code by relying on abstractions rather than concrete implementations.

## Single Responsibility Principle (SRP)

Every software component should have only one and one job or responsibility.

- A **class** should be responsible for one specific task.
- A **function/method** should perform one specific action.
- A **module** should handle one specific area of functionality.

### Cohesion

Cohesion refers to how closely related and focused the responsibilities of a single module, class, or function are. Highly cohesive means that the elements within a module or class are strongly related and work together to achieve a single purpose. Low cohesion a class has unrelated responsibilities bundled together, which makes it harder to maintain and understand..

Low cohesion example:

```csharp
using System;

public class UserManager
{
    public void addUser(string user)
    {
        // Logic to add a user
    }

    public void deleteUser(string user)
    {
        // Logic to delete a user
    }

    public void writeToFile(string data)
    {
        // Logic to write data to a file
    }

    public string readFromFile(string fileName)
    {
        // Logic to read data from a file
        return fileName;
    }
}
```

**Increasing Cohesion: Splitting Responsibilities into Separate Classes**
To improve cohesion and follow the SRP, we can split this class into smaller, more focused classes. One class will handle user management, and another will take care of file operations.

```csharp
public class UserManager
{
    public void addUser(string user)
    {
        // Logic to add a user
    }

    public void deleteUser(string user)
    {
        // Logic to delete a user
    }
}

public class FileManager
{
    public void writeToFile(string data)
    {
        // Logic to write data to a file
    }

    public string readFromFile(string fileName)
    {
        // Logic to read data from a file
        return fileName;
    }
}
```

### Coupling

How closely connected different modules, classes, or methods are to each other.

We aim for **low coupling**, meaning that classes and components should be as independent from each other as possible.

**High coupling**, on the other hand, means that classes are tightly dependent on one another, making it harder to change or reuse parts of the system.

#### High Coupling in Code: One Class Doing Too Much

```csharp
using System;

public class Student {
    private string studentId;

    public string getStudentId() {
        return studentId;
    }

    public void setStudentId(string studentId) {
        this.studentId = studentId;
    }

    public void save() {
        // Database logic directly in the Student class
        Console.WriteLine("Connecting to database...");
        Console.WriteLine("Saving student with ID: " + studentId);
        // Other complex database code here
    }
}
```

#### Reducing Coupling: Separate Database Logic from Student Class

```csharp
using System;

public class DatabaseManager {
    public void SaveToDatabase(string studentId) {
        // Logic for interacting with the database
        Console.WriteLine("Connecting to database...");
        Console.WriteLine("Saving student with ID: " + studentId);
        // Actual database save logic here
    }
}

public class Student {
    private string studentId;
    private DatabaseManager dbManager;  // Dependency on DatabaseManager

    public Student(DatabaseManager dbManager) {
        this.dbManager = dbManager;
    }

    public string GetStudentId() {
        return studentId;
    }

    public void SetStudentId(string studentId) {
        this.studentId = studentId;
    }

    public void Save() {
        // Delegate database operations to the DatabaseManager
        dbManager.SaveToDatabase(studentId);
    }
}
```

### Separation of Concerns

SoC is a design principle for separating a computer program into distinct sections, such that each section addresses a separate concern. A concern is a set of information that affects the code of a computer program.

#### Example of Separation of Concerns

build a simple web application to handle user registration. We'll separate the concerns into different modules:

- Database operations
- Business logic

**1. Database Operations:**
For interacting with the database. It includes functions for adding and retrieving users from the database.

```csharp
using System;
using System.Data.SQLite;

public class Database
{
    private SQLiteConnection connection;

    public Database(string dbName)
    {
        connection = new SQLiteConnection($"Data Source={dbName};Version=3;");
        connection.Open();
        create_table();
    }

    private void create_table()
    {
        string query = @"CREATE TABLE IF NOT EXISTS users (
                            id INTEGER PRIMARY KEY AUTOINCREMENT,
                            username TEXT NOT NULL,
                            email TEXT NOT NULL)";
        using (var command = new SQLiteCommand(query, connection))
        {
            command.ExecuteNonQuery();
        }
    }

    public void add_user(string username, string email)
    {
        string query = "INSERT INTO users (username, email) VALUES (@username, @email)";
        using (var command = new SQLiteCommand(query, connection))
        {
            command.Parameters.AddWithValue("@username", username);
            command.Parameters.AddWithValue("@email", email);
            command.ExecuteNonQuery();
        }
    }
}
```

**2. Business Logic:**
Or user registration, including validation of usernames and emails. It uses the database operations module to interact with the database.

```csharp
using System;
public class UserService {
    private Database db;

    public UserService(Database db) {
        this.db = db;
    }

    public void register_user(string username, string email) {
        // Register user here.
    }

    public bool is_valid_username(string username) {
        // Check if username is valid
        return true; // Placeholder
    }

    public bool is_valid_email(string email) {
        // Check if email is valid
        return true; // Placeholder
    }
}
```

### Single Responsibility Principle (SRP) vs. Coupling

- **SRP** Focuses on ensuring that a class has only one reason to change.
- **Coupling** Focuses on minimizing dependencies between classes or modules.
- **Relationship**: Applying SRP can lead to lower coupling because when each class has a single responsibility, classes are less likely to depend heavily on each other.

### Single Responsibility Principle (SRP) vs. Cohesion

- **SRP** Ensures that a class has a single responsibility.
- **Cohesion** Ensures that the tasks performed by a class are closely related.
- **Relationship**: High cohesion often results from applying SRP because a class with a single responsibility will naturally have related tasks grouped together.

### Single Responsibility Principle vs. Separation of Concerns (SoC)

- **SRP** Focuses on single responsibility within a class or module.
- **SoC** Focuses on dividing the program into distinct features with minimal overlap.
- **Relationship**: Applying SRP is a way to achieve SoC. By ensuring that each class has a single responsibility, the overall design adheres to the principle of separating concerns.

## The Open/Closed Principle (OCP)

Software entities (such as classes, modules, and functions) should be open for extension, but closed for modification.

- **Closed for modification**: Once a class is developed and tested, its source code should not be changed in the future to add new behavior. By keeping the core code unchanged, you ensure that the tested and stable parts of the system remain reliable. If every change required modifying the existing code, you could introduce new bugs or break the functionality that has already been proven to work.
- **Open for extension**: can add new functionality to the system without modifying the existing code. This is typically achieved through techniques like **inheritance**, **interfaces**, or **polymorphism**, which allow you to extend the behavior of the code by adding new components (like subclasses or new methods) rather than modifying what’s already in place. This keeps the system flexible and adaptable while preserving the integrity of the original code.

### Code without OCP

```csharp
using System;

public class Invoice
{
    private double amount;

    public Invoice(double amount)
    {
        this.amount = amount;
    }

    public double getAmount()
    {
        return amount;
    }

    // This method handles generating basic invoices
    public void generateInvoice()
    {
        Console.WriteLine("Generating basic invoice for amount: " + amount);
    }

    // This method handles applying discounts on the invoice
    public void applyDiscount()
    {
        Console.WriteLine("Applying discount on invoice: " + amount);
    }
}

// Example usage
class Program
{
    static void Main(string[] args)
    {
        Invoice invoice = new Invoice(100);
        invoice.generateInvoice();
        invoice.applyDiscount();
    }
}
```

If we need to add a new type of invoice later (like an **international** invoice), we would have to modify this class. Without applying the Open/Closed Principle, we’ll have to modify the existing:

```csharp
// Adding new logic for international invoices
public void generateInvoice(string type) {
    if (type == "basic") {
        Console.WriteLine("Generating basic invoice for amount: " + amount);
    } else if (type == "international") {
        Console.WriteLine("Generating international invoice for amount: " + amount);
    }
}
```

### **Refactoring the Code to Follow OCP**

1. we’ll create an interface for generating invoices
2. create separate classes for each type of invoice
3. and we modify the Invoice class to delegate invoice generation to an object of type **InvoiceGenerator**

```csharp
using System;

// 1. Interface for invoice generation
public interface InvoiceGenerator {
    void generateInvoice(double amount);
}

// 2. Class for generating basic invoices
public class BasicInvoice : InvoiceGenerator {
    public void generateInvoice(double amount) {
        Console.WriteLine("Generating basic invoice for amount: " + amount);
    }
}

// Class for generating international invoices
public class InternationalInvoice : InvoiceGenerator {
    public void generateInvoice(double amount) {
        Console.WriteLine("Generating international invoice for amount: " + amount);
    }
}

// 3. Modified Invoice class
public class Invoice {
    private double amount;
    private InvoiceGenerator invoiceGenerator;

    // Constructor takes an InvoiceGenerator to delegate invoice generation
    public Invoice(double amount, InvoiceGenerator invoiceGenerator) {
        this.amount = amount;
        this.invoiceGenerator = invoiceGenerator;
    }

    // Delegate invoice generation to the InvoiceGenerator
    public void GenerateInvoice() {
        invoiceGenerator.GenerateInvoice(amount);
    }

    public void ApplyDiscount() {
        Console.WriteLine("Applying discount on invoice: " + amount);
    }
}

```

additionally, we can add new types of invoices by creating new classes that implement the InvoiceGenerator interface.
```csharp
public interface InvoiceGenerator {
    void generateInvoice(double amount);
}

// New class for generating detailed invoices
public class DetailedInvoice : InvoiceGenerator {
    public void generateInvoice(double amount) {
        Console.WriteLine("Generating detailed invoice with breakdown for amount: " + amount);
    }
}

```

### Caution When Applying OCP
While the Open/Closed Principle is highly beneficial, it’s important to approach its application with caution to avoid unnecessary complexity.
- **Over-engineering**: Avoid creating too many abstractions or interfaces for every small change. This can lead to a complex codebase that is hard to understand and maintain.
- **Start Simple**: Begin with a straightforward design and only introduce abstractions when there is a clear need for extension.
- **Balance Between Flexibility and Simplicity**: Make sure the system doesn’t become unnecessarily abstract, especially when the future requirements are not clear.
- **Think About Actual Needs**: When applying OCP, always think about real-world needs. Designing code for possible future extensions that may never happen can lead to wasted effort and complexity. Focus on the current requirements, and refactor for extensibility when new demands arise.

## The Liskov Substitution Principle (LSP)
Objects of a superclass should be replaceable with objects of a subclass without affecting the correctness of the program.

### Example Violates LSP
**Car** vs **ElectricCar**

```csharp
using System;

public class Car {
    public void start() {
        Console.WriteLine("Car is starting");
    }

    public void stop() {
        Console.WriteLine("Car is stopping");
    }

    public virtual void refuel() {
        Console.WriteLine("Car is refueling");
    }
}

public class ElectricCar : Car {
    public override void refuel() {
        // Electric cars don't use fuel, so what do we do here?
        throw new NotSupportedException("Electric cars don't need refueling");
    }

    public void reCharge() {
        Console.WriteLine("Car is recharging");
    }
}
```
the ElectricCar class overrides the refuel() method from the Car class, but it cannot implement the functionality properly because electric cars don’t use fuel. This leads to an issue where calling the refuel() method on an ElectricCar will break the program by throwing an exception.
```csharp
class Program {
    static void Main(string[] args) {
        // Creating an instance of Car
        Car myCar = new Car();
        myCar.Start();
        myCar.Refuel();  // This works fine as expected for a regular car
        myCar.Stop();

        // Creating an instance of ElectricCar
        Car myElectricCar = new ElectricCar();  // We are treating ElectricCar as a Car
        myElectricCar.Start();

        // Here's the problem:
        // When we call the refuel() method, it throws an exception
        // because ElectricCar doesn't need refueling
        try {
            myElectricCar.Refuel();  // This will throw a NotSupportedException
        } catch (NotSupportedException ex) {
            Console.WriteLine(ex.Message);
        }

        myElectricCar.Stop();  // This works, but refuel() breaks the program
    }
}

```

### Refactoring to Follow LSP
Create hierarchy that properly represents the relationship between different types of vehicles.
1. Create a general **Vehicle** class that includes common behaviors like start and stop.
```csharp
using System;
public abstract class Vehicle {
    public void start() {
        Console.WriteLine("Vehicle is starting");
    }

    public void stop() {
        Console.WriteLine("Vehicle is stopping");
    }

    // Abstract method for fuel/energy behavior
    public abstract void refuel();
}
```
2. Refactor Car and ElectricCar Classes
```csharp
public class Car : Vehicle {
    public override void refuel() {
        Console.WriteLine("Car is refueling");
    }
}

public class ElectricCar : Vehicle {
    public override void refuel() {
        // Call recharge() instead of refueling with gasoline
        recharge();
    }

    // Electric cars need to recharge instead of refuel
    private void recharge() {
        Console.WriteLine("Electric car is recharging");
    }
}

// Main class to test the hierarchy
public class Solution {
    public static void Main(string[] args) {
        // Using Car object
        Vehicle myCar = new Car();
        myCar.Start();
        myCar.Refuel();  // Car-specific refueling behavior
        myCar.Stop();

        Console.WriteLine();

        // Using ElectricCar object
        Vehicle myElectricCar = new ElectricCar();
        myElectricCar.Start();
        myElectricCar.Refuel();  // Calls Recharge() via Refuel()
        myElectricCar.Stop();
    }
}
```
- Both `Car` and `ElectricCar` inherit from `Vehicle`, and they can be used interchangeably without breaking the program.
- Each subclass (`Car` and `ElectricCar`) implements its own version of the `refuel()` method. For the electric car, `refuel()` calls the specific recharge() method, maintaining expected behavior without breaking the contract of the Vehicle class.
- This ensures that the **Liskov Substitution Principle** is followed: an instance of `ElectricCar` can replace `Car` without causing issues.

### Composition
"w"e explored how using inheritance can sometimes violate the Liskov Substitution Principle. Another way to follow LSP without falling into the pitfalls of inheritance is by using **composition over inheritance**. 

#### Problem with Inheritance (Violating LSP)
```csharp
using System;

public class Bird {
    public virtual void fly() {
        Console.WriteLine("Bird is flying");
    }
}

public class Penguin : Bird {
    public override void fly() {
        // Penguins can't fly, so this breaks the substitution
        throw new NotSupportedException("Penguins can't fly");
    }
}
```

#### Using Composition to Follow LSP
```csharp
using System;
// Interface for flying behavior
public interface Flyable
{
    void fly();
}

// Bird class that contains general behavior for all birds
public class Bird {
    public void eat() {
        Console.WriteLine("Bird is eating");
    }
}

// Class for flying birds that uses the Flyable interface
public class Sparrow : Bird, Flyable {
    public void fly() {
        Console.WriteLine("Sparrow is flying");
    }
}

// Class for non-flying birds like Penguin
public class Penguin : Bird {
    // Penguins do not implement Flyable, so no fly() method is needed
}

class Main {
    public static void Main(string[] args) {
        // Creating a Sparrow that can fly
        Sparrow mySparrow = new Sparrow();
        mySparrow.Eat();
        mySparrow.Fly();  // Works because sparrows can fly

        // Creating a Penguin that cannot fly
        Penguin myPenguin = new Penguin();
        myPenguin.Eat();
        // myPenguin.Fly();  // This will not compile, because Penguins don't have a fly() method
    }
}
```
## The Interface Segregation Principle (ISP)
Clients should not be forced to depend on interfaces they do not use, a class should only implement the methods it actually needs.

### Example Violates ISP
Universal *Printer* Interface but we implement basic printer that only prints.
```csharp
using System;


public interface Printer
{
    // Method to print a document
    void printDocument(string document);

    // Method to scan a document
    void scanDocument(string document);

    // Method to fax a document
    void faxDocument(string document);

    // Method to staple a document
    void stapleDocument(string document);
}


public class BasicPrinter : Printer
{
    public override void printDocument(string document)
    {
        Console.WriteLine("Printing document: " + document);
    }

    public override void scanDocument(string document)
    {
        // This printer can't scan, so we throw an exception
        throw new NotSupportedException("Basic printer can't scan documents.");
    }

    public override void faxDocument(string document)
    {
        // This printer can't fax, so we throw an exception
        throw new NotSupportedException("Basic printer can't fax documents.");
    }

    public override void stapleDocument(string document)
    {
        // This printer can't staple, so we throw an exception
        throw new NotSupportedException("Basic printer can't staple documents.");
    }
}
```
The **BasicPrinter** class is forced to implement methods it doesn’t need, such as `scanDocument()`, `faxDocument()`, and `stapleDocument()`. Since it can’t perform these actions, we end up throwing exceptions.

### Refactoring to Follow ISP
Break the Large Interface into Smaller Interfaces
```csharp
using System;

// Interface for basic printing functionality
public interface Printer
{
    void printDocument(string document);
}

// Interface for scanning functionality
public interface Scanner
{
    void scanDocument(string document);
}

// Interface for faxing functionality
public interface Fax
{
    void faxDocument(string document);
}

// Interface for stapling functionality
public interface Stapler
{
    void stapleDocument(string document);
}


public class BasicPrinter : Printer
{
    public void printDocument(string document)
    {
        System.Console.WriteLine("Printing document: " + document);
    }
}

public class AdvancedPrinter : Printer, Scanner, Fax, Stapler
{
    public void printDocument(string document)
    {
        System.Console.WriteLine("Printing document: " + document);
    }

    public void scanDocument(string document)
    {
        System.Console.WriteLine("Scanning document: " + document);
    }

    public void faxDocument(string document)
    {
        System.Console.WriteLine("Faxing document: " + document);
    }

    public void stapleDocument(string document)
    {
        System.Console.WriteLine("Stapling document: " + document);
    }
}

// Main class to test the new design
public class Solution
{
    public static void Main(string[] args)
    {
        // Testing BasicPrinter (only supports printing)
        Printer basicPrinter = new BasicPrinter();
        basicPrinter.printDocument("Basic Document");

        // Testing AdvancedPrinter (supports all functions)
        AdvancedPrinter advancedPrinter = new AdvancedPrinter();
        advancedPrinter.printDocument("Advanced Document");
        advancedPrinter.scanDocument("Advanced Document");
        advancedPrinter.faxDocument("Advanced Document");
        advancedPrinter.stapleDocument("Advanced Document");
    }
}
```

### Situations Where might Violate ISP
**1. Low Cohesion**: happens when a class tries to handle multiple unrelated tasks, making the class less focused and harder to maintain. Low cohesion often leads to ISP violations because the class implements more methods than necessary. Here, **MediaDevice** tries to handle both playing and recording, which can create low cohesion for a device that only plays media but doesn’t record.
```csharp
// MediaDevice interface with methods for audio and video functionalities
public interface MediaDevice
{
    void playAudio();
    void playVideo();
    void recordVideo();
}
```

**2. Fat Interface**: contains too many methods, usually covering multiple unrelated functionalities. Fat interfaces force classes to depend on methods they don’t need, leading to bloated code and ISP violations.

**Example: Payment Interface**
This interface is too large and covers different types of payment methods. It forces all classes implementing it to handle multiple payment options, even if they don’t need them.
```csharp
// PaymentProcessor interface with methods for different payment types
public interface PaymentProcessor
{
    void processCreditCard();
    void processPayPal();
    void processBankTransfer();
}
```

**3. Empty or Unsupported Methods**: When classes implement an interface and include empty or unsupported methods, it’s a clear sign of an ISP violation.
```csharp
using System;

public class Car : Vehicle
{
    public void drive()
    {
        Console.WriteLine("Car is driving...");
    }

    public void fly()
    {
        // Cars can't fly
        throw new NotSupportedException("Car can't fly");
    }

    public void sail()
    {
        // Cars can't sail
        throw new NotSupportedException("Car can't sail");
    }
}
```

** 4. Class with too many responsibilities**: It often implements multiple methods from a large interface. This indicates that the interface may need to be broken into smaller ones. Example Employee interface.
```csharp
using System;

public class CustomerSupport : Employee
{
    public void calculateSalary()
    {
        // Customer support doesn't calculate salaries
        throw new NotSupportedException("Customer support doesn't calculate salaries");
    }

    public void handleCustomerQueries()
    {
        Console.WriteLine("Handling customer queries...");
    }

    public void manageTeam()
    {
        // Customer support doesn't manage teams
        throw new NotSupportedException("Customer support doesn't manage teams");
    }
}
```

### How ISP Relates to Other SOLID Principles
 - SRP: ISP supports SRP by keeping interfaces focused on a single responsibility, ensuring that classes implement only what they need.

 - OCP: ISP aligns with OCP by making it easy to extend systems with new interfaces without modifying existing ones.

 - LSP: ISP helps with LSP because classes implementing smaller interfaces respect their contract, making substitution safer and more predictable.

## Dependency Inversion Principle (DIP)
High-level modules should not depend on low-level modules. Both should depend on abstractions.

Abstractions should not depend on details; details should depend on abstractions.

means that software design should prioritize flexibility and decoupling by ensuring that high-level policies are not directly tied to low-level implementation details. Both high-level and low-level modules should rely on abstractions (e.g., interfaces or abstract classes) to establish a flexible relationship.

### Concepts
 - **High-Level Modules**: These are components that contain the core business logic of the application. They should dictate the flow of the application without being dependent on specific implementation details.
 - **Low-Level Modules**: These modules handle specific, detailed tasks, such as database operations or network communication. They should not directly control the application's main flow.
 - **Abstractions**: Interfaces or abstract classes that define general behaviors. High-level and low-level modules depend on these abstractions to communicate, keeping the system loosely coupled.


### Understanding Dependency Inversion Principle with Example
Like `EmailService` sends notifications. Without DIP, the high-level module (e.g., `NotificationService`) would depend directly on the low-level `EmailService`. This makes it difficult to switch to other notification types, such as SMS or push notifications, without modifying the `NotificationService`.
```csharp
public class EmailService
{
    public void sendEmail(string message)
    {
        System.Console.WriteLine("Sending email: " + message);
    }
}

public class NotificationService
{
    private EmailService emailService;

    public NotificationService()
    {
        emailService = new EmailService(); // Tight coupling to EmailService
    }

    public void send(string message)
    {
        emailService.sendEmail(message);
    }
}
```

### Refactoring to Follow DIP
1. Introduce an Abstraction with interface, `NotificationSender`
2. Implement Low-Level Modules Based on the Abstraction, `EmailService`, `SMSService` that depend on the `NotificationSender` interface.
3. Modify the High-Level Module to Depend on the Abstraction, refactor the `NotificationService` to depend on the `NotificationSender`

```mermaid
flowchart LR
   NotificationService -- Depend on --> NotificationSender <-- Depend on --> EmailService 
```

```csharp
// 1. Abstraction for sending notifications
public interface NotificationSender
{
    void send(string message);
}


//2. EmailService that implements NotificationSender
public class EmailService : NotificationSender
{
    public void send(string message)
    {
        System.Console.WriteLine("Sending email: " + message);
    }
}

//2. SMSService that implements NotificationSender
public class SMSService : NotificationSender
{
    public void send(string message)
    {
        System.Console.WriteLine("Sending SMS: " + message);
    }
}

//3. NotificationService depends on NotificationSender interface
public class NotificationService
{
    private NotificationSender notificationSender;

    // Constructor takes a NotificationSender, allowing for flexibility
    public NotificationService(NotificationSender notificationSender)
    {
        this.notificationSender = notificationSender;
    }

    public void send(string message)
    {
        notificationSender.send(message);
    }
}
```

### Real-World Examples
1. **Dependency Injection (DI)**: Frameworks like Spring (Java), Dagger (Java/Kotlin), and ASP.NET Core (C#) implement DIP by allowing high-level modules to depend on abstractions. They manage the creation and injection of low-level dependencies, promoting loose coupling and easier testing. One of the most popular ways to apply DIP. It involves providing the dependencies (i.e., low-level modules) to a class from the outside rather than having the class create its own dependencies. This allows the high-level module to depend on abstractions, making the system more flexible.
```csharp
using System;

// Web Application Service


// Abstraction for data repository
public interface DataRepository
{
    void save(string data);
}

// Low-level implementation for MySQL
public class MySQLRepository : DataRepository
{
    public void save(string data)
    {
        Console.WriteLine("Saving data to MySQL: " + data);
    }
}

// Service that uses Dependency Injection
public class DataService
{
    private DataRepository repository;

    public DataService(DataRepository repository)
    {
        this.repository = repository;
    }

    public void process(string data)
    {
        // Business logic
        repository.save(data);
    }
}

// Example usage
public class Program
{
    public static void Main(string[] args)
    {
        DataRepository mysqlRepo = new MySQLRepository();
        DataService dataService = new DataService(mysqlRepo);
        dataService.process("Sample Data");
    }
}
```
the `DataService` is decoupled from the specific implementation of `DataRepository`. It can easily switch to a different database without modifying the existing code.

2. **Service Locator Pattern**: This pattern provides a centralized registry where high-level modules can request instances of low-level services. The service locator abstracts the creation and management of these services, allowing high-level modules to depend on abstractions rather than concrete implementations.
```csharp
using System;
using System.Collections.Generic;

// Abstraction for Payment Service
interface IPaymentService {
    void ProcessPayment(double amount);
}

// Implementation for PayPal
class PayPalService : IPaymentService {
    public void ProcessPayment(double amount) {
        Console.WriteLine($"Processing payment via PayPal: ${amount}");
    }
}

// Service Locator to manage services
class ServiceLocator {
    private static Dictionary<string, IPaymentService> services = new Dictionary<string, IPaymentService>();

    public static void RegisterService(string serviceName, IPaymentService service) {
        services[serviceName] = service;
    }

    public static IPaymentService GetPaymentService(string serviceName) {
        return services.ContainsKey(serviceName) ? services[serviceName] : null;
    }
}

// High-level class that depends on abstractions
class Solution {
    private ServiceLocator locator;

    public Solution(ServiceLocator locator) {
        this.locator = locator;
    }

    public void ProcessPayment(string serviceName, double amount) {
        var paymentService = ServiceLocator.GetPaymentService(serviceName);
        paymentService?.ProcessPayment(amount);
    }

    public static void Main(string[] args) {
        // Registering PayPalService
        ServiceLocator.RegisterService("PayPal", new PayPalService());

        // Creating Solution instance
        var solution = new Solution(new ServiceLocator());

        // Using Solution class to process payments
        solution.ProcessPayment("PayPal", 100.0);
    }
}
```
3. **Inversion of Control (IoC) Frameworks**
IoC frameworks such as Spring in Java, or .NET Core in C#, use dependency injection to apply DIP at scale. These frameworks manage the lifecycle and dependencies of objects for you, allowing for loosely coupled architecture.
```csharp
public class OrderService
{
    private readonly IPaymentService paymentService;

    // Constructor with dependency injection
    public OrderService(IPaymentService paymentService)
    {
        this.paymentService = paymentService;
    }

    public void placeOrder(double amount)
    {
        paymentService.processPayment(amount);
    }
}


// Register services (IoC) Container setup
builder.Services.AddScoped<IPaymentService, PaymentService>();
builder.Services.AddScoped<IOrderService, OrderService>();
```

### How DIP Relates to Other SOLID Principles
 - **Single Responsibility Principle (SRP):** DIP supports SRP by decoupling classes and minimizing dependencies. This ensures that each class focuses on one responsibility, while the details are abstracted away.
 - **Open/Closed Principle (OCP):** DIP helps achieve OCP by making modules open for extension but closed for modification. High-level modules rely on abstractions, allowing for easy extension by introducing new implementations.
 - **Liskov Substitution Principle (LSP):** DIP complements LSP by ensuring that subclasses or implementations adhere to a common interface, making substitutions safe and seamless.
 - **Interface Segregation Principle (ISP):** DIP encourages designing smaller, specific interfaces. High-level modules can then depend on minimal abstractions, avoiding fat interfaces and ensuring that each module only uses what it needs.