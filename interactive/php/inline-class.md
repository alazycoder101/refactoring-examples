inline-class:php

###

1.en. In the recipient class, create the public fields and methods present in the donor class. Methods should refer to the equivalent methods of the donor class.

2.en. Replace all references to the donor class with references to the fields and methods of the recipient class.

3.en. Use <a href="/move-field">Move Field</a> and <a href="/move-method">Move Method</a> for moving functionality from the original class to the recipient class. Continue doing so until nothing remains in the original class.

4.en. Delete the original class.



###

```
class Person {
  private $name;
  private $officeTelephone; // TelephoneNumber
  
  public function __construct() {
    $this->officeTelephone = new TelephoneNumber();
  }
  
  public function getName() {
    return $this->name;
  }
  public function getOfficeTelephone() {
    return $this->officeTelephone;
  }
  public function getTelephoneNumber() {
    return $this->officeTelephone->getTelephoneNumber();
  }
}

class TelephoneNumber {
  private $areaCode;
  private $number;

  public function getAreaCode() {
    return $this->areaCode;
  }
  public function setAreaCode($arg) {
    $this->areaCode = $arg;
  }
  public function getNumber() {
    return $this->number;
  }
  public function setNumber($arg) {
    $this->number = $arg;
  }
  public function getTelephoneNumber() {
    return ("(" . $this->areaCode . ") " . $this->number);
  }
}

// Somewhere in client code
$martin = new Person();
$martin->getOfficeTelephone()->setAreaCode("781");
```

###

```
class Person {
  private $name;
  private $areaCode;
  private $number;
  
  public function getName() {
    return $this->name;
  }
  public function getTelephoneNumber() {
    return ("(" . $this->areaCode . ") " . $this->number);
  }
  public function getAreaCode() {
    return $this->areaCode;
  }
  public function setAreaCode($arg) {
    $this->areaCode = $arg;
  }
  public function getNumber() {
    return $this->number;
  }
  public function setNumber($arg) {
    $this->number = $arg;
  }
}


// Somewhere in client code
$martin = new Person();
$martin->setAreaCode("781");
```

###

Set step 1

#|en| Let's look at <i>Inline Class</i> using the person class and its phone number as an example.

Select name of "TelephoneNumber"

#|en| We want to include the <code>TelephoneNumber</code> class back in the <code>Person</code> class, since it become unnecessary complex for our needs.

Go to the end of "Person"

#|en| We start by declaring all visible methods of the phone number class in the <code>Person</code> class.

Print:
```

  public function getAreaCode() {
    return $this->officeTelephone->getAreaCode();
  }
  public function setAreaCode($arg) {
    $this->officeTelephone->setAreaCode($arg);
  }
  public function getNumber() {
    return $this->officeTelephone->getNumber();
  }
  public function setNumber($arg) {
    $this->officeTelephone->setNumber($arg);
  }
```

Select "officeTelephone" in "getAreaCode"
+Select "officeTelephone" in "setAreaCode"
+Select "officeTelephone" in "getNumber"
+Select "officeTelephone" in "setNumber"

#|en| For the first step, all these methods will delegate to the phone number object.

Set step 2

Select "$martin->getOfficeTelephone()->setAreaCode("781")"

#|en| Now find all cases where the phone number class is used in client code and replace it with calls to the delegate methods in <code>Person</code>.

Print "$martin->setAreaCode("781")"

Set step 3

#|en| We can then proceed to <a href="/move-method">Move Method</a> and <a href="/move-field">Move Field</a> for moving all fields and methods to the <code>Person</code> class. These changes can be done one by one or, if there are not too many, all at once.

Select:
```
  private $areaCode;
  private $number;


```

#|en| First move the fields.

Remove selected

Go to "private $name;|||"

Print:
```

  private $areaCode;
  private $number;
```

Select body of "getAreaCode" in "TelephoneNumber"

#|en| Then move each method…

Wait 500ms

Select body of "getAreaCode" in "Person"

Replace:
```
    return $this->areaCode;
```

Select whole of "getAreaCode" in "TelephoneNumber"

Remove selected


Select body of "setAreaCode" in "TelephoneNumber"

#|en| …one by one…

Wait 500ms

Select body of "setAreaCode" in "Person"

Replace:
```
    $this->areaCode = $arg;
```

Select whole of "setAreaCode" in "TelephoneNumber"

Remove selected


Select body of "getNumber" in "TelephoneNumber"

#|en| …move all the methods…

Wait 500ms

Select body of "getNumber" in "Person"

Replace:
```
    return $this->number;
```

Select whole of "getNumber" in "TelephoneNumber"

Remove selected

Select body of "setNumber" in "TelephoneNumber"

#|en| …each and every one…


Wait 500ms

Select body of "setNumber" in "Person"

Replace:
```
    $this->number = $arg;
```

Select whole of "setNumber" in "TelephoneNumber"

Remove selected

Select body of "getTelephoneNumber" in "TelephoneNumber"

#|en| …and finally the last getter of the phone number itself.

Wait 500ms

Select body of "getTelephoneNumber" in "Person"

Replace:
```
    return ("(" . $this->areaCode . ") " . $this->number);
```

Select whole of "getTelephoneNumber" in "TelephoneNumber"

Remove selected

#C|ru| Запустим тесты, чтобы убедиться, что код всё ещё работает исправно.
#S Всё хорошо, можно продолжать.

#C|en| Let's run autotests to make sure that the code is still in working order.
#S All is well, so let's continue.

#C|uk| Запустимо тести, щоб переконатися, що код все ще працює справно.
#S Все добре, можна продовжувати.

Set step 4

Select whole "TelephoneNumber"

#|en| At this point, we need only to remove the <code>TelephoneNumber</code> class from the program.

Select:
```
  private $officeTelephone; // TelephoneNumber
  
  public function __construct() {
    $this->officeTelephone = new TelephoneNumber();
  }

```
+ Select whole "getOfficeTelephone"

#|en| Start by removing its field and getter in the <code>Person</code> class.

Remove selected

Select whole "TelephoneNumber"

#|en| Voila! Nothing is holding us back now from removing the class itself. Thank you for the good times, <code>TelephoneNumber</code>, they were good indeed!

Remove selected

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
