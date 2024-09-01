replace-type-code-with-class:php

###

1.en. Create a new class and give it a new name that corresponds to the purpose of the coded type. Here we will call it <i>type class</i>.

2.en. Copy the field containing type code to the <i>type class</i> and make it private. Then create a getter for the field. A value will be set for this field only from the constructor.

3.en. For each value of the coded type, create a static method in the <i>type class</I>.

4.en. In the original class, replace the type of the coded field with <i>type class</i>. Create a new object of this type in the constructor as well as in the field setter. Change the field getter so that it calls the <i>type class</i> getter.

5.en. Replace any mentions of values of the coded type with calls of the relevant <i>type class</i> static methods.

6.en. Remove the coded type constants from the original class and make the <i>type class</i> constructor private.



###

```
class Person {
  const O = 0;
  const A = 1;
  const B = 2;
  const AB = 3;

  private $bloodGroup; // int

  public function __construct($code) {
    $this->bloodGroup = $code;
  }
  public function setBloodGroup($code) {
    $this->bloodGroup = $code;
  }
  public function getBloodGroup() {
    return $this->bloodGroup;
  }
}

// Somewhere in client code.
$parent = new Person(Person::O);
if ($parent->getBloodGroup() == Person::AB) {
  // ...
}
$child->setBloodGroup($parent->getBloodGroup());
```

###

```
class Person {
  private $bloodGroup; // BloodGroup

  public function __construct(BloodGroup $bloodGroup) {
    $this->bloodGroup = $bloodGroup;
  }
  public function setBloodGroup(BloodGroup $bloodGroup) {
    $this->bloodGroup = $bloodGroup;
  }
  public function getBloodGroup() {
    return $this->bloodGroup;
  }
}

class BloodGroup {
  private $code;

  private function __construct($arg) {
    $this->code = $arg;
  }
  public function getCode() {
    return $this->code;
  }

  public static function O() {
    return new BloodGroup(0);
  }
  public static function A() {
    return new BloodGroup(1);
  }
  public static function B() {
    return new BloodGroup(2);
  }
  public static function AB() {
    return new BloodGroup(3);
  }
}

// Somewhere in client code.
$parent = new Person(BloodGroup::O());
if ($parent->getBloodGroup() == BloodGroup::AB()) {
  // ...
}
$child->setBloodGroup($parent->getBloodGroup());
```

###

Set step 1

#|en| Let's look at <i>Replace Type Code With Class</i>, using the example of a person class that contains blood type fields.

Select:
```
  const |||O = 0|||;
  const |||A = 1|||;
  const |||B = 2|||;
  const |||AB = 3|||;
```

#|en| Blood types are coded as four constants of this class.

Go to after "Person"

#|en| We start refactoring by creating a new <code>BloodGroup</code> class, which will be responsible for blood types.

Type:
```


class BloodGroup {
}
```

Set step 2

#|en| We place the blood type field from the <code>Person</code> class, its getter and the constructor, which initialize the field value.

Go to the end of "BloodGroup"

Type:
```

  private $code;

  public function __construct($arg) {
    $this->code = $arg;
  }
  public function getCode() {
    return $this->code;
  }
```

Set step 3

#|en| Now let's create static methods for each of the type code values from the original class. These methods should return instances of the <code>BloodGroup</code> class.

Go to the end of "class BloodGroup"

Print:
```


  public static function O() {
    return new BloodGroup(0);
  }
  public static function A() {
    return new BloodGroup(1);
  }
  public static function B() {
    return new BloodGroup(2);
  }
  public static function AB() {
    return new BloodGroup(3);
  }
```

#C|ru| Можем запустить тестирование, чтобы убедиться в правильности кода.
#S Всё хорошо, можем продолжать.

#C|en| Let's double-check the code by testing.
#S All is well, so let's continue.

#C|uk| Можемо запустити тестування, щоб переконатися в правильності коду.
#S Все добре, можемо продовжувати.

Set step 4

Select:
```
  private $bloodGroup; // |||int|||
```

#|en| In the original class, change the type of the coded field to <code>BloodGroup</code>.


Type:
```
BloodGroup
```


Select:
```
  public function __construct($code) {
    $this->bloodGroup = |||$code|||;
  }
  public function setBloodGroup($code) {
    $this->bloodGroup = |||$code|||;
  }
```

#|en| Change the code of the setter and constructor accordingly.

Type:
```
new BloodGroup($code)
```

Go to:
```
return $this->bloodGroup|||;
```

#|en| Then change the field getter so that it calls the getter of the <code>BloodGroup</code> class.

Print "->getCode()"

Set step 5

Select:
```
  const |||O = 0|||;
  const |||A = 1|||;
  const |||B = 2|||;
  const |||AB = 3|||;
```

#|en| It is now time to replace all type code values with calls to the corresponding static methods of the <i>type class</i>.

Select "new Person(|||Person::O|||);"

Wait 500ms

Type "BloodGroup::O()->getCode()"

Wait 500ms

Select "$parent->getBloodGroup() == |||Person::AB|||"

Wait 500ms

Type "BloodGroup::AB()->getCode()"

#C|ru| После всех замен стоит запустить тесты.
#S Всё работает, отлично!

#C|en| After performing the replacements, we should perform some testing.
#S All working. Excellent!

#C|uk| Після всіх замін, варто запустити тести.
#S Все працює, супер.

Select parameters in "__construct"
+ Select parameters in "setBloodGroup"
+ Select:
```
return $this->bloodGroup|||->getCode()|||;
```

#|en| In the end, it is better to avoid using any numeric codes for <code>BloodGroup</code> and use objects instead. Let's try to do so in the <code>Person</code> class.

Select parameters in "__construct"
+ Select parameters in "setBloodGroup"

Replace "BloodGroup $bloodGroup"

Wait 500ms

Select:
```
new BloodGroup($code)
```

Replace "$bloodGroup"

Wait 500ms

Select:
```
return $this->bloodGroup|||->getCode()|||;
```

Remove selected

Select:
```
$parent = new Person(BloodGroup::O()|||->getCode()|||);
if ($parent->getBloodGroup() == BloodGroup::AB()|||->getCode()|||) {
```

#|en| These changes will probably cause the client code to break, but this can be fixed by simply getting rid of the codes there as well.

Remove selected


Set step 6
Select:
```
  const O = 0;
  const A = 1;
  const B = 2;
  const AB = 3;


```

#|en| You can remove unused constants from the <code>Person</code> class.

Remove selected

Select "|||public||| function __construct" in "BloodGroup"

#|en|^ And finally, you should make the <code>BloodGroup</code> constructor private.

Replace "private"

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
