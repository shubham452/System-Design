Perfect 👍 Let’s do the **Factory Design Pattern** in Java the same way we did Singleton:

---

# 🔹 1. What is Factory Design Pattern?

* A **Creational Design Pattern**.
* Instead of creating objects directly with `new`, you use a **Factory class** (or method) to create objects.
* The factory decides which **subclass object** to return based on some input.

👉 Think of it like a **car showroom**:
You don’t go to the factory yourself to build a car → you just ask the dealer (factory) and get the car you need.

---

# 🔹 2. Why use Factory Pattern?

* **Encapsulation of object creation** → client doesn’t need to know `new` details.
* **Loose coupling** → client depends on abstraction, not concrete classes.
* **Flexibility** → add new subclasses without changing client code.

---

# 🔹 3. Real-life Examples

* GUI Toolkit: `ButtonFactory` may return `WindowsButton` or `MacButton`.
* Database Driver Manager: Returns MySQL, PostgreSQL, or Oracle connection.
* Payment Gateway: Returns `CreditCardPayment`, `UPIPayment`, or `PayPalPayment`.

---

# 🔹 4. Basic Factory Pattern in Java

### Step 1: Create an interface (abstraction)

```java
interface Shape {
    void draw();
}
```

### Step 2: Create concrete classes

```java
class Circle implements Shape {
    public void draw() {
        System.out.println("Drawing Circle");
    }
}

class Rectangle implements Shape {
    public void draw() {
        System.out.println("Drawing Rectangle");
    }
}

class Square implements Shape {
    public void draw() {
        System.out.println("Drawing Square");
    }
}
```

### Step 3: Create the Factory class

```java
class ShapeFactory {
    // Factory method
    public Shape getShape(String shapeType) {
        if (shapeType == null) {
            return null;
        }
        if (shapeType.equalsIgnoreCase("CIRCLE")) {
            return new Circle();
        } else if (shapeType.equalsIgnoreCase("RECTANGLE")) {
            return new Rectangle();
        } else if (shapeType.equalsIgnoreCase("SQUARE")) {
            return new Square();
        }
        return null;
    }
}
```

### Step 4: Client uses the factory

```java
public class FactoryPatternDemo {
    public static void main(String[] args) {
        ShapeFactory shapeFactory = new ShapeFactory();

        Shape s1 = shapeFactory.getShape("CIRCLE");
        s1.draw();

        Shape s2 = shapeFactory.getShape("RECTANGLE");
        s2.draw();

        Shape s3 = shapeFactory.getShape("SQUARE");
        s3.draw();
    }
}
```

✅ **Output:**

```
Drawing Circle
Drawing Rectangle
Drawing Square
```

---

# 🔹 5. Advantages

* **Encapsulation** → Object creation hidden inside factory.
* **Flexibility** → Adding a new shape (e.g., Triangle) needs only a new class + factory update.
* **Polymorphism** → Factory returns `Shape` (interface), but actual object depends on input.

---

# 🔹 6. Real-world Example: Payment Gateway

```java
interface Payment {
    void pay(int amount);
}

class CreditCardPayment implements Payment {
    public void pay(int amount) {
        System.out.println("Paid " + amount + " using Credit Card");
    }
}

class UPIPayment implements Payment {
    public void pay(int amount) {
        System.out.println("Paid " + amount + " using UPI");
    }
}

class PayPalPayment implements Payment {
    public void pay(int amount) {
        System.out.println("Paid " + amount + " using PayPal");
    }
}

// Factory
class PaymentFactory {
    public Payment getPaymentMethod(String type) {
        if (type.equalsIgnoreCase("CREDIT")) return new CreditCardPayment();
        else if (type.equalsIgnoreCase("UPI")) return new UPIPayment();
        else if (type.equalsIgnoreCase("PAYPAL")) return new PayPalPayment();
        return null;
    }
}

public class PaymentDemo {
    public static void main(String[] args) {
        PaymentFactory factory = new PaymentFactory();

        Payment p1 = factory.getPaymentMethod("CREDIT");
        p1.pay(1000);

        Payment p2 = factory.getPaymentMethod("UPI");
        p2.pay(500);
    }
}
```

✅ **Output:**

```
Paid 1000 using Credit Card
Paid 500 using UPI
```

---

# 🔹 7. Summary

* **Singleton** → Ensures only **one instance** of a class exists.
* **Factory** → Provides a way to **create objects without exposing creation logic**.
* **When to use?**

  * Use **Singleton** when you need **only one shared object**.
  * Use **Factory** when you need to **decide which object to create at runtime**.

---

👉 Do you want me to also cover the **Factory Method vs Abstract Factory Pattern** difference (since interviewers often ask this as a follow-up)?
