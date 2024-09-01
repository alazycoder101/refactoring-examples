replace-nested-conditional-with-guard-clauses:php

###

1.en. Isolate all guard clauses that lead to calling an exception or immediate return of a value from the method. Place these conditions at the beginning of the method.

2.en. After rearrangement is complete and all tests are successfully completed, see whether you can use <a href="/consolidate-conditional-expression">Consolidate Conditional Expression</a> for guard clauses that lead to the same exceptions or returned values.



###

```
class Payout {
  // ...
  function getPayAmount() {
    $result = 0;
    if ($this->isDead) {
      $result = $this->deadAmount();
    }
    else {
      if ($this->isSeparated) {
        $result = $this->separatedAmount();
      }
      else {
        if ($this->isRetired) {
          $result = $this->retiredAmount();
        }
        else {
          $result = $this->normalPayAmount();
        }
      }
    }
    return $result;
  }
}
```

###

```
class Payout {
  // ...
  function getPayAmount() {
    if ($this->isDead) {
      return $this->deadAmount();
    }
    if ($this->isSeparated) {
      return $this->separatedAmount();
    }
    if ($this->isRetired) {
      return $this->retiredAmount();
    }
    return $this->normalPayAmount();
  }
}
```

###

Set step 1

#|en| Imagine a payroll system with special rules for employees who have passed away, live apart, or have retired. These cases are unusual but do occur.

Select "$this->isDead"
+ Select "$this->isSeparated"
+ Select "$this->isRetired"

#|en|+ Once some of the special conditions are triggered…

Select "$result = $this->normalPayAmount()"

#|en|<= …a corresponding method is called. Then it's is taken to the end of the method and returned as is. Such could be pretty difficult to understand, especially if there are lot of conditional branches. To fix it, we could place guard clauses, e.g. return the value right away if some condition is met.

Select "|||$result =||| $this->deadAmount();"

Replace "return"

Wait 500ms

Select:
```
    }
|||    else {
|||
```

+Select:
```
|||    }
|||    return $result;
```

Wait 500ms

Remove selected

Wait 500ms

Select:
```
      if ($this->isSeparated) {
        $result = $this->separatedAmount();
      }
      else {
        if ($this->isRetired) {
          $result = $this->retiredAmount();
        }
        else {
          $result = $this->normalPayAmount();
        }
      }
```

Deindent

Wait 500ms

Select "$this->isSeparated"

#|en| Continue performing replacements, one at a time.

Select "|||$result =||| $this->separatedAmount();"

Replace "return"

Wait 500ms

Select:
```
    }
|||    else {
|||
```

+Select:
```
|||    }
|||    return $result;
```

Wait 500ms

Remove selected

Wait 500ms

Select:
```
      if ($this->isRetired) {
        $result = $this->retiredAmount();
      }
      else {
        $result = $this->normalPayAmount();
      }
```

Deindent

Wait 500ms

Select "$this->isRetired"

#|en| And the last one.

Select "|||$result =||| $this->retiredAmount();"

Replace "return"

Wait 500ms

Select:
```
    }
|||    else {
|||
```

+Select:
```
|||    }
|||    return $result;
```

Wait 500ms

Remove selected

Wait 500ms

Select:
```
    $result = $this->normalPayAmount();
```

Deindent

Wait 500ms

Select:
```
    $result = 0;

```

#|en| After these changes, you can get rid of the <code>result</code> variable entirely.

Remove selected

Wait 500ms

Select "$result ="

Replace "return"

Wait 500ms

Select:
```
    return $result;

```

Wait 500ms

Remove selected

#|en| Multi-level sub-conditionals are often written by programmers taught that a method should contain only one exit point. But in modern programming, this rule have become obsolete.

#|en| If, during execution, method did everything, it could, it's better to exit as soon as possible. Otherwise, going over an empty <code>else</code> block only throws up roadblocks to performance and readability.

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
