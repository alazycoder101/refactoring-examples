parameterize-method:php

###

1.en. Create a new method with a parameter and move code that is shared by all methods to it, by using <a href="/extract-method">extract method</a>.

2.en. In the code of the new method, replace the special/differing value with a parameter.

3.en. For each old method, find the places where it is called, replacing these calls with calls to the new method that include a parameter. Then delete the old method.



###

```
class Employee {
  // ...
  public function promoteToManager() {
    $this->type = Employee::MANAGER;
    $this->salary *= 1.5;
  }
  public function tenPercentRaise() {
    $this->salary *= 1.1;
  }
  public function fivePercentRaise() {
    $this->salary *= 1.05;
  }
}

// Somewhere in client code
if ($employee->yearsOfExperience > 5) {
  if (count($employee->clients) > 10) {
    $employee->promoteToManager();
  }
  else {
    $employee->fivePercentRaise();
  }
}
```

###

```
class Employee {
  // ...
  public function promoteToManager() {
    $this->type = Employee::MANAGER;
    $this->raise(0.5);
  }
  public function raise($factor) {
    $this->salary *= (1 + $factor);
  }
}

// Somewhere in client code
if ($employee->yearsOfExperience > 5) {
  if (count($employee->clients) > 10) {
    $employee->promoteToManager();
  }
  else {
    $employee->raise(0.05);
  }
}
```

###

Set step 1

#|en| Start refactoring by searching for repeating code.

Select "$this->salary *= 1.5"
+ Select "$this->salary *= 1.1"
+ Select "$this->salary *= 1.05"

#|en| In our case, this is the code for increasing salaries, which differs only by the increase coefficient.

Set step 2

Go to the end of "Employee"

#|en| Let's start by creating a new method with the parameter. Later on, we will send the salary increase coefficient there.

Print:
```

  public function raise($factor) {
    $this->salary *= (1 + $factor);
  }
```

#|en| Replace the repeating code with calls to our method with the correct parameter.

Select "$this->salary *= 1.5"

Replace "$this->raise(0.5)"

Wait 500ms

Select "$this->salary *= 1.1"

Replace "$this->raise(0.1)"

Wait 500ms

Select "$this->salary *= 1.05"

Replace "$this->raise(0.05)"

Select name of "tenPercentRaise"
+ Select name of "fivePercentRaise"

#|en| Now, let's get rid of "lazy" methods that only delegate to the method with parameter.

Select "$employee->|||fivePercentRaise()|||"

#|en| First, find all their calls and replace them with calls to our new method with parameter.

Print "raise(0.05)"

Select whole "fivePercentRaise"
+ Select whole "tenPercentRaise"

#|en| After the changes are complete, you can remove the methods themselves.

Remove selected

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
