separate-query-from-modifier:php

###

1.en. Create a new <i>query method</i> to return what the original method did.

2.en. Change the original method so that it returns only the result of calling the new <i>query method</i>.

3.en. Replace all references to the original method with a call to the <i>query method</i> but immediately before this line, insert a call to the original method.

4.en. Get rid of the value-returning code in the original method, which now has become a proper <i>modifier method</i>.



###

```
class Guard {
  // ...
  public function checkSecurity($people) {
    $found = $this->findCriminalAndAlert($people);
    $this->someLaterCode($found);
  }
  public function findCriminalAndAlert($people) {
    for ($i = 0; $i < count($people); $i++) {
      if ($people[$i] == "Don") {
        $this->sendAlert();
        return "Don";
      }
      if ($people[$i] == "John") {
        $this->sendAlert();
        return "John";
      }
    }
    return "";
  }
}
```

###

```
class Guard {
  // ...
  public function checkSecurity($people) {
    $this->doSendAlert($people);
    $found = $this->findCriminal($people);
    $this->someLaterCode($found);
  }
  public function doSendAlert($people) {
    if ($this->findCriminal($people) != "") {
      $this->sendAlert();
    }
  }
  public function findCriminal($people) {
    for ($i = 0; $i < count($people); $i++) {
      if ($people[$i] == "Don") {
        return "Don";
      }
      if ($people[$i] == "John") {
        return "John";
      }
    }
    return "";
  }
}
```

###

Set step 1

Select name of "findCriminalAndAlert"

#|en| Let's look at <i>Separate Query from Modifier</i> refactoring using a security system class as our example. The class has a method that tells us the name of a violator and sends a warning.

#|en| The main problem with this method is that it is used for two different purposes.

Select "return "Don""
+ Select "return "John""

#|en| First, it finds and returns a list of names that are then used for different purposes.

Select "found" in "checkSecurity"

#|en| An example of such use can be found in the <code>checkSecurity</code> method.

Select "sendAlert()" in "findCriminalAndAlert"

#|en| Secondly, it alerts about the people found.

#|en| With this approach, even if we only need to get a list of names we take the risk of accidentally sending alerts. This refactoring technique will save us from that risk. In our case, searching for people will be the "query" and sending alerts will be the "modifier".

Go to the end of "Guard"

#|en| To separate the query from the modifier, first create an appropriate query that returns the same value as the original method, but does not lead to side effects.

Print:
```

  public function findCriminal($people) {
    for ($i = 0; $i < count($people); $i++) {
      if ($people[$i] == "Don") {
        return "Don";
      }
      if ($people[$i] == "John") {
        return "John";
      }
    }
    return "";
  }
```

Set step 2

Select "return" in "findCriminalAndAlert"

#|en| Then, one by one, replace all cases of <code>return</code> in the original method with calls for the new query.

Select "return |||"Don"|||" in "findCriminalAndAlert"

Replace "$this->findCriminal($people)"

Select "return |||"John"|||" in "findCriminalAndAlert"

Replace "$this->findCriminal($people)"

Select "return |||""|||" in "findCriminalAndAlert"

Replace "$this->findCriminal($people)"

Set step 3

Select:
```
    $found = $this->findCriminalAndAlert($people);
```

#|en| Now change all the methods from which reference is made so that two calls occur in them: first for the modifier, then for the query.

Select:
```
    |||$found = |||$this->findCriminalAndAlert($people);
```

Remove selected

Go to "findCriminalAndAlert($people);|||"

Print:
```

    $found = $this->findCriminal($people);
```

Set step 4

Select in "findCriminalAndAlert":
```
        return $this->findCriminal($people);

```
+Select in "findCriminalAndAlert":
```
    return $this->findCriminal($people);

```

#|en| Once this has been completed for all calls, we remove the return code from the modifier.

Remove selected

Select name of "findCriminalAndAlert"

#|en| We should also now change the name of the original method for consistency.

Print "doSendAlert"

Select "findCriminalAndAlert"

Replace "doSendAlert"

Select body of "doSendAlert"

#|en| Of course, the result contains a great deal of duplicate code since the modifier still uses the body of the query. But now we can apply <a href="/substitute-algorithm">Substitute Algorithm</a> without the risk of breaking any other code.

Print:
```
    if ($this->findCriminal($people) != "") {
      $this->sendAlert();
    }
```

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
