# Warehouse Inventory Management
class WarehouseInventory:
    def __init__(self):
        self.products = []  # Array for storing product details
        self.stock_stack = StockStack()  # Stack for LIFO product storage
        self.order_queue = OrderQueue()  # Queue for order processing
        self.inventory_history = InventoryHistory()  # Linked List for inventory changes

    def add_product(self, product_name, quantity):
        self.products.append({"name": product_name, "quantity": quantity})
        for _ in range(quantity):
            self.stock_stack.push(product_name)  # Add to stock stack
        self.inventory_history.add_entry(f"Added {quantity} of {product_name}")
        print(f"{quantity} units of {product_name} added to inventory.")

    def process_order(self, customer_name, product_name, quantity):
        count = 0
        for _ in range(quantity):
            if self.stock_stack.pop() == product_name:  # Check if product is in stock
                count += 1
        if count == quantity:
            self.order_queue.enqueue((customer_name, product_name, quantity))
            self.inventory_history.add_entry(f"Processed order for {customer_name}: {quantity} {product_name}")
            print(f"Order placed: {customer_name} ordered {quantity} of {product_name}")
        else:
            print(f"Not enough stock available for {product_name}!")

    def fulfill_order(self):
        order = self.order_queue.dequeue()
        if order:
            customer_name, product_name, quantity = order
            print(f"Order shipped: {customer_name} received {quantity} of {product_name}")
        else:
            print("No orders to fulfill.")

    def show_inventory(self):
        print("\nCurrent Inventory:")
        for product in self.products:
            print(f"{product['name']}: {product['quantity']} units")

    def show_order_queue(self):
        self.order_queue.display_orders()

    def show_inventory_history(self):
        self.inventory_history.display_history()


class StockStack:
    def __init__(self):
        self.stack = []

    def push(self, product):
        self.stack.append(product)

    def pop(self):
        return self.stack.pop() if self.stack else None


class OrderQueue:
    def __init__(self):
        self.queue = []

    def enqueue(self, order):
        self.queue.append(order)

    def dequeue(self):
        return self.queue.pop(0) if self.queue else None

    def display_orders(self):
        if not self.queue:
            print("No pending orders.")
            return
        print("\nPending Orders:")
        for order in self.queue:
            print(f"{order[0]} ordered {order[2]} of {order[1]}")


class InventoryHistory:
    def __init__(self):
        self.head = None

    def add_entry(self, entry):
        new_node = InventoryNode(entry)
        if not self.head:
            self.head = new_node
        else:
            temp = self.head
            while temp.next:
                temp = temp.next
            temp.next = new_node

    def display_history(self):
        if not self.head:
            print("No inventory changes recorded.")
            return
        print("\nInventory History:")
        temp = self.head
        while temp:
            print(f"{temp.entry}")
            temp = temp.next


class InventoryNode:
    def __init__(self, entry):
        self.entry = entry
        self.next = None


# Main program execution
warehouse = WarehouseInventory()

while True:
    print("\nWarehouse Inventory Management")
    print("1. Add Product")
    print("2. Process Order")
    print("3. Fulfill Order")
    print("4. Show Inventory")
    print("5. Show Order Queue")
    print("6. Show Inventory History")
    print("7. Exit")

    choice = input("Enter your choice: ")

    if choice == "1":
        name = input("Enter product name: ")
        quantity = int(input("Enter quantity: "))
        warehouse.add_product(name, quantity)
    elif choice == "2":
        customer = input("Enter customer name: ")
        product = input("Enter product name: ")
        quantity = int(input("Enter quantity: "))
        warehouse.process_order(customer, product, quantity)
    elif choice == "3":
        warehouse.fulfill_order()
    elif choice == "4":
        warehouse.show_inventory()
    elif choice == "5":
        warehouse.show_order_queue()
    elif choice == "6":
        warehouse.show_inventory_history()
    elif choice == "7":
        print("Exiting... Thank you!")
        break
    else:
        print("Invalid choice! Please try again.")
