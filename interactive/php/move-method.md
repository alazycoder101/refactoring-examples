move-method:php

###

1.en. Check all features used by the old method in its class. It may be a good idea to move them as well.

2.en. Check whether the method has been declared in superclasses and subclasses. If not, declare a new method in the recipient class and move the code of the old method to it.

3.en. Replace the body of the old method with delegation to the new method.

4.en. Check whether you can get rid of the old method entirely.



###

```
class Account {
  // ...
  private $type; // AccountType
  private $daysOverdrawn;

  public function overdraftCharge() {
    if ($this->type->isPremium()) {
      $result = 10;
      if ($this->daysOverdrawn > 7) {
        $result += ($this->daysOverdrawn - 7) * 0.85;
      }
      return $result;
    }
    else {
      return $this->daysOverdrawn * 1.75;
    }
  }
  public function bankCharge() {
    $result = 4.5;
    if ($this->daysOverdrawn > 0) {
      $result += $this->overdraftCharge();
    }
    return $result;
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
  private $daysOverdrawn;

  public function bankCharge() {
    $result = 4.5;
    if ($this->daysOverdrawn > 0) {
      $result += $this->type->overdraftCharge($this);
    }
    return $result;
  }
}

class AccountType {
  // ...
  public function overdraftCharge(Account $account) {
    if ($this->isPremium()) {
      $result = 10;
      if ($account->getDaysOverdrawn() > 7) {
        $result += ($account->getDaysOverdrawn() - 7) * 0.85;
      }
      return $result;
    }
    else {
      return $account->getDaysOverdrawn() * 1.75;
    }
  }
}
```

###

Set step 1

#|en| Let's look at <i>Move Method</i> using a bank account class as an example.

Select "private |||$type|||"

#|en| Say, there will be several new account types, and each account type will have different rules for how to calculate overdraft fees, when the bank's customer attempts to spend more money than is available.

#|en| We want to move the method for calculating this overdraft fee to inside the class that represents the account type.

Select name of "overdraftCharge"

#|en| First see which fields and methods are used by <code>overdraftCharge()</code> and decide whether you can move only it – or else move it and everything related to it as well.

Select "private |||$type|||"

#|en| The <code>type</code> field stores the account type. There is no reason to move it anywhere else.

Select "private |||$daysOverdrawn|||"

#|en| Moving the <code>daysOverdrawn</code> field would not make sense either, since its value will be different in every other account.

Select name of "overdraftCharge"

#|en| So we will move only the <code>overdraftCharge()</code> method.

Set step 2

Select whole "overdraftCharge"

#|en| Awesome, let's copy the <code>overdraftCharge()</code> method to the <code>AccountType</code> class.

Go to the end of "AccountType"

Print:
```

  public function overdraftCharge() {
    if ($this->type->isPremium()) {
      $result = 10;
      if ($this->daysOverdrawn > 7) {
        $result += ($this->daysOverdrawn - 7) * 0.85;
      }
      return $result;
    }
    else {
      return $this->daysOverdrawn * 1.75;
    }
  }
```

Select name of "overdraftCharge" in "AccountType"

#|en| Now, we should edit the method so that it will work correctly in the new location.

Select "type->" in "overdraftCharge" of "AccountType"

#|en| First remove the <code>type</code> field from the method, since the method is inside the class that implements the account type and, therefore, all methods could be called from it directly.

Remove selected

Select "daysOverdrawn" in "overdraftCharge" of "AccountType"


#|en| Now we should go through the fields and methods  that left in <code>Account</code> class but still needed inside the method we move. In our case, this is the <code>daysOverdrawn</code> field.

#|en| In theory, there are four options for saving a method or field of the original class: <ol><li>Move the field or method to the target class.</li><li>Create a reference from the target class to the original one or restore the previously existing one.</li><li>Pass an instance of the original class as a parameter of the target class method.</li><li>Pass the field value as a parameter.</li></ol>

#|en| In our case, let's pass the field value as a parameter…

Go to parameters of "overdraftCharge" of "AccountType"

Print "$daysOverdrawn"

Select "$this->daysOverdrawn" in "overdraftCharge" of "AccountType"

#|en| …and use this parameter in the method body.

Replace "$daysOverdrawn"

#C|ru| Запустим тесты, чтобы проверить код на наличие ошибок.
#S Всё отлично, можем продолжать!

#C|en| Let's launch autotests to check for errors in code.
#S Everything is OK! We can keep going.

#C|uk| Запустимо тести, щоб перевірити код на наявність помилок.
#S Все добре, можемо продовжувати.

Set step 3

Select body of "overdraftCharge" in "Account"

#|en| Now we can replace the body of the original method in <code>Account</code> class with simple delegation.

Print:
```
    return $this->type->overdraftCharge($this->daysOverdrawn);
```

#C|ru| Запустим тесты ещё раз на всякий случай.
#S Всё отлично, можем продолжать!

#C|en| Let's run the tests again, just in case.
#S Everything is OK! We can keep going.

#C|uk| Запустимо тести ще раз на всякий випадок.
#S Все добре, можемо продовжувати.

Set step 4

Select name of "overdraftCharge" in "Account"

#|en| The code works fine, so we can continue and remove the <code>overdraftCharge()</code> method from the original class entirely.

Select "overdraftCharge()" in "bankCharge"

#|en| But first, find all places where it is called and readdress these to the method in the <code>AccountType</code> class.

Print:
```
type->overdraftCharge($this->daysOverdrawn)
```

#|en| In case, when the moved being method is not private, make sure that other classes are not using it. It's easy in strongly-typed programming languages (Java, C#) – compilation will uncover everything that may have been missed. In other languages, autotest is your friend.

Select whole "overdraftCharge" in "Account"

#|en| After redirecting all method calls to the new class, we can entirely remove the method declaration in the <code>Account</code> class.

Remove selected

Select name in "Account"

#|en| We can compile and test after each removal or perform everything in one fell swoop.

#|en| So can we say that we are done moving the method?<br/><br/>Not quite…

#|en| Let's look at one nuance. In this case, the method referred to a single field, which allowed us to pass its value to the parameter. If the method had called any other method of the <code>Account</code> class, we would not be able to do this. In such situations, you must pass the entire object in the parameters and retrieve everything you need from it. Let's see how it's done.

Select parameters in "overdraftCharge"

#|en| First, pass an instance of the original class to the method being moved.

Print:
```
Account $account
```

Select "$daysOverdrawn" in "overdraftCharge"

#|en| In addition, all fields and methods of interest should now be taken directly from the instance received.

Print:
```
$account->getDaysOverdrawn()
```

Select "overdraftCharge(|||$this->daysOverdrawn|||)"

#|en| And third, you must add passing of the current instance of the <code>Account</code> class to all method calls.

Print "$this"

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
