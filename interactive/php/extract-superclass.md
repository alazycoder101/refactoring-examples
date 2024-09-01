extract-superclass:php

###

1.en. Create an abstract superclass.

2.en. Use <a href="/pull-up-field">Pull Up Field</a>, <a href="/pull-up-method">Pull Up Method</a>, and <a href="/pull-up-constructor-body">Pull Up Constructor Body</a> to move the common functionality to a superclass. Start with the fields, since in addition to the common fields you will need to move the fields that are used in the common methods.

3.en. Look for places in the client code where use of subclasses can be replaced with your new class (such as in type declarations).



###

```
class Employee {
  private $name;
  private $annualCost;
  private $id;

  public function __construct($name, $id, $annualCost) {
    $this->name = $name;
    $this->id = $id;
    $this->annualCost = $annualCost;
  }
  public function getAnnualCost() {
    return $this->annualCost;
  }
  public function getId() {
    return $this->id;
  }
  public function getName() {
    return $this->name;
  }
}

class Department {
  private $name;
  private $staff = array();

  public function __construct($name) {
    $this->name = $name;
  }
  public function getTotalAnnualCost() {
    $result = 0;
    foreach ($this->staff as $each) {
      $result += $each->getAnnualCost();
    }
    return $result;
  }
  public function getHeadCount() {
    return count($staff);
  }
  public function getStaff() {
    return $this->staff;
  }
  public function addStaff(Employee $arg) {
    $this->staff[] = $arg;
  }
  public function getName() {
    return $this->name;
  }
}
```

###

```
abstract class Party {
  protected $name;

  protected function __construct($name) {
    $this->name = $name;
  }
  public function getName() {
    return $this->name;
  }
  public abstract function getAnnualCost();
  public abstract function getHeadCount();
}

class Employee extends Party {
  private $annualCost;
  private $id;

  public function __construct($name, $id, $annualCost) {
    parent::__construct($name);
    $this->id = $id;
    $this->annualCost = $annualCost;
  }
  public function getAnnualCost() {
    return $this->annualCost;
  }
  public function getId() {
    return $this->id;
  }
  public function getHeadCount() {
    return 1;
  }
}

class Department extends Party {
  private $items = array();

  public function __construct($name) {
    parent::__construct($name);
  }
  public function getAnnualCost() {
    $result = 0;
    foreach ($this->items as $each) {
      $result += $each->getAnnualCost();
    }
    return $result;
  }
  public function getHeadCount() {
    $headCount = 0;
    foreach ($this->items as $each) {
      $headCount += $each->getHeadCount();
    }
    return $headCount;
  }
  public function getItems() {
    return $this->items;
  }
  public function addItem(Party $arg) {
    $this->items[] = $arg;
  }
}
```

###

Set step 1

Select name of "Employee"
+ Select name of "Department"

#|en| Let's look at <i>Extract Superclass</i> using the example of employees and their department.

Select "private $name"

#|en| These classes have several traits in common. First, as with employees, departments also have names.

Select "private $annualCost"
+ Select name of "getAnnualCost"
+ Select name of "getTotalAnnualCost"

#|en| Second, for both classes there is an annual budget, although the calculation ways are slightly different.

#|en| For this reason, it would be good to extract these aspects to a shared parent class.

Go to before "Employee"

#|en| To start, we create a new parent class, and we define the existing classes as subclasses of it.

Print:
```



```

Go to:
```
|||

class Employee
```

Print:
```
abstract class Party {
}
```

Wait 500ms

Go to "class Employee|||"

Replace " extends Party"

Wait 500ms

Go to "class Department|||"

Replace " extends Party"

Wait 500ms

Set step 2

Select:
```
  private $name;
```

#|en| Now we can start pulling up code to the parent class. Usually it is simpler to employ <a href="/pull-up-field">Pull Up Field</a> first.

Go to start of "Party"

Print:
```

  protected $name;
```

Select:
```
  private $name;

```

Remove selected

Select whole of "getName"

#|en| Then use <a href="/pull-up-method">Pull Up Method</a> on the methods for accessing the field.

Go to end of "Party"

Print:
```


  public function getName() {
    return $this->name;
  }
```

Wait 500ms

Select in "Employee":
```

  public function getName() {
    return $this->name;
  }
```
+ Select in "Department":
```

  public function getName() {
    return $this->name;
  }
```

Remove selected

Select:
```
$this->name = $name;
```

#|en| The fields should be protected from the public, but for this we must first do <a href="/pull-up-constructor-body">Pull Up Constructor Body</a> to initialize them.

Go to before "getName" in "Party"

Print:
```

  protected function __construct($name) {
    $this->name = $name;
  }
```

#|en| In the subclasses, we can go ahead and remove code initialization, placing parent constructor calls there instead.

Select "$this->name = $name;" in "Employee"
+ Select "$this->name = $name;" in "Department"

Replace "parent::__construct($name);"

#|en| The name has been moved, which leaves us only the annual budget.

Select name of "getAnnualCost"
+ Select name of "getTotalAnnualCost"

#|en| The <code>getTotalAnnualCost</code> and <code>getAnnualCost</code> methods have the same purpose, so they should have the same name. Use <a href="/rename-method">Rename Method</a> to give them the same name.

Select name of "getTotalAnnualCost"

Replace "getAnnualCost"

#|en| The bodies of the methods are currently different, so we cannot use <a href="/pull-up-method">Pull Up Method</a>. On the other hand, we can declare an abstract method with the same name in the parent class.

Go to the end of "Party"

Print:
```

  public abstract function getAnnualCost();
```

Wait 500ms

Set step 3

Select name of "Party"

#|en| Having made these changes, let's look at clients of both classes to determine whether we can make them use the new parent class.

Select body of "getAnnualCost" in "Department"

#|en| One of the clients of the classes is the <code>Department</code> class itself, which contains a collection of employee classes. The <code>getAnnualCost</code> method uses only the annual budget calculation method, which is now declared in <code>Party</code>.

#|en| This behavior offers a new opportunity. We can consider using the <a href="https://refactoring.guru/design-patterns/composite">Composite</a> pattern on <code>Department</code> and <code>Employee</code>.

#|en| That allows including one department in another. The result is new functionality, so strictly speaking this goes beyond refactoring.

Select 1st "$staff" in "Department"

#|en| Be that as it may, if the Composite pattern were necessary, we would get it by changing the type of the <code>staff</code> field.

Select "$|||staff|||" in "Department"
+Select ">|||staff|||" in "Department"

#|en| After that, it would be good to give the list a more generic name.

Replace "items"

Wait 500ms

Select name of "getStaff" in "Department"
+Select name of "addStaff" in "Department"

#|en| And appropriately edit the <code>getStaff</code> and <code>addStaff</code> methods.

Select name of "getStaff" in "Department"

Replace "getItems"

Wait 500ms

Select name of "addStaff" in "Department"

Replace "addItem"

Wait 500ms

Select "Employee" in "Department"

Print "Party"

Wait 500ms

Select body of "getHeadCount"

#|en| To complete the Composite pattern, the <code>getHeadCount</code> method should be made recursive.

Print:
```
    $headCount = 0;
    foreach ($this->items as $each) {
      $headCount += $each->getHeadCount();
    }
    return $headCount;
```

#|en| But for this approach to work, we must create an equivalent method in <code>Employee</code> that simply returns <code>1</code>.

Go to the end of "Employee"

Print:
```

  public function getHeadCount() {
    return 1;
  }
```

Go to the end of "Party"

#|en| After that this method should also be declared abstract in the parent class.

Print:
```

  public abstract function getHeadCount();
```

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
