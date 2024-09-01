extract-method:php

###

1.en. Create a new method and name it in a way that makes its purpose self-evident.

2.en. Copy the relevant code fragment to your new method. Delete the fragment from its old location and put a call for the new method there instead.

3.en. In the new method, create parameters for passing values from the original method.

4.en. Pass the results and other changed data back to the original method.



###

```
function printOwing() {
  $e = $this->orders->elements();
  $outstanding = 0;

  // print banner
  print("*****************************\n");
  print("****** Customer totals ******\n");
  print("*****************************\n");

  // print owings
  while ($e->hasMoreElements()) {
    $each = $e->nextElement();
    $outstanding += $each->getAmount();
  }

  // print details
  print("name: " . $this->name);
  print("amount: " . $outstanding);
}
```

###

```
function printOwing() {
  $this->printBanner();
  $outstanding = $this->getOutstanding();
  $this->printDetails($outstanding);
}

function printBanner() {
  print("*****************************\n");
  print("****** Customer totals ******\n");
  print("*****************************\n");
}

function printDetails($outstanding) {
  print("name: " . $this->name);
  print("amount: " . $outstanding);
}

function getOutstanding() {
  $e = $this->orders->elements();
  $outstanding = 0;
  while ($e->hasMoreElements()) {
    $each = $e->nextElement();
    $outstanding += $each->getAmount();
  }
  return $outstanding;
}
```

###

Set step 1

#|en| Let's take a look at <i>Extract Method</i> using this function as an example.

Select in "printOwing":
```
  // print banner
  print("*****************************\n");
  print("****** Customer totals ******\n");
  print("*****************************\n");
```

#|en| We begin with a very, very simple case. The code for displaying a banner can be easily extracted via copy and paste.

Wait 500ms

Go to the end of file

Print:
```


function printBanner() {
  print("*****************************\n");
  print("****** Customer totals ******\n");
  print("*****************************\n");
}
```

Set step 2

Select in "printOwing":
```
  // print banner
  print("*****************************\n");
  print("****** Customer totals ******\n");
  print("*****************************\n");
```

#|en| After that, we replace the code in the original method with a call to the new method.

Remove selected

Print:
```
  $this->printBanner();
```

#C|ru| Запускаем авто-тесты, чтобы убедиться, что всё работает.
#S Ура, мы успешно извлекли первый метод.

#C|en| Let's run autotests to verify that everything is functional.
#S Cause for celebration – we have successfully extracted the first method!

#C|uk| Запускаємо авто-тести, щоб переконатися, що все працює.
#S Ура, ми успішно витягли перший метод.

Set step 3

Select:
```
  // print details
  print("name: " . $this->name);
  print("amount: " . |||$outstanding|||);

```

#|en| Now things get trickier. The problem with extracting complex methods is buried in local variables.

Select in "printOwing":
```
  // print details
  print("name: " . $this->name);
  print("amount: " . $outstanding);

```

#|en| Let's try to extract the method for displaying the details.

Wait 500ms

Go to the end of file

Print:
```


function printDetails() {
  print("name: " . $this->name);
  print("amount: " . $outstanding);
}
```

Select in "printOwing":
```
  // print details
  print("name: " . $this->name);
  print("amount: " . $outstanding);
```

Remove selected

Print "  $this->printDetails();"

#C|ru| Давайте запустим тестирование.
#F Ошибка! Переменная <code>outstanding</code> в методе <code>printDetails()</code> не определена.

#C|en| Let's start testing.
#F Error! The variable <code>outstanding</code> in method <code>printDetails()</code> is not defined.

#C|uk| Давайте запустимо тестування.
#F Помилка! Змінна <code>outstanding</code> в методі <code>printDetails()</code> не визначена.

Select "$outstanding" in "printDetails"

#|en| Ah… Yes, we really did move the <code>outstanding</code> variable out of the original method but no value is assigned to it in the new method.

#|en| The better solution is to convert that variable to a method parameter and pass its value from the original method.

Go to parameters of "printDetails"

Print "$outstanding"

Go to text "printDetails(|||)" in "printOwing"

Print "$outstanding"

#C|ru| Запустим авто-тесты сейчас.
#S Отлично, всё работает! Двигаемся дальше.

#C|en| Let's start autotests now.
#S A-OK! Let's keep moving.

#C|uk| Запустимо авто-тести зараз.
#S Супер, все працює! Рухаємося далі.

Set step 4

Select in "printOwing":
```
  $e = $this->orders->elements();
  $outstanding = 0;
```
+ Select in "printOwing":
```
  // print owings
  while ($e->hasMoreElements()) {
    $each = $e->nextElement();
    $outstanding += $each->getAmount();
  }
```

#|en| Now on to the extraction of the code for calculating amounts outstanding.


Wait 500ms

Go to the end of file

Print:
```


function getOutstanding() {
  $e = $this->orders->elements();
  $outstanding = 0;
  while ($e->hasMoreElements()) {
    $each = $e->nextElement();
    $outstanding += $each->getAmount();
  }
}
```

Select in "printOwing":
```
  $e = $this->orders->elements();
  $outstanding = 0;


```

Remove selected

Select in "printOwing":
```

  // print owings
  while ($e->hasMoreElements()) {
    $each = $e->nextElement();
    $outstanding += $each->getAmount();
  }

```

Remove selected

Print "  $this->getOutstanding();"

Select in "getOutstanding":
```
  |||$e||| = $this->orders->elements();
  |||$outstanding||| = 0;
```

#|en| In this case, additional difficulties are caused by local variables to which new values are assigned. It is quite possible that these values are used in the remaining code of the main method.

#|en| If a value is assigned to the parameter, you can get rid of this by using <i>Remove Assignments to Parameters</i> refactoring.

Select in "getOutstanding":
```
  |||$outstanding||| = 0;
```

#|en|<+ Let's check each variable.

+ Select in "printOwing":
```
  $this->printDetails(|||$outstanding|||);
```

#|en|<= Here, the problem is caused by the <code>outstanding</code> variable, which is then used in the <code>printDetails()</code> call.

#|en|< Pass it back to the original method.

Go to the end of "getOutstanding"

Print:
```

  return $outstanding;
```

Go to text "|||$this->getOutstanding()" in "printOwing"

Print "$outstanding = "

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
