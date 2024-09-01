introduce-local-extension:php

###


1.en. Create a new extension class and make it the inheritor of the utility class.



2.en. Create a constructor that uses the parameters of the constructor of the utility class.



3.en. Create an alternative "converting" constructor that accepts only an object of the original class in its parameters.



4.en. Create new extended methods in the class. Move foreign methods from other classes to this class or else delete the foreign methods if their functionality is already present in the extension.



5.en. Replace use of the utility class with the new extension class in places where its functionality is needed.




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

```
class Account {
  // ...
  public function schedulePayment() {
    $previousDate = new MyNewDate($this->previousDate);
    $paymentDate = $previousDate->nextWeek();
    // Issue a payment using paymentDate.
    // ...
  }
}

// Local extension class.
class MyNewDate extends DateTime {
  public function __construct() {
  	$args = func_get_args();
  	if (isset($args[0]) && is_a($args[0], 'DateTime')) {
      call_user_func_array([$this, 'parent::__construct'],
        [$args[0]->format('Y-m-d H:i:s')]);
    }
    else {
      call_user_func_array([$this, 'parent::__construct'], $args);
    }
  }

  public function nextWeek() {
  	return $this->modify('+7 days');
  }
}
```

###

Set step 1

#|en| <i>Introduction of a local extension</i> can be performed in two ways: by creating either subclass or a wrapper class. In this example, we will use inheritance.

#|en| First, we create a new subclass of the original <code>Date</code> class.

Go to the end of file

Print:
```


// Local extension class.
class MyNewDate extends DateTime {
}
```

Set step 2

Go to the start of "MyNewDate"

#|en| Then repeat the original's constructors via simple delegation.

Print:
```

  public function __construct() {
  	$args = func_get_args();
  	call_user_func_array([$this, 'parent::__construct'], $args);
  }
```

Set step 3

Select name of "__construct"

#|en| Now we should add a converting constructor that accepts the original as an argument. You can't overload constructors in PHP, so we'll just extend the existing one.

Select:
```
  	call_user_func_array([$this, 'parent::__construct'], $args);
```

Replace:
```
  	if (isset($args[0]) && is_a($args[0], 'DateTime')) {
      call_user_func_array([$this, 'parent::__construct'],
        [$args[0]->format('Y-m-d H:i:s')]);
    }
    else {
      call_user_func_array([$this, 'parent::__construct'], $args);
    }
```

Set step 4

Select whole "nextWeek"

#|en| When the class constructors are ready, you can add new methods to it or move foreign methods from other classes. Let's move the <code>nextWeek()</code> method with the help of <a href="/move-method">Move Method</a>.

Go to the end of "MyNewDate"

Print:
```


  private static function nextWeek(DateTime $arg) {
  	$previousDate = clone $arg;
  	return $previousDate->modify('+7 days');
  }
```

Select parameters of "nextWeek" in "MyNewDate"

#|en| The method parameter is no longer needed since the method is inside the <code>DateTime</code> inheritor. Thus the needed data can be taken from its own object.

Remove selected

Select in "nextWeek" of "MyNewDate":
```
  	$previousDate = clone $arg;

```

Remove selected

Select "$previousDate" in "nextWeek" of "MyNewDate"

Replace "$this"

Select "|||private static||| function nextWeek" in "MyNewDate"

#|en| In addition, the method stops being static and private – after all, we need to be able to call it from other classes.

Replace "public"

Wait 500ms

Set step 5

Select "self::nextWeek($this->previousDate)"

#|en| Now we replace all usages of the old foreign method with our new extension class.

Go to the start of "schedulePayment"

Print:
```

    $previousDate = new MyNewDate($this->previousDate);
```

Select "self::nextWeek($this->previousDate)"

Wait 500ms

Print "$previousDate->nextWeek()"

Select whole "nextWeek" in "Account"
+ Select:
```

  /**
   * Foreign method. Should be in the DateTime class.
   */

```
#|en| After all changes are complete, we remove the external method from the client class.

Remove selected

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
