replace-exception-with-test:php

###


1.en. Create a conditional for an edge case and move it before the try/catch block.



2.en. Move code from the <code>catch</code> section inside this conditional.



3.en. In the <code>catch</code> section, place the code for throwing a usual unnamed exception and run all the tests.



4.en. If no exceptions were thrown during the tests, get rid of the <code>try</code>/<code>catch</code> operator.




###

```
class ResourcePool {
  // ...
  private $available; // SplStack 
  private $allocated; // SplStack 

  public function getResource() {
    try {
      $result = $this->available->pop();
      $this->available->push($this->allocated, $result);
      return $result;
    } catch (RuntimeException $e) {
      $result = new Resource();
      $this->available->push($this->allocated, $result);
      return $result;
    }
  }
}
```

###

```
class ResourcePool {
  // ...
  private $available; // SplStack 
  private $allocated; // SplStack 

  public function getResource() {
    if ($this->available->isEmpty()) {
      $result = new Resource();
    }
    else {
      $result = $this->available->pop();
    }
    $this->available->push($this->allocated, $result);
    return $result;
  }
}
```

###

Set step 1

#|en| For this example, we take an object that controls resources that are expensive to create but reusable. A good example of this situation is database connections.

Select "private |||$available|||"
#|en|+ The administrator has two pools. One of them contains resources available for use…

Select "private |||$allocated|||"

#|en|<= …and the other pool contains already allocated resources.

Select "$this->available->pop()"

#|en|< When a client needs a resource, the administrator provides it from the pool of available resources and moves it to the allocated pool. When the client frees up the resource, the administrator returns it back.

Select "$result = new Resource();"

#|en|< If a client requests a resource and no free resources remain, the administrator creates a new resource.

#|en|< "Insufficient resources" is not an unexpected event, so using an exception is not truly justified.

Go to the start of "getResource"

#|en| So let's try to get rid of the exception. First, at the beginning of the method, create a conditional whose condition coincides with the condition for throwing an exception. Place all the remaining code in <code>else</code>.

Print:
```

    if ($this->available->isEmpty()) {
    }
    else {
```

Go to:
```
    }|||
  }
```

Print:
```

    }
```

Select:
```
    try {
      $result = $this->available->pop();
      $this->available->push($this->allocated, $result);
      return $result;
    } catch (RuntimeException $e) {
      $result = new Resource();
      $this->available->push($this->allocated, $result);
      return $result;
    }

```

Indent

Set step 2

Select:
```
        $result = new Resource();
        $this->available->push($this->allocated, $result);
        return $result;

```

#|en| Then copy the code from the <code>catch</code> section to inside the guard clause.

Go to "isEmpty()) {|||"

Print:
```

      $result = new Resource();
      $this->available->push($this->allocated, $result);
      return $result;
```

Set step 3

Go to "catch (RuntimeException $e) {|||"

#|en| This code should never reach the <code>catch</code> section. But to be 100% sure, insert a check inside the section and run all the tests.

Print:
```

        throw new RuntimeException("Should not reach here.");
```

#C|ru| Посмотрим, что покажут авто-тесты.
#S Всё отлично, можем продолжать!

#C|en| Let's run the autotests.
#S Everything is OK! We can keep going.

#C|uk| Подивимося, що покажуть авто-тести.
#S Все добре, можемо продовжувати.

Set step 4

#|en| Now we can remove the <code>try</code> / <code>catch</code> section without worrying about possible errors.

Select:
```
      try {

```

Remove selected

Select:
```
      } catch (RuntimeException $e) {
        throw new RuntimeException("Should not reach here.");
        $result = new Resource();
        $this->available->push($this->allocated, $result);
        return $result;
      }

```

Remove selected

Select:
```
        $result = $this->available->pop();
        $this->available->push($this->allocated, $result);
        return $result;
```

Deindent

Select:
```
      $this->available->push($this->allocated, $result);
      return $result;

```

#|en| After this, it is usually possible to tidy up the conditional code. In this case, we can apply <a href="/consolidate-duplicate-conditional-fragments">Consolidate Duplicate Conditional Fragments</a>.

Go to:
```
    }|||
  }
```

Print:
```

    $this->available->push($this->allocated, $result);
    return $result;
```

Wait 500ms

Select:
```
      $this->available->push($this->allocated, $result);
      return $result;

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
