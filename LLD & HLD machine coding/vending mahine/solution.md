

### **Directory Structure**

```text
solutions/
└── java/
    └── src/
        └── vendingmachine/
            ├── model/                 <-- Data Entities
            │   ├── Coin.java
            │   ├── Item.java
            │   └── Inventory.java
            ├── state/                 <-- State Pattern Logic
            │   ├── VendingMachineState.java
            │   ├── IdleState.java
            │   ├── HasMoneyState.java
            │   └── DispensingState.java
            ├── service/               <-- Main Logic (Singleton)
            │   └── VendingMachine.java
            └── VendingMachineDemo.java <-- Driver Class

```

---

### **1. Folder: `model/**`

*Contains the data objects and the inventory logic.*

**File:** `src/vendingmachine/model/Coin.java`

```java
package vendingmachine.model;

public enum Coin {
    PENNY(1),
    NICKEL(5),
    DIME(10),
    QUARTER(25);

    private int value;

    Coin(int value) {
        this.value = value;
    }

    public int getValue() {
        return value;
    }
}

```

**File:** `src/vendingmachine/model/Item.java`

```java
package vendingmachine.model;

public class Item {
    private String name;
    private int price;

    public Item(String name, int price) {
        this.name = name;
        this.price = price;
    }

    public int getPrice() {
        return price;
    }

    public String getName() {
        return name;
    }
}

```

**File:** `src/vendingmachine/model/Inventory.java`

```java
package vendingmachine.model;

import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

public class Inventory {
    private Map<String, Integer> stockMap = new ConcurrentHashMap<>();
    private Map<String, Item> itemMap = new ConcurrentHashMap<>();

    public void addItem(String code, Item item, int quantity) {
        itemMap.put(code, item);
        stockMap.put(code, quantity);
    }

    public Item getItem(String code) {
        return itemMap.get(code);
    }

    public synchronized void reduceStock(String code) {
        int currentStock = stockMap.getOrDefault(code, 0);
        if (currentStock > 0) {
            stockMap.put(code, currentStock - 1);
        }
    }

    public boolean isAvailable(String code) {
        return stockMap.getOrDefault(code, 0) > 0;
    }
}

```

---

### **2. Folder: `state/**`

*Contains the interface and implementations for the machine's behavior states.*

**File:** `src/vendingmachine/state/VendingMachineState.java`

```java
package vendingmachine.state;

import vendingmachine.model.Coin;

public interface VendingMachineState {
    void insertCoin(Coin coin);
    void selectItem(String code);
    void dispense();
    void refund();
}

```

**File:** `src/vendingmachine/state/IdleState.java`

```java
package vendingmachine.state;

import vendingmachine.model.Coin;
import vendingmachine.service.VendingMachine;

public class IdleState implements VendingMachineState {
    private VendingMachine machine;

    public IdleState(VendingMachine machine) {
        this.machine = machine;
    }

    @Override
    public void insertCoin(Coin coin) {
        System.out.println("Coin inserted: " + coin);
        machine.addBalance(coin.getValue());
        machine.setState(machine.getHasMoneyState());
    }

    @Override
    public void selectItem(String code) {
        System.out.println("Please insert coins first.");
    }

    @Override
    public void dispense() {
        System.out.println("Payment required.");
    }

    @Override
    public void refund() {
        System.out.println("No money to refund.");
    }
}

```

**File:** `src/vendingmachine/state/HasMoneyState.java`

```java
package vendingmachine.state;

import vendingmachine.model.Coin;
import vendingmachine.model.Inventory;
import vendingmachine.model.Item;
import vendingmachine.service.VendingMachine;

public class HasMoneyState implements VendingMachineState {
    private VendingMachine machine;

    public HasMoneyState(VendingMachine machine) {
        this.machine = machine;
    }

    @Override
    public void insertCoin(Coin coin) {
        System.out.println("Coin inserted: " + coin);
        machine.addBalance(coin.getValue());
    }

    @Override
    public void selectItem(String code) {
        Inventory inventory = machine.getInventory();
        if (!inventory.isAvailable(code)) {
            System.out.println("Item out of stock.");
            return;
        }

        Item item = inventory.getItem(code);
        if (machine.getBalance() < item.getPrice()) {
            System.out.println("Insufficient funds. Price: " + item.getPrice());
            return;
        }

        machine.setCurrentItem(item);
        machine.setState(machine.getDispensingState());
        machine.dispense();
    }

    @Override
    public void dispense() {
        System.out.println("Select item first.");
    }

    @Override
    public void refund() {
        System.out.println("Refunding: " + machine.getBalance());
        machine.setBalance(0);
        machine.setState(machine.getIdleState());
    }
}

```

**File:** `src/vendingmachine/state/DispensingState.java`

```java
package vendingmachine.state;

import vendingmachine.model.Coin;
import vendingmachine.model.Inventory;
import vendingmachine.model.Item;
import vendingmachine.service.VendingMachine;

public class DispensingState implements VendingMachineState {
    private VendingMachine machine;

    public DispensingState(VendingMachine machine) {
        this.machine = machine;
    }

    @Override
    public void insertCoin(Coin coin) {
        System.out.println("Please wait, dispensing...");
    }

    @Override
    public void selectItem(String code) {
        System.out.println("Please wait, dispensing...");
    }

    @Override
    public void dispense() {
        Item item = machine.getCurrentItem();
        Inventory inventory = machine.getInventory();

        inventory.reduceStock(item.getName());

        int change = machine.getBalance() - item.getPrice();
        machine.setBalance(0);

        System.out.println("Dispensing " + item.getName());
        if (change > 0) {
            System.out.println("Returning change: " + change);
        }

        machine.setState(machine.getIdleState());
    }

    @Override
    public void refund() {
        System.out.println("Cannot refund during dispensing.");
    }
}

```

---

### **3. Folder: `service/**`

*Contains the Singleton Vending Machine controller.*

**File:** `src/vendingmachine/service/VendingMachine.java`

```java
package vendingmachine.service;

import vendingmachine.model.Coin;
import vendingmachine.model.Inventory;
import vendingmachine.model.Item;
import vendingmachine.state.*;

public class VendingMachine {
    private static VendingMachine instance;
    private Inventory inventory;
    private VendingMachineState idleState;
    private VendingMachineState hasMoneyState;
    private VendingMachineState dispensingState;
    private VendingMachineState currentState;
    private int balance;
    private Item currentItem;

    private VendingMachine() {
        inventory = new Inventory();
        idleState = new IdleState(this);
        hasMoneyState = new HasMoneyState(this);
        dispensingState = new DispensingState(this);
        currentState = idleState;
        balance = 0;
    }

    public static VendingMachine getInstance() {
        if (instance == null) {
            synchronized (VendingMachine.class) {
                if (instance == null) {
                    instance = new VendingMachine();
                }
            }
        }
        return instance;
    }

    public synchronized void insertCoin(Coin coin) {
        currentState.insertCoin(coin);
    }

    public synchronized void selectItem(String code) {
        currentState.selectItem(code);
    }

    public synchronized void dispense() {
        currentState.dispense();
    }

    public void setState(VendingMachineState state) {
        this.currentState = state;
    }

    public VendingMachineState getHasMoneyState() {
        return hasMoneyState;
    }

    public VendingMachineState getDispensingState() {
        return dispensingState;
    }

    public VendingMachineState getIdleState() {
        return idleState;
    }

    public Inventory getInventory() {
        return inventory;
    }

    public void addBalance(int amount) {
        this.balance += amount;
    }

    public int getBalance() {
        return balance;
    }

    public void setBalance(int balance) {
        this.balance = balance;
    }

    public void setCurrentItem(Item item) {
        this.currentItem = item;
    }

    public Item getCurrentItem() {
        return currentItem;
    }
}

```

---

### **4. Root Folder**

*Contains the main driver class.*

**File:** `src/vendingmachine/VendingMachineDemo.java`

```java
package vendingmachine;

import vendingmachine.model.Coin;
import vendingmachine.model.Item;
import vendingmachine.service.VendingMachine;

public class VendingMachineDemo {
    public static void main(String[] args) {
        VendingMachine machine = VendingMachine.getInstance();

        // 1. Add Stock
        Item coke = new Item("Coke", 25);
        Item pepsi = new Item("Pepsi", 35);
        machine.getInventory().addItem("Coke", coke, 5);
        machine.getInventory().addItem("Pepsi", pepsi, 5);

        // 2. Buy Coke
        System.out.println("--- Transaction 1: Buy Coke ---");
        machine.insertCoin(Coin.QUARTER);
        machine.selectItem("Coke");

        // 3. Buy Pepsi with insufficient funds
        System.out.println("\n--- Transaction 2: Insufficient Funds ---");
        machine.insertCoin(Coin.QUARTER);
        machine.selectItem("Pepsi");
        machine.insertCoin(Coin.DIME);
        machine.selectItem("Pepsi");
    }
}

```
