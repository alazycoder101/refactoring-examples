change-value-to-reference:php

###

1.en. Use <a href="/replace-constructor-with-factory-method">Replace Constructor with Factory Method</a> on the class from which the references are to be generated.

2.en. Determine which object will be responsible for providing access to references. Instead of creating a new object, when you need one you now need to get it from a storage object or static dictionary field.

3.en. Determine whether references will be created in advance or dynamically as necessary. If objects are created in advance, make sure to load them before use.

4.en. Change the factory method so that it returns a reference. If objects are created in advance, decide how to handle errors when a non-existent object is requested.



###

```
class Customer {
  private $name;
  public function __construct($name) {
    $this->name = $name;
  }
  public function getName() {
    return $this->name;
  }
}

class Order {
  //…
  private $customer; // Customer
  public function getCustomerName() {
      return $this->customer->getName();
  }
  public function setCustomer($customerName) {
    $this->customer = new Customer($customerName);
  }
  public function __construct($customerName) {
    $this->customer = new Customer($customerName);
  }
}

// Some client code, which uses Order class.
private static function numberOfOrdersFor($orders, $customer) {
  $result = 0;
  foreach ($orders as $order) {
    if ($order->getCustomerName() === $customer) {
      $result++;
    }
  }
  return $result;
}
```

###

```
class Customer {
  private static $instances = array();

  // This code should be executed at the program launch.
  public static function loadCustomers() {
    Customer::instances["Lemon Car Hire"] = new Customer("Lemon Car Hire");
    Customer::instances["Associated Coffee Machines"] = new Customer("Associated Coffee Machines");
    Customer::instances["Bilston Gasworks"] = new Customer("Bilston Gasworks");
  }

  private $name;
  public static function getNamed($name) {
    return Customer::instances[$name];
  }
  private function __construct($name) {
    $this->name = $name;
  }
  public function getName() {
    return $this->name;
  }
}

class Order {
  //…
  private $customer; // Customer
  public function getCustomerName() {
      return $this->customer->getName();
  }
  public function setCustomer($customerName) {
    $this->customer = Customer::getNamed($customerName);
  }
  public function __construct($customerName) {
    $this->customer = Customer::getNamed($customerName);
  }
}

// Some client code, which uses Order class.
private static function numberOfOrdersFor($orders, $customer) {
  $result = 0;
  foreach ($orders as $order) {
    if ($order->getCustomerName() === $customer) {
      $result++;
    }
  }
  return $result;
}
```

###

Set step 1

#|en| Let's look at <i>Replace Data Value with Object</i> using the customer/order class example. We will pick up where we finished the <i>Replace Data Value with Object</i> refactoring.

Select name of "Customer"

#|en|+ Here we have a customer class…

Select name of "Order"

#|en|+ …that is used in the order class…

Select name of "numberOfOrdersFor"

#|en|= …and client code that is used by both classes.

Select name of "Customer"

#|en| Currently, the customer in the order class is used as a data value. In other words, each order has own instance of <code>Customer</code> even if the actual customer is the same. We want to change the code so that multiple orders for the same customer use the same instance of the <code>Customer</code> class.

#|en| In our case, this means that for each customer name, there must exist one and only one instance of the customer class.

#|en| We start with <a href="/replace-constructor-with-factory-method">Replace Constructor with Factory Method</a>. This lets us keep an eye on the process of creation of customer objects, which is extremely important for what we are going to do. We create the factory method in the customer class.

Go to before "__construct" in "Customer"
Print:
```

  public static function create($name) {
    return new Customer($name);
  }
```

Select "new Customer($customerName)"

#|en| Then we replace the call to the <code>Customer</code> class constructor with a reference to the factory method.

Replace "Customer::create($customerName)"

Select visibility of "__construct" in "Customer"

#|en| We can now make the customer constructor private.

Print "private"

Set step 2

Select name of "Customer"

#|en| A decision must be made: Which object will be responsible for providing access to instances of the customer class? It would be good to have a registry object for this purpose, containing a pool of all reference objects and retrieving the necessary instances from it. For example, if you need to put several products in an order, each product can be stored inside the order object.

#|en| But, in this case, no such object exists for customers. To not create a new class for storing a customer registry, you can set up storage by using a static field in the <code>Customer</code> class.

Go to the start of "Customer"

Print:
```

  private static $instances = array();

```

Set step 3

#|en| Then decide how to create customers: in advance or dynamically (as needed). We will use the first way. When launching the application, we will load the clients that are currently "in use". We can take this information from a database or file, for example.

#|en| For simplicity, we will use explicit code for loading customers. This makes it possible to change it by using <a href="/substitute-algorithm">Substitute Algorithm</a>.

Print:
```

  // This code should be executed at the program launch.
  public static function loadCustomers() {
    Customer::instances["Lemon Car Hire"] = new Customer("Lemon Car Hire");
    Customer::instances["Associated Coffee Machines"] = new Customer("Associated Coffee Machines");
    Customer::instances["Bilston Gasworks"] = new Customer("Bilston Gasworks");
  }

```

Set step 4

Select name of "create"

#|en| Now we modify the factory method of the <code>Customer</code> class so that it returns the previously created customer.

Select "new Customer($name)" in "create"

Replace "Customer::instances[$name]"

Select name of "create"

#|en| And since the <code>create()</code> method now always returns an existing customer, this should be clarified with the help of <a href="/rename-method">Rename Method</a>.

Print "getNamed"

Wait 500ms

Select "Customer::|||create|||"

Replace "getNamed"

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
