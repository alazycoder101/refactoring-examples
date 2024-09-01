replace-delegation-with-inheritance:php

###

1.en. Make the class a subclass of the delegate class.

2.en. Place the current object in a field containing a reference to the delegate object.

3.en. Delete the methods with simple delegation one by one. If their names were different, use <a href="/rename-method">Rename Method</a> to give all the methods a single name.

4.en. Replace all references to the delegate field with references to the current object.

5.en. Remove the delegate field.



###

```
class Person {
  private $name;

  public function getName() {
    return $this->name;
  }
  public function setName($name) {
    $this->name = $name;
  }
  public function getLastName() {
    return substr($this->name, 0, strrpos($this->name, ' ') + 1);
  }
}

class Employee {
  protected $person;

  public function __construct() {
    $this->person = new Person();
  }
  public function getName() {
    return $this->person->getName();
  }
  public function setName($name) {
    $this->person->setName($name);
  }
  public function toString() {
    return "Emp: " . $this->person->getLastName();
  }
}
```

###

```
class Person {
  private $name;

  public function getName() {
    return $this->name;
  }
  public function setName($name) {
    $this->name = $name;
  }
  public function getLastName() {
    return substr($this->name, 0, strrpos($this->name, ' ') + 1);
  }
}

class Employee extends Person {
  public function toString() {
    return "Emp: " . $this->getLastName();
  }
}
```

###

Set step 1

#|en| We have an <code>Employee</code> class that delegates certain work to the <code>Person</code> class.

#|en| Inheritance would be more appropriate here since the employee class needs practically all data from <code>Person</code>.

#|en| Let's start refactoring by declaring the <code>Employee</code> as a subclass of <code>Person</code>.

Go to "class Employee|||"

Print " extends Person"

#C|ru| Здесь стоит запустить авто-тесты, чтобы убедиться в отсутствии конфликтующих методов. Они возникают, если методы с одинаковым именем возвращают значения различных типов или генерируют разные исключительные ситуации. Все проблемы такого рода исправляются с помощью <a href="/ru/rename-method">Переименования метода</a>.
#S В данном простом примере таких затруднений не возникает.

#C|en| Here you should run autotests to make sure there are no conflicting methods. These issues can arise if identically named methods return values of different types or generate different exceptions. For all such issues, use  <a href="/rename-method">Rename Method</a>.
#S No such difficulties are present in our simple example.

#C|uk| Тут варто запустити авто-тести, щоб переконатися у відсутності конфліктуючих методів. Вони виникають, якщо методи з однаковим ім'ям повертають значення різних типів або генерують різні виняткові ситуації. Всі проблеми такого роду виправляються за допомогою <a href="/uk/rename-method"> Перейменування методу </a>.
#S В даному простому прикладі таких ускладнень не виникає.

Set step 2

Select "new Person()"

#|en| Next, force the field, which contained reference to a <code>Person</code> object, to reference its own object. We will get rid of it later, but for now it will keep the code working.

Print "$this"

Set step 3

Select whole "getName" in "Employee"
+ Select whole "setName" in "Employee"

#|en| We also should remove all simple delegate methods from <code>Employee</code>, such as <code>getName</code> and <code>setName</code>. If we forget to remove them, a stack overflow will occur due to infinite recursion.

Remove selected

Set step 4

Select "person->"

#|en| Then we get rid of delegation calls by calling our object's methods.

Remove selected

Set step 5

Select:
```
  protected $person;

  public function __construct() {
    $this->person = $this;
  }

```

#|en| After all replacements, we can finally remove the field of the associated object and it's initialization code, which are no longer necessary.

Remove selected

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
