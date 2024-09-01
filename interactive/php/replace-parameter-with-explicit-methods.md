replace-parameter-with-explicit-methods:php

###

1.en. For each variant of the method, create a separate method. Run these methods based on the value of a parameter in the main method.

2.en. Find all places where the original method is called. In these places, place a call for one of the new parameter-dependent variants.

3.en. When no calls to the original method remain, delete it.



###

```
class Order {
  // ...
  const FIXED_DISCOUNT = 0;
  const PERCENT_DISCOUNT = 1;

  public function applyDiscount($type, $discount) {
    switch ($type) {
      case Order::FIXED_DISCOUNT:
        $this->price -= $discount;
        break;
      case Order::PERCENT_DISCOUNT:
        $this->price *= $discount;
        break;
      default:
        throw new Exception('Invalid discount type');
    }
  }
}

// Somewhere in client code
if ($weekend) {
  $order->applyDiscount(Order::FIXED_DISCOUNT, 10);
}
if (count($order->items) > 5) {
  $order->applyDiscount(Order::PERCENT_DISCOUNT, 0.2);
}
```

###

```
class Order {
  // ...
  public function applyFixedDiscount($discount) {
    $this->price -= $discount;
  }
  public function applyPercentDiscount($discount) {
    $this->price *= $discount;
  }
}

// Somewhere in client code
if ($weekend) {
  $order->applyFixedDiscount(10);
}
if (count($order->items) > 5) {
  $order->applyPercentDiscount(0.2);
}
```

###

Set step 1

Select name of "Order"

#|en| Let's look at this technique using an order class as an example.

Select name of "applyDiscount"

#|en| This class has a method for applying discounts that handle both fixed discounts and percentage-based ones.

#|en| Let's start refactoring by extracting each version to a separate method.

Select "$this->price -= $discount;"

Wait 1000ms

Go to after "applyDiscount"

Print:
```

  public function applyFixedDiscount($discount) {
    $this->price -= $discount;
  }
```

Select "$this->price *= $discount;"

Wait 1000ms

Go to after "applyFixedDiscount"

Print:
```

  public function applyPercentDiscount($discount) {
    $this->price *= $discount;
  }
```

Set step 2

#|en| Now find all places where the original method is called, replacing them with calls to our new methods.

Select "applyDiscount(Order::FIXED_DISCOUNT, "

Replace "applyFixedDiscount("

Wait 1000ms

Select "applyDiscount(Order::PERCENT_DISCOUNT, "

Replace "applyPercentDiscount("

#C|ru| Запускаем тестирование, чтобы убедиться в отсутствии ошибок.
#S Отлично, все работает!

#C|en| Let's launch autotests to check for errors in code.
#S Wonderful, it's all working!

#C|uk| Запускаємо тестування, щоб переконатися у відсутності помилок.
#S Супер, все працює.

Set step 3

#|en| Once changes are complete, remove the original method and now-useless constants.

Select whole "applyDiscount"

Remove selected

Wait 1000ms

Select:
```
  const FIXED_DISCOUNT = 0;
  const PERCENT_DISCOUNT = 1;


```
Remove selected

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
