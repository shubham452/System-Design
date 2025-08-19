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

👉 Do you want me to also show you the **Shallow Copy vs Deep Copy** difference with Prototype in Java? That’s a common interview follow-up question.
