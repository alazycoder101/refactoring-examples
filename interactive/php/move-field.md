move-field:php

###


1.en. If the field is public, encapsulate it.



2.en. Create a field copy and methods for accessing the field in the target class.



3.en. Decide how you will refer to the target class. It is quite possible that you already have a field or method that returns an appropriate class instance, but if not, you will need to create one of these.



4.en. Replace all references to the old class with the relevant calls to methods in the target class.



5.en. Delete the field in the original class.




###

```
class Account {
  // ...
  private $type; // AccountType
  private $interestRate;

  public function interestForAmount_days($amount, $days) {
    return $this->interestRate * $amount * $days / 365.0;
  }
}

class AccountType {
  // ...
}
```

###

```
class Account {
  // ...
  private $type; // AccountType

  public function interestForAmount_days($amount, $days) {
    return $this->getInterestRate() * $amount * $days / 365.0;
  }
  public function interestForBigFamily($familySize) {
    return $this->getInterestRate() / $familySize;
  }

  // other 10 methods, which use getInterestRate()

  private function getInterestRate() {
    return $this->type->getInterestRate();
  }
  private function setInterestRate($arg) {
    $this->type->setInterestRate($arg);
  }
}

class AccountType {
  // ...
  private $interestRate;

  public function getInterestRate() {
    return $this->interestRate;
  }
  public function setInterestRate($arg) {
    $this->interestRate = $arg;
  }
}
```

###

Set step 1

#|en| Let's look at <i>Move Field</i> using a bank account class as our example.

Select "private |||$interestRate|||"

#|en| We want to move the <code>interestRate</code> field to the <code>AccountType</code> class.

Select name of "interestForAmount_days"

#|en| Several methods refer to this field. One of them is the <code>interestForAmount_days()</code> method.

Set step 2

Go to the end of "AccountType"

#|en| Let's start by creating the same field and same access methods in the target class.

Print:
```

  private $interestRate;

  public function getInterestRate() {
    return $this->interestRate;
  }
  public function setInterestRate($arg) {
    $this->interestRate = $arg;
  }
```

#C|ru| На всякий случай запустим авто-тесты.
#S Все хорошо, можно продолжать.

#C|en| Just to be safe, let's run autotests.
#S All is well, so let's continue.

#C|uk| Про всякий випадок, запустимо авто-тести.
#S Все добре, можна продовжувати.

Set step 3

Select "private |||$type|||"

#|en| In our example, the <code>Account</code> class contains a field for accessing the account type object. For this reason, we can access the moved field through it.

Set step 4

#|en| We replace all references to the old field with appropriate calls to methods in the target class.

Select "interestRate" in "interestForAmount_days"

Replace "type->getInterestRate()"

Set step 5

Select in "Account":
```
  private $interestRate;

```

#|en| Once changes are complete, we can remove the original field.

Remove selected

#C|ru| Тут же стоит запустить авто-тесты, чтобы обнаружить потенциальные ошибки.
#S Все отлично, код исправно работает.

#C|en| Run autotests to identify potential errors.
#S Outstanding. The code is doing what we intended.

#C|uk| Тут же варто запустити авто-тести, щоб виявити потенційні помилки.
#S Все добре, код справно працює.

#|en| Remember that if a class has many methods that use the moved field, you may want to self-encapsulate it to simplify later refactoring. Let's look at a quick example.

Select whole "Account"

Replace instantly:
```
class Account {
  // ...
  private $type; // AccountType
  private $interestRate;

  public function interestForAmount_days($amount, $days) {
    return $this->getInterestRate() * $amount * $days / 365.0;
  }
  public function interestForBigFamily($familySize) {
    return $this->getInterestRate() / $familySize;
  }

  // other 10 methods, which use getInterestRate()

  private function getInterestRate() {
    return $this->interestRate;
  }
  private function setInterestRate($arg) {
    $this->interestRate = $arg;
  }
}

```

Select name of "interestForAmount_days"
+ Select name of "interestForBigFamily"
+ Select "other 10 methods"

#|en| In this case, you will not need to make changes in all methods right away…

Select "$this->interestRate = $arg" in "setInterestRate"
+Select "return $this->interestRate" in "getInterestRate"

#|en| …only in the access methods (getter and setter).

Select "return $this->interestRate" in "getInterestRate"

Replace "return $this->type->getInterestRate()"

Select "$this->interestRate = $arg" in "setInterestRate"

Replace "$this->type->setInterestRate($arg)"

Select in "Account":
```
  private $interestRate;

```

#|en| Then the original field can be removed.

Remove selected

Select name of "interestForAmount_days"
+ Select name of "interestForBigFamily"

#|en| Later, if desired, you can redirect access methods for clients so that they use the new object.

Select name of "Account"

#|en| Self-encapsulating allows refactoring via baby steps. And when a class is undergoing major changes, that is a good thing.

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
