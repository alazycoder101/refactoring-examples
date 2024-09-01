replace-temp-with-query:php

###

1.en. Make sure that a value is assigned to the variable once and only once within the method.

2.en. Use <b>extract method</b> to move the expression in question to the new method.

3.en. Replace the variable with a query to your new method.



###

```
class Product {
  // ...
  function getPrice() {
    $basePrice = $this->quantity * $this->itemPrice;
    if ($basePrice > 1000) {
      $discountFactor = 0.95;
    }
    else {
      $discountFactor = 0.98;
    }
    return $basePrice * $discountFactor;
  }
}
```

###

```
class Product {
  // ...
  function getPrice() {
    return $this->basePrice() * $this->discountFactor();
  }
  private function basePrice() {
    return $this->quantity * $this->itemPrice;
  }
  private function discountFactor() {
    if ($this->basePrice() > 1000) {
      return 0.95;
    }
    else {
      return 0.98;
    }
  }
}
```

###

Set step 1

#|en| Let's look at <i>Replace Temp with Query</i> using a simple method as an example.

Select "|||$basePrice||| = "
+Select "|||$discountFactor||| ="

#|en| Replace the variables <code>basePrice</code> and <code>discountFactor</code> one by one with calls to the respective methods.

Select "$basePrice = "

#|en| First, make sure that there is just one value assignment to the variable within the method.

#|en| This is all true in our case, so we can continue.

Set step 2

Select "$basePrice = $this->quantity * $this->itemPrice"

#|en| For the second step, we create a <code>basePrice()</code> method and move the expression forming the <code>basePrice</code> variable to it.

Go to the end of "Product"

Print:
```

  private function basePrice() {
    return $this->quantity * $this->itemPrice;
  }
```

Select "$this->quantity * $this->itemPrice" in "getPrice"

#|en| Now we can use a method call instead of the initial expression. Thus, we now have a new method and all of the old code still works.

Print "$this->basePrice()"

Set step 3

Select "(|||$basePrice||| >"

#|en| Now is the perfect time to replace the variable with a direct method call.

#|en| Replace the first variable and then run autotests to make sure that nothing is broken.

Print "$this->basePrice()"

#C|ru| Запускаем тестирование.
#S Всё отлично, можем продолжать!

#C|en| Let's start testing.
#S Everything is OK! We can keep going.

#C|uk| Запускаємо тестування.
#S Все добре, можна продовжувати.

Select "return |||$basePrice|||"

#|en| Perform the next replacement.

Print "$this->basePrice()"

#C|ru| Запускаем тестирование.
#S Всё отлично, можем продолжать!

#C|en| Let's start testing.
#S Everything is OK! We can keep going.

#C|uk| Запускаємо тестування.
#S Все добре, можна продовжувати.

Select:
```
    $basePrice = $this->basePrice();

```

#|en| The previous replacement was the last one, so we can remove the variable declaration.

Remove selected

Select "$discountFactor"

#|en| The first variable is done. We can repeat all this to extract <code>discountFactor</code>.

Go to the end of "Product"

#|en| Create a new method…

Print:
```

  private function discountFactor() {
    if ($this->basePrice() > 1000) {
      return 0.95;
    }
    else {
      return 0.98;
    }
  }
```

Select in "getPrice":
```
    if ($this->basePrice() > 1000) {
      $discountFactor = 0.95;
    }
    else {
      $discountFactor = 0.98;
    }
```

#|en| …use it to initialize the variable and remove old code…

Print "    $discountFactor = $this->discountFactor();"

#|en| Note how difficult it would have been to extract <code>discountFactor</code> if we had not first replaced <code>basePrice</code> with a method call.

#|en| Ultimately the <code>getPrice()</code> method comes to look as follows.

Select:
```
    $discountFactor = $this->discountFactor();

```

Remove selected

Select "$discountFactor" in "getPrice"

Replace "$this->discountFactor()"

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
