# Dotnet Zero To Hero

**Dotnet Zero To Hero** is a learning path that takes you from the very basics of **C#** to building real-world applications with **.NET**.  
The goal is to give you strong fundamentals, then layer on real examples, so you can move from “I kind of know C#” to “I can design and reason about real systems.”

---

## Table of Contents

1. [OOP in C#](#oop-in-c)
   - [Lesson 1: Why Does OOP Exist? (The Intuition)](#lesson-1-why-does-oop-exist-the-intuition)
   - [Lesson 2: Encapsulation](#lesson-2-Encapsulation)

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



