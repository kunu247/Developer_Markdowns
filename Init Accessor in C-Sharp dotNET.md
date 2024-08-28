## Init Accessor in C# .NET: A Simple Explanation

**Imagine a locked box.**

- You can put things _inside_ the box only when it's being created (like when you're buying a new toy chest).
- Once the box is closed, you can't put anything else in it.
- You can, however, take things _out_ of the box anytime.

**That's essentially what an init accessor does.**

### What is it?

- It's a special type of setter for a property.
- It allows you to assign a value to a property _only_ during object creation.
- Once the object is created, you cannot change the value using the init accessor.

### Why use it?

- **Immutability:** Helps create immutable objects, meaning their values cannot be changed after creation. This can improve code reliability and predictability.
- **Object initialization:** Simplifies object creation using object initializers.
- **Validation:** Can be used to validate property values during object creation.

### How to use it?

C#

```
public class Person
{
    public string FirstName { get; init; }
    public string LastName { get; init; }

    public Person(string firstName, string lastName)
    {
        FirstName = firstName;
        LastName = lastName;   
    }
}
```

In this example:

- `FirstName` and `LastName` have `init` accessors.
- You can assign values to these properties only in the constructor or using an object initializer:

C#

```
var person = new Person("John", "Doe"); // Using constructor
var person2 = new Person { FirstName = "Jane", LastName = "Smith" }; // Using object initializer
```

- After the object is created, you cannot change `FirstName` or `LastName` using the `init` accessor.

### Key Points

- `init` accessor is for setting property values.
- It can only be used during object creation.
- Helps create immutable objects.
- Simplifies object initialization.
- Can be used for validation.

**In essence, an init accessor is like a one-time key to fill a box. Once it's used, the box is sealed.**

**Would you like to see more examples or delve deeper into specific use cases?**