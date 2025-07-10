# Event-Driven Architecture (EDA)

## 📌 Core Definition
**EDA** is a design pattern where components communicate through events rather than direct calls.  
Key characteristics:
- Asynchronous
- Loosely coupled
- Highly scalable

## 🛠️ Example: E-Commerce Order Flow
```mermaid
sequenceDiagram
    User->>OrderService: Places order
    OrderService->>EventBroker: Emits OrderPlaced
    EventBroker->>InventoryService: OrderPlaced
    EventBroker->>PaymentService: OrderPlaced
    EventBroker->>NotificationService: OrderPlaced