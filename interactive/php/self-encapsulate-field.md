self-encapsulate-field:php

###

1.en. Create a getter (and optional setter) for the field.

2.en. Find all direct invocations of the field and replace them with getter and setter calls.



###

```
class IntRange {
  private $low;
  private $high;

  public function includes($arg) {
    return $arg >= $this->low && $arg <= $this->high;
  }
  public function grow($factor) {
    $this->high = $this->high * $factor;
  }
  public function __construct($low, $high) {
    $this->low = $low;
    $this->high = $high;
  }
}
```

###

```
class IntRange {
  private $low;
  private $high;

  public function getLow() {
    return $this->low;
  }
  public function getHigh() {
    return $this->high;
  }
  public function setLow($arg) {
    $this->low = $arg;
  }
  public function setHigh($arg) {
    $this->high = $arg;
  }
  public function includes($arg) {
    return $arg >= $this->getLow() && $arg <= $this->getHigh();
  }
  public function grow($factor) {
    $this->setHigh($this->getHigh() * $factor);
  }
  public function __construct($low, $high) {
    $this->low = $low;
    $this->high = $high;
  }
}
```

###

Set step 1

#|en| Let's look at <i>Self-Encapsulation</i> using the example of a range class.<br/><br/>Self-encapsulation differs from regular encapsulation by requiring even its own class to access fields through getters and setters.

Go to before "includes"

#|en| These access methods must be created if they do not yet exist. So go ahead and create getters and setters in our class.

Print:
```

  public function getLow() {
    return $this->low;
  }
  public function getHigh() {
    return $this->high;
  }
  public function setLow($arg) {
    $this->low = $arg;
  }
  public function setHigh($arg) {
    $this->high = $arg;
  }
```

Set step 2

Select "low" in "includes"
+ Select "high" in "includes"
+ Select "high" in "grow"

#|en| Our example has several methods that use direct access to fields.

#|en| To finish self-encapsulation, let's replace all references to fields in these methods with getter and setter calls.

Select "low" in "includes"

Replace "getLow()"

Wait 500ms

Select "high" in "includes"

Replace "getHigh()"

Wait 500ms

Select "high = " in "grow"

Replace "setHigh("

Wait 500ms

Go to "|||;" in "grow"

Print ")"

Wait 500ms

Select "($this->|||high|||" in "grow"

Replace "getHigh()"

Select "$this->low" in "__construct"
+Select "$this->high" in "__construct"

#|en| As you may have noticed, we did not touch the assignment in the constructor. It is often assumed that a setter is used after an object has already been created, so its behavior may be different than during initialization.

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
