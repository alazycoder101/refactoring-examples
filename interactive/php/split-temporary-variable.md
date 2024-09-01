split-temporary-variable:php

###

1.en. Find the place in your code where the variable is first filled with a value. In this place, rename the variable and give it a name that corresponds to the value assigned.

2.en. Use the new name instead of the old one in places where this value of the variable is used.

3.en. Repeat as needed for places where the variable is assigned a different value.



###

```
public function getDistanceTravelled($time) {
  $result = 0;;
  $acc = $this->primaryForce / $this->mass;
  $primaryTime = Math.min($time, $this->delay);
  $result = 0.5 * $acc * $primaryTime * $primaryTime;

  $secondaryTime = $time - $this->delay;
  if ($secondaryTime > 0) {
    $primaryVel = $acc * $this->delay;
    $acc = ($this->primaryForce + $this->secondaryForce) / $this->mass;
    $result += $primaryVel * $secondaryTime + 0.5 * $acc * $secondaryTime * $secondaryTime;
  }
  return $result;
}
```

###

```
public function getDistanceTravelled($time) {
  $result = 0;;
  $primaryAcceleration = $this->primaryForce / $this->mass;
  $primaryTime = Math.min($time, $this->delay);
  $result = 0.5 * $primaryAcceleration * $primaryTime * $primaryTime;

  $secondaryTime = $time - $this->delay;
  if ($secondaryTime > 0) {
    $primaryVel = $primaryAcceleration * $this->delay;
    $secondaryAcceleration = ($this->primaryForce + $this->secondaryForce) / $this->mass;
    $result += $primaryVel * $secondaryTime + 0.5 * $secondaryAcceleration * $secondaryTime * $secondaryTime;
  }
  return $result;
}
```

###

Set step 1

#|en| Let's look at <i>Split Temporary Variable</i> using a small method, which calculates the movement of a ball in space as a function of time and the forces acting on it.

Select "|||$acc||| ="

#|en|^ Notably for our example, the <code>acc</code> variable is set in it twice.

#|en|+ It performs two tasks: it contains the initial acceleration caused by the first force…

#|en|^= …and later acceleration caused by both forces.

#|en|^ So it is better to split up this variable, with each part responsible for only one task.

Select 1st "$acc"

#|en| Start by changing the name of the variable. For this purpose, it is convenient to select a name that reflects the first use of the variable.

Print "$primaryAcceleration"

Set step 2

Select "$result = 0.5 * |||$acc|||"
+ Select "|||$acc||| * $this->delay"

#|en| Then we should rename the variable in all places where it is used, including the place where the new value is assigned.

Print "$primaryAcceleration"

Select "$acc"

#|en| This was the last replacement. All that's left now is the second case of variable use.

#C|ru| Запустим тестирование.
#S Всё отлично, можем продолжать!

#C|en| Let's launch autotests.
#S Everything is OK! We can keep going.

#C|uk| Запустимо тестування.
#S Все добре, можна продовжувати.

Set step 3

Select 1st "|||$acc||| "

#|en| Now we can repeat all these actions with the second assignment of a temporary variable. We remove the initial name of the variable once and for all, and then replace it with a new name that fits the second task.

Print "$secondaryAcceleration"

Wait 500ms

Select " |||$acc||| "

Replace "$secondaryAcceleration"

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
