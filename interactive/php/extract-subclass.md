extract-subclass:php

###


1.en. Create a new subclass from the class of interest.



2.en. If you need additional data to create objects from a subclass, create a constructor and add the necessary parameters to it. Do not forget to call the constructor's parent implementation.



3.en. Find all calls to the constructor of the parent class. When the functionality of a subclass is necessary, replace the parent constructor with the subclass constructor.



4.en. Move the necessary methods and fields from the parent class to the subclass. Do this via <a href="/push-down-method">Push Down Method</a> and <a href="/push-down-field">Push Down Field</a>. It is simpler to start by moving the methods first. This way, the fields remain accessible throughout the whole process: from the parent class prior to the move, and from the subclass itself after the move is complete.



5.en. After the subclass is ready, find all the old fields that controlled the choice of functionality. Delete these fields by using polymorphism to replace all the operators in which the fields had been used.




###

```
class JobItem {
  private $quantity;
  private $unitPrice;
  private $employee; // Employee
  private $isLabor;

  public function __construct($quantity, $unitPrice, $isLabor, Employee $employee) {
    $this->quantity = $quantity;
    $this->unitPrice = $unitPrice;
    $this->isLabor = $isLabor;
    $this->employee = $employee;
  }
  public function getTotalPrice() {
    return $this->quantity * $this->getUnitPrice();
  }
  public function getQuantity() {
    return $this->quantity;
  }
  public function getUnitPrice() {
    return ($this->isLabor) ?
      $this->employee->getRate() : $this->unitPrice;
  }
  public function getEmployee() {
    return $this->employee;
  }
}

class Employee {
  private $rate;
  public function __construct($rate) {
    $this->rate = $rate;
  }
  public function getRate() {
    return $this->rate;
  }
}

// Somewhere in client code
$kent = new Employee(50);
$j1 = new JobItem(5, 0, true, kent);
$j2 = new JobItem(15, 10, false, null);
$total = $j1->getTotalPrice() + $j2->getTotalPrice();
```

###

```
abstract class JobItem {
  private $quantity;

  protected function __construct($quantity) {
    $this->quantity = $quantity;
  }
  public function getTotalPrice() {
    return $this->quantity * $this->getUnitPrice();
  }
  public function getQuantity() {
    return $this->quantity;
  }
  public abstract function getUnitPrice();
}

class PartsItem extends JobItem {
  private $unitPrice;

  public function __construct($quantity, $unitPrice) {
    parent::__construct($quantity);
    $this->unitPrice = $unitPrice;
  }
  public function getUnitPrice() {
    return $this->unitPrice;
  }
}

class LaborItem extends JobItem {
  private $employee; // Employee

  public function __construct($quantity, Employee $employee) {
    parent::__construct($quantity);
    $this->employee = $employee;
  }
  public function getEmployee() {
    return $this->employee;
  }
  public function getUnitPrice() {
    return $this->employee->getRate();
  }
}

class Employee {
  private $rate;
  public function __construct($rate) {
    $this->rate = $rate;
  }
  public function getRate() {
    return $this->rate;
  }
}

// Somewhere in client code
$kent = new Employee(50);
$j1 = new LaborItem(5, kent);
$j2 = new PartsItem(15, 10);
$total = $j1->getTotalPrice() + $j2->getTotalPrice();
```

###

Set step 1

#|en| We start with the <code>JobItem</code> class, which tracks the time and materials used to fix a client's car in a local garage. This class is also responsible for calculating the price client should pay.

Select name of "getUnitPrice"

#|en| The price usually consists of several items. First, it's the fixed cost of certain parts. Second, it's the cost of a mechanic's time, multiplied by his rate (that can be taken directly from the <code>Employee</code> class).

#|en| So, the price is calculated in several ways, all of which sit in a single class. And that starts to smell as a <i>Large Class</i>.

#|en| As a solution, we could extract the <code>LaborItem</code> subclass and move all code, which are associated with manual work, to that subclass. Then we could leave only fixed amounts in the original class.

Go to after "JobItem"

#|en| So, let's create a subclass.

Print:
```


class LaborItem extends JobItem {
}
```


Set step 2

Select name of "LaborItem"

#|en| Now we should start to push down the labor-related methods.

#|en| Above all, we need a constructor because <code>JobItem</code> does not have the constructor we need, one that would accept only the employee object and number of hours spent.

Select parameters of "__construct" in "JobItem"

#|en| For now, we will copy the signature of the parent constructor.

Go to the start of "LaborItem"

Print:
```

  public function __construct($quantity, $unitPrice, $isLabor, Employee $employee) {
    parent::__construct($quantity, $unitPrice, $isLabor, $employee);
  }
```

Select parameters of "__construct" in "LaborItem"

#|en| That is enough to make the new subclass stop displaying errors. However, this constructor is unwieldy: some arguments are necessary for <code>LaborItem</code> and others are not. We will fix this a little later.

Set step 3

Select 1st "new JobItem"

#|en| During the next step, we need to search for references to the <code>JobItem</code> constructor and cases when the <code>LaborItem</code> constructor should be called instead.

Print "new LaborItem"

#|en| At this point we will not touch the variable type, changing only the type of the constructor.

#|en| That is because the new type should be used only where necessary. We do not yet have a specific interface for the subclass, so it is better to not declare any varieties.

Select parameters of "__construct" in "JobItem"
+ Select parameters of "__construct" in "LaborItem"

#|en| That is the perfect time to perform housekeeping on the lists of constructors parameters. Let's apply <a href="/remove-parameter">Remove Parameter</a> to each of them.

Select visibility of "__construct" in "JobItem"

#|en| First, we need to refer to the parent class. We create a new constructor and declare the previous one protected (the subclass still needs it).

Go to "$isLabor|||, Employee $employee" in "JobItem"

Print " = false"

Go to "Employee $employee|||" in "JobItem"

Print " = null"

Select ", false, null"

#|en| External constructor calls should now use the new constructor.

Remove selected

Wait 500ms

#C|ru| Выполним тестирование, чтобы удостовериться в отсутствии ошибок.
#S Всё отлично, можем продолжать!

#C|en| Let's run testing to make sure there are no errors.
#S Everything is OK! We can keep going.

#C|uk| Виконаємо тестування, щоб упевнитися у відсутності помилок.
#S Все добре, можемо продовжувати.

Select "($quantity, |||$unitPrice, $isLabor, |||Employee $employee)"

#|en| Now, we apply <a href="/remove-parameter">Remove Parameter</a> to the subclass constructor to get rid of unnecessary parameters.

Remove selected

Wait 500ms

Select "parent::__construct($quantity, |||$unitPrice|||, $isLabor, $employee)"

Replace "0"

Wait 500ms

Select "parent::__construct($quantity, 0, |||$isLabor|||, $employee)"

Replace "true"

Wait 500ms

Select "new LaborItem(5, |||0, true, |||kent)"

Remove selected

Wait 500ms

Set step 4

Select name of "JobItem"

#|en| Subsequently we can push down parts of <code>JobItem</code> to the subclass. First look at the methods.

Select whole of "getEmployee"

#|en| Start with applying <a href="/push-down-method">Push Down Method</a> to <code>getEmployee</code>. 

Remove selected

Go to the end of "LaborItem"

Print:
```

  public function getEmployee() {
    return $this->employee;
  }
```

Select "|||private||| $employee;"

#|en| Since the <code>employee</code> field will later be pushed down into a subclass, for now we will declare it protected.

Print "protected"

Select ", Employee $employee = null" in "JobItem"
+ Select "|||, $employee|||)"


#|en| Once the <code>employee</code> field is protected, we can clean up the constructors so that <code>employee</code> is initialized only in the subclass.

Remove selected

Select:
```
    $this->employee = $employee;

```

Remove selected

Go to the end of "__construct" in "LaborItem"

Print:
```

    $this->employee = $employee;
```

Set step 5

Select "private $isLabor;"

#|en| The <code>isLabor</code> field is used to indicate information now implied by the hierarchy, so the field can be removed<br/><br/>The best way to do so is to first use <a href="/self-encapsulate-field">Self-Encapsulate Field</a> and then override the getter in subclasses so that it return own fixed value (such methods usually called "polymorphic constant method").

#|en| So we declare <code>isLabor</code> getters in both classes.

Go to the end of "JobItem"

Print:
```

  protected function isLabor() {
    return false;
  }
```

Go to the end of "LaborItem"

Print:
```

  protected function isLabor() {
    return true;
  }
```

#|en| Now replace use of the field with calls to the getters.

Select "isLabor" in "getUnitPrice"

Replace "isLabor()"

Select:
```
  private $isLabor;

```
+ Select ", $isLabor = false"
+ Select "|||, true|||)"
+ Select:
```
    $this->isLabor = $isLabor;

```

#|en| Remove the <code>isLabor</code> field.

Remove selected

Select "isLabor" in "getUnitPrice"

#|en| Now look at the uses of the <code>isLabor</code> methods. They should be refactored using <a href="/replace-conditional-with-polymorphism">Replace Conditional With Polymorphism</a>.

Select body of "getUnitPrice"

Replace "    return $this->unitPrice;"

Wait 500ms

Go to the end of "LaborItem"

Print:
```

  public function getUnitPrice() {
    return $this->employee->getRate();
  }
```

Select whole "isLabor" in "JobItem"
+Select whole "isLabor" in "LaborItem"

#|en| Then it becomes clear that <code>isLabor</code> methods are not used anywhere and can be safely removed from all classes.

Remove selected

Select name of "JobItem"

#|en| After pushing methods down to a subclass, you can consider moving some of the fields as well. We can apply <a href="/push-down-field">Push Down Field</a> to these fields. In some cases, this is impossible because the fields are still used in the context of superclass.

#|en| In our case, everything is ready for us to move the <code>employee</code> field to <code>LaborItem</code>.

Select:
```
  protected $employee; // Employee

```

Remove selected

Go to start of "LaborItem"

Print:
```

  private $employee; // Employee

```

Select:
```
  private $unitPrice;
```

#C|ru| Выполним тестирование, чтобы удостовериться в отсутствии ошибок.
#S Всё отлично, можем продолжать!

#C|en| Let's run testing to make sure there are no errors.
#S Everything is OK! We can keep going.

#C|uk| Виконаємо тестування, щоб упевнитися у відсутності помилок.
#S Все добре, можемо продовжувати.

#|en| So extraction of <code>LaborItem</code> is complete. But one more thing remains. Since the price of spare parts (<code>unitPrice</code>) is used only in the <code>JobItem</code> class and is not needed in <code>LaborItem</code>, we can go one step further and apply <a href="/extract-subclass">Extract Subclass</a> to <code>JobItem</code> again and create a class that represents spare parts.

Go to after "JobItem"

#|en| Create a <code>PartsItem</code> subclass and change the client code to use the constructor of the created subclass.

Print:
```


class PartsItem extends JobItem {
  public function __construct($quantity, $unitPrice) {
    parent::__construct($quantity, $unitPrice);
  }
}
```

Wait 500ms

Select "new |||JobItem|||"

Replace "PartsItem"

Wait 500ms

Select ", |||$unitPrice|||" in "JobItem"
+Select "$this->unitPrice = $unitPrice;" in "JobItem"
+Select name of "getUnitPrice" in "JobItem"

#|en| Before pushing down the <code>unitPrice</code> field we must first push down its initialization code, as well as the method in which it is used.

Select "|||private||| $unitPrice"

#|en| To avoid compilation errors, change the visibility of the field, making it accessible to the <code>PartsItem</code> class.

Replace "protected"

Wait 500ms

Select:
```
    $this->unitPrice = $unitPrice;

```

#|en| So, push down initialization code of the field.

Remove selected

Go to end of "__construct" in "PartsItem"

Print:
```

    $this->unitPrice = $unitPrice;
```

Wait 500ms

Select ", $unitPrice" in "JobItem"

Remove selected

Wait 500ms

Select "|||, $unitPrice|||);" in "PartsItem"
+Select ", 0" in "LaborItem"

Wait 500ms

Remove selected

Wait 500ms

Select name of "getUnitPrice" in "JobItem"

#|en| Then push down the <code>getUnitPrice</code> method.

Select body of "getUnitPrice" in "JobItem"

Remove selected

Wait 500ms

Go to end of "PartsItem"

Print:
```

  public function getUnitPrice() {
    return $this->unitPrice;
  }
```

Wait 500ms

Select whole "getUnitPrice" in "JobItem"

#|en| After that, declare the parent method as abstract.

Print:
```

```
Go to after "getQuantity" in "JobItem"
Print:
```

  public abstract function getUnitPrice();
```

Wait 500ms

Select name of "JobItem"

#|en| And with it, a <code>JobItem</code> class.

Go to "|||class JobItem"
Print " "
Go to "||| class JobItem"
Print "abstract"

Wait 500ms

Select visibility of "__construct" in "JobItem"

Replace "protected"

Wait 500ms

Select:
```
  protected $unitPrice;

```

#|en| Finally, we push down the field itself.

Remove selected

Go to start of "PartsItem"

Wait 500ms

Print:
```

  private $unitPrice;

```

Wait 500ms

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
