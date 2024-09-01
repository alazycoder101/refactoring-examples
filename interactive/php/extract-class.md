extract-class:php

###

1.en. Create a new class to contain the relevant functionality.

2.en. Create a relationship between the old class and the new one.

3.en. Use <a href="/move-field">move field</a> and <a href="/move-method">move method</a> for each field that method that you have decided to move to a new class.

4.en. Decide whether to make the new class accessible from outside the object of the old class.



###

```
class Person {
  private $name;
  private $officeAreaCode;
  private $officeNumber;
  
  public function getName() {
    return $this->name;
  }
  public function getTelephoneNumber() {
    return ("(" . $this->officeAreaCode . ") " . $this->officeNumber);
  }
  public function getOfficeAreaCode() {
    return $this->officeAreaCode;
  }
  public function setOfficeAreaCode($arg) {
    $this->officeAreaCode = arg;
  }
  public function getOfficeNumber() {
    return $this->officeNumber;
  }
  public function setOfficeNumber($arg) {
    $this->officeNumber = $arg;
  }
}
```

###

```
class Person {
  private $name;
  private $officeTelephone; // TelephoneNumber
  
  public function __construct() {
    $this->officeTelephone = new TelephoneNumber();
  }
  
  public function getName() {
    return $this->name;
  }
  public function getOfficeTelephone() {
    return $this->officeTelephone;
  }
  public function getTelephoneNumber() {
    return $this->officeTelephone->getTelephoneNumber();
  }
}

class TelephoneNumber {
  private $areaCode;
  private $number;

  public function getAreaCode() {
    return $this->areaCode;
  }
  public function setAreaCode($arg) {
    $this->areaCode = $arg;
  }
  public function getNumber() {
    return $this->number;
  }
  public function setNumber($arg) {
    $this->number = $arg;
  }
  public function getTelephoneNumber() {
    return ("(" . $this->areaCode . ") " . $this->number);
  }
}
```

###

Set step 1

#|en| Let's look at <i>Extract Class</i> using the example of a simple class that describes a person.

Select:
```
  private |||$officeAreaCode|||;
  private |||$officeNumber|||;
```
+ Select name of "getTelephoneNumber"
+ Select name of "getOfficeAreaCode"
+ Select name of "setOfficeAreaCode"
+ Select name of "getOfficeNumber"
+ Select name of "setOfficeNumber"

#|en| In this example, we can isolate methods related to phone numbers to a separate class.

Go to the end of file

#|en| Let's start by defining the phone number class.

Print:
```


class TelephoneNumber {
}
```

Set step 2

Select name of "Person"

#|en| Easy as pie! Now we create a reference from the <code>Person</code> class to the phone number class.

Go to "private $officeNumber;|||"

Print:
```

  private $officeTelephone; // TelephoneNumber
  
  public function __construct() {
    $this->officeTelephone = new TelephoneNumber();
  }
```

Set step 3

Select "private |||$officeAreaCode|||"
+ Select name of "getOfficeAreaCode"
+ Select name of "setOfficeAreaCode"

#|en| Everything is ready to start moving fields and methods. We use <a href="/move-field">Move Field</a> to move the <code>officeAreaCode</code> field to the <code>TelephoneNumber</code> class.

Go to the start of "TelephoneNumber"

Print:
```

  private $areaCode;

  public function getAreaCode() {
    return $this->areaCode;
  }
  public function setAreaCode($arg) {
    $this->areaCode = $arg;
  }
```

Select "areaCode" in "TelephoneNumber"

#|en| Did you notice? We immediately renamed the field to be more neutral. That improves our chances of reusing the class.

Select name of "getOfficeAreaCode"
+ Select name of "setOfficeAreaCode"

#|en| Now we should change the methods, which used the moved field so that they access it through a <code>TelephoneNumber</code> object.

Select body of "getOfficeAreaCode"

Replace:
```
    return $this->officeTelephone->getAreaCode();
```

Wait 500ms

Select body of "setOfficeAreaCode"

Replace:
```
    $this->officeTelephone->setAreaCode($arg);
```

#|en| We can also turn all cases of direct field access to the proper getter/setter calls.

Select "officeAreaCode" in "getTelephoneNumber"

Replace "getOfficeAreaCode()"

Select:
```
  private $officeAreaCode;

```

#|en| At last, we can remove the field from the original class.

Remove selected

Select "private |||$officeNumber|||"

#|en| The <code>areaCode</code> is all done. Similarly, we move the <code>officeNumber</code> field…

Go to "private $areaCode;|||"

Print:
```

  private $number;
```

Go to the end of "TelephoneNumber"

Print:
```

  public function getNumber() {
    return $this->number;
  }
  public function setNumber($arg) {
    $this->number = $arg;
  }
```

Select name of "getTelephoneNumber"

#|en| …and the method for getting the formatted phone number <code>getTelephoneNumber()</code>.

Go to the end of "TelephoneNumber"

Print:
```

  public function getTelephoneNumber() {
    return ("(" . $this->areaCode . ") " . $this->number);
  }
```

Select "private |||$officeNumber|||"

#|en| After that, we can delegate all phone functionality to the <code>TelephoneNumber</code> class.

Select body of "getTelephoneNumber"

Replace:
```
    return $this->officeTelephone->getTelephoneNumber();
```

Select body of "getOfficeNumber"

Replace:
```
    return $this->officeTelephone->getNumber();
```

Select body of "setOfficeNumber"

Replace:
```
    $this->officeTelephone->setNumber($arg);
```

Select:
```
  private $officeNumber;

```

Remove selected

#C|ru| Выполним тестирование, чтобы удостоверится, что код остался рабочим.
#S Всё отлично, код работает корректно.

#C|en| Let's launch autotests to check for errors in code.
#S Everything is OK! Code works correctly.

#C|uk| Виконаємо тестування, щоб упевниться, що код залишився робочим.
#S Все добре, код працює коректно.

Set step 4

Select "private $officeTelephone"

#|en|+ Here we should decide how available we want this new field to be for a client code. We can hide it entirely using delegation methods for accessing all the fields (as is currently done)…

Select whole "getOfficeAreaCode"
+ Select whole "setOfficeAreaCode"
+ Select whole "getOfficeNumber"
+ Select whole "setOfficeNumber"

#|en|= …or remove all these methods and make the field public.

Remove selected


#|en| To do this, we will need to create a public getter for the associated object so that clients can access it.

Go to before "getTelephoneNumber"

Print:
```

  public function getOfficeTelephone() {
    return $this->officeTelephone;
  }
```

Select name of "getOfficeTelephone"

#|en| But if we want to make the field public, let's consider some of the dangers related to object references. What about the fact that the client can change the area code when opening a phone number? Any code that has access to a class instance via the public getter could perform such change.

#|en| The following options are possible: <ul><li>Any object can change any part of the phone number. In this case the phone number becomes a reference and you should look at <a href="/change-value-to-reference">Change Value to Reference</a>. Access to the phone number is implemented through an instance of <code>Person</code>.</li><li>We do not want anyone to be able to change a phone number except through the methods of an instance of the <code>Person</code> class. The phone number can be made read-only or access to it can be limited to an appropriate method.</li><li>We can also clone an instance of the <code>TelephoneNumber</code> class before providing access to it. But this can cause confusion because people will think that they can change this value. In addition, clients may have problems with references if the phone number is frequently passed .</li></ul>

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
