replace-subclass-with-fields:php

###

1.en. Apply <a href="/replace-constructor-with-factory-method">Replace Constructor with Factory Method</a> to the subclasses.

2.en. Replace subclass constructor calls with superclass factory method calls.

3.en. In the superclass, declare fields for storing the values of each of the subclass methods that return constant values.

4.en. Create a protected superclass constructor for initializing the new fields.

5.en. Create or modify the existing subclass constructors so that they call the new superclass constructor.

6.en. Implement each constant method in the parent class so that it returns the value of the corresponding field. Then remove the method from the subclass.

7.en. If the subclass constructor has additional functionality, use <a href="/inline-method">Inline Method</a> to incorporate the constructor into the superclass factory method.

8.en. Delete the subclass.



###

```
abstract class Person {
  abstract function isMale();
  abstract function getCode();
}

class Male extends Person {
  function isMale() {
    return true;
  }
  function getCode() {
    return 'M';
  }
}
class Female extends Person {
  function isMale() {
    return false;
  }
  function getCode() {
    return 'F';
  }
}

// Client code
$kent = new Male();
print("Person's gender is: " . $kent->getCode());

```

###

```
class Person {
  static function createMale() {
    return new Person(true, 'M');
  }
  static function createFemale() {
    return new Person(false, 'F');
  }
  protected function __construct($isMale, $code) {
    $this->isMale = $isMale;
    $this->code = $code;
  }

  private $isMale;
  private $code;

  function isMale() {
    return $this->isMale;
  }
  function getCode() {
    return $this->code;
  }
}

// Client code
$kent = Person::createMale();
print("Person's gender is: " . $kent->getCode());

```

###

Set step 1

#|en| Let's look at <i>Replace Subclass With Fields</i>, using the example of the same person class and its gender subclasses.

Select "return true"
+ Select "return false"
+ Select "return 'M'"
+ Select "return 'F'"

#|en| The only difference between the subclasses is that they have implementations of the abstract methods that return hard-coded constants. It is preferable to get rid of such classes.

Go to the beginning of "Person"

#|en| First use <a href="/replace-constructor-with-factory-method">Replace Constructor With Factory Method</a>. In our case, we need the factory method for each subclass.

Print:
```

  static function createMale() {
    return new Male();
  }
  static function createFemale() {
    return new Female();
  }
```

Set step 2

Select "$kent = |||new Male()|||"

#|en| Then replace all calls to subclass constructors with calls to the relevant factory methods.

Print "Person::createMale()"

#|en| After replacing all these calls, the code should not contain any more mentions of the subclasses.

Set step 3

Go to after "createFemale"

#|en| Now, in the parent class, we should declare fields for each method that returns constants in subclasses.

Print:
```


  private $isMale;
  private $code;

```

Set step 4

Go to after "createFemale"

#|en| Add a protected constructor to the parent class.

Print:
```

  protected function __construct($isMale, $code) {
    $this->isMale = $isMale;
    $this->code = $code;
  }
```

Set step 5

Go to the start of "Male"

#|en| Add constructors that call this new constructor in subclasses.

Print:
```

  function __construct() {
    parent::__construct(true, 'M');
  }
```

Go to the start of "Female"

Print:
```

  function __construct() {
    parent::__construct(false, 'F');
  }
```

#C|ru| После этого можно выполнить тестирование.
#S Всё отлично, можем продолжать!

#C|en| After this, let's perform testing.
#S Everything is OK! We can keep going.

#C|uk| Після цього можна виконати тестування.
#S Все добре, можна продовжувати.

Set step 6

Select "  abstract function isMale();"

#|en| The fields are created and initialized, but are not yet used. Now we can get the fields "in the game" by placing access methods in the parent class and removing subclass methods.

Print:
```
  function isMale() {
    return $this->isMale;
  }
```

Wait 500ms

Select whole "isMale" in "Male"

Remove selected

Wait 500ms

Select whole "isMale" in "Female"

Remove selected

Select "  abstract function getCode();"

Replace:
```
  function getCode() {
    return $this->code;
  }
```

Wait 500ms

Select whole "getCode" in "Male"

Remove selected

Wait 500ms

Select whole "getCode" in "Female"

Remove selected

Set step 7

Select "|||abstract||| class Person"
+ Select "new Male()"
+ Select "new Female()"

#|en| All subclasses are empty at this point. That allows us to remove the "abstract" keyword from the Person class and use its constructor instead the ones from subclasses (that we could simply remove).

Select "|||abstract |||class Person"

Remove selected

Wait 500ms

Select "new Male()"

Replace "new Person(true, 'M')"

Set step 8

Select whole "Male"

#|en| The <code>Male</code> class should now be removed.

Remove selected

#C|ru| Проведём тестирование, чтобы убедиться, что никакой другой код не сломался.
#S Всё хорошо, можно проделать ту же операцию с классом <code>Female</code>

#C|en| Test to ascertain whether any other code has been broken in the process.
#S Looking good! Let's do the same with the <code>Female</code> class.

#C|uk| Проведемо тестування, щоб переконатися, що ніякої іншої код не зламався.
#S Все добре, можна виконати ту ж операцію з класом <code>Female</code.


Select "new Female()"

Replace "new Person(false, 'F')"

Wait 500ms

Select whole "Female"
+Select:
```
|||
|||// Client code
```

Remove selected

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
