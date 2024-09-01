extract-interface:php

###

1.en. Create an empty interface.

2.en. Declare common operations in the interface.

3.en. Declare the necessary classes as implementing the interface.

4.en. Change type declarations in the client code to use the new interface.



###

```
class TimeSheet {
  // ...
  public function charge(Employee $employee, $days) {
    $base = $employee->getRate() * $days;
    if ($employee->hasSpecialSkill()) {
      return $base * 1.05;
    }
    else {
      return $base;
    }
  }
}

class Employee {
  // ...
  public function getRate() {
    // ...
  }
  public function hasSpecialSkill() {
    // ...
  }
}
```

###

```
class TimeSheet {
  // ...
  public function charge(Billable $employee, $days) {
    $base = $employee->getRate() * $days;
    if ($employee->hasSpecialSkill()) {
      return $base * 1.05;
    }
    else {
      return $base;
    }
  }
}

interface Billable {
  public function getRate();
  public function hasSpecialSkill();
}

class Employee implements Billable {
  // ...
  public function getRate() {
    // ...
  }
  public function hasSpecialSkill() {
    // ...
  }
}
```

###

Set step 1

#|en| Say, we have a <code>TimeSheet</code> class that is used for payroll.

Select "$employee->getRate()"
+ Select "$employee->hasSpecialSkill()"

#|en| For this, the class must know an employee's rate of pay and any special skills.

#|en| The employee has many other characteristics, in addition, to pay rate and special skills, but only the latter two are needed in this application. The fact that only this subset is needed can be emphasized by defining an interface for it.

Go to before "Employee"

Print:
```

interface Billable {
  public function getRate();
  public function hasSpecialSkill();
}

```

Go to "class Employee|||"

#|en| Then we declare Employee as implementing this interface.

Print " implements Billable"

Select "|||Employee||| $employee"

#|en| Then we can change the declaration of the <code>charge</code> method to show that only part of the Employee behavior is used.

Print "Billable"

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

#|en| In this case, a hidden benefit appears, in the form of in-code documentation. This benefit is not worth the work if talking about just one method, but if several classes start to use the <code>Billable</code> interface, this can be rather valuable.

#|en| A major payoff comes when we want to invoice cost for office equipment as well. All we need to do is implement the <code>Billable</code> interface in those classes. After that, we can include computers cost in the timesheet.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
