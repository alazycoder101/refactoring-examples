replace-conditional-with-polymorphism:php

###


1.en. If the conditional is in a method that performs other actions as well, perform <a href="/extract-method">Extract Method</a>.



2.en. For each hierarchy subclass, redefine the method that contains the conditional and copy the code of the corresponding conditional branch to that location.



3.en. Delete this branch from the conditional.



4.en. Repeat replacement until the conditional is empty. Then delete the conditional and declare the method abstract.




###

```
class Employee {
  // ...
  private $type; // EmployeeType
  public function getTypeCode() {
    return $this->type->getTypeCode();
  }

  public $monthlySalary;
  public $commission;
  public $bonus;
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
}
class Engineer extends EmployeeType {
  public function getTypeCode() {
    return EmployeeType::ENGINEER;
  }
}
class Salesman extends EmployeeType {
  public function getTypeCode() {
    return EmployeeType::SALESMAN;
  }
}
class Manager extends EmployeeType {
  public function getTypeCode() {
    return EmployeeType::MANAGER;
  }
}
```

###

```
class Employee {
  // ...
  private $type; // EmployeeType
  public function getTypeCode() {
    return $this->type->getTypeCode();
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

#|en| Let's take a look at this refactoring in the context of code for calculating payroll for different types of employees (see <a href="/replace-type-code-with-state-strategy">Replace Type Code with State/Strategy</a>).

Select body of "payAmount"

#|en| See that big conditional inside the <code>payAmount()</code> method? Let's try to get rid of it.

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
