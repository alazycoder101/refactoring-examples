consolidate-conditional-expression:php

###

1.en. Consolidate the conditionals in a single expression by using <code>AND</code> and <code>OR</code>.

2.en. Perform <a href="/extract-method">Extract Method</a> on the operator conditions and give the method a name that reflects the expression's purpose.



###

```
class Payout {
  // ...

  public $seniority;
  public $monthsDisabled;
  public $isPartTime;

  public function disabilityAmount() {
    if ($this->seniority < 2) {
      return 0;
    }
    if ($this->monthsDisabled > 12) {
      return 0;
    }
    if ($this->isPartTime) {
      return 0;
    }
    // compute the disability amount
    // ...
  }

  public function vacationAmount() {
    if ($this->onVacation()) {
      if ($this->lengthOfService() > 10) {
        return 1;
      }
    }
    return 0.5;
  }
}
```

###

```
class Payout {
  // ...

  public $seniority;
  public $monthsDisabled;
  public $isPartTime;

  public function disabilityAmount() {
    if ($this->isNotEligibleForDisability()) {
      return 0;
    }
    // compute the disability amount
    // ...
  }
  private function isNotEligibleForDisability() {
    return ($this->seniority < 2) || ($this->monthsDisabled > 12) || ($this->isPartTime);
  }

  public function vacationAmount() {
    return ($this->onVacation() && $this->lengthOfService() > 10) ? 1 : 0.5;
  }
}
```

###

Set step 1

#|en| Let's look at <i>Consolidate Conditional Expression</i>, using the method for calculating workman's injury compensation.

Select "if" in "disabilityAmount"
+ Select "return 0" in "disabilityAmount"

#|en| As you see, there are a number of conditions that return an identical result.

#|en| We can merge these checks into a single expression using the <code>OR</code> operator.

Go to:
```
}|||
    // compute the disability amount
```

Print:
```

    if (($this->seniority < 2) || ($this->monthsDisabled > 12) || ($this->isPartTime)) {
      return 0;
    }
```

Select:
```
    if ($this->seniority < 2) {
      return 0;
    }
    if ($this->monthsDisabled > 12) {
      return 0;
    }
    if ($this->isPartTime) {
      return 0;
    }

```

Remove selected

Set step 2

Select "($this->seniority < 2) || ($this->monthsDisabled > 12) || ($this->isPartTime)"

#|en| This condition looks too long and hard to comprehend. So we can <a href="/extract-method">Extract Method</a> and make more clear what the conditional is looking for (no compensation to be paid).

Go to after "disabilityAmount"

Print:
```

  private function isNotEligibleForDisability() {
    return ($this->seniority < 2) || ($this->monthsDisabled > 12) || ($this->isPartTime);
  }
```

Select "($this->seniority < 2) || ($this->monthsDisabled > 12) || ($this->isPartTime)" in "disabilityAmount"

Replace "$this->isNotEligibleForDisability()"

#C|ru| Запускаем тестирование.
#S Отлично, все работает!

#C|en| Let's start testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо тестування.
#S Супер, все працює.

Select "if" in "vacationAmount"

#|en| The previous example demonstrated the <code>OR</code> operation but the same thing can be done using <code>AND</code>.

#|en| These conditions can be replaced as follows:

Go to the end of "vacationAmount"

Print:
```


    if ($this->onVacation() && $this->lengthOfService() > 10) {
      return 1;
    }
    else {
      return 0.5;
    }
```

Select:
```
    if ($this->onVacation()) {
      if ($this->lengthOfService() > 10) {
        return 1;
      }
    }
    return 0.5;


```

Remove selected

#|en| If the code only checks a condition and returns a value, we can simplify it to a greater degree by using a ternary operator.

Select body of "vacationAmount"

Replace:
```
    return ($this->onVacation() && $this->lengthOfService() > 10) ? 1 : 0.5;
```

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
