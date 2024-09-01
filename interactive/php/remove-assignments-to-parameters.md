remove-assignments-to-parameters:php

###

1.en. Create a local variable and assign the initial value of your parameter.

2.en. In all method code that follows this line, replace the parameter with your new local variable.



###

```
public function discount($inputVal, $quantity, $yearToDate) {
  if ($inputVal > 50) {
    $inputVal -= 2;
  }
  if ($quantity > 100) {
    $inputVal -= 1;
  }
  if ($yearToDate > 10000) {
    $inputVal -= 4;
  }
  return $inputVal;
}
```

###

```
public function discount($inputVal, $quantity, $yearToDate) {
  $result = $inputVal;
  if ($inputVal > 50) {
    $result -= 2;
  }
  if ($quantity > 100) {
    $result -= 1;
  }
  if ($yearToDate > 10000) {
    $result -= 4;
  }
  return $result;
}
```

###

Set step 1

#|en| Let's look at <i>Remove Assignments to Parameters</i> using a small discount calculation method as an example.

Select "$inputVal" in parameters of "discount"

#|en|+ Take a look at the <code>inputVal</code> parameter.

Select 3nd "$inputVal"
+Select 4th "$inputVal"
+Select 5th "$inputVal"

#|en|^= The initial value of this parameter changes inside the method body.

Set step 2

#|en|^ Let's replace usage of the parameter with a new variable. We will be changing that variable's value instead of the parameter and then return it as the result of our method.

Go to the start of "discount"

Print:
```

  $result = $inputVal;
```

Select "$result = $inputVal"

#|en| First, we initialize our variable with the parameter value.

Select 4th "$inputVal"
+Select 5th "$inputVal"
+Select 6th "$inputVal"
+Select 7th "$inputVal"

#|en| Then, we should replace all references to the parameter in method's body with the variable that we have created.

Print "$result"

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
