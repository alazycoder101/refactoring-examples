remove-setting-method:php

###

1.en. The value of a field should be changeable only in the constructor. If the constructor does not contain a parameter for setting the value, add one.

2.en. If the code in the setter is too complicated, it makes sense to make it into a separate initialization method.

3.en. If subclasses initialize private fields of the parent class, then replace it with a call to the constructor of the parent class.



###

```
class Account {
  // ...
  private $id;

  public function __construct($id) {
    $this->setId($id);
  }
  public function setId($id) {
    $this->id = $id;
  }
}
```

###

```
class Account {
  // ...
  private $id;

  public function __construct($id) {
    $this->initializeId($id);
  }
  protected function initializeId($id) {
    $this->id = 'ID' . $id;
  }
}

class InterestAccount extends Account {
  private $interestRate;
  public function __construct($id, $interestRate) {
    $this->initializeId($id);
    $this->interestRate = $interestRate;
  }
}
```

###

Set step 1

#|en| Let's look at <b>Remove Setter Method</b> using a simple example of a bank account class. The class has an ID field that should be created once and never change again.

Select name of "setId"

#|en| However, the class currently has a setter for that field, which we want to eliminate.

Select body of "setId"

#|en| The simplest solution would be to integrate the setter's code into the constructor.

Select body of "__construct"

Replace:
```
    $this->id = $id;
```

Select whole "setId"

Remove selected

Set step 2

Select name of "Account"

#|en| In effect, we have already done everything for a case as simple as this one. But there are other, more difficult cases.

Select whole "__construct"

Replace instant:
```
  public function __construct($id) {
    $this->setId($id);
  }
  public function setId($id) {
    $this->id = 'ID' . $id;
  }

```

Select body of "setId"

#|en|< For example, what if the setter performs calculations on an argument.

#|en|< If the change is simple, as it is here, it can also be moved to the constructor.

#|en|< However, if the change is complex and consists of calls to several methods, it is better to create a new method for initializing the value.

Select visibility of "setId"

Replace "private"

Wait 500ms

Select "setId"

Replace "initializeId"

Set step 3

Go to the end of file

#|en| Excellent. Now let's review one more case.

Print instant:
```


class InterestAccount extends Account {
  private $interestRate;
  public function __construct($id, $interestRate) {
    $this->setId($id);
    $this->interestRate = $interestRate;
  }
}
```

Select name of "InterestAccount"

#|en| Another unpleasant situation arises when there are subclasses initializing private variables of a parent class.

Select "$this->setId($id)"

#|en| Then, instead of calling a setter, we should call the parent constructor.

Print "parent::__construct($id)"

Select "parent::__construct($id)"

#|en| If that is impossible, we must call the proper initialization method. By the way, if it's private, you should make it protected first.

Select visibility of "initializeId"

Replace "protected"

Wait 500ms

Select "parent::__construct($id)"

Replace "$this->initializeId($id)"

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
