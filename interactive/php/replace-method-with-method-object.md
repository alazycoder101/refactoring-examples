replace-method-with-method-object:php

###


1.en. Create a new class. Name it based on the purpose of the method that you are refactoring.



2.en. In the new class, create a private field for storing a reference to an instance of the class in which the method was previously located.



3.en. In addition, create a private field for each local variable and parameter of the method.



4.en. Create a constructor that accepts all parameters of the original method and initializes the relevant private fields.



5.en. Declare the main method and copy the code of the original method to it, replacing the local variables with private fields.



6.en. Replace the body of the original method in the original class by creating a method object and calling its main method.




###

```
class Account {
  // ...
  function gamma($inputVal, $quantity, $yearToDate) {
    $importantValue1 = ($inputVal * $quantity) + $this->delta();
    $importantValue2 = ($inputVal * $yearToDate) + 100;
    if (($yearToDate - $importantValue1) > 100) {
      $importantValue2 -= 20;
    }
    $importantValue3 = $importantValue2 * 7;
    // and so on…
    return $importantValue3 - 2 * $importantValue1;
  }
  // ...
}
```

###

```
class Account {
  // ...
  function gamma($inputVal, $quantity, $yearToDate) {
    return (new Gamma($this, $inputVal, $quantity, $yearToDate))->compute();
  }
  // ...
}

class Gamma {
  private $account; // Account
  private $importantValue1;
  private $importantValue2;
  private $importantValue3;
  private $inputVal;
  private $quantity;
  private $yearToDate;

  public function __construct(Account $source, $inputValArg, $quantityArg, $yearToDateArg) {
    $this->account = $source;
    $this->inputVal = $inputValArg;
    $this->quantity = $quantityArg;
    $this->yearToDate = $yearToDateArg;
  }
  public function compute() {
    $this->importantValue1 = ($this->inputVal * $this->quantity) + $this->account->delta();
    $this->importantValue2 = ($this->inputVal * $this->yearToDate) + 100;
    $this->importantThing();
    $this->importantValue3 = $this->importantValue2 * 7;
    // and so on…
    return $this->importantValue3 - 2 * $this->importantValue1;
  }
  private function importantThing() {
    if (($this->yearToDate - $this->importantValue1) > 100) {
      $this->importantValue2 -= 20;
    }
  }
}
```

###

Set step 1

#|en| A thorough example would require an entire chapter, so we will demonstrate this refactoring on a method that does not require it (for this reason, it's better not to question the logic of the method – it was devised without any grand plan in mind).

Select name of "gamma"

#|en| We see that one of the class methods has many sophisticated calculations and entanglement of local variables. All this makes it hard to refactor the class.

#|en| Let's convert this method to a separate class so that the local variables become fields of the class. That will isolate it and ease the further refactoring.

Go to the end of file

#|en| So, let's create a new class.

Print:
```


class Gamma {
}
```

Set step 2

#|en| First, create an immutable field for storing the source object, in the <code>Gamma</code> class.

Go to the end of "Gamma"

Print:
```

  private $account; // Account
```

Set step 3


Select 1st "$importantValue1"
+Select 1st "$importantValue2"
+Select 1st "$importantValue3"

#|en| Move all variables from the method that we want to separate…

Go to the end of "Gamma"

Print:
```

  private $importantValue1;
  private $importantValue2;
  private $importantValue3;
```

Select "gamma(|||$inputVal, $quantity, $yearToDate|||)"

#|en| …and create fields for each of the method's parameters.

Go to the end of "Gamma"

Print:
```

  private $inputVal;
  private $quantity;
  private $yearToDate;
```

Set step 4

Go to the end of "Gamma"

#|en| Create a constructor that will accept the method's parameters and store them in class fields for further use.

Print:
```


  public function __construct(Account $source, $inputValArg, $quantityArg, $yearToDateArg) {
    $this->account = $source;
    $this->inputVal = $inputValArg;
    $this->quantity = $quantityArg;
    $this->yearToDate = $yearToDateArg;
  }
```

#C|ru| Запустим тесты, чтобы проверить код на наличие ошибок.
#S Все отлично, пока что ничего не успело сломаться.

#C|en| Let's launch autotests to check for errors in code.
#S All is well, nothing has managed to break yet.

#C|uk| Запустимо тести, щоб перевірити код на наявність помилок.
#S Все добре, поки що нічого не встигло зламатися.

Set step 5

Select whole "gamma" in "Account"

#|en| Move the original method, changing it so that fields are used instead of the variables and parameters of the old method.

Go to the end of "Gamma"

Print:
```

  public function compute() {
    $importantValue1 = ($inputVal * $quantity) + $this->delta();
    $importantValue2 = ($inputVal * $yearToDate) + 100;
    if (($yearToDate - $importantValue1) > 100) {
      $importantValue2 -= 20;
    }
    $importantValue3 = $importantValue2 * 7;
    // and so on…
    return $importantValue3 - 2 * $importantValue1;
  }
```

Select 1st "$importantValue1" in "compute"

#|en| It's time to replace local variables with fields.

Select "$importantValue1" in "compute"
Replace "$this->importantValue1"
Select "$importantValue2" in "compute"
Replace "$this->importantValue2"
Select "$importantValue3" in "compute"
Replace "$this->importantValue3"
Select "$inputVal" in "compute"
Replace "$this->inputVal"
Select "$quantity" in "compute"
Replace "$this->quantity"
Select "$yearToDate" in "compute"
Replace "$this->yearToDate"

Select "delta()" in "compute"

#|en| Modify any calls to the <code>Account</code> methods so that they are run via the <code>account</code> field.

Print "account->delta()"

Set step 6

Select body of "gamma" in "Account"

#|en| Then simply replace the body of the old method with a call to the method in the new class.

Print:
```
    return (new Gamma($this, $inputVal, $quantity, $yearToDate))->compute();
```

#C|ru| Запустим тесты, чтобы проверить код на наличие ошибок.
#S Все работает отлично!

#C|en| Let's launch autotests to check for errors in code.
#S Everything is working great!

#C|uk| Запустимо тести, щоб перевірити код на наявність помилок.
#S Все працює добре.

Select:
```
    if (($this->yearToDate - $this->importantValue1) > 100) {
      $this->importantValue2 -= 20;
    }
```

#|en| The benefit of this refactoring is that you can now easily apply <a href="/extract-method">Extract Method</a> to the <code>compute()</code> method without worrying about passing correct arguments between sub-methods.

Go to the end of "Gamma"

Print:
```

  private function importantThing() {
    if (($this->yearToDate - $this->importantValue1) > 100) {
      $this->importantValue2 -= 20;
    }
  }
```

Wait 500ms

Select in "compute":
```
    if (($this->yearToDate - $this->importantValue1) > 100) {
      $this->importantValue2 -= 20;
    }
```

Replace "    $this->importantThing();"

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
