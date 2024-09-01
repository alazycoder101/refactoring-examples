replace-type-code-with-state-strategy:php

###


1.en. Use <a href="/self-encapsulate-field">Self Encapsulate Field</a> to create a getter for the field that contains type code.



2.en. Create a new class that will play the role of <i>state</i> (or <i>strategy</i>). Create an abstract getter of the coded field in it.



3.en. Create state subclasses for each value of the coded type.



4.en. In the abstract state class, create a static factory method that accepts the value of the coded type as a parameter. Depending on this parameter, the factory method will create objects of various states. For this, in its code create a large conditional; it will be the only one when refactoring is complete.



5.en. In the original class, change the type of the coded field to the state class. In the field's setter, call the factory state method for getting new state objects.



6.en. Move the fields and methods from the superclass to the corresponding state subclasses.



7.en. When everything movable has been moved, use <a href="/replace-conditional-with-polymorphism">Replace Conditional with Polymorphism</a> in order to get rid of conditionals that use type code once and for all.




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
        throw new Exception("Incorrect Employee Code");
    }
  }
}
```

###

```
class Employee {
  // ...
  private $type; // EmployeeType

  public function __construct($arg) {
    $this->type = EmployeeType::newType($arg);
  }
  public function getTypeCode() {
    return $this->type->getTypeCode();
  }
  public function setTypeCode($arg) {
    $this->type = EmployeeType::newType($arg);
  }

  public $monthlySalary;
  public $commission;
  public $bonus;
  public function payAmount() {
    return $this->type->payAmount($this);
  }
}

abstract class EmployeeType {
  const ENGINEER = 0;
  const SALESMAN = 1;
  const MANAGER = 2;

  abstract public function getTypeCode();
  public static function newType($code) {
    switch ($code) {
      case self::ENGINEER:
        return new Engineer();
      case self::SALESMAN:
        return new Salesman();
      case self::MANAGER:
        return new Manager();
      default:
        throw new Exception("Incorrect Employee Code");
    }
  }

  abstract public function payAmount(Employee $employee);
}
class Engineer extends EmployeeType {
  public function getTypeCode() {
    return EmployeeType::ENGINEER;
  }
  public function payAmount(Employee $employee) {
    return $employee->monthlySalary;
  }
}
class Salesman extends EmployeeType {
  public function getTypeCode() {
    return EmployeeType::SALESMAN;
  }
  public function payAmount(Employee $employee) {
    return $employee->monthlySalary + $employee->commission;
  }
}
class Manager extends EmployeeType {
  public function getTypeCode() {
    return EmployeeType::MANAGER;
  }
  public function payAmount(Employee $employee) {
    return $employee->monthlySalary + $employee->bonus;
  }
}
```

###

Set step 1

#|en| Let's look at <i>Replace Type Code with State/Strategy</i> in the context of the payroll class considered earlier. We have several types of employees; these types are used to calculate the salary amount for each particular employee.

Select "public |||$type|||"

#|en| Let's start by applying <a href="/self-encapsulate-field">Self-Encapsulate Field</a> to the employee type.

Select "|||public||| $type"

Replace "private"

Go to after "__construct"

Print:
```

  public function getType() {
    return $this->type;
  }
  public function setType($arg) {
    $this->type = $arg;
  }
```

Wait 500ms

Select "switch ($this->|||type|||) {"

Replace "getType()"

Select whole "setType"

#|en| We assume that the company is progressive and enlightened and so allows its managers to ascend to engineers. So the type code can be changed and using subclasses to eliminate type coding is not possible. This causes us to use the <a href="https://refactoring.guru/design-patterns/state">State</a> pattern.

Set step 2

Go to the end of file

#|en| Declare the state class (as an abstract class with an abstract method for returning type code).

Print:
```


abstract class EmployeeType {
  abstract public function getTypeCode();
}
```

Set step 3

#|en| Now create subclasses for each type of employee.


Print:
```

class Engineer extends EmployeeType {
  public function getTypeCode() {
    return Employee::ENGINEER;
  }
}
class Salesman extends EmployeeType {
  public function getTypeCode() {
    return Employee::SALESMAN;
  }
}
class Manager extends EmployeeType {
  public function getTypeCode() {
    return Employee::MANAGER;
  }
}
```

Set step 4

Go to the end of "EmployeeType"

#|en| Create a static method in the state class. It will return an instance of the necessary subclass, depending on the value accepted.

Print:
```

  public static function newType($code) {
    switch ($code) {
      case Employee::ENGINEER:
        return new Engineer();
      case Employee::SALESMAN:
        return new Salesman();
      case Employee::MANAGER:
        return new Manager();
      default:
        throw new Exception("Incorrect Employee Code");
    }
  }
```

Select "switch ($code)"

#|en| As you can see, here we are introducing a large <code>switch</code> operator. That's not great news, but once we are done with refactoring, this operator will be the only one in the code and will be run only when a type is changed.

#C|ru| Запустим тестирование, чтобы убедиться в отсутствии ошибок.
#S Всё отлично, можем продолжать!

#C|en| Let's launch autotests to check for errors in code.
#S Everything is OK! We can keep going.

#C|uk| Запустимо тестування, щоб переконатися у відсутності помилок.
#S Все добре, можна продовжувати.

Set step 5

Go to "private $type;|||"

#|en| Now we need to connect the created subclasses to <code>Employee</code> by modifying the access methods for the type code and constructor.

Print " // EmployeeType"

Wait 500ms

Select:
```
  public function getType() {
    return $this->|||type|||;
  }
```

Replace "type->getTypeCode()"

Wait 500ms

Select:
```
    $this->type = |||$arg|||;
```

#|en| The setter body and constructor are replaced with a call to the factory method.

Print "EmployeeType::newType($arg)"

Select name of "setType"
+ Select name of "getType"

#|en| Since access methods now return a code, not the type object itself, we should rename them to make things clear to future readers.

Select "setType("

Replace "setTypeCode("

Select "getType("

Replace "getTypeCode("


Select:
```

  const ENGINEER = 0;
  const SALESMAN = 1;
  const MANAGER = 2;

```

#|en| We complete this step by moving all type code constants from <code>Employee</code> to <code>EmployeeType</code>.

Remove selected

Go to the beginning of "EmployeeType"

Print:
```

  const ENGINEER = 0;
  const SALESMAN = 1;
  const MANAGER = 2;

```

Wait 500ms

Select "|||Employee|||::" in "newType"

Replace "self"

Wait 500ms

Select "|||self|||::" in "payAmount"

Replace "EmployeeType"

Wait 500ms

Select "|||Employee|||::" in "Engineer"
+ Select "|||Employee|||::" in "Manager"
+ Select "|||Employee|||::" in "Salesman"

Wait 500ms

Type "EmployeeType"

Set step 6

#|en| Everything is now ready for applying <a href="/replace-conditional-with-polymorphism">Replace Conditional With Polymorphism</a>.

Select body of "payAmount"

#|en| First extract the implementation of <code>payAmount</code> to a new method in a type class.

Go to the end of "EmployeeType"

Print:
```


  public function payAmount() {
    switch ($this->getTypeCode()) {
      case EmployeeType::ENGINEER:
        return $this->monthlySalary;
      case EmployeeType::SALESMAN:
        return $this->monthlySalary + $this->commission;
      case EmployeeType::MANAGER:
        return $this->monthlySalary + $this->bonus;
      default:
        throw new Exception("Incorrect Employee Code");
    }
  }
```

Select "monthlySalary" in "EmployeeType"
+Select "commission" in "EmployeeType"
+Select "bonus" in "EmployeeType"

#|en| We need datа from the <code>Employee</code> object, so in the method we create the parameter to which the main <code>Employee</code> object will be passed.

Go to "payAmount(|||) {" in "EmployeeType"

Print "Employee $employee"

Select "$this->monthlySalary" in "EmployeeType"

Replace "$employee->monthlySalary"

Select "$this->commission" in "EmployeeType"

Replace "$employee->commission"

Select "$this->bonus" in "EmployeeType"

Replace "$employee->bonus"

Select body of "payAmount"

#|en| After these actions, we can set up delegation from the <code>Employee</code> class.

Print "    return $this->type->payAmount($this);"

#|en| Then start moving code to subclasses. Create <code>payAmount</code> methods in each of the subclasses and move payroll calculations there for the relevant employee types.

Go to the end of "class Engineer"

Print:
```

  public function payAmount(Employee $employee) {
    return $employee->monthlySalary;
  }
```

Wait 1000ms

Go to the end of "class Salesman"

Print:
```

  public function payAmount(Employee $employee) {
    return $employee->monthlySalary + $employee->commission;
  }
```

Wait 1000ms


Go to the end of "class Manager"

Print:
```

  public function payAmount(Employee $employee) {
    return $employee->monthlySalary + $employee->bonus;
  }
```

Set step 7

Select name of "payAmount" in "EmployeeType"

#|en| Now that the methods have been created, you can make the <code>payAmount</code> method in <code>EmployeeType</code>  abstract.

Select:
```
  public function payAmount(Employee $employee) {
    switch ($this->getTypeCode()) {
      case EmployeeType::ENGINEER:
        return $employee->monthlySalary;
      case EmployeeType::SALESMAN:
        return $employee->monthlySalary + $employee->commission;
      case EmployeeType::MANAGER:
        return $employee->monthlySalary + $employee->bonus;
      default:
        throw new Exception("Incorrect Employee Code");
    }
  }
```

Replace:
```
  abstract public function payAmount(Employee $employee);
```

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
