remove-control-flag:php

###

1.en. Find the value assignment to the control flag that causes the exit from the loop or current iteration.

2.en. Replace it with <code>break</code> if exiting a loop, <code>continue</code> if exiting an iteration, or <code>return</code> if needing to return a value from the function.

3.en. Remove the remaining code and checks associated with the control flag.



###

```
function checkSecurity(array $people) {
  $found = false;
  for ($i = 0; $i < count($people); $i++) {
    if (!$found) {
      if ($people[$i] == "Don") {
        sendAlert();
        $found = true;
      }
      if ($people[$i] == "John") {
        sendAlert();
        $found = true;
      }
    }
  }
}
```

###

```
function checkSecurity(array $people) {
  for ($i = 0; $i < count($people); $i++) {
    if ($people[$i] == "Don") {
      sendAlert();
      break;
    }
    if ($people[$i] == "John") {
      sendAlert();
      break;
    }
  }
}
```

###

Set step 1

#|en| The following function checks whether the list of people contains anybody suspicious; these suspicious names (Don and John) are hard-coded.

Select "|||$found||| = false"

#|en| In this function, the variable <code>found</code> is a control flag. It is initialized by one value…

Select "|||$found||| = true"

#|en| …which changes as the function is run…

Select "(!$found)"

#|en| …after which the code does not do anything more until the loop is finished.

Select "$found = true"

#|en| This refactoring starts with us looking for any assignments to the control variable that affect the execution flow of the program. In our case, this is assignments of the <code>true</code> value.

Set step 2

#|en| According to the logic of this method, we can simply replace assignments to control flags with <code>break</code> operator.

Print "break"

Set step 3

#|en| Then we can remove all other mentions of the control flag.


Select:
```
    if (!$found) {

```

+ Select:
```
      }
|||    }
|||  }
}
```

Remove selected

Select "      "

Replace "    "

Wait 500ms

Select:
```
  $found = false;

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
