replace-data-value-with-object:php

###

1.en. Create a new class and copy your field and a getter for accessing the field to the class.

2.en. Create a constructor that accepts the original value of the field.

3.en. In the original class, change the field type to the new class.

4.en. Change the access methods so that they delegate to the new class.



###

```
class Order {
  // ...
  private $customer; // String

  public function __construct($customer) {
    $this->customer = $customer;
  }
  public function getCustomer() {
    return $this->customer;
  }
  public function setCustomer($customer) {
    $this->customer = $customer;
  }
}

// Client code, which uses Order class.
private static function numberOfOrdersFor($orders, $customer) {
  $result = 0;
  foreach ($orders as $order) {
    if ($order->getCustomer() === $customer) {
      $result++;
    }
  }
  return $result;
}
```

###

```
class Order {
  // ...
  private $customer; // Customer

  public function __construct($customerName) {
    $this->customer = new Customer($customerName);
  }
  public function getCustomerName() {
    return $this->customer->getName();
  }
  public function setCustomer($customerName) {
    $this->customer = new Customer($customerName);
  }
}

class Customer {
  private $name;

  public function __construct($name) {
    $this->name = $name;
  }
  public function getName() {
    return $this->name;
  }
}

// Client code, which uses Order class.
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

#|en| Let's look at the <i>Replace Data Value with Object</i> refactoring, using an order class as an example.

Select "private |||$customer|||"

#|en| In this example, the customer in the order class is stored as a string. Alternatively, we could create a <code>Customer</code> class and move the other customer data and behaviors to this class.

Go to after "Order"

Print:
```


class Customer {
}
```

#|en| The class is now ready. Let's move the customer name field to it, since the field is used in the rest of the order code.

Go to end of "Customer"

Print:
```

  private $name;

  public function getName() {
    return $this->name;
  }
```

Set step 2

Go to before of "getName"

#|en| We should also create a constructor that accepts the initial value of the name.

Print:
```

  public function __construct($name) {
    $this->name = $name;
  }
```

Set step 3

Select "private $customer"

#|en| Now we can change the type of the <code>Customer</code> field. We should also change the associated methods so that they work with instances of the <code>Customer</code> class.

#|en| Let's start with changing the type of the customer field.

Select "String" in "Order"

Replace "Customer"

Set step 4

#|en| Now we will make the getter for the user name return the value from the associated object.

Select "return |||$this->customer|||" in "getCustomer"

Replace "$this->customer->getName()"

Select name of "__construct"
+ Select name of "setCustomer"

#|en| Then change the constructor and access setter so that they fill the customer field with a new <code>Customer</code> object.

Select "= |||$customer|||" in "__construct"
+ Select "= |||$customer|||" in "setCustomer"

Replace "new Customer($customer)"

Select name of "setCustomer"

#|en| Note that the setter creates a new instance of the customer class each time. That makes the customer is value object. In other words, if there are two orders made by single customer, the orders will still have two separate customer objects.

#|en| Value objects should be made immutable to avoid certain unpleasant errors related to the fact that objects are always passed via references. By the way, later we will still need to convert <code>Customer</code> to a reference object, but that's out of current refactoring scope.

#C|ru| В данный момент можно выполнить тестирование.
#S Всё отлично, код работает корректно.

#C|en| Now we can perform testing.
#S Everything is OK! Code works correctly.

#C|uk| В даний момент можна виконати тестування.
#S Все добре, код працює коректно.

Go to name of "Order"

#|en| All we have left now is to look at <code>Order</code> methods which work with <code>Customer</code> and make a few small changes in them.

Select name of "getCustomer"

#|en| FIrst, we apply <a href="/rename-method">Rename Method</a> to the getter to make clear that it returns a name, not an object.

Replace "getCustomerName"

Wait 500ms

Select "$order->|||getCustomer|||"

Replace "getCustomerName"

Select "$customer" in "__construct"
+ Select "$customer" in "setCustomer"

#|en| It also is a good idea to change the names of the parameters in the constructor and setter.

Replace "$customerName"

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

#|en| Before we finish, note that here and in many other cases, one more step is necessary. You may need to add a credit score, address, etc. to the <code>Customer</code>. You cannot do this yet, since <code>Customer</code> is used as a value object. That is, each order has its own instance of the <code>Customer</code> class.

#|en| To create the necessary attributes in the <code>Customer</code> class, use the <a href="/change-value-to-reference">Change Value to Reference</a> refactoring technique on it. After that refactoring, all orders for the same customer will refer to the same instance of the <code>Customer</code> class.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
