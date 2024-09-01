extract-variable:php

###

1.en. Create a local variable and give it the necessary value.

2.en. Replace the original expression with your new variable.



###

```
public function price() {
  // Price consists of: base price - discount + shipping cost
  return $this->quantity * $this->itemPrice -
    Math.max(0, $this->quantity - 500) * $this->itemPrice * 0.05 +
    Math.min($this->quantity * $this->itemPrice * 0.1, 100.0);
}
```

###

```
public function price() {
  $basePrice = $this->quantity * $this->itemPrice;
  $quantityDiscount = Math.max(0, $this->quantity - 500) * $this->itemPrice * 0.05;
  $shipping = Math.min($basePrice * 0.1, 100.0);
  return $basePrice - $quantityDiscount + $shipping;
}
```

###

Set step 1

#|en| Let's look at <i>Extract Variable</i> using this simple method as an example.

#|en| As you see, the method contains a single enormous expression capable of throwing off both police dogs and the most determined programmers.

#|en| Let's split this expression into separate parts, placing each part in a separate variable.

Select "$this->quantity * $this->itemPrice"

#|en| First define the <code>basePrice</code> as the number of products in the order, multiplied by the unit cost…

Set step 2

Go to "shipping cost|||"

Print:
```

  $basePrice = $this->quantity * $this->itemPrice;
```

Select 2nd "$this->quantity * $this->itemPrice"
+ Select 3rd "$this->quantity * $this->itemPrice"

#|en| …and use the new variable in the formula. The expression is repeated several times so we will replace all identical calculations with the variable.

Print "$basePrice"

#C|ru| Давайте запустим тесты и удостоверимся, что все хорошо.
#S Всё отлично, можем продолжать!

#C|en| Let's test and make sure that everything is OK.
#S Everything is OK! We can keep going.

#C|uk| Давайте запустимо тести і переконався, що все добре.
#S Все добре, можемо продовжувати.

Select:
```
    Math.max(0, $this->quantity - 500) * $this->itemPrice * 0.05 +
    Math.min($basePrice * 0.1, 100.0);
```

#|en| Now replace the remaining parts of the complex expression with variables.

Select:
```
Math.max(0, $this->quantity - 500) * $this->itemPrice * 0.05
```

#|en| Define the <code>quantityDiscount</code> and move calculation to a new variable.

Go to "$this->quantity * $this->itemPrice;|||"

Print:
```

  $quantityDiscount = Math.max(0, $this->quantity - 500) * $this->itemPrice * 0.05;
```

Select:
```

    Math.max(0, $this->quantity - 500) * $this->itemPrice * 0.05
```

Replace " $quantityDiscount"

#C|ru| Готово, теперь запустим тесты и удостоверимся, что не возникло ошибок.
#S Всё отлично, можем продолжать!

#C|en| All done! Let's test for any possible errors.
#S Everything is OK! We can keep going.

#C|uk| Готово, тепер запустимо тести і переконався, що не виникло помилок.
#S Все добре, можемо продовжувати.

Select:
```
Math.min($basePrice * 0.1, 100.0)
```

#|en| The final part of calculation is <code>shipping</code>. We use a separate variable here as well.

Go to "$this->itemPrice * 0.05;|||"

Print:
```

  $shipping = Math.min($basePrice * 0.1, 100.0);
```

Select:
```

    Math.min($basePrice * 0.1, 100.0)
```

Replace " $shipping"

Select:
```
  // Price consists of: base price - discount + shipping cost

```

#|en| Since the expression is now obvious and intuitive, we can remove the comment.

Remove selected

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
