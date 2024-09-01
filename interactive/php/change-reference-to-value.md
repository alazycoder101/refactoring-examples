change-reference-to-value:php

###

1.en. Make the object unchangeable. The object should not have any setters or other methods that change its state and data (<a href="/remove-setting-method">Remove Setting Method</a> may help here). The only place where data should be assigned to the fields of a value object is a constructor.

2.en. Check whether you can delete the factory method and make the object constructor public.



###

```
class Customer {
  private $name;
  private $birthDate;

  public function getName() {
    return $this->name;
  }
  public function getBirthDate() {
    return $this->birthday;
  }
  public function setBirthDate(DateTime $birthDate) {
    $this->birthDate = $birthDate;
  }
  private function __construct($name) {
    $this->name = $name;
  }

  private static $instances = array();

  public static function get($name) {
    if (!isset($this->instances[$name])) {
      $value = new Customer($name);
      $this->instances[$name] = $value;
    }
    else {
      $value = $this->instances[$name]
    }
    return value;
  }
}

// Somewhere in client code
$john = Customer::get("John Smith");
$john->setBirthDate(new DateTime("1985-01-01"));
```

###

```
class Customer {
  private $name;
  private $birthDate;

  public function getName() {
    return $this->name;
  }
  public function getBirthDate() {
    return $this->birthday;
  }
  public function __construct($name, DateTime birthDate) {
    $this->name = $name;
    $this->birthDate = $birthDate;
  }
}

// Somewhere in client code
$john = new Customer("John Smith", new Date(1985, 1, 1));
```

###

Set step 1

#|en| Let's look at <i>Change Reference to Value</i> using a customer class as an example.

Select "private |||$name|||"
+ Select "private |||$birthDate|||"

#|en| This class contains a customer's name and date of birth. This class gives rise to reference objects, meaning that only one instance of the <code>Customer</code> class is created for one real customer.

Select "Customer::get("John Smith")"

#|en| The code for getting the instance may look as follows:

Select visibility of "__construct"

#|en| The <code>Customer</code> class keeps a registry of its instances. We cannot simply access the constructor (because it is private).

Select name of "get"

#|en| Instead, we call a static factory method that looks for the customer among the objects already created. And only if such an object has not been created does the factory method run the real constructor and then add the newly created object to the registry.

#|en| Now let's say that we have multiple orders referring to the same client. Suddenly, the code of one of the orders changes the value of the client's date of birth. Since both orders refer to the same client object, the new date of birth will be available from the other order as well.

#|en| Would this be made impossible if each order had own instance of the <code>Customer</code> class? Probably not. That is why the main requirement of this refactoring is making the class immutable. In some cases, this is simply not possible, and the refactoring should not be executed.

Select whole "setBirthDate"

#|en| Following this logic, we should remove the setter of the date of birth field. Then, initialize the value of the field in the constructor. This could be done with <a href="/remove-setting-method">Remove Setting Method</a>.

Remove selected

Go to the end of parameters of "__construct"

Print ", DateTime birthDate"

Go to the end of "__construct"

Print:
```

    $this->birthDate = $birthDate;
```

Select:
```

$john->setBirthDate(new DateTime("1985-01-01"));
```

#|en| Since the class no longer contains a setter, we need to remove its use in the client code. Note that we don't have anything to compensate that setter yet. But don't worry, we will get to this a bit later.

Remove selected

Set step 2

Select:
```

  private static $instances = array();


```
+ Select whole "get"

#|en| And one last thing. Since we no longer need to keep a registry of created objects, we can remove the factory method and make the constructor public.

Remove selected

Select visibility of "__construct"

Replace "public"

Select "Customer::get("John Smith")"

#|en| The client code will also change as the result of these changes.

Print "new Customer("John Smith", new Date(1985, 1, 1))"

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
