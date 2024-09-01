preserve-whole-object:php

###

1.en. Create a parameter in the method for the object from which you can get the necessary values.

2.en. Now start removing the old parameters from the method one by one, replacing them with calls to the relevant methods of the parameter object. Test the program after each replacement of a parameter.

3.en. Delete the getter code from the parameter object that had preceded the method call.



###

```
class Room {
  // ...
  public function withinPlan(HeatingPlan $plan) {
    $low = $this->getLowestTemp();
    $high = $this->getHighestTemp();
    return $plan->withinRange($low, $high);
  }
}

class HeatingPlan {
  private $range; // TempRange
  public function withinRange($low, $high) {
    return ($low >= $range->getLow() && $high <= $range->getHigh());
  }
}
```

###

```
class Room {
  // ...
  public function withinPlan(HeatingPlan $plan) {
    return $plan->withinRange($this);
  }
}

class HeatingPlan {
  private $range; // TempRange
  public function withinRange(Room $room) {
    return ($room->getLowestTemp() >= $range->getLow() && $room->getHighestTemp() <= $range->getHigh());
  }
}
```

###

Set step 1

#|en| Let's look at this refactoring using the class that describes hotel rooms and logs their daily temperature.

Select "$plan->withinRange"

#|en| The class should analyze room's micro climate and react with certain actions. For now, only temperature is compared with a predefined temperature plan. Then, depending on the results of the comparison, class could issue a heat or cool command, or even send an email to the house owner if the temperature is dangerously high.

Select "$low, $high" in "withinPlan"

#|en| Currently, we are passing only the temperature for analysis but at any time we may need to check another room parameter, such as humidity.

#|en| With current implementation, we would have to add more and more parameters to the method. To avoid this, we can pass the entire room object instead of specific values. That will allow to take any room data straight from the room object, without changing signature of the method.

Go to parameters of "withinRange"

#|en| So for the first step, we add a parameter to the <code>withinRange</code> method.

Print "Room $room, "

Go to "$plan->withinRange(|||"

Print "$this, "

Set step 2

#|en| One by one, we should remove parameters with data, that could be retrieved from the object we pass into the method.

Select ", $high" in parameters of "withinRange"

Wait 250ms

Remove selected

Select "&& |||$high|||"

Replace "$room->getHighestTemp()"

Wait 500ms

Select ", $high"

Remove selected

#C|ru| Запускаем тестирование, а затем повторяем действия для оставшегося параметра.
#S Отлично, все работает, продолжаем!

#C|en| Test and then repeat these actions for the remaining parameter.
#S Everything is good! Let's continue.

#C|uk| Запускаємо тестування, а потім повторюємо дії для залишився параметра.
#S Супер, все працює, продовжуємо.

Select ", $low" in parameters of "withinRange"

Wait 500ms

Remove selected

Select "|||$low||| >="

Replace "$room->getLowestTemp()"

Wait 500ms

Select ", $low"

Remove selected


#C|ru| Запускаем тестирование ещё раз, чтобы убедиться, что код остался рабочим.
#S Тесты успешно проходят!

#C|en| Test again to make sure the code still works.
#S The tests are completed successfully!

#C|uk| Запускаємо тестування ще раз, щоб переконатися, що код залишився робочим.
#S Тести успішно проходять.

Select:
```
    $low = $this->getLowestTemp();
    $high = $this->getHighestTemp();

```

Set step 3

#|en| And finally, let's remove the unused variables from <code>withinPlan</code>.

Remove selected

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
