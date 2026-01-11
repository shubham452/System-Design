

### **Interview Problem: Design a Vending Machine**

**Scenario:**
We need to design the software control system for a Vending Machine that dispenses beverages and snacks. The machine has a keypad for selection, a slot for inserting money (coins/notes), and a dispensing tray.

#### **1. Functional Requirements (What it must do)**

* **Inventory Management:**
* The machine contains a set of items (e.g., Coke, Pepsi, Chips).
* Each item has a specific Price, Name, and Quantity.
* Items are stored in specific locations (e.g., Row 1, Col A -> Code "1A").


* **Selection Process:**
* The user should be able to select an item using its code.
* The machine should display the price of the selected item.


* **Payment & Transaction:**
* The machine accepts coins (e.g., 1, 5, 10, 25 cents) and notes.
* The user can insert money *before* or *after* selecting the item (clarify this flow).
* Once the inserted amount covers the price, the user can request the item.


* **Dispensing & Change:**
* If the full amount is paid, the machine dispenses the product.
* The machine must return the exact change if excess money is provided.
* If the machine does not have enough change, it should not accept the transaction.


* **Cancellation:**
* The user can cancel the transaction at any time before dispensing, and the machine must refund the full amount inserted.


* **Admin Operations:**
* An Admin/Supplier can open the machine to refill items and collect/refill cash.



#### **2. Design Constraints & Extensions (The "Gotchas")**

* **State Management:** The machine behaves differently depending on its state. For example, you cannot insert money if the machine is "Out of Order," and you cannot select an item if the machine is "Dispensing."
* **Concurrency:** (Optional for Easy/Medium) Assume only one user uses it at a time for now.
* **Extensibility:** We might want to add Card Payment or UPI later.

---

### **How to Approach This in the Interview**

Don't just jump into coding! Start by asking **Clarifying Questions** to narrow the scope. This shows you think like a senior engineer.

**Good Clarifying Questions to Ask:**

1. *"Does the machine support multiple payment types (Card/Cash) or just Cash?"* (Usually starts with just Cash to test your 'Change Calculation' logic).
2. *"Can a user buy multiple items in one transaction, or is it one item per session?"* (Stick to one item per session to keep it simple).
3. *"How do we handle the situation where the machine runs out of change?"* (Should we throw an error immediately or check before accepting cash?)

### **Key Design Patterns Expected**

1. **State Pattern (Critical):** To handle the flow `Idle -> Selection -> MoneyInserted -> Dispensing`. Using `if-else` flags (e.g., `isMoneyInserted = true`) is a **red flag**. You must use the State pattern.
2. **Strategy Pattern:** For the `PaymentAlgorithm` (Cash vs Card) if extended.
3. **Factory Pattern:** To create different `Item` types (Beverage vs Snack).
