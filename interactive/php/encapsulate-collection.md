encapsulate-collection:php

###

1.en. Create methods for adding and deleting collection elements.

2.en. Assign an empty collection to the field as the initial value.

3.en. Find the setter calls for the collection field. Change the setter so that it uses element add/remove operations.

4.en. Find all calls of the collection getter after which the collection is changed. Change the code so that it uses your new methods for adding and deleting elements from the collection.

5.en. Change the getter so that it returns a read-only representation of the collection.

6.en. Inspect the client code that uses the collection for code that would look better inside of the collection class itself.



###

```
class Course {
  public __construct($name, $isAdvanced) {
    // ...
  }
  public function isAdvanced() {
    // ...
  }
}

class Person {
  private $courses; // SplObjectStorage

  public function getCourses() {
    return $this->courses;
  }
  public function setCourses(SplObjectStorage $arg) {
    $this->courses = $arg;
  }
}

// Client code
$kent = new Person();
$s = new SplObjectStorage();
$s->attach(new Course("Smalltalk Programming", false));
$s->attach(new Course("Appreciating Single Malts", true));
$kent->setCourses($s);
assert(2 === $kent->getCourses()->count());
$refact = new Course("Refactoring", true);
$kent->getCourses()->attach($refact);
$kent->getCourses()->attach(new Course("Brutal Sarcasm", false));
assert(4 === $kent->getCourses()->count());
$kent->getCourses()->detach($refact);
assert(3 === $kent->getCourses()->count());

$count = 0;
foreach ($kent->getCourses() as $course) {
  if ($course->isAdvanced()) {
    $count++;
  }
}
print("Advanced courses: " . $count);

```

###

```
class Course {
  public __construct($name, $isAdvanced) {
    // ...
  }
  public function isAdvanced() {
    // ...
  }
}

class Person {
  private $courses; // SplObjectStorage

  public __construct() {
    $this->courses = new SplObjectStorage();
  }
  public function getCourses() {
    return clone $this->courses;
  }
  public function initializeCourses(SplObjectStorage $arg) {
    assert($this->courses->count() > 0, "Courses are not empty");
    $this->courses->addAll($arg);
  }
  public function addCourse($arg) {
    $this->courses->attach($arg);
  }
  public function removeCourse($arg) {
    $this->courses->detach($arg);
  }
  public function numberOfAdvancedCourses() {
    $count = 0;
    foreach ($this->courses as $course) {
      if ($course->isAdvanced()) {
        $count++;
      }
    }
    return $count;
  }
  public function numberOfCourses() {
    return $this->courses->count();
  }
}

// Client code
$kent = new Person();
$kent->addCourse(new Course("Smalltalk Programming", false));
$kent->addCourse(new Course("Appreciating Single Malts", true));
assert(2 === $kent->numberOfCourses());
$refact = new Course("Refactoring", true);
$kent->addCourse($refact);
$kent->addCourse(new Course("Brutal Sarcasm", false));
assert(4 === $kent->numberOfCourses());
$kent->removeCourse($refact);
assert(3 === $kent->numberOfCourses());

print("Advanced courses: " . $kent->numberOfAdvancedCourses());

```

###

Set step 1

#|en| Let's look at <i>Encapsulate Collection</i>, using a catalog of training courses as our example.

Select name of "Course"

#|en| The class of courses is rather simple.

Select name of "Person"

#|en| Plus there is also a class of course students.

Go to "$kent = |||new Person();"

#|en| With this interface, clients add courses via the following code.

Go to the end of "class Person"

#|en| Thus, first we need proper modification methods for this collection.

Print:
```

  public function addCourse($arg) {
    $this->courses->attach($arg);
  }
  public function removeCourse($arg) {
    $this->courses->detach($arg);
  }
```

Set step 2

Go to before "getCourses"

#|en| We can also make life easier for ourselves by initializing the field:

Print:
```

  public __construct() {
    $this->courses = new SplObjectStorage();
  }
```

Set step 3

Select name of "setCourses"

#|en| Now look at the uses of the <code>setCourses</code> setter. If there are many clients and the setter is used intensively, replace the method body so that it uses add/remove operations.

Select "$kent->setCourses($s)"

#|en| The complexity of this procedure depends on the way in which the collection setter is used. In the most simple case, the client initializes values by using the setter, meaning that courses do not exist prior to use of the method.

Select body of "setCourses"

#|en| If this is the case, change the setter body so that it uses collection's <code>add</code> method.

Print:
```
    assert($this->courses->count() > 0, "Courses are not empty");
    foreach ($arg as $item) {
      $this->courses->attach($item);
    }
```

Select name of "setCourses"

#|en| After this modification, use <a href="/rename-method">Rename Method</a> to make your intentions obvious.

Select "setCourses"

Replace "initializeCourses"

#|en| But in a general case, this method should first remove all existing collection items, and then add new ones.

#|en| If we know that there is no other behavior when elements are added during initialization, we can get rid of the cycle and use addAll.


Select:
```
    foreach ($arg as $item) {
      $this->courses->attach($item);
    }
```

Replace:
```
    $this->courses->addAll($arg);
```

#|en| Remember that we cannot simply assign a value to a set even if the previous set was empty. If the client plans to modify the set after passing it, this will violate encapsulation. So we always have to create copies.

Select:
```
$s = new SplObjectStorage();

```

#|en| If client code simply creates a set and uses its setter, we should force it to use add/remove methods instead…

Remove selected

Select:
```
|||$s->attach|||(new Course("Smalltalk Programming", false));
|||$s->attach|||(new Course("Appreciating Single Malts", true));
```

Replace "$kent->addCourse"

Wait 500ms

Select:
```
$kent->initializeCourses($s);

```

#|en| …and get rid of the call to the initialization method completely.

Remove selected

Set step 4


Select "getCourses()->attach"
+ Select "getCourses()->detach"

#|en| Now let's see who is using the collection's getter. Our foremost interest should go to cases when it is used to modify the collection.

#|en| We need to replace such calls with add/remove calls.

Select "getCourses()->attach"

Replace "addCourse"

Wait 500ms

Select "getCourses()->detach"

Replace "removeCourse"

Set step 5

Select:
```
return |||$this->courses|||;
```

#|en| As the finishing touch, let's change the getter's body so that it returns a read-only value (an immutable representation of the collection).


Print:
```
clone $this->courses
```

#C|ru| Запустим тестирование, чтобы убедиться в отсутствии ошибок.
#S Отлично, все работает!

#C|en| Let's launch autotests to check for errors in code.
#S Wonderful, it's all working!

#C|uk| Запустимо тестування, щоб переконатися у відсутності помилок.
#S Супер, все працює.

Select:
```
private |||$courses|||
```

#|en| At this point, we can consider the collection fully encapsulated. Nobody can change its elements other than by using the <code>Person</code> class' method.

Set step 6

Select:
```
$count = 0;
foreach ($kent->getCourses() as $course) {
  if ($course->isAdvanced()) {
    $count++;
  }
}

```

#|en| Now that we have a proper interface for the <code>Person</code> class, we can start moving the relevant code to this class. Here is an example of the code.

#|en| Apply <a href="/extract-method">Extract Method</a> to the code to move it to <code>Person</code>.

Go to the end of "class Person"

Print:
```

  public function numberOfAdvancedCourses() {
    $count = 0;
    foreach ($this->courses as $course) {
      if ($course->isAdvanced()) {
        $count++;
      }
    }
    return $count;
  }
```

Select:
```
$count = 0;
foreach ($kent->getCourses() as $course) {
  if ($course->isAdvanced()) {
    $count++;
  }
}

```

Remove selected

Select:
```
print("Advanced courses: " . |||$count|||);
```

Replace "$kent->numberOfAdvancedCourses()"

Select "$kent->getCourses()->count()"

#|en| Take a loot at this code. It's quite frequently encountered.

Go to the end of "Person"

#|en| It can be replaced with a more readable version.

Print:
```

  public function numberOfCourses() {
    return $this->courses->count();
  }
```

Select "$kent->getCourses()->count()"

Replace "$kent->numberOfCourses()"

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
