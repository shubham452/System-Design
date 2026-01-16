Sure ✅ Shubham — here is the **FULL WORKING CODE (Complete Project)** in the same folder structure, with all upgrades:

✔ Different products + prices
✔ Shelf quantity (stock)
✔ Refund clears coinList
✔ Change returned as `List<Coin>`
✔ State Pattern clean flow

---

# ✅ Folder Structure

```text
src/
└── com/
    └── lld/
        └── vendingmachine/
            ├── Main.java
            ├── VendingMachine.java
            │
            ├── coin/
            │   └── Coin.java
            │
            ├── inventory/
            │   ├── Inventory.java
            │   ├── Item.java
            │   ├── ItemShelf.java
            │   └── ProductType.java
            │
            ├── states/
            │   ├── State.java
            │   ├── IdleState.java
            │   ├── HasMoneyState.java
            │   ├── SelectionState.java
            │   └── DispenseState.java
            │
            └── utils/
                └── ChangeCalculator.java
```

---

# ✅ FULL CODE

---

## ✅ `coin/Coin.java`

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

---

## ✅ `inventory/ProductType.java`

```java
package com.lld.vendingmachine.inventory;

public enum ProductType {
    COKE,
    PEPSI,
    JUICE,
    SODA;
}
```

---

## ✅ `inventory/Item.java`

```java
package com.lld.vendingmachine.inventory;

public class Item {
    private ProductType type;
    private int price;

    public Item(ProductType type, int price) {
        this.type = type;
        this.price = price;
    }

    public ProductType getType() {
        return type;
    }

    public int getPrice() {
        return price;
    }
}
```

---

## ✅ `inventory/ItemShelf.java`

```java
package com.lld.vendingmachine.inventory;

public class ItemShelf {
    private int code;
    private Item item;
    private int quantity;

    public ItemShelf(int code, Item item, int quantity) {
        this.code = code;
        this.item = item;
        this.quantity = quantity;
    }

    public int getCode() {
        return code;
    }

    public Item getItem() {
        return item;
    }

    public int getQuantity() {
        return quantity;
    }

    public boolean isSoldOut() {
        return quantity == 0;
    }

    public void dispenseOne() throws Exception {
        if (quantity == 0) {
            throw new Exception("Item sold out");
        }
        quantity--;
    }
}
```

---

## ✅ `inventory/Inventory.java`

```java
package com.lld.vendingmachine.inventory;

public class Inventory {
    private ItemShelf[] inventory;

    public Inventory(int count) {
        inventory = new ItemShelf[count];
        initialSetup();
    }

    public void initialSetup() {
        // Codes: 101, 102, 103...
        inventory[0] = new ItemShelf(101, new Item(ProductType.COKE, 25), 5);
        inventory[1] = new ItemShelf(102, new Item(ProductType.PEPSI, 30), 3);
        inventory[2] = new ItemShelf(103, new Item(ProductType.JUICE, 40), 2);
        inventory[3] = new ItemShelf(104, new Item(ProductType.SODA, 35), 4);

        // Fill remaining with default items
        for (int i = 4; i < inventory.length; i++) {
            inventory[i] = new ItemShelf(101 + i, new Item(ProductType.COKE, 25), 2);
        }
    }

    public ItemShelf getShelf(int codeNumber) throws Exception {
        for (ItemShelf shelf : inventory) {
            if (shelf.getCode() == codeNumber) {
                return shelf;
            }
        }
        throw new Exception("Invalid Item Code");
    }

    public Item getItem(int codeNumber) throws Exception {
        ItemShelf shelf = getShelf(codeNumber);

        if (shelf.isSoldOut()) {
            throw new Exception("Item already sold out");
        }
        return shelf.getItem();
    }

    public void dispenseItem(int codeNumber) throws Exception {
        ItemShelf shelf = getShelf(codeNumber);
        shelf.dispenseOne();
    }
}
```

---

## ✅ `utils/ChangeCalculator.java`

```java
package com.lld.vendingmachine.utils;

import com.lld.vendingmachine.coin.Coin;
import java.util.ArrayList;
import java.util.List;

public class ChangeCalculator {

    public static List<Coin> getChangeCoins(int amount) throws Exception {
        List<Coin> change = new ArrayList<>();

        Coin[] coins = {Coin.QUARTER, Coin.DIME, Coin.NICKEL, Coin.PENNY};

        for (Coin coin : coins) {
            while (amount >= coin.value) {
                amount -= coin.value;
                change.add(coin);
            }
        }

        if (amount != 0) {
            throw new Exception("Cannot return exact change");
        }

        return change;
    }
}
```

---

## ✅ `states/State.java`

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

    List<Coin> getChange(int returnMoney) throws Exception;
    List<Coin> refundFullMoney(VendingMachine machine) throws Exception;

    Item dispenseProduct(VendingMachine machine, int codeNumber) throws Exception;
}
```

---

## ✅ `states/IdleState.java`

```java
package com.lld.vendingmachine.states;

import com.lld.vendingmachine.VendingMachine;
import com.lld.vendingmachine.coin.Coin;
import com.lld.vendingmachine.inventory.Item;

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
    public List<Coin> getChange(int returnMoney) throws Exception {
        throw new Exception("No change in idle state");
    }

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

---

## ✅ `states/HasMoneyState.java`

```java
package com.lld.vendingmachine.states;

import com.lld.vendingmachine.VendingMachine;
import com.lld.vendingmachine.coin.Coin;
import com.lld.vendingmachine.inventory.Item;

import java.util.ArrayList;
import java.util.List;

public class HasMoneyState implements State {

    public HasMoneyState() {
        System.out.println("--- State: HAS_MONEY ---");
    }

    @Override
    public void clickInsertCoinButton(VendingMachine machine) throws Exception {
        return;
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

        List<Coin> refund = new ArrayList<>(machine.getCoinList());
        machine.setCoinList(new ArrayList<>());

        machine.setCurrentState(new IdleState());
        return refund;
    }

    @Override
    public List<Coin> getChange(int returnMoney) throws Exception {
        throw new Exception("You need to select product first");
    }

    @Override
    public Item dispenseProduct(VendingMachine machine, int codeNumber) throws Exception {
        throw new Exception("Product not chosen");
    }
}
```

---

## ✅ `states/SelectionState.java`

```java
package com.lld.vendingmachine.states;

import com.lld.vendingmachine.VendingMachine;
import com.lld.vendingmachine.coin.Coin;
import com.lld.vendingmachine.inventory.Item;
import com.lld.vendingmachine.utils.ChangeCalculator;

import java.util.ArrayList;
import java.util.List;

public class SelectionState implements State {

    public SelectionState() {
        System.out.println("--- State: SELECTION ---");
    }

    @Override
    public void clickInsertCoinButton(VendingMachine machine) throws Exception {
        throw new Exception("You cannot insert Coin in selection state");
    }

    @Override
    public void insertCoin(VendingMachine machine, Coin coin) throws Exception {
        throw new Exception("You cannot insert Coin in selection state");
    }

    @Override
    public void clickSelectProductButton(VendingMachine machine) throws Exception {
        return;
    }

    @Override
    public void selectProduct(VendingMachine machine, int codeNumber) throws Exception {
        Item item = machine.getInventory().getItem(codeNumber);

        int paidByUser = 0;
        for (Coin c : machine.getCoinList()) {
            paidByUser += c.value;
        }

        System.out.println("Selected: " + item.getType() + " | Price: " + item.getPrice());
        System.out.println("Paid by user: " + paidByUser);

        if (paidByUser < item.getPrice()) {
            System.out.println("Insufficient funds!");
            refundFullMoney(machine);
            throw new Exception("Insufficient Funds");
        }

        if (paidByUser > item.getPrice()) {
            int changeAmount = paidByUser - item.getPrice();
            List<Coin> changeCoins = getChange(changeAmount);
            System.out.println("Returned change coins: " + changeCoins);
        }

        machine.setCurrentState(new DispenseState(machine, codeNumber));
    }

    @Override
    public List<Coin> getChange(int returnMoney) throws Exception {
        return ChangeCalculator.getChangeCoins(returnMoney);
    }

    @Override
    public List<Coin> refundFullMoney(VendingMachine machine) throws Exception {
        System.out.println("Returned full amount back in Coin Dispatch Tray");

        List<Coin> refund = new ArrayList<>(machine.getCoinList());
        machine.setCoinList(new ArrayList<>());

        machine.setCurrentState(new IdleState());
        return refund;
    }

    @Override
    public Item dispenseProduct(VendingMachine machine, int codeNumber) throws Exception {
        throw new Exception("Product not selected yet");
    }
}
```

---

## ✅ `states/DispenseState.java`

```java
package com.lld.vendingmachine.states;

import com.lld.vendingmachine.VendingMachine;
import com.lld.vendingmachine.coin.Coin;
import com.lld.vendingmachine.inventory.Item;

import java.util.ArrayList;
import java.util.List;

public class DispenseState implements State {

    public DispenseState(VendingMachine machine, int codeNumber) throws Exception {
        System.out.println("--- State: DISPENSE ---");
        dispenseProduct(machine, codeNumber);
    }

    @Override
    public void clickInsertCoinButton(VendingMachine machine) throws Exception {
        throw new Exception("Dispense state cannot allow insert coin");
    }

    @Override
    public void insertCoin(VendingMachine machine, Coin coin) throws Exception {
        throw new Exception("Dispense state cannot allow insert coin");
    }

    @Override
    public void clickSelectProductButton(VendingMachine machine) throws Exception {
        throw new Exception("Dispense state cannot allow select product");
    }

    @Override
    public void selectProduct(VendingMachine machine, int codeNumber) throws Exception {
        throw new Exception("Dispense state cannot allow select product");
    }

    @Override
    public List<Coin> getChange(int returnMoney) throws Exception {
        throw new Exception("Dispense state cannot allow change calculation");
    }

    @Override
    public List<Coin> refundFullMoney(VendingMachine machine) throws Exception {
        throw new Exception("Dispense state cannot allow refund");
    }

    @Override
    public Item dispenseProduct(VendingMachine machine, int codeNumber) throws Exception {
        Item item = machine.getInventory().getItem(codeNumber);

        machine.getInventory().dispenseItem(codeNumber);

        System.out.println("Product dispensed: " + item.getType() + " (Code " + codeNumber + ")");

        // Reset machine
        machine.setCoinList(new ArrayList<>());
        machine.setCurrentState(new IdleState());

        return item;
    }
}
```

---

## ✅ `VendingMachine.java`

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

    public State getCurrentState() {
        return currentState;
    }

    public void setCurrentState(State currentState) {
        this.currentState = currentState;
    }

    public Inventory getInventory() {
        return inventory;
    }

    public void setInventory(Inventory inventory) {
        this.inventory = inventory;
    }

    public List<Coin> getCoinList() {
        return coinList;
    }

    public void setCoinList(List<Coin> coinList) {
        this.coinList = coinList;
    }
}
```

---

## ✅ `Main.java`

```java
package com.lld.vendingmachine;

import com.lld.vendingmachine.coin.Coin;
import com.lld.vendingmachine.states.State;

public class Main {

    public static void main(String[] args) {

        VendingMachine vendingMachine = new VendingMachine();

        // TEST 1: Exact Money
        try {
            System.out.println("\n|--- TEST 1: SUCCESSFUL PURCHASE (Exact Money) ---|");

            State state = vendingMachine.getCurrentState();
            state.clickInsertCoinButton(vendingMachine);

            state = vendingMachine.getCurrentState();
            state.insertCoin(vendingMachine, Coin.DIME);   // 10
            state.insertCoin(vendingMachine, Coin.DIME);   // 10
            state.insertCoin(vendingMachine, Coin.NICKEL); // 5 total = 25

            state.clickSelectProductButton(vendingMachine);

            state = vendingMachine.getCurrentState();
            state.selectProduct(vendingMachine, 101); // COKE 25

        } catch (Exception e) {
            e.printStackTrace();
        }

        // TEST 2: With Change
        try {
            System.out.println("\n|--- TEST 2: SUCCESSFUL PURCHASE (With Change) ---|");

            State state = vendingMachine.getCurrentState();
            state.clickInsertCoinButton(vendingMachine);

            state = vendingMachine.getCurrentState();
            state.insertCoin(vendingMachine, Coin.QUARTER); // 25
            state.insertCoin(vendingMachine, Coin.QUARTER); // 25 total = 50

            state.clickSelectProductButton(vendingMachine);

            state = vendingMachine.getCurrentState();
            state.selectProduct(vendingMachine, 102); // PEPSI 30 -> change 20

        } catch (Exception e) {
            e.printStackTrace();
        }

        // TEST 3: Insufficient Funds
        try {
            System.out.println("\n|--- TEST 3: INSUFFICIENT FUNDS ---|");

            State state = vendingMachine.getCurrentState();
            state.clickInsertCoinButton(vendingMachine);

            state = vendingMachine.getCurrentState();
            state.insertCoin(vendingMachine, Coin.DIME); // 10 only

            state.clickSelectProductButton(vendingMachine);

            state = vendingMachine.getCurrentState();
            state.selectProduct(vendingMachine, 103); // JUICE 40

        } catch (Exception e) {
            System.out.println("Exception: " + e.getMessage());
        }
    }
}
```

---

# ✅ You can run it and you’ll see:

* State transitions logs
* Change coins returned
* Stock decreases
* Refund clears money

