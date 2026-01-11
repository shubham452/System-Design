
### **1. Folder & File Structure**

```text
src/
└── com/
    └── lld/
        └── vendingmachine/
            │
            ├── Main.java                  // Entry point to run the application
            ├── VendingMachine.java        // Context Class
            │
            ├── coin/
            │   └── Coin.java              // Enum for Money
            │
            ├── inventory/
            │   ├── Inventory.java         // Manages the items
            │   ├── Item.java              // Product entity
            │   ├── ItemShelf.java         // Represents a slot (Code + Item)
            │   └── ProductType.java       // Enum for product types
            │
            └── states/
                ├── State.java             // Interface
                ├── IdleState.java
                ├── HasMoneyState.java
                ├── SelectionState.java
                └── DispenseState.java

```

---

### **2. Implementation Code**

#### **A. The Models (`src/com/lld/vendingmachine/coin` & `inventory`)**

**File:** `src/com/lld/vendingmachine/coin/Coin.java`

```java
package com.lld.vendingmachine.coin;

public enum Coin {
    PENNY(1),
    NICKEL(5),
    DIME(10),
    QUARTER(25);

    public int value;

    Coin(int value) {
        this.value = value;
    }
}

```

**File:** `src/com/lld/vendingmachine/inventory/ProductType.java`

```java
package com.lld.vendingmachine.inventory;

public enum ProductType {
    COKE,
    PEPSI,
    JUICE,
    SODA;
}

```

**File:** `src/com/lld/vendingmachine/inventory/Item.java`

```java
package com.lld.vendingmachine.inventory;

public class Item {
    ProductType type;
    int price;

    public Item(ProductType type, int price) {
        this.type = type;
        this.price = price;
    }

    public int getPrice() {
        return price;
    }

    public ProductType getType() {
        return type;
    }
}

```

**File:** `src/com/lld/vendingmachine/inventory/ItemShelf.java`

```java
package com.lld.vendingmachine.inventory;

public class ItemShelf {
    int code;
    Item item;
    boolean soldOut;

    public ItemShelf(int code, Item item) {
        this.code = code;
        this.item = item;
        this.soldOut = false;
    }

    public int getCode() { return code; }
    public Item getItem() { return item; }
    public boolean isSoldOut() { return soldOut; }
    public void setSoldOut(boolean soldOut) { this.soldOut = soldOut; }
}

```

**File:** `src/com/lld/vendingmachine/inventory/Inventory.java`

```java
package com.lld.vendingmachine.inventory;

public class Inventory {
    ItemShelf[] inventory = null;

    public Inventory(int count) {
        inventory = new ItemShelf[count];
        initialSetup();
    }

    public void initialSetup() {
        // Initialize inventory with dummy data
        for (int i = 0; i < inventory.length; i++) {
            Item newItem = new Item(ProductType.COKE, 25);
            // Codes start from 101, 102, etc.
            inventory[i] = new ItemShelf(101 + i, newItem); 
        }
    }

    public Item getItem(int codeNumber) throws Exception {
        for (ItemShelf shelf : inventory) {
            if (shelf.getCode() == codeNumber) {
                if (shelf.isSoldOut()) {
                    throw new Exception("Item already sold out");
                }
                return shelf.getItem();
            }
        }
        throw new Exception("Invalid Item Code");
    }

    public void updateSoldOutItem(int codeNumber) {
        for (ItemShelf shelf : inventory) {
            if (shelf.getCode() == codeNumber) {
                shelf.setSoldOut(true);
            }
        }
    }
}

```

---

#### **B. The State Interface (`src/com/lld/vendingmachine/states`)**

**File:** `src/com/lld/vendingmachine/states/State.java`

```java
package com.lld.vendingmachine.states;

import com.lld.vendingmachine.VendingMachine;
import com.lld.vendingmachine.coin.Coin;
import com.lld.vendingmachine.inventory.Item;
import java.util.List;

public interface State {
    void clickInsertCoinButton(VendingMachine machine) throws Exception;
    void insertCoin(VendingMachine machine, Coin coin) throws Exception;
    void clickSelectProductButton(VendingMachine machine) throws Exception;
    void selectProduct(VendingMachine machine, int codeNumber) throws Exception;
    int getChange(int returnMoney) throws Exception;
    List<Coin> refundFullMoney(VendingMachine machine) throws Exception;
    Item dispenseProduct(VendingMachine machine, int codeNumber) throws Exception;
}

```

---

#### **C. The Context Class (`src/com/lld/vendingmachine`)**

**File:** `src/com/lld/vendingmachine/VendingMachine.java`

```java
package com.lld.vendingmachine;

import com.lld.vendingmachine.coin.Coin;
import com.lld.vendingmachine.inventory.Inventory;
import com.lld.vendingmachine.states.IdleState;
import com.lld.vendingmachine.states.State;
import java.util.ArrayList;
import java.util.List;

public class VendingMachine {
    private State currentState;
    private Inventory inventory;
    private List<Coin> coinList;

    public VendingMachine() {
        currentState = new IdleState();
        inventory = new Inventory(10);
        coinList = new ArrayList<>();
    }

    public State getCurrentState() { return currentState; }
    public void setCurrentState(State currentState) { this.currentState = currentState; }
    
    public Inventory getInventory() { return inventory; }
    public void setInventory(Inventory inventory) { this.inventory = inventory; }
    
    public List<Coin> getCoinList() { return coinList; }
    public void setCoinList(List<Coin> coinList) { this.coinList = coinList; }
}

```

---

#### **D. Concrete States (`src/com/lld/vendingmachine/states`)**

**File:** `src/com/lld/vendingmachine/states/IdleState.java`

```java
package com.lld.vendingmachine.states;

import com.lld.vendingmachine.VendingMachine;
import com.lld.vendingmachine.coin.Coin;
import com.lld.vendingmachine.inventory.Item;
import java.util.ArrayList;
import java.util.List;

public class IdleState implements State {

    public IdleState() {
        System.out.println("--- State: IDLE ---");
    }

    @Override
    public void clickInsertCoinButton(VendingMachine machine) throws Exception {
        machine.setCurrentState(new HasMoneyState());
    }

    @Override
    public void insertCoin(VendingMachine machine, Coin coin) throws Exception {
        throw new Exception("You must click insert coin button first");
    }

    @Override
    public void clickSelectProductButton(VendingMachine machine) throws Exception {
        throw new Exception("First insert coin");
    }

    @Override
    public void selectProduct(VendingMachine machine, int codeNumber) throws Exception {
        throw new Exception("First insert coin");
    }

    @Override
    public int getChange(int returnMoney) throws Exception { return 0; }

    @Override
    public List<Coin> refundFullMoney(VendingMachine machine) throws Exception {
        throw new Exception("No money to refund");
    }

    @Override
    public Item dispenseProduct(VendingMachine machine, int codeNumber) throws Exception {
        throw new Exception("Product not chosen");
    }
}

```

**File:** `src/com/lld/vendingmachine/states/HasMoneyState.java`

```java
package com.lld.vendingmachine.states;

import com.lld.vendingmachine.VendingMachine;
import com.lld.vendingmachine.coin.Coin;
import com.lld.vendingmachine.inventory.Item;
import java.util.List;

public class HasMoneyState implements State {

    public HasMoneyState() {
        System.out.println("--- State: HAS_MONEY ---");
    }

    @Override
    public void clickInsertCoinButton(VendingMachine machine) throws Exception {
        return; // Already in HasMoney state
    }

    @Override
    public void insertCoin(VendingMachine machine, Coin coin) throws Exception {
        System.out.println("Accepted coin: " + coin.name());
        machine.getCoinList().add(coin);
    }

    @Override
    public void clickSelectProductButton(VendingMachine machine) throws Exception {
        machine.setCurrentState(new SelectionState());
    }

    @Override
    public void selectProduct(VendingMachine machine, int codeNumber) throws Exception {
        throw new Exception("You need to click on select product button first");
    }

    @Override
    public List<Coin> refundFullMoney(VendingMachine machine) throws Exception {
        System.out.println("Returning full amount back to user");
        machine.setCurrentState(new IdleState());
        return machine.getCoinList();
    }

    @Override
    public int getChange(int returnMoney) throws Exception { throw new Exception("You need to select product first"); }

    @Override
    public Item dispenseProduct(VendingMachine machine, int codeNumber) throws Exception {
        throw new Exception("Product not chosen");
    }
}

```

**File:** `src/com/lld/vendingmachine/states/SelectionState.java`

```java
package com.lld.vendingmachine.states;

import com.lld.vendingmachine.VendingMachine;
import com.lld.vendingmachine.coin.Coin;
import com.lld.vendingmachine.inventory.Item;
import java.util.List;

public class SelectionState implements State {

    public SelectionState() {
        System.out.println("--- State: SELECTION ---");
    }

    @Override
    public void clickInsertCoinButton(VendingMachine machine) throws Exception {
        throw new Exception("You can not insert Coin in selection state");
    }

    @Override
    public void insertCoin(VendingMachine machine, Coin coin) throws Exception {
        throw new Exception("You can not insert Coin in selection state");
    }

    @Override
    public void clickSelectProductButton(VendingMachine machine) throws Exception {
        return;
    }

    @Override
    public void selectProduct(VendingMachine machine, int codeNumber) throws Exception {
        // 1. Get item details
        Item item = machine.getInventory().getItem(codeNumber);

        // 2. Total Paid by User
        int paidByUser = 0;
        for(Coin c : machine.getCoinList()){
            paidByUser = paidByUser + c.value;
        }

        // 3. Compare Price
        if(paidByUser < item.getPrice()){
            System.out.println("Insufficient funds. Price: " + item.getPrice() + ", Paid: " + paidByUser);
            refundFullMoney(machine);
            throw new Exception("Insufficient Funds");
        } 
        else if(paidByUser >= item.getPrice()) {
            if(paidByUser > item.getPrice()){
                getChange(paidByUser - item.getPrice());
            }
            machine.setCurrentState(new DispenseState(machine, codeNumber));
        }
    }

    @Override
    public int getChange(int returnMoney) throws Exception {
        System.out.println("Returned change in Coin Dispatch Tray: " + returnMoney);
        return returnMoney;
    }

    @Override
    public List<Coin> refundFullMoney(VendingMachine machine) throws Exception {
        System.out.println("Returned full amount back in Coin Dispatch Tray");
        machine.setCurrentState(new IdleState());
        return machine.getCoinList();
    }

    @Override
    public Item dispenseProduct(VendingMachine machine, int codeNumber) throws Exception {
        throw new Exception("Product not selected yet");
    }
}

```

**File:** `src/com/lld/vendingmachine/states/DispenseState.java`

```java
package com.lld.vendingmachine.states;

import com.lld.vendingmachine.VendingMachine;
import com.lld.vendingmachine.coin.Coin;
import com.lld.vendingmachine.inventory.Item;
import java.util.List;
import java.util.ArrayList;

public class DispenseState implements State {

    public DispenseState(VendingMachine machine, int codeNumber) throws Exception {
        System.out.println("--- State: DISPENSE ---");
        dispenseProduct(machine, codeNumber);
    }

    @Override
    public void clickInsertCoinButton(VendingMachine machine) throws Exception {
        throw new Exception("dispense state cannot allow insert coin");
    }

    @Override
    public void insertCoin(VendingMachine machine, Coin coin) throws Exception {
        throw new Exception("dispense state cannot allow insert coin");
    }

    @Override
    public void clickSelectProductButton(VendingMachine machine) throws Exception {
        throw new Exception("dispense state cannot allow select product");
    }

    @Override
    public void selectProduct(VendingMachine machine, int codeNumber) throws Exception {
        throw new Exception("dispense state cannot allow select product");
    }

    @Override
    public int getChange(int returnMoney) throws Exception {
        throw new Exception("dispense state cannot allow change calculation");
    }

    @Override
    public List<Coin> refundFullMoney(VendingMachine machine) throws Exception {
        throw new Exception("dispense state cannot allow refund");
    }

    @Override
    public Item dispenseProduct(VendingMachine machine, int codeNumber) throws Exception {
        System.out.println("Product has been dispensed: Code " + codeNumber);
        Item item = machine.getInventory().getItem(codeNumber);
        machine.getInventory().updateSoldOutItem(codeNumber);
        
        // Reset Machine
        machine.setCoinList(new ArrayList<>());
        machine.setCurrentState(new IdleState());
        return item;
    }
}

```

---

#### **E. The Entry Point (`src/com/lld/vendingmachine`)**

**File:** `src/com/lld/vendingmachine/Main.java`

```java
package com.lld.vendingmachine;

import com.lld.vendingmachine.coin.Coin;
import com.lld.vendingmachine.states.State;

public class Main {

    public static void main(String args[]) {
        VendingMachine vendingMachine = new VendingMachine();

        try {
            System.out.println("|--- TEST 1: SUCCESSFUL PURCHASE ---|");
            
            State vendingState = vendingMachine.getCurrentState();
            
            // 1. Idle -> Click Insert
            vendingState.clickInsertCoinButton(vendingMachine);
            
            // 2. HasMoney -> Insert Coins
            vendingState = vendingMachine.getCurrentState();
            vendingState.insertCoin(vendingMachine, Coin.DIME);   // 10
            vendingState.insertCoin(vendingMachine, Coin.DIME);   // 10
            vendingState.insertCoin(vendingMachine, Coin.NICKEL); // 5 (Total 25)
            
            // 3. HasMoney -> Click Select Product
            vendingState.clickSelectProductButton(vendingMachine);
            
            // 4. Selection -> Select Code 102
            vendingState = vendingMachine.getCurrentState();
            vendingState.selectProduct(vendingMachine, 102);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

```
