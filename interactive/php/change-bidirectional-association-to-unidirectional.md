change-bidirectional-association-to-unidirectional:php

###

1.en. Make sure that one of the following is true for your classes:<ul><li>Association is not used at all,</li><li>Another way of getting the associated object is available (such as by querying a database), or</li><li>The association object can be passed as an argument to the methods that use it.</li></ul>

2.en. Depending on your situation, instead of using a field containing an association with the relevant object, you may want to use a parameter or method call for obtaining the associated object in a different way.

3.en. Delete the code that assigns the associated object to the field.

4.en. Delete the now-unused field.



###

```
class Order {
  // ...
  private $customer; // Customer

  public function getCustomer() {
    return $this->customer;
  }
  public function setCustomer(Customer $arg) {
    // Remove order from old customer.
    if ($this->customer != null) {
      $this->customer->friendOrders()->remove($this);
    }
    $this->customer = $arg;
    // Add order to new customer.
    if ($this->customer != null) {
      $this->customer->friendOrders()->add($this);
    }
  }

  public function getDiscountedPrice() {
    return $this->getGrossPrice() * (1 - $this->getCustomer()->getDiscount());
  }
}

class Customer {
  // ...
  private $orders = array();

  // Should be used in Order class only.
  public function friendOrders() {
    return $orders;
  }
  public function addOrder(Order $arg) {
    $arg->setCustomer($this);
  }

  public function getPriceFor(Order $order) {
     assert(array_search($order, $this->orders, TRUE), "Order can not be found");
     return $order->getDiscountedPrice();
  }
}
```

###

```
class Order {
  // ...
  public function getCustomer() {
    foreach (Customer::getInstances() as $customer) {
      if ($customer->containsOrder($this)) {
        return $customer;
      }
    }
    return null;
  }

  public function getDiscountedPrice() {
    return $this->getGrossPrice() * (1 - $this->getCustomer()->getDiscount());
  }
}

class Customer {
  // ...
  private $orders = array();

  public function addOrder(Order $arg) {
    $this->orders[] = $arg;
  }

  public function getPriceFor(Order $order) {
     assert(array_search($order, $this->orders, TRUE), "Order can not be found");
     return $order->getDiscountedPrice();
  }
}
```

###

Set step 1

#|en| We will start <i>Change Bidirectional Association to Unidirectional</i> from the place where we have stopped in the inverse refactoring.

Select name of "Order"
+ Select name of "Customer"

#|en| In other words, we have <code>Customer</code> and <code>Order</code> classes with a bidirectional association.

#|en| Two new methods have been added to the code since completion of the previous refactoring.

Select name of "getPriceFor"

#|en|V First, the method for getting order price in the customer object.

+ Select name of "getDiscountedPrice"

#|en| Then the method for getting price with discount in the order class.

Select "private |||$customer|||; // Customer"

#|en| Few days ago a new requirement was received, which says that orders must only be created only for existing customers. This lets us eliminate the bidirectional association between orders and customer, and only keeping customers aware of their orders.

Select "|||$this->getCustomer()|||->getDiscount()"

#|en|+ The hardest part of this refactoring technique is making sure that it is possible. Refactoring itself is easy, but we must make sure that it is safe. The problem comes down to whether any of order class' code needs a customer field. If that is the case, removing the field requires you to provide an alternative method for getting the customer object.

Set step 2

#|en|^= First, we review all usages of the customer field and it's getter. Is there another way to provide the customer object or it's data? Often thу best solution means passing the customer as an argument to the methods, which use the field.

Go to parameters of "getDiscountedPrice"

Print "Customer $customer"

Wait 500ms

Select "|||$this->getCustomer()|||->getDiscount()"

Replace "$customer"

Select:
```
     return $order->getDiscountedPrice();
```

#|en| This works particularly well for methods that called by client code already containing a customer object. In this case, you just pass it as a method's argument.

Go to:
```
     return $order->getDiscountedPrice(|||);
```

Print "$this"

Wait 1000ms

Select body of "getCustomer"

#|en| Another alternative is to change the customer field getter so that it would get the right object by looking it in some object repository like this:

Print:
```
    foreach (Customer::getInstances() as $customer) {
      if ($customer->containsOrder($this)) {
        return $customer;
      }
    }
    return null;
```

Select parameters of "getDiscountedPrice"

#|en| The previous introduction of a parameter into the method can now be removed since the customer getter will return the correct object.

Remove selected

Select "|||$customer|||->getDiscount()"

Replace "$this->getCustomer()"

Wait 500ms

Select "getDiscountedPrice(|||$this|||);"

Remove selected

Select name of "getCustomer"

#|en| Slow… But it works. In the context of a database, things may even become a little faster if a database query is used.

Set step 3

Select:
```
  // Should be used in Order class only.

```
+ Select whole "friendOrders"
+ Select body of "addOrder"

#|en| Now prepare to remove the <code>setCustomer</code> method by replacing their calls in the code of the customer class with direct addition of order objects to the collection.

Select:
```
  // Should be used in Order class only.

```
+ Select whole "friendOrders"

Remove selected

Select body of "addOrder"

Type:
```
    $this->orders[] = $arg;
```

Select whole "setCustomer"

#|en| Then we can freely remove the method in the order class.

Remove selected

Set step 4

Select:
```
  private $customer; // Customer


```

#|en| At last, we can remove the field itself, fully eliminating the bidirectional association between the classes.

Remove selected

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
