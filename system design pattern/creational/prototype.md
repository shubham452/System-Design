Perfect 👍 Now let’s cover the **Prototype Design Pattern** in Java step by step (like we did with Singleton, Factory, Abstract Factory, Builder).

---

# 🔹 1. What is Prototype Design Pattern?

* **Creational Design Pattern**.
* Used when we want to **create new objects by copying (cloning) existing ones**, instead of creating from scratch.
* Helps when object creation is **costly, time-consuming, or complex**.

👉 Think of it like:

* You photocopy a document instead of rewriting it.
* You clone a Git repo instead of building from zero.

---

# 🔹 2. Why Use Prototype?

* To **avoid expensive object creation** (e.g., database calls, API calls).
* To create **different variations** of objects quickly by cloning and modifying.
* Helps when system should be **independent of how objects are created**.

---

# 🔹 3. Real-Life Examples

* **Clone a resume template** in a job portal.
* **Duplicate shapes in graphics editors** (like Photoshop, Figma).
* **Game characters** → duplicate an enemy with slightly different powers.

---

# 🔹 4. Prototype Pattern in Java

### Step 1: Prototype Interface

```java
interface Prototype extends Cloneable {
    Prototype clone();
}
```

---

### Step 2: Concrete Class (implements Prototype)

```java
class Shape implements Prototype {
    private String type;
    private int x;
    private int y;

    public Shape(String type, int x, int y) {
        this.type = type;
        this.x = x;
        this.y = y;
    }

    // Implement clone method
    @Override
    public Prototype clone() {
        return new Shape(this.type, this.x, this.y); // Deep copy
    }

    public void setPosition(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public void draw() {
        System.out.println("Drawing " + type + " at (" + x + "," + y + ")");
    }
}
```

---

### Step 3: Client Code

```java
public class PrototypeDemo {
    public static void main(String[] args) {
        // Original object
        Shape circle = new Shape("Circle", 10, 20);
        circle.draw();

        // Clone object
        Shape circleCopy = (Shape) circle.clone();
        circleCopy.setPosition(30, 40);
        circleCopy.draw();

        // Another clone
        Shape circleCopy2 = (Shape) circle.clone();
        circleCopy2.setPosition(50, 60);
        circleCopy2.draw();
    }
}
```

---

### ✅ Output

```
Drawing Circle at (10,20)
Drawing Circle at (30,40)
Drawing Circle at (50,60)
```

---

# 🔹 5. Advantages

* Avoids costly re-creation of objects.
* Simplifies object creation when it involves many configurations.
* Easy to add new object types without modifying existing code.

---

# 🔹 6. Real-World Usage in Java

* `Object.clone()` method (Java provides built-in cloning via `Cloneable`).
* **Prototype Beans** in Spring Framework (`@Scope("prototype")`).
* In **game dev/graphics editors** for duplicating objects.

---

# 🔹 7. Prototype vs Other Creational Patterns

| Pattern              | Purpose                                          |
| -------------------- | ------------------------------------------------ |
| **Singleton**        | Only 1 instance globally.                        |
| **Factory**          | Creates new instance each time.                  |
| **Abstract Factory** | Creates families of related objects.             |
| **Builder**          | Builds a complex object step by step.            |
| **Prototype**        | Clones existing objects instead of building new. |

---

✅ **Summary**

* Prototype lets us **clone objects** instead of building new ones.
* Useful when object creation is **expensive**.
* Implemented using **`clone()`** method.

---

Perfect 👍 Let’s go deeper into **Prototype Pattern** with **Shallow Copy vs Deep Copy** in Java, because this is a very common interview question.

---

# 🔹 1. Shallow Copy vs Deep Copy

| Feature        | Shallow Copy                                                                     | Deep Copy                                                                  |
| -------------- | -------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| **Definition** | Copies only object references (nested objects are **shared**).                   | Copies the object **and all its nested objects**.                          |
| **Memory**     | Original and copy share the same inner objects → changing one affects the other. | Each copy has its **own copy of nested objects** → completely independent. |
| **Speed**      | Faster (just copies references).                                                 | Slower (creates full new objects).                                         |
| **Use case**   | When nested objects are immutable or don’t change.                               | When independent copies are needed.                                        |

---

# 🔹 2. Example: Shallow Copy

```java
class Address {
    String city;

    public Address(String city) {
        this.city = city;
    }
}

class PersonShallow implements Cloneable {
    String name;
    Address address;

    public PersonShallow(String name, Address address) {
        this.name = name;
        this.address = address;
    }

    // Shallow copy (default clone)
    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone(); 
    }
}

public class ShallowCopyDemo {
    public static void main(String[] args) throws CloneNotSupportedException {
        Address address = new Address("Delhi");
        PersonShallow p1 = new PersonShallow("Shubham", address);

        // Shallow copy
        PersonShallow p2 = (PersonShallow) p1.clone();

        System.out.println("Before change: p1 city = " + p1.address.city + ", p2 city = " + p2.address.city);

        // Change city in p2 → also affects p1 (shared reference!)
        p2.address.city = "Mumbai";

        System.out.println("After change: p1 city = " + p1.address.city + ", p2 city = " + p2.address.city);
    }
}
```

### ✅ Output

```
Before change: p1 city = Delhi, p2 city = Delhi
After change: p1 city = Mumbai, p2 city = Mumbai
```

👉 Both objects share the same `Address` reference.

---

# 🔹 3. Example: Deep Copy

```java
class AddressDeep {
    String city;

    public AddressDeep(String city) {
        this.city = city;
    }
}

class PersonDeep implements Cloneable {
    String name;
    AddressDeep address;

    public PersonDeep(String name, AddressDeep address) {
        this.name = name;
        this.address = address;
    }

    // Deep copy (manually clone inner object)
    @Override
    protected Object clone() throws CloneNotSupportedException {
        PersonDeep cloned = (PersonDeep) super.clone();
        cloned.address = new AddressDeep(this.address.city); // new copy of Address
        return cloned;
    }
}

public class DeepCopyDemo {
    public static void main(String[] args) throws CloneNotSupportedException {
        AddressDeep address = new AddressDeep("Delhi");
        PersonDeep p1 = new PersonDeep("Shubham", address);

        // Deep copy
        PersonDeep p2 = (PersonDeep) p1.clone();

        System.out.println("Before change: p1 city = " + p1.address.city + ", p2 city = " + p2.address.city);

        // Change city in p2 → does NOT affect p1
        p2.address.city = "Mumbai";

        System.out.println("After change: p1 city = " + p1.address.city + ", p2 city = " + p2.address.city);
    }
}
```

### ✅ Output

```
Before change: p1 city = Delhi, p2 city = Delhi
After change: p1 city = Delhi, p2 city = Mumbai
```

👉 Each object has its own copy of `Address`.

---

# 🔹 4. Key Takeaways for Interviews

* **Shallow copy**: copies references → nested objects are shared.
* **Deep copy**: copies everything → nested objects are cloned too.
* In Java, `Object.clone()` by default gives **shallow copy**.
* To make **deep copy**, we must override `clone()` and manually copy nested objects.
* **Prototype pattern** often requires **deep copy** if independent objects are needed.

---

✅ Summary:

* **Prototype = cloning existing objects.**
* **Shallow Copy** → faster, but shares nested references.
* **Deep Copy** → independent, safer, but slower.

---

Do you want me to also show **how Prototype is implemented in real frameworks (like Spring’s `@Scope("prototype")`)** with an example?

