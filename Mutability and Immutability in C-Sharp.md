## Mutability and Immutability in C#

### Mutability

Think of a mutable object as a piece of clay. You can mold it, shape it, and change it as much as you want. Once it's created, you can modify its properties directly.

In C#, most objects are mutable by default. This means you can change their values after they've been created.

**Example:**

C#

```
public class MutablePerson
{
    public string Name { get; set; }
    public int Age { get; set; }
}
```

In this example, you can create a `MutablePerson` object and change its `Name` and `Age` properties later.

### Immutability

An immutable object is like a frozen block of ice. Once it's created, it stays the same forever. You cannot change its properties after it's been created.

In C#, you can create immutable objects by making all properties read-only.

**Example:**

C#

```
public class ImmutablePerson
{
    public string Name { get; }
    public int Age { get; }

    public ImmutablePerson(string name, int age)
    {
        Name = name;
        Age    = age;
    }
}
```

In this example, the `Name` and `Age` properties can only be set in the constructor. Once an `ImmutablePerson` object is created, its values cannot be changed.

### Key Differences

|Feature|Mutable|Immutable|
|---|---|---|
|Modifiability|Can be changed after creation|Cannot be changed after creation|
|Performance|Generally faster|Can be slower due to creating new objects|
|Thread safety|More prone to issues|Generally thread-safe|
|Complexity|Simpler to use|Can be more complex to implement|

### When to Use Which

- **Mutable objects** are often used for performance-critical scenarios or when you need to frequently modify object state.
- **Immutable objects** are great for creating thread-safe applications, ensuring data integrity, and simplifying reasoning about code.

**Remember:** While C# doesn't have built-in support for creating immutable objects directly, you can achieve immutability by carefully designing your classes and using techniques like defensive copying.

**Would you like to see examples of how to use mutable and immutable objects in different scenarios?**