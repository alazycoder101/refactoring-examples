hide-delegate:php

###

1.en. For each <i>delegate class</i> method that is called in the code, create a method in the <i>server class</i> that delegates calls to the <i>delegate class</i>.

2.en. Change the client code so that it calls the methods of the <i>server-class</i>.

3.en. If after all these changes you no longer need direct access to the <i>delegate class</i>, you can proceed to remove access to the <i>delegate class</i> from the <i>server class</i>. 



###

```
class Person {
  private $department; // Department

  public function getDepartment() {
    return $this->department;
  }
  public function setDepartment(Department $arg) {
    $this->department = $arg;
  }
}

class Department {
  private $chargeCode;
  private $manager; // Person

  public function __construct(Person $manager) {
    $this->manager = $manager;
  }
  public function getManager() {
    return $this->manager;
  }

  //…
}

// Somewhere in client code
$manager = $john->getDepartment()->getManager();
```

###

```
class Person {
  private $department; // Department

  public function setDepartment(Department $arg) {
    $this->department = $arg;
  }
  public function getManager() {
    return $this->department->getManager();
  }
}

class Department {
  private $chargeCode;
  private $manager; // Person

  public function __construct(Person $manager) {
    $this->manager = $manager;
  }
  public function getManager() {
    return $this->manager;
  }

  //…
}

// Somewhere in client code
$manager = $john->getManager();
```

###

Set step 1

#|en| Let's look at <i>Hide Delegate</i> using the classes representing an employee and the employee's department as an example.

Select "$manager = $john->getDepartment()->getManager();"

#|en| If the client needs to know the manager of a certain employee, the client must first know in which department that person works.

#|en| That way, along with the manager value, client code gets full access to the <code>Department</code> object and its other fields. If that doesn't look very safe to you… you're right.

Set step 2

Go to the end of "Person"

#|en| This association can be reduced by hiding the <code>Department</code> class from the client, by creating a simple delegate method in <code>Person</code>.

Print:
```

  public function getManager() {
    return $this->department->getManager();
  }
```

Set step 3

Select "$john->getDepartment()->getManager();"

#|en| Now let's modify the code so that it uses the new method.

Print "$john->getManager();"

Select whole "getDepartment"

#|en| Once all necessary methods have been delegated, we can remove the method in the <code>Person</code> class that provided access to the <code>Department</code> instance.

Remove selected

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
