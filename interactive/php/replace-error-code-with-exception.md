replace-error-code-with-exception:php

###

1.en. Find all calls to a method that returns error codes and, instead of checking for an error code, wrap it in <code>try</code>/<code>catch</code> blocks.

2.en. Inside the method, instead of returning an error code, throw an exception.

3.en. Change the method description so that it contains information about the exception being thrown (<code>@throws</code> section).



###

```
class Account {
  // ...
  private $balance;

  /**
   * Withdraw money from account.
   * @param int $amount Amount to withdraw.
   * @return Zero on success, -1 on error.
   */
  public function withdraw($amount) {
    if ($amount > $this->balance) {
      return -1;
    }
    else {
      $this->balance -= $amount;
      return 0;
    }
  }
}

// Somewhere in client code.
if ($account->withdraw($amount) == -1) {
  handleOverdrawn();
}
else {
  doTheUsualThing();
}
```

###

```
class Account {
  // ...
  private $balance;

  /**
   * Withdraw money from account.
   * @param int $amount Amount to withdraw.
   * @throws BalanceException
   */
  public function withdraw($amount) {
    if ($amount > $this->balance) {
      throw new BalanceException();
    }
    $this->balance -= $amount;
  }
}
class BalanceException extends Exception {}

// Somewhere in client code.
try {
  $account->withdraw($amount);
  doTheUsualThing();
} catch (BalanceException $e) {
  handleOverdrawn();
}
```

###

Set step 1

#|en| Let's look at this refactoring in the context of withdrawals from a bank account.

Go to "if ($amount > $this->balance) {|||"

#|en|<+ If a customer attempts to withdraw more money than his or her current balance allows, an error code will be returned (<code>-1</code>)…

Select "$account->withdraw($amount) == -1"

#|en|= …which is then checked in the client code.

#|en| We can replace all this by throwing an exception and then "catching" it in the client code.

Go to after "Account"

#|en| First, we create a new exception class.

Print:
```

class BalanceException extends Exception {}
```

#|en| Then, wrap our method body with the <code>try</code>/<code>catch</code> block.

Select:
```
if ($account->withdraw($amount) == -1) {
  handleOverdrawn();
}
else {
  doTheUsualThing();
}
```

Replace:
```
try {
  $account->withdraw($amount);
  doTheUsualThing();
} catch (BalanceException $e) {
  handleOverdrawn();
}
```

Set step 2

#|en| After that, change the method so that it throws an exception instead of returning an error code.

Select:
```
      return -1;
```

Replace:
```
      throw new BalanceException();
```

Wait 500ms

Select:
```
      return 0;

```

Remove selected

Select:
```
|||    else {
|||      $this->balance -= $amount;
|||    }
|||
```

#|en| This code can be simplified a bit if we remove <code>else</code>.

Remove selected

Select:
```
      $this->balance -= $amount;
```

Deindent

Select name of "Account"

#|en| This step is not very safe because we are forced to change all references to the method, as well as the method itself, in a single step. Otherwise, the compiler will shake its head at us in disapproval. If there are many calls, we will have to make a mammoth modification without any intermediate compilation or testing.

#|en| In such cases, it is better to create a new method and place the code of the old one inside it, including exceptions. Replace the code of the old method with <code>try</code>/<code>catch</code> blocks that return error codes. After this, the code will remain functional and we could replace error code handlers, one by one, with calls to the new method and <code>try</code>/<code>catch</code> blocks.

Set step 3

Select:
```
@return Zero on success, -1 on error.
```
#|en| After all of the changes, we must update the method's description, indicating that the method now throws exceptions.

Print:
```
@throws BalanceException
```

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
