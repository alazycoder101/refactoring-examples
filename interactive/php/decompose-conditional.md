decompose-conditional:php

###


1.en. Extract the conditional to a separate method via <a href="/extract-method">Extract Method</a>.



2.en. Repeat the process for the <code>then</code> and <code>else</code> blocks.




###

```
class Stadium {
  // ...
  public $summerRate;
  public $winterRate;
  public $winterServiceCharge;

  public function getTicketPrice(DateTime $date, $quantity) {
    if ($date->format("m") > "05" && $date->format("m") < "09") {
      $charge = $quantity * $this->summerRate;
    }
    else {
      $charge = $quantity * $this->winterRate + $this->winterServiceCharge;
    }
    return $charge;
  }
}
```

###

```
class Stadium {
  // ...
  public $summerRate;
  public $winterRate;
  public $winterServiceCharge;

  public function getTicketPrice(DateTime $date, $quantity) {
    if ($this->isSummer($date)) {
      $charge = $this->summerCharge($quantity);
    }
    else {
      $charge = $this->winterCharge($quantity);
    }
    return $charge;
  }

  private function isSummer(DateTime $date) {
    return $date->format("m") > "05" && $date->format("m") < "09";
  }
  private function summerCharge($quantity) {
    return $quantity * $this->summerRate;
  }
  private function winterCharge($quantity) {
    return $quantity * $this->winterRate + $this->winterServiceCharge;
  }
}
```

###

Set step 1

#|en| Let's look at <i>Decompose Conditional</i> using a simple class, that calculates the cost of a stadium ticket.

Select name of "getTicketPrice"

#|en| The cost depends on the season (summer or winter).

#|en| Our task is to make this conditional easier to understand. We can start by extracting the condition to a new method with a more obvious name.

Go to the end of "Stadium"

Print:
```


  private function isSummer(DateTime $date) {
  }
```

Select "$date->format("m") > "05" && $date->format("m") < "09""

Wait 500ms

Go to the end of "isSummer"

Print:
```

    return $date->format("m") > "05" && $date->format("m") < "09";
```

Wait 500ms

Select "$date->format("m") > "05" && $date->format("m") < "09"" in "getTicketPrice"

Remove selected

Print "$this->isSummer($date)"


#C|ru| Запускаем тестирование, чтобы убедиться в отсутствии ошибок.
#S Отлично, все работает!

#C|en| Let's launch autotests to check for errors in code.
#S Wonderful, it's all working!

#C|uk| Запускаємо тестування, щоб переконатися у відсутності помилок.
#S Супер, все працює.

Select "$this->isSummer($date)" in "getTicketPrice"

#|en| The condition became much clearer now. However, many programmers in such situations do not extract the components of the conditional, thinking about conditions as short and not worth the effort.

#|en| But no matter how short the condition is, there is often a big difference between the purpose of the code and its body. Figuring this out requires looking at the code in detail. In this case, doing so is easy but even here the extracted method looks more like a comment.

Set step 2

Select "$charge = $quantity * $this->summerRate;"

#|en| Now we turn to the body of the conditional. First we extract everything inside <code>then</code> to a new method.

Go to the end of "Stadium"

Print:
```

  private function summerCharge($quantity) {
    return $quantity * $this->summerRate;
  }
```

Select "$charge = $quantity * $this->summerRate;"

Replace "$charge = $this->summerCharge($quantity);"

Select "$charge = $quantity * $this->winterRate + $this->winterServiceCharge;"

#|en| Then we turn our attention to <code>else</code>.

Go to the end of "Stadium"

Print:
```

  private function winterCharge($quantity) {
    return $quantity * $this->winterRate + $this->winterServiceCharge;
  }
```

Select "$charge = $quantity * $this->winterRate + $this->winterServiceCharge;"

Replace "$charge = $this->winterCharge($quantity);"

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
