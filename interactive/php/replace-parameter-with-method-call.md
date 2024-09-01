replace-parameter-with-method-call:php

###

1.en. Make sure that the value-getting code does not use parameters from the current method, since they will be unavailable from inside another method. If so, moving the code is not possible.

2.en. If the relevant code is more complicated than a single method or function call, use <a href="/extract-method">Extract Method</a> to isolate this code in a new method and make the call simple.

3.en. In the code of the main method, replace all references to the parameter being replaced with calls to the method that gets the value.

4.en. Use <a href="/remove-parameter">Remove Parameter</a> to eliminate the now-unused parameter.



###

```
class Order {
  // ...
  public function getPrice() {
    $basePrice = $this->quantity * $this->itemPrice;
    if ($this->quantity > 100) {
      $discountLevel = 2;
    }
    else {
      $discountLevel = 1;
    }
    $finalPrice = $this->discountedPrice($basePrice, $discountLevel);
    return $finalPrice;
  }
  private function discountedPrice($basePrice, $discountLevel) {
    if ($discountLevel == 2) {
      return $basePrice * 0.1;
    }
    else {
      return $basePrice * 0.05;
    }
  }
}
```

###

```
class Order {
  // ...
  public function getPrice() {
    return $this->discountedPrice();
  }
  private function discountedPrice() {
    if ($this->getDiscountLevel() == 2) {
      return $this->getBasePrice() * 0.1;
    }
    else {
      return $this->getBasePrice() * 0.05;
    }
  }
  private function getDiscountLevel() {
    if ($this->quantity > 100) {
      return 2;
    }
    else {
      return 1;
    }
  }
  private function getBasePrice() {
    return $this->quantity * $this->itemPrice;
  }
}
```

###

Set step 1

#|en| Let's look at this refactoring using yet another order price example.

Select name of "getPrice"
+Select name of "discountedPrice"

#|en|^ The method for getting the discount (<code>discountedPrice</code>) is currently nearly impossible to use separately from the method for getting the price (<code>getPrice</code>), since you must get the values of all parameters prior to it.

Select parameters of "discountedPrice"

#|en| But what if we eliminate all parameters in <code>discountedPrice</code>? Let's try.

Select:
```
    if ($this->quantity > 100) {
      $discountLevel = 2;
    }
    else {
      $discountLevel = 1;
    }

```

Set step 2

#|en| To start, we extract <code>discountLevel</code> to its own method.

Go to after "discountedPrice"

Print:
```

  private function getDiscountLevel() {
    if ($this->quantity > 100) {
      return 2;
    }
    else {
      return 1;
    }
  }
```

Set step 3

Select "$discountLevel" in body of "discountedPrice"

#|en| Now we can use this method instead of this parameter in the discount calculation method.

Print "$this->getDiscountLevel()"

Set step 4

Select ", $discountLevel" in parameters of "discountedPrice"

#|en| One of the parameters is no longer needed so we can use <a href="/remove-parameter">Remove Parameter</a>

Remove selected

Wait 500ms

Select ", $discountLevel"

Wait 500ms

Remove selected

Select:
```
    if ($this->quantity > 100) {
      $discountLevel = 2;
    }
    else {
      $discountLevel = 1;
    }

```

#|en| We can then remove parameter calculation, which is no longer used.

Remove selected

#C|ru| Запускаем тестирование.
#S Отлично, все работает, продолжаем!

#C|en| Let's start testing.
#S Everything is good! Let's continue.

#C|uk| Запускаємо тестування.
#S Супер, все працює, продовжуємо.

Select parameters of "discountedPrice"

#|en| One parameter, one more to go…

Select "$this->quantity * $this->itemPrice"

#|en| Let's extract the base price calculation to its own method.

Go to after "getDiscountLevel"

Print:
```

  private function getBasePrice() {
    return $this->quantity * $this->itemPrice;
  }
```

Select "$basePrice" in body of "discountedPrice"

#|en| Now use this method in <code>discountedPrice</code>.

Print "$this->getBasePrice()"

Wait 250ms

Select "$basePrice" in parameters of "discountedPrice"

#|en| As before, we can now get rid of this parameter as well.

Remove selected

Select "discountedPrice(|||$basePrice|||)"

Wait 500ms

Remove selected

Wait 500ms

Select:
```
    $basePrice = $this->quantity * $this->itemPrice;

```

#|en| Then clean up the code of the method for getting the price…

Remove selected

Select body of "getPrice"

#|en| …or if we make it a bit more pretty:

Print "    return $this->discountedPrice();"


#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
