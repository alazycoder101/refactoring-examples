introduce-foreign-method:php

###


1.en. Create a new method in the client class.



2.en. In this method, create a parameter to which the object of the utility class will be passed. If this object can be obtained from the client class, you do not have to create such a parameter.



3.en. Extract the relevant code fragments to this method and replace them with method calls.



4.en. Leave a comment for the method, labeling it as <i>Foreign method</i> and requesting that the method be placed in a utility class if this becomes possible in the future.




###

```
class Account {
  // ...
  public function schedulePayment() {
    $previousDate = clone $this->previousDate;
    $paymentDate = $previousDate->modify('+7 days');

    // Issue a payment using paymentDate.
    // ...
  }
}
```

###

```
class Account {
  // ...
  public function schedulePayment() {
    $paymentDate = self::nextWeek($this->previousDate);
    // Issue a payment using paymentDate.
    // ...
  }

  /**
   * Foreign method. Should be in the DateTime class.
   */
  private static function nextWeek(DateTime $arg) {
  	$previousDate = clone $arg;
  	return $previousDate->modify('+7 days');
  }
}
```

###

Set step 1

#|en| Let's look at <i>Introduce Foreign Method</i> using the example of a bank account class.

Select:
```
    $previousDate = clone $this->previousDate;
    $paymentDate = $previousDate->modify('+7 days');

```

#|en| This class has code that opens a new billing period one week in the future from the current time.

#|en| Ideally, the <code>DateTime</code> class would have a method for getting a date seven days in the future (something resembling <code>$previousDate->nextWeek()</code>) but it does not, and what's more, it is standard so we cannot change it.

Go to the end of "Account"

#|en| What we can do though is create a "foreign" method in its own class.

Print:
```


  private function nextWeek() {
  	$previousDate = clone $this->previousDate;
  	return $previousDate->modify('+7 days');
  }
```

Set step 2

Go to parameters of "nextWeek"

#|en| To make the method more universal, we will add a parameter of the <code>DateTime</code> class to it. Essentially, we are extending the functionality of the object passed in this parameter.

Print "DateTime $arg"

Select "$this->previousDate" in "nextWeek"

Replace "$arg"

Go to type of "nextWeek"

#|en| You should also declare the method static to make it accessible from other static methods of <code>Account</code> should any exists now or in future.

Print "static "

Set step 3

Select:
```
    $previousDate = clone $this->previousDate;
    $paymentDate = $previousDate->modify('+7 days');

```

#|en| The method can now be used in the other code.

Print "    $paymentDate = self::nextWeek($this->previousDate);"

Set step 4

Go to before "nextWeek"

#|en| For the finishing touch, let's add a comment to the "foreign method" about its purpose and our intentions. That will help to avoid potential confusion regarding its use. And if a new class is created in the program later for storing additional date-related functions, this method can be easily found and moved to a better place.

Print:
```

  /**
   * Foreign method. Should be in the DateTime class.
   */
```

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
