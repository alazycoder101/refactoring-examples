replace-type-code-with-subclasses:php

###

1.en. Use <a href="/self-encapsulate-field">Self Encapsulate Field</a> to create a getter for the field that contains type code.

2.en. Make the superclass constructor private. Create a static factory method with the same parameters as the superclass constructor.

3.en. Create a unique subclass for each value of the coded type. In it, redefine the getter of the coded type so that it returns the corresponding value of the coded type.

4.en. Delete the field with type code from the superclass. Make its getter abstract.

5.en. Now that you have subclasses, you can start to move the fields and methods from the superclass to corresponding subclasses

6.en. When everything movable has been moved, use <a href="/replace-conditional-with-polymorphism">Replace Conditional with Polymorphism</a> in order to get rid of conditionals that use type code once and for all.



###

```
class Employee {
  // ...
  const ENGINEER = 0;
  const SALESMAN = 1;
  const MANAGER = 2;

  public $type;

  public function __construct($arg) {
    $this->type = $arg;
  }

  public $monthlySalary;
  public $commission;
  public $bonus;
  public function payAmount() {
    switch ($this->type) {
      case self::ENGINEER:
        return $this->monthlySalary;
      case self::SALESMAN:
        return $this->monthlySalary + $this->commission;
      case self::MANAGER:
        return $this->monthlySalary + $this->bonus;
      default:
        throw new RuntimeException("Incorrect Employee Code");
    }
  }
}
```

###

```
abstract class Employee {
  // ...
  const ENGINEER = 0;
  const SALESMAN = 1;
  const MANAGER = 2;

  abstract public function getType();

  public static function create($type) {
    switch ($type) {
      case self::ENGINEER:
        return new Engineer();
      case self::SALESMAN:
        return new Salesman();
      case self::MANAGER:
        return new Manager();
      default:
        throw new RuntimeException("Incorrect Employee Code");
    }
  }

  public $monthlySalary;
  public function payAmount() {
    return $this->monthlySalary;
  }
}

class Engineer extends Employee {
  public function getType() {
    return Employee::ENGINEER;
  }
}

class Salesman extends Employee {
  public $commission;
  public function getType() {
    return Employee::SALESMAN;
  }
  public function payAmount() {
    return $this->monthlySalary + $this->commission;
  }
}

class Manager extends Employee {
  public $bonus;
  public function getType() {
    return Employee::MANAGER;
  }
  public function payAmount() {
    return $this->monthlySalary + $this->bonus;
  }
}
```

###

Set step 1

#|en| Let's look at <i>Replace Type Code With Subclasses</i>, using an payroll class as our example. We have several types of employees, which affects their salary values.

Select "public |||$type|||"

#|en| We start by applying <a href="/self-encapsulate-field">Self-Encapsulate Field</a> to the employee type.

Select "|||public||| $type"

Replace "private"

Go to before "__construct"

Print:
```

  public function getType() {
    return $this->type;
  }

```

Select "switch (|||$this->type|||)"

Replace "$this->getType()"

Set step 2

Select parameters of "__construct"

#|en| Since the <code>Employee</code> constructor uses type code as a parameter, we should replace it with a factory method.

Go to before "__construct"

Print:
```

  public static function create($type) {
    return new Employee($type);
  }
```

Wait 500ms

Select visibility of "__construct"

Replace "private"

Set step 3

Select 1st "ENGINEER"

#|en| Now we can start converting <code>Engineer</code> to a subclass. First create the subclass itself…

Go to the end of file

Print:
```


class Engineer extends Employee {
}
```

Go to the end of "Engineer"

#|en| …then create the method to replace the type code.

Print:
```

  public function getType() {
    return Employee::ENGINEER;
  }
```

Select body of "create"

#|en| We need to change the factory method as well so that it creates the necessary object.

Print:
```
    switch ($type) {
      case self::ENGINEER:
        return new Engineer();
      default:
        return new Employee($type);
    }
```

Go to the end of file

#|en| Continue these actions one by one, until all code has been replaced by subclasses.

Print:
```


class Salesman extends Employee {
  public function getType() {
    return Employee::SALESMAN;
  }
}
```

Go to:
```
      case self::ENGINEER:
        return new Engineer();|||
```

Print:
```

      case self::SALESMAN:
        return new Salesman();
```

Wait 1000ms

Go to the end of file

Print:
```


class Manager extends Employee {
  public function getType() {
    return Employee::MANAGER;
  }
}
```

Go to:
```
      case self::SALESMAN:
        return new Salesman();|||
```

Print:
```

      case self::MANAGER:
        return new Manager();
```

Select:
```
  private $type;


```

Set step 4

#|en| Then we can eliminate the field with type code in <code>Employee</code>…

Remove selected


Go to:
```
  |||public function getType() {
    return $this->type;
  }
```

#|en| …and make <code>getType</code> an abstract method.

Print "abstract "

Select:
```
  abstract public function getType()||| {
    return $this->type;
  }|||
```

Replace ";"

Go to before "Employee"

#|en| This will make the <code>Employee</code> class abstract as well.

Print "abstract "

Select:
```
      default:
        |||return new Employee($type);|||

```

#|en| After all these changes, we can no longer create <code>Employee</code> objects as the default implementation. So it is important to remember to get rid of the type field only after creating all subclasses.

Replace:
```
throw new RuntimeException("Incorrect Employee Code");
```

Select whole of "__construct"

Remove selected

Select "switch ($type) {" in "create"

#|en| Note that we ended up creating another big <code>switch</code> operator. Generally speaking this <a href="/smells/switch-statements">gives off a bad whiff</a> but once refactoring is done, it will be the only operator remaining in the code.

Set step 5

Select:
```
  public $monthlySalary;
  public $commission;
  public $bonus;
```
+ Select name of "payAmount"

#|en| After creating the subclasses, use <a href="/push-down-method">Push Down Method</a> and <a href="/push-down-field">Push Down Field</a> on all methods and fields that relate to only specific types of employees.

#|en| In our case, we will create <code>payAmount</code> methods in each of the subclasses and move payroll calculations there for the relevant types of employees.

Select:
```
  public $commission;

```

Remove selected

Go to the start of "Salesman"

Print:
```

  public $commission;
```

Select:
```
      case self::SALESMAN:
        return $this->monthlySalary + $this->commission;

```

Remove selected

Go to the end of "Salesman"

Print:
```

  public function payAmount() {
    return $this->monthlySalary + $this->commission;
  }
```

Wait 500ms

Select:
```
  public $bonus;

```

#|en| Do the same thing with the manager class.

Remove selected

Go to the start of "Manager"

Print:
```

  public $bonus;
```

Select:
```
      case self::MANAGER:
        return $this->monthlySalary + $this->bonus;

```

Remove selected


Go to the end of "Manager"

Print:
```

  public function payAmount() {
    return $this->monthlySalary + $this->bonus;
  }
```

Set step 6

Select body of "payAmount"

#|en| After all the code has been moved to the subclasses, you can either declare the method in the superclass abstract or else leave the default implementation there (which is what we will do).

Print:
```
    return $this->monthlySalary;
```



#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
