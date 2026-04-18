# Dotnet Zero To Hero

**Dotnet Zero To Hero** is a learning path that takes you from the very basics of **C#** to building real-world applications with **.NET**.  
The goal is to give you strong fundamentals, then layer on real examples, so you can move from “I kind of know C#” to “I can design and reason about real systems.”

---

## Table of Contents

1. [OOP in C#](#oop-in-c)
   - [Lesson 1: Why Does OOP Exist? (The Intuition)](#lesson-1-why-does-oop-exist-the-intuition)
   - [Lesson 2: Encapsulation](#lesson-2-Encapsulation)
   - [Lesson 3: Abstraction](#lesson-3-Abstraction)
   - [Lesson 4: Inheritance](#lesson-4-Inheritance)
   - [Lesson 5: Polymorphism](#lesson-5-Polymorphism)
2. [LINQ in C#](#linq-in-c)
   - [Lesson 1: Why Does LINQ Exist? (The Intuition)](#lesson-1-why-does-linq-exist-the-intuition)
   - [Lesson 2: Where and Select — The Bread and Butter](#lesson-2-where-and-select--the-bread-and-butter)

---

## OOP in C\#

OOP (Object-Oriented Programming) is one of the most important ideas in C#.  
It’s all about **organizing code** so it stays understandable, reusable, and safe as your project grows.

### Lesson 1: Why Does OOP Exist? (The Intuition)

#### The Toy Box Problem

Imagine you have a **toy box**. Inside, you have:

- cars  
- dolls  
- blocks  
- crayons  

Every morning, you dump everything on the floor. Your cars are mixed with crayons. Your dolls are buried under blocks. When someone says *“find the red car”*, you spend 20 minutes digging through chaos.

Now imagine you have **separate labeled bins**:

- one for cars  
- one for dolls  
- one for blocks  
- one for crayons  

Each bin has a lid. You know exactly where things are. You can grab the **car bin** without touching the crayons.

That’s the **intuition** behind OOP:  
instead of one giant messy pile of data and functions, you **group related things together** into neat, labeled boxes.

---

### Now Let's Grow Up — The Real Problem in Code

Before OOP, a lot of software was written in a style called **procedural programming**.

- Everything was **functions** and **global data**.
- Data lived in one place; functions that operated on it lived somewhere else.
- As systems grew, it became a **maintenance nightmare**.

Imagine a small banking application written in a very procedural style:

```csharp
// Procedural style — the nightmare

static double[] accountBalances = new double[10000];
static string[] accountNames    = new string[10000];
static string[] accountTypes    = new string[10000];
static bool[]   accountFrozen   = new bool[10000];

static void Deposit(int accountIndex, double amount)
{
    if (!accountFrozen[accountIndex])
    {
        accountBalances[accountIndex] += amount;
    }
}

static void Withdraw(int accountIndex, double amount)
{
    if (!accountFrozen[accountIndex])
    {
        accountBalances[accountIndex] -= amount;
    }
}

static void Transfer(int from, int to, double amount)
{
    Withdraw(from, amount);
    Deposit(to, amount);
}
```

This **works**. Money moves. But as the system grows, it becomes a **ticking time bomb**.

#### Why This Style Hurts

| Problem                               | What Happens                                                                 |
|---------------------------------------|------------------------------------------------------------------------------|
| Any function can change any data      | A junior dev writes `accountBalances[5] = -999999;` somewhere. No one notices for weeks. |
| Data and behavior are separated       | The arrays live in one place, the functions in another. You must **mentally track** which functions touch which arrays. |
| Adding a new account type is surgery  | Want a `SavingsAccount` with interest? You start sprinkling `if/else` into every function. |
| 10 developers = 10 disasters          | Everyone is stepping on the same global data, sometimes in conflicting ways. |
| You can’t reuse anything cleanly      | The `Deposit` logic is glued to **these specific arrays**. You can’t easily reuse it in another project. |

In the real world (1970s–80s), large codebases like this became **unmanageable**.  
Millions of lines of code, all tangled together, made it almost impossible to safely change or extend behavior.

---

### The Core Insight That Created OOP

People like **Alan Kay** (inspired by the Simula language) asked a brilliant question:

> *“What if, instead of separating data and functions, we bundled them together into self‑contained units that model real-world things?”*

So instead of:

- a bunch of parallel arrays, and
- loose functions that operate on them,

we say:

> **An `Account` is a thing that:**
>
> - **KNOWS** its balance, its owner, whether it’s frozen (data)  
> - **CAN** deposit, withdraw, transfer (behavior)  
> - **PROTECTS** its own balance from outside tampering (rules)

That “thing” is an **object**.  
The **blueprint** for creating those objects is a **class**.

Here’s how that might look in C#:

```csharp
public class BankAccount
{
    private double _balance;
    private string _owner;
    private bool _frozen;

    public BankAccount(string owner, double initialBalance)
    {
        _owner = owner;
        _balance = initialBalance;
        _frozen = false;
    }

    public void Deposit(double amount)
    {
        if (_frozen)
            throw new InvalidOperationException("Account is frozen.");

        if (amount <= 0)
            throw new ArgumentException("Amount must be positive.", nameof(amount));

        _balance += amount;
    }

    public void Withdraw(double amount)
    {
        if (_frozen)
            throw new InvalidOperationException("Account is frozen.");

        if (amount <= 0)
            throw new ArgumentException("Amount must be positive.", nameof(amount));

        if (amount > _balance)
            throw new InvalidOperationException("Insufficient funds.");

        _balance -= amount;
    }

    public double GetBalance()
    {
        return _balance;
    }

    public void Freeze()  => _frozen = true;
    public void Unfreeze() => _frozen = false;
}
```

Key differences from the procedural version:

- Nobody can just do `_balance = -999999;` from outside.
- The **data** (`_balance`, `_owner`, `_frozen`) and the **rules** (`Deposit`, `Withdraw`, `Freeze`, `Unfreeze`) live **together**.
- The object **protects its own state**.

This is the fundamental shift of OOP:  
**bundle data + behavior + rules into one unit, and protect the unit from random external changes.**

---

### The Four Pillars of OOP

OOP gives you four core tools to manage complexity:

| Pillar         | One‑Line Intuition                                                                   |
|----------------|--------------------------------------------------------------------------------------|
| **Encapsulation** | “Lock your data in a safe. Give out only the keys people actually need.”          |
| **Abstraction**   | “You drive a car without knowing how the engine works under the hood.”            |
| **Inheritance**   | “A puppy is a dog. A dog is an animal. Puppies automatically know how to breathe.”|
| **Polymorphism**  | “You say *‘speak’* to every animal. The dog barks, the cat meows, the snake hisses. Same command, different behavior.” |

These are **not** academic decorations.  
Each pillar solves a **real, painful problem** that appears as software gets larger:

- hiding unnecessary details,  
- preventing invalid states,  
- sharing behavior cleanly,  
- and letting different types respond in their own way to the same message.



# Lesson 2: Encapsulation

Encapsulation is about **bundling data and behavior together** and **controlling who can access what**.  
You can think of it as putting your data in a **vault** and giving the outside world only a few **safe buttons** to press.

---

## The Real-World Analogy: ATM Machine

Think about an **ATM machine**.

You walk up to it, insert your card, type your PIN, and press **“Withdraw ₹1000”**. The machine gives you cash.

You have **no idea** what’s happening inside:

- which database it’s talking to  
- what encryption it uses  
- how it validates your PIN  
- how it talks to your bank’s servers  

More importantly — you **cannot reach inside** and grab extra cash.

The ATM exposes only a **small set of safe buttons**. You interact through that interface; the internal machinery is protected.

That’s **encapsulation**:

> **Bundle data + behavior together, and control who can access what.**

It has two main parts:

1. **Bundling** — the data and the methods that operate on that data live in the same unit (the **class**).  
2. **Access control** — you explicitly decide what’s `public` (the **buttons**) and what’s `private` (the **vault**).

---

## Encapsulation in C\# Code

Let’s model an `Employee` in C#:

```csharp
public class Employee
{
    // PRIVATE — nobody outside this class can touch these directly
    private string _name;
    private decimal _salary;
    private string _department;

    public Employee(string name, decimal salary, string department)
    {
        _name = name;
        _department = department;

        // Use the method so validation always happens
        SetSalary(salary);
    }

    // PUBLIC — controlled access points (read-only view)
    public string Name       => _name;
    public string Department => _department;
    public decimal Salary    => _salary;

    public void SetSalary(decimal salary)
    {
        if (salary < 0)
            throw new ArgumentOutOfRangeException(nameof(salary), "Salary cannot be negative.");

        if (salary > 10_000_000m)
            throw new ArgumentOutOfRangeException(nameof(salary), "Salary exceeds maximum allowed.");

        _salary = salary;
    }

    public void Promote(decimal raisePercent)
    {
        if (raisePercent < 0 || raisePercent > 50)
            throw new ArgumentOutOfRangeException(nameof(raisePercent), "Raise must be between 0% and 50%.");

        _salary *= 1 + (raisePercent / 100m);
    }
}
```

What’s happening here?

- The **fields** (`_name`, `_salary`, `_department`) are `private`. Outside code can’t randomly set them.  
- The **methods** `SetSalary` and `Promote` enforce **rules**:
  - no negative salary  
  - no absurdly huge salary  
  - no silly raises like −200%

The class is like an ATM: you don’t hand cash directly to `_salary`; you press approved buttons.

---

## TypeScript Comparison (Optional)

TypeScript also has access modifiers, but enforcement is different:

```ts
class Employee {
  // TypeScript uses access modifiers just like C#
  private name: string;
  private salary: number;
  private department: string;

  constructor(name: string, salary: number, department: string) {
    this.name = name;
    this.salary = salary; // TS doesn't enforce at runtime by default
    this.department = department;
  }

  getName(): string {
    return this.name;
  }

  getSalary(): number {
    return this.salary;
  }

  setSalary(salary: number): void {
    if (salary < 0) throw new Error("Salary cannot be negative");
    this.salary = salary;
  }
}
```

**Key difference:**

- C#’s `private` is enforced by the runtime and the compiler.  
- TypeScript’s `private` is only enforced by the **TypeScript compiler**; at runtime it’s just JavaScript, and anyone can still access the underlying property if they really want.

This matters in production because strong encapsulation in C# helps **prevent whole classes of bugs**.

---

## The Common Mistake: “Getters and Setters Everywhere = Encapsulation”

This is the #1 misconception.

```csharp
// BAD — This is NOT real encapsulation. This is a lie.
public class User
{
    private string _password;

    public string GetPassword()
    {
        return _password;  // You just exposed the password to the world!
    }

    public void SetPassword(string password)
    {
        _password = password;  // No validation, no hashing — pointless wrapper.
    }
}
```

You made the field `private`, then gave **unrestricted** get and set access.  
That’s like locking your front door but taping the key to the frame.

You’ve gained almost **nothing**.

Real encapsulation looks like this:

```csharp
// GOOD — Actual protection and controlled behavior
public class User
{
    private string _passwordHash;

    public void SetPassword(string rawPassword)
    {
        if (string.IsNullOrWhiteSpace(rawPassword) || rawPassword.Length < 8)
            throw new ArgumentException("Password must be at least 8 characters.", nameof(rawPassword));

        // Pretend HashPassword() uses a real hashing algorithm like BCrypt/Argon2
        _passwordHash = HashPassword(rawPassword);
    }

    public bool CheckPassword(string rawPassword)
    {
        return VerifyPassword(rawPassword, _passwordHash);
    }

    // NOTICE: there is NO GetPassword() — you can NEVER read the password back.
    // That's intentional. That's encapsulation doing its job.

    private string HashPassword(string rawPassword)
    {
        // placeholder for a real hashing call
        return Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(rawPassword));
    }

    private bool VerifyPassword(string rawPassword, string hash)
    {
        // placeholder for a real verify call
        return HashPassword(rawPassword) == hash;
    }
}
```

Key ideas:

- The object decides what operations are meaningful.  
- Not every field needs a getter.  
- Not every field needs a setter.  
- You expose **behavior**, not raw **data**.

---

## Real Production Use-Case: Connection Pool

Here’s a simplified pattern you’ll see in real systems: a **connection pool**.

```csharp
using System.Collections.Generic;
using System.Data.Common;

public class ConnectionPool
{
    private readonly List<DbConnection> _available = new();
    private readonly List<DbConnection> _inUse     = new();
    private readonly int _maxSize;

    public ConnectionPool(int maxSize)
    {
        if (maxSize <= 0)
            throw new ArgumentOutOfRangeException(nameof(maxSize));

        _maxSize = maxSize;
    }

    public DbConnection Acquire()
    {
        lock (_available)
        {
            if (_available.Count == 0 && _inUse.Count >= _maxSize)
            {
                throw new InvalidOperationException("Connection pool exhausted.");
            }

            DbConnection conn;

            if (_available.Count == 0)
            {
                conn = CreateNewConnection();
            }
            else
            {
                var lastIndex = _available.Count - 1;
                conn = _available[lastIndex];
                _available.RemoveAt(lastIndex);
            }

            _inUse.Add(conn);
            return conn;
        }
    }

    public void Release(DbConnection connection)
    {
        if (connection is null) return;

        lock (_available)
        {
            _inUse.Remove(connection);
            _available.Add(connection);
        }
    }

    private DbConnection CreateNewConnection()
    {
        // Only THIS class knows how to create connections
        // e.g., SqlConnection / NpgsqlConnection / etc.
        throw new NotImplementedException("Create and open a real DbConnection here.");
    }
}
```

Why encapsulation matters here:

1. If anyone could directly access `_available` or `_inUse`, they could grab a connection without it being tracked, leading to **connection leaks** that crash production at 3 AM.  
2. The `lock` and thread-safety work **only because** all access goes through `Acquire()` and `Release()`. If the lists were public, internal synchronization wouldn’t help.  
3. The internal implementation can change (use different collections, add health checks, metrics, etc.) and **no calling code needs to change** as long as the public API stays the same.

---

## Connecting Back to the Intuition

Encapsulation is like your **toy bins** or a **vending machine**:

- **The bin** = the **class** (data + behavior bundled).  
- **The lid** = `private` (you can’t just reach in).  
- **The label/handle** = `public` methods (controlled way to interact).

A well-encapsulated class is a vending machine:

- You put money in,  
- press a button,  
- and get a drink.

You never touch:

- the internal inventory list,  
- the refrigeration unit,  
- or the coin counter directly.

If the company replaces the entire cooling system, you don’t care. Your **interaction (the public API)** is unchanged.

---

## Test Yourself — Encapsulation

Think through these questions:

1. **Question 1**  
   You have a `ShoppingCart` class with a private `List&lt;Item&gt; _items` field. You write:

   ```csharp
   public List<Item> GetItems()
   {
       return _items;
   }
   ```

   Is this good encapsulation? Why or why not?

2. **Question 2**  
   Your teammate says:  
   *“Making everything public is faster to develop. We can add private later when we need it.”*  
   What’s wrong with this reasoning?

3. **Question 3**  
   In the `BankAccount` example, why did we make `_balance` private but still provide `GetBalance()`?  
   What could go wrong if we also added a `SetBalance(double amount)` method and let anyone call it?

---

## Key Takeaways

1. **Never return mutable references** to internal state (prefer defensive copies or read-only views).  
2. **Expose behavior, not data** – for example, `Deposit(amount)` instead of `SetBalance(newBalance)`.  
3. **Start private, open up only when necessary** – you can always relax a restriction later, but you can’t easily take back a `public` API once other code depends on it.


# Lesson 3: Abstraction

Now that you’ve seen **encapsulation**, let’s talk about its sibling that everyone confuses it with: **abstraction**.

---

## Encapsulation vs Abstraction

People often say:  
> “Encapsulation hides data. Abstraction also hides… stuff. So they’re the same?”

No.

Memorize this table:

| Concept          | What It Hides                    | Why It Hides It                           |
|-----------------|-----------------------------------|-------------------------------------------|
| **Encapsulation** | The **data** (internal state)      | To **protect** it from invalid modification |
| **Abstraction**   | The **complexity** (how it works)  | To **simplify** how you use it              |

- **Encapsulation** says:  
  > “You can’t touch my `balance` field directly.”
- **Abstraction** says:  
  > “You don’t need to know *how* `Transfer()` works internally — just call it.”

They’re related:

- Encapsulation gives you the **tools** (private fields, access modifiers).
- Abstraction is the **design** (what you show vs what you hide).

Encapsulation **enables** abstraction, but they solve different problems.

---

## Real-World Analogy: Driving a Car

You drive a car almost every day. When you want to go faster, you press the **gas pedal**. That’s it.

Do you know what happens when you press it?

- The throttle opens  
- Fuel injectors spray the right amount of fuel  
- Spark plugs fire at precise timings  
- Pistons compress fuel–air mixture  
- Combustion pushes the crankshaft  
- Transmission transfers torque to the wheels  
- The ECU adjusts dozens of parameters in real time  

You know **none** of this. And you **don’t need to**.

The car gives you:

- a steering wheel  
- a few pedals  
- a gear selector  

That’s the **abstraction**: a **simple interface** hiding overwhelming mechanical and electronic complexity.

**Critical point:**  
If Toyota replaces the gasoline engine with an electric motor:

- The internal implementation changes completely.  
- Your pedals and steering wheel **still work the same way**.

The abstraction (how *you* use the car) stays stable while the implementation evolves. That’s the power.

---

## Abstraction in C#: Payment Processing

In C#, abstraction is often expressed through **interfaces** and **abstract base classes**.

### Step 1: Define the Abstraction

```csharp
// The ABSTRACTION — what does a payment processor DO (not HOW)?
public interface IPaymentProcessor
{
    PaymentResult Charge(Money amount, PaymentMethod method);
    PaymentResult Refund(string transactionId, Money amount);
    PaymentStatus CheckStatus(string transactionId);
}
```

This interface says:

- Any payment processor must be able to **charge**, **refund**, and **check status**.

It says **nothing** about:

- Stripe vs PayPal vs Razorpay  
- HTTP vs gRPC  
- How retries, logging, or authentication work  

---

### Step 2: One Implementation (Stripe)

```csharp
public class StripePaymentProcessor : IPaymentProcessor
{
    private readonly StripeClient _stripeClient;

    public StripePaymentProcessor(string apiKey)
    {
        _stripeClient = new StripeClient(apiKey);
    }

    public PaymentResult Charge(Money amount, PaymentMethod method)
    {
        // Stripe-specific API calls, tokens, etc.
        var charge = _stripeClient.CreateCharge(
            amountInCents: amount.ToCents(),
            currency: amount.Currency,
            sourceToken: method.Token);

        return new PaymentResult(charge.Id, charge.Status);
    }

    public PaymentResult Refund(string transactionId, Money amount)
    {
        // Stripe-specific refund logic
        throw new NotImplementedException();
    }

    public PaymentStatus CheckStatus(string transactionId)
    {
        // Stripe-specific status logic
        throw new NotImplementedException();
    }
}
```

---

### Step 3: Another Implementation (PayPal)

```csharp
public class PayPalPaymentProcessor : IPaymentProcessor
{
    public PaymentResult Charge(Money amount, PaymentMethod method)
    {
        // Completely different API, auth, and flow
        // But the CALLER DOESN'T CARE — same interface
        throw new NotImplementedException();
    }

    public PaymentResult Refund(string transactionId, Money amount)
    {
        throw new NotImplementedException();
    }

    public PaymentStatus CheckStatus(string transactionId)
    {
        throw new NotImplementedException();
    }
}
```

---

### Step 4: Code Against the Abstraction

```csharp
public class CheckoutService
{
    private readonly IPaymentProcessor _processor;  // Could be Stripe, PayPal, anything

    public CheckoutService(IPaymentProcessor processor)
    {
        _processor = processor;
    }

    public Order Checkout(Cart cart, PaymentMethod method)
    {
        Money total = cart.CalculateTotal();
        PaymentResult result = _processor.Charge(total, method);  // Don't know. Don't care.

        if (result.IsSuccessful)
        {
            return Order.Create(cart, result.TransactionId);
        }

        throw new PaymentFailedException(result.ErrorMessage);
    }
}
```

`CheckoutService`:

- **Knows** only the **abstraction**: `IPaymentProcessor`.  
- **Doesn’t care** whether it’s Stripe, PayPal, or something else.

Next year:

- You add `CryptoPaymentProcessor : IPaymentProcessor`.  
- You wire it into DI.  
- **`CheckoutService` doesn’t change at all.**

That is abstraction working correctly.

---

## TypeScript Comparison (Optional)

```ts
interface PaymentProcessor {
  charge(amount: Money, method: PaymentMethod): Promise<PaymentResult>;
  refund(transactionId: string, amount: Money): Promise<PaymentResult>;
  checkStatus(transactionId: string): Promise<PaymentStatus>;
}

class CheckoutService {
  constructor(private processor: PaymentProcessor) {}

  async checkout(cart: Cart, method: PaymentMethod): Promise<Order> {
    const total = cart.calculateTotal();
    const result = await this.processor.charge(total, method);

    if (result.isSuccessful) {
      return Order.create(cart, result.transactionId);
    }

    throw new PaymentFailedException(result.errorMessage);
  }
}
```

Same idea: code uses the **interface**, not the concrete class.

---

## Leaky Abstractions (Common Mistake)

A **leaky abstraction** forces the caller to know internal details — defeating the whole purpose.

```csharp
// BAD — Leaky abstraction
public interface IFileStorage
{
    void Save(string filePath, byte[] data);
    byte[] Load(string filePath);

    string GetAwsS3BucketName();     // LEAK: AWS-specific detail
    void SetRegion(string awsRegion);// LEAK: AWS-specific detail
}
```

If you switch from S3 to Azure Blob Storage:

- Every caller that uses `GetAwsS3BucketName()` breaks.  
- You must touch many files just to change implementation.

Correct version:

```csharp
// GOOD — Clean abstraction
public interface IFileStorage
{
    void Save(string key, byte[] data);
    byte[] Load(string key);
    void Delete(string key);
    bool Exists(string key);
}
```

Now this could be:

- S3  
- Azure Blob  
- Google Cloud Storage  
- Local disk  

Callers **never know** which one — and don’t need to.

---

## Real Production Example: IDbConnection

In .NET, you’ve likely used `DbConnection` / `IDbConnection`:

```csharp
using var conn = dataSource.CreateConnection();       // Could be SQL Server, Postgres, etc.
await conn.OpenAsync();

using var cmd = conn.CreateCommand();
cmd.CommandText = "SELECT * FROM Users WHERE Id = @id";

var param = cmd.CreateParameter();
param.ParameterName = "@id";
param.Value = userId;
cmd.Parameters.Add(param);

using var reader = await cmd.ExecuteReaderAsync();
```

This same code works with:

- SQL Server  
- PostgreSQL  
- MySQL  
- SQLite  
- Oracle  

…as long as you plug in the correct provider.

`DbConnection` / `IDbConnection` is an **abstraction**.  
Each database driver is a different **implementation**.  
You write code **once** against the abstraction.

---

## How Encapsulation and Abstraction Work Together

Consider `StripePaymentProcessor`:

- **Abstraction** (`IPaymentProcessor`) says:  
  > “You only see `Charge`, `Refund`, `CheckStatus`.”
- **Encapsulation** says:  
  > “The `_stripeClient`, API key, retry logic, auth headers — all `private`. You can’t touch them directly.”

So:

- **Abstraction** decides **what you show**.  
- **Encapsulation** decides **how you protect what you hide**.

They are two sides of the same coin.

---

## Test Yourself — Abstraction

1. **Logger example**

   ```csharp
   public interface ILogger
   {
       void Log(string message);
       void SetLogFile(FileInfo file);
       void FlushBufferToFile();
   }
   ```

   - Is this a good abstraction?  
   - What happens if you later add a `CloudLogger` that sends logs to a remote service instead of a file?

2. **Processor parameter choice**

   ```csharp
   // Option A
   public void ProcessPayment(StripePaymentProcessor processor) { ... }

   // Option B
   public void ProcessPayment(IPaymentProcessor processor) { ... }
   ```

   - Why does this choice matter in a codebase with **50 developers and 200 microservices**?

3. **Abstraction vs Encapsulation**

   - Can you have **encapsulation without abstraction**?  
   - Can you have **abstraction without strong encapsulation**?  
   - Think of a short example of each.

---

# Lesson 4: Inheritance

## Real-World Analogy

You’re a **human**. You didn’t learn from scratch how to:

- breathe,  
- pump blood,  
- digest food.

You **inherited** those capabilities from being a **mammal**, which inherited from **animals**, which inherited from **living organisms**.

Chain:

- `LivingOrganism` → `Animal` → `Mammal` → `Human`

Each level adds something:

- **LivingOrganism**: can grow, can reproduce  
- **Animal**: can move, can eat  
- **Mammal**: warm-blooded, has hair, produces milk  
- **Human**: can speak language, write code, regret not writing tests  

You didn’t have to “redefine” breathing.  
You got it for free from **higher levels** and added your own capabilities.

That’s **inheritance**:

> A child class automatically gets state + behavior from its parent, and can add or modify behavior on top.

---

## Inheritance in C#: Employee Example

### Base Class (Parent)

```csharp
public class Employee
{
    private readonly string _name;
    private readonly string _employeeId;
    private readonly decimal _baseSalary;

    public Employee(string name, string employeeId, decimal baseSalary)
    {
        _name = name;
        _employeeId = employeeId;
        _baseSalary = baseSalary;
    }

    public virtual decimal CalculatePay()
    {
        return _baseSalary;   // Default pay logic
    }

    public string Name        => _name;
    public string EmployeeId  => _employeeId;
    protected decimal BaseSalary => _baseSalary;
}
```

### Child Class: Salaried Employee

```csharp
public class SalariedEmployee : Employee
{
    private readonly decimal _annualBonus;

    public SalariedEmployee(string name, string id, decimal baseSalary, decimal annualBonus)
        : base(name, id, baseSalary)  // Call parent constructor
    {
        _annualBonus = annualBonus;
    }

    public override decimal CalculatePay()
    {
        // Monthly pay: base + 1/12th of bonus
        return BaseSalary + (_annualBonus / 12m);
    }
}
```

### Another Child: Hourly Employee

```csharp
public class HourlyEmployee : Employee
{
    private decimal _hoursWorked;
    private readonly decimal _hourlyRate;

    public HourlyEmployee(string name, string id, decimal hourlyRate)
        : base(name, id, baseSalary: 0m)  // No fixed base salary
    {
        _hourlyRate = hourlyRate;
    }

    public void LogHours(decimal hours)
    {
        _hoursWorked += hours;
    }

    public override decimal CalculatePay()
    {
        decimal pay = _hoursWorked * _hourlyRate;

        if (_hoursWorked > 160) // Overtime after 160 hours/month
        {
            decimal overtimeHours = _hoursWorked - 160;
            pay += overtimeHours * _hourlyRate * 0.5m; // 1.5x for overtime
        }

        return pay;
    }
}
```

`SalariedEmployee` and `HourlyEmployee`:

- **Inherit** fields and behavior (e.g., `Name`, `EmployeeId`) from `Employee`.  
- Only implement what’s **different**: pay calculation.

---

## TypeScript Comparison (Optional)

```ts
class Employee {
  constructor(
    protected name: string,
    protected employeeId: string,
    protected baseSalary: number
  ) {}

  calculatePay(): number {
    return this.baseSalary;
  }
}

class SalariedEmployee extends Employee {
  constructor(
    name: string,
    id: string,
    baseSalary: number,
    private annualBonus: number
  ) {
    super(name, id, baseSalary);
  }

  calculatePay(): number {
    return this.baseSalary + this.annualBonus / 12;
  }
}
```

Same idea: child extends parent, overrides behavior.

---

## The Big Trap: Inheritance for Code Reuse

**Rule:**  
> Inheritance models an **“is-a”** relationship — not “I want to reuse code.”

Good:

- A `SalariedEmployee` **is an** `Employee`.  
- A `Dog` **is an** `Animal`.

Bad (but very common):

```csharp
// DISASTER — Classic misuse
public class Stack : List<object>
{
    public void Push(object item)
    {
        Add(item);  // Reusing List's Add — seems convenient…
    }

    public object Pop()
    {
        return this[Count - 1];
    }
}
```

This compiles. But:

```csharp
var stack = new Stack();
stack.Push("first");
stack.Push("second");

// But Stack is a List<object>, so it has ALL List methods:
stack.Insert(0, "injected"); // Stacks shouldn't insert in the middle
stack.RemoveAt(0);           // Remove by index? Not a stack behavior.
stack.Clear();               // Wipes everything arbitrarily
```

A **stack is not a list**.  
By inheriting from `List<object>`, you expose dozens of operations that **break the stack’s rules**.

### The Fix: Composition Over Inheritance

```csharp
// CORRECT — Stack HAS a list; it is not a list
public class Stack<T>
{
    private readonly List<T> _elements = new();

    public void Push(T item)
    {
        _elements.Add(item);
    }

    public T Pop()
    {
        if (_elements.Count == 0)
            throw new InvalidOperationException("Stack is empty.");

        int lastIndex = _elements.Count - 1;
        T item = _elements[lastIndex];
        _elements.RemoveAt(lastIndex);
        return item;
    }

    public T Peek()
    {
        if (_elements.Count == 0)
            throw new InvalidOperationException("Stack is empty.");

        return _elements[_elements.Count - 1];
    }

    public bool IsEmpty() => _elements.Count == 0;
}
```

Key principle (from the “Gang of Four” book):

> **“Favor composition over inheritance.”**

- Use **inheritance** when the relationship truly is **“is-a”**.  
- Use **composition** when the relationship is **“has-a”** or **“uses-a”**.

| Relationship | Example                            | Use               |
|-------------|-------------------------------------|-------------------|
| is-a        | `Dog` is an `Animal`                | Inheritance       |
| has-a       | `Car` has an `Engine`               | Composition       |
| uses-a      | `CheckoutService` uses `IPaymentProcessor` | Composition / DI   |

---

## Real Production Example: Web Frameworks

Framework base class:

```csharp
public abstract class HttpHandler
{
    public void Handle(HttpContext context)
    {
        switch (context.Request.Method)
        {
            case "GET":  HandleGet(context);  break;
            case "POST": HandlePost(context); break;
            default:
                context.Response.StatusCode = 405;
                break;
        }
    }

    protected virtual void HandleGet(HttpContext context)
    {
        context.Response.StatusCode = 405;
    }

    protected virtual void HandlePost(HttpContext context)
    {
        context.Response.StatusCode = 405;
    }
}
```

Your handler:

```csharp
public class UserHandler : HttpHandler
{
    protected override void HandleGet(HttpContext context)
    {
        string id = context.Request.Query["id"];
        var user = _userRepository.FindById(id);
        context.Response.WriteAsJson(user);
    }

    protected override void HandlePost(HttpContext context)
    {
        var user = context.Request.ReadBodyAs<User>();
        _userRepository.Save(user);
        context.Response.StatusCode = 201;
    }

    private readonly IUserRepository _userRepository;

    public UserHandler(IUserRepository userRepository)
    {
        _userRepository = userRepository;
    }
}
```

- **One** level of inheritance.  
- Framework gives routing + dispatching.  
- You override what you need.

If someone builds a chain like:

`HttpHandler → AuthenticatedHandler → JsonHandler → CrudHandler → UserHandler`

- That’s 4–5 levels.  
- A change in a base class can break children in non-obvious ways.  
- This is the **fragile base class problem**.

In practice, modern code keeps inheritance **shallow** (1–2 levels max), and uses **interfaces + composition** for most reuse.

---

## Connecting Encapsulation + Abstraction + Inheritance

In the `Employee` example:

- **Encapsulation**: fields (`_name`, `_employeeId`, `_baseSalary`) are private; children use properties, not direct fields.  
- **Abstraction**: callers care only about `CalculatePay()`, not *how* pay is computed.  
- **Inheritance**: `SalariedEmployee` and `HourlyEmployee` reuse common data/behavior and override only what’s different.

If any pillar is missing:

- Public fields? Child classes (or other code) can corrupt state.  
- No common method like `CalculatePay()`? Callers need big `if/else` chains.  
- No inheritance? You copy–paste the same properties into multiple classes.

---

## Test Yourself — Inheritance

1. Your teammate writes:

   ```csharp
   public class LoginButton : Rectangle
   {
       private string _label;

       public void OnClick()
       {
           // login logic
       }
   }
   ```

   - What’s wrong with this design?  
   - What would you use instead?

2. Why does C# only allow **single inheritance** for classes (one parent) but allow implementing **multiple interfaces**?  
   - What problem does this avoid?

3. In the `Employee` example, why might you prefer **private fields + protected properties** over making the field itself `protected`?  
   - What risks come with exposing `protected` fields directly?

---

# Lesson 5: Polymorphism

This is the most powerful pillar — it’s where everything clicks together.

---

## Real-World Analogy

You walk into a room with a **dog**, a **cat**, and a **parrot**. You say:

> “Speak!”

- The dog barks.  
- The cat meows.  
- The parrot says “Hello!”.

Same command: **“Speak!”**  
Different behavior for each animal.

You:

- Don’t shout **“bark”** only at dogs or **“meow”** only at cats.  
- Don’t need to know which animal you’re talking to — you can be blindfolded.

You just send **one message**; each animal knows how to respond.

That’s **polymorphism** (“many forms”).

---

## Why This Matters in Code

Without polymorphism:

```csharp
// WITHOUT polymorphism — the nightmare
public decimal CalculateTotalPayroll(List<object> employees)
{
    decimal total = 0;

    foreach (var emp in employees)
    {
        if (emp is SalariedEmployee salaried)
        {
            total += salaried.BaseSalary + salaried.AnnualBonus / 12m;
        }
        else if (emp is HourlyEmployee hourly)
        {
            total += hourly.HoursWorked * hourly.HourlyRate;
        }
        else if (emp is CommissionEmployee commission)
        {
            total += commission.BaseSalary + commission.SalesAmount * commission.CommissionRate;
        }
        else if (emp is InternEmployee intern)
        {
            total += intern.Stipend;
        }
        // Every new employee type = new else-if here
        // And in CalculateTax(), and in GeneratePayStub(), etc.
    }

    return total;
}
```

Every time you add a new employee type:

- You must find and edit **every** `if (emp is ...)` chain.  
- Miss one? You get subtle bugs in production.

With polymorphism:

```csharp
// WITH polymorphism — elegant, extensible
public decimal CalculateTotalPayroll(List<Employee> employees)
{
    decimal total = 0;

    foreach (var emp in employees)
    {
        total += emp.CalculatePay();  // One call. That's it.
    }

    return total;
}
```

Add `ContractorEmployee` next year?

- Implement `CalculatePay()` in that class.  
- `CalculateTotalPayroll` doesn’t change. It already works.

This is the **Open/Closed Principle**:

- **Open for extension** (new employee types).  
- **Closed for modification** (existing code doesn’t change).

---

## Two Forms of Polymorphism in C\#

### 1. Method Overriding (Runtime Polymorphism) — The Big One

```csharp
public abstract class Shape
{
    public abstract double CalculateArea();
    public abstract double CalculatePerimeter();

    public string Describe()
    {
        return $"{GetType().Name}: Area={CalculateArea():F2}, Perimeter={CalculatePerimeter():F2}";
    }
}

public class Circle : Shape
{
    private readonly double _radius;

    public Circle(double radius) => _radius = radius;

    public override double CalculateArea() => Math.PI * _radius * _radius;
    public override double CalculatePerimeter() => 2 * Math.PI * _radius;
}

public class Rectangle : Shape
{
    private readonly double _width;
    private readonly double _height;

    public Rectangle(double width, double height)
    {
        _width = width;
        _height = height;
    }

    public override double CalculateArea() => _width * _height;
    public override double CalculatePerimeter() => 2 * (_width + _height);
}

public class Triangle : Shape
{
    private readonly double _a, _b, _c;

    public Triangle(double a, double b, double c)
    {
        _a = a;
        _b = b;
        _c = c;
    }

    public override double CalculatePerimeter() => _a + _b + _c;

    public override double CalculateArea()
    {
        double s = CalculatePerimeter() / 2;
        return Math.Sqrt(s * (s - _a) * (s - _b) * (s - _c));
    }
}
```

Usage:

```csharp
var shapes = new List<Shape>
{
    new Circle(5),
    new Rectangle(4, 6),
    new Triangle(3, 4, 5)
};

foreach (var shape in shapes)
{
    Console.WriteLine(shape.Describe());
}

double totalArea = shapes.Sum(s => s.CalculateArea());
```

- Variable type: `Shape`.  
- Actual object: `Circle`, `Rectangle`, `Triangle`.  
- At **runtime**, .NET chooses the right `CalculateArea()` / `CalculatePerimeter()`.

The caller **doesn’t switch on type** — the object decides how to respond.

---

### 2. Method Overloading (Compile-Time Polymorphism)

Same method name, different parameters:

```csharp
public enum LogLevel { Info, Warning, Error }

public class Logger
{
    public void Log(string message)
    {
        Console.WriteLine($"[INFO] {message}");
    }

    public void Log(string message, LogLevel level)
    {
        Console.WriteLine($"[{level}] {message}");
    }

    public void Log(Exception ex)
    {
        Console.WriteLine($"[ERROR] {ex.GetType().Name}: {ex.Message}");
        Console.WriteLine(ex.StackTrace);
    }
}
```

Usage:

```csharp
var logger = new Logger();

logger.Log("Server started");                             // Log(string)
logger.Log("Disk space low", LogLevel.Warning);           // Log(string, LogLevel)
logger.Log(new NullReferenceException("Oops"));           // Log(Exception)
```

The **compiler** decides which method to call based on argument types.  
This is **compile-time** polymorphism.

---

## Interface Polymorphism — Most Common in Real Apps

You see this pattern everywhere in production code.

```csharp
public interface INotificationSender
{
    Task SendAsync(string recipient, string subject, string body);
}

public class EmailSender : INotificationSender
{
    private readonly SmtpClient _smtpClient;

    public EmailSender(SmtpClient smtpClient) => _smtpClient = smtpClient;

    public async Task SendAsync(string recipient, string subject, string body)
    {
        var message = new MailMessage("noreply@app.com", recipient, subject, body);
        await _smtpClient.SendMailAsync(message);
    }
}

public class SmsSender : INotificationSender
{
    private readonly TwilioClient _twilioClient;

    public SmsSender(TwilioClient twilioClient) => _twilioClient = twilioClient;

    public async Task SendAsync(string recipient, string subject, string body)
    {
        await _twilioClient.SendSmsAsync(recipient, $"{subject}: {body}");
    }
}

public class SlackSender : INotificationSender
{
    private readonly SlackClient _slackClient;

    public SlackSender(SlackClient slackClient) => _slackClient = slackClient;

    public async Task SendAsync(string recipient, string subject, string body)
    {
        await _slackClient.PostMessageAsync(recipient, $"*{subject}*\n{body}");
    }
}
```

Now the consumer:

```csharp
public class OrderService
{
    private readonly IEnumerable<INotificationSender> _senders;

    public OrderService(IEnumerable<INotificationSender> senders)
    {
        _senders = senders;
    }

    public async Task PlaceOrderAsync(Order order)
    {
        // ... process order ...

        foreach (var sender in _senders)
        {
            await sender.SendAsync(
                order.Customer.ContactInfo,
                "Order Confirmed",
                $"Your order #{order.Id} has been placed."
            );
        }
    }
}
```

To add **push notifications**:

- Implement `PushNotificationSender : INotificationSender`.  
- Register it in DI.  
- `OrderService` doesn’t change at all.

That’s polymorphism at work.

---

## Common Mistake: Checking Types Instead of Using Polymorphism

Bad smell:

```csharp
// BAD — "type checking" anti-pattern
public decimal CalculateShippingCost(Order order)
{
    if (order.ShippingMethod is StandardShipping standard)
        return standard.Weight * 5.0m;
    else if (order.ShippingMethod is ExpressShipping express)
        return express.Weight * 12.0m + 15.0m;
    else if (order.ShippingMethod is OvernightShipping overnight)
        return overnight.Weight * 20.0m + 30.0m;
    else
        throw new NotSupportedException("Unknown shipping method");
}
```

Every `if (x is SomeType)` on a hierarchy is a missed polymorphism opportunity.

Better design:

```csharp
public abstract class ShippingMethod
{
    public abstract decimal CalculateCost(decimal weight);
}

public class StandardShipping : ShippingMethod
{
    public override decimal CalculateCost(decimal weight) => weight * 5.0m;
}

public class ExpressShipping : ShippingMethod
{
    public override decimal CalculateCost(decimal weight) => weight * 12.0m + 15.0m;
}

public class OvernightShipping : ShippingMethod
{
    public override decimal CalculateCost(decimal weight) => weight * 20.0m + 30.0m;
}

public class Order
{
    public decimal Weight { get; init; }
    public ShippingMethod ShippingMethod { get; init; } = default!;
}
```

Now:

```csharp
public decimal CalculateShippingCost(Order order)
{
    return order.ShippingMethod.CalculateCost(order.Weight);  // One line. Done.
}
```

Add a new `FreeShippingForMembers` class: no changes here.

---

## Real Production Example: ASP.NET Core Middleware

ASP.NET Core pipeline uses interface polymorphism heavily:

```csharp
public interface IMiddleware
{
    Task InvokeAsync(HttpContext context, RequestDelegate next);
}

public class AuthenticationMiddleware : IMiddleware
{
    public async Task InvokeAsync(HttpContext context, RequestDelegate next)
    {
        if (!context.Request.Headers.ContainsKey("Authorization"))
        {
            context.Response.StatusCode = 401;
            return;
        }

        // Validate token...

        await next(context);
    }
}

public class LoggingMiddleware : IMiddleware
{
    public async Task InvokeAsync(HttpContext context, RequestDelegate next)
    {
        var stopwatch = Stopwatch.StartNew();
        await next(context);
        stopwatch.Stop();

        Console.WriteLine($"{context.Request.Path} took {stopwatch.ElapsedMilliseconds}ms");
    }
}
```

The pipeline:

- Doesn’t know what your middlewares are.  
- Just calls `InvokeAsync` on each registered `IMiddleware`.  
- You can add/remove middlewares without changing the core pipeline code.

That’s polymorphism running across **millions** of requests.

---

## All Four Pillars Together

```csharp
// ABSTRACTION — defines WHAT, not HOW
public interface IPaymentProcessor
{
    PaymentResult Charge(decimal amount);
}

// INHERITANCE — shared behavior
public abstract class BasePaymentProcessor : IPaymentProcessor
{
    // ENCAPSULATION — internal state protected
    private readonly List<PaymentResult> _transactionLog = new();

    public PaymentResult Charge(decimal amount)
    {
        if (amount <= 0)
            throw new ArgumentException("Amount must be positive.", nameof(amount));

        // POLYMORPHISM — each subclass implements its own logic
        var result = ProcessCharge(amount);
        _transactionLog.Add(result);
        return result;
    }

    protected abstract PaymentResult ProcessCharge(decimal amount);

    public IReadOnlyList<PaymentResult> GetTransactionHistory()
        => _transactionLog.AsReadOnly();
}

public class StripeProcessor : BasePaymentProcessor
{
    private readonly StripeClient _client;

    public StripeProcessor(string apiKey)
    {
        _client = new StripeClient(apiKey);
    }

    protected override PaymentResult ProcessCharge(decimal amount)
    {
        var charge = _client.Charges.Create(amount);
        return new PaymentResult(charge.Id, charge.Status);
    }
}

public class PayPalProcessor : BasePaymentProcessor
{
    protected override PaymentResult ProcessCharge(decimal amount)
    {
        // PayPal-specific logic
        throw new NotImplementedException();
    }
}
```

Where each pillar appears:

| Pillar          | Where It Appears                                           |
|-----------------|------------------------------------------------------------|
| Encapsulation   | `_transactionLog` is private; exposed via read-only list. |
| Abstraction     | `IPaymentProcessor` defines `Charge` as the contract.     |
| Inheritance     | `StripeProcessor` reuses logic from `BasePaymentProcessor`.|
| Polymorphism    | Each `ProcessCharge` behaves differently per subclass.     |

---

## Test Yourself — Polymorphism

1. **Which method runs?**

   ```csharp
   Employee emp = new SalariedEmployee("Alice", "E001", 5000m, 12000m);
   Console.WriteLine(emp.CalculatePay());
   ```

   - The variable is typed as `Employee` but holds a `SalariedEmployee`.  
   - Which `CalculatePay()` runs — base or derived — and why?

2. **Refactor this using polymorphism:**

   ```csharp
   public void ExportReport(object dataSource)
   {
       if (dataSource is SqlDatabase db)
           db.Query("SELECT * FROM Reports");
       else if (dataSource is CsvFile csv)
           csv.ReadAll();
       else if (dataSource is ApiEndpoint api)
           api.Fetch();
   }
   ```

   - What interface would you create?  
   - How would each data source implement it?

3. **`virtual` / `override` vs `new` in C#**

   - What does `virtual` + `override` do?  
   - What happens if you accidentally use `new` instead of `override`?  
   - How can that break polymorphism?






