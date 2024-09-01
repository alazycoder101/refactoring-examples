introduce-parameter-object:php

###


1.en. Create a new class that will represent your group of parameters. Make the class immutable.



2.en. In the method that you want to refactor, use <a href="/add-parameter">Add Parameter</a>, which is where your parameter object will be passed. In all method calls, pass the object created from old method parameters to this parameter.



3.en. Now start deleting old parameters from the method one by one, replacing them in the code with fields of the parameter object. Test the program after each parameter replacement.



4.en. When done, see whether there is any point in moving a part of the method (or sometimes even the whole method) to a parameter object class. If so, use <a href="/move-method">Move Method</a> or <a href="/extract-method">Extract Method</a>.




###

```
class Account {
  // ...
  private $transactions = array();

  public function getFlowBetween(DateTime $start, DateTime $end) {
    $result = 0;
    foreach ($this->transactions as $transaction) {
      if ($transaction->getDate() >= $start && $transaction->getDate() <= $end) {
        $result += $transaction->getValue();
      }
    }
    return $result;
  }
}

class Transaction {
  private $chargeDate; // DateTime
  private $value;

  public function __construct($value, DateTime $chargeDate) {
    $this->value = $value;
    $this->chargeDate = $chargeDate;
  }
  public function getDate() {
    return $this->chargeDate;
  }
  public function getValue() {
    return $this->value;
  }
}

// Somewhere in client code…
$flow = $account->getFlowBetween($startDate, $endDate);
```

###

```
class Account {
  // ...
  private $transactions = array();

  public function getFlowBetween(DateRange $range) {
    $result = 0;
    foreach ($this->transactions as $transaction) {
      if ($range->includes($transaction->getDate())) {
        $result += $transaction->getValue();
      }
    }
    return $result;
  }
}

class Transaction {
  private $chargeDate; // DateTime
  private $value;

  public function __construct($value, DateTime $chargeDate) {
    $this->value = $value;
    $this->chargeDate = $chargeDate;
  }
  public function getDate() {
    return $this->chargeDate;
  }
  public function getValue() {
    return $this->value;
  }
}

class DateRange {
  private $start; // DateTime
  private $end; // DateTime

  public function __construct(DateTime $start, DateTime $end) {
    $this->start = $start;
    $this->end = $end;
  }
  public function getStart() {
    return $this->start;
  }
  public function getEnd() {
    return $this->end;
  }
  public function includes(DateTime $arg) {
    return ($arg >= $this->start && $arg <= $this->end);
  }
}

// Somewhere in client code…
$flow = $account->getFlowBetween(new DateRange($startDate, $endDate));
```

###

Set step 1

#|en| Let's look at this refactoring, using the bank account and transactions classes.

Select name of "getFlowBetween"

#|en| We are interested in the method for getting the total for all transactions during an indicated period of time.

Select parameters in "getFlowBetween"

#|en| As you can see, the method takes a range of two dates as its parameters. Pretty common situation? But instead of passing two dates, it would be better to pass a single date range object.

#|en| In future, that will allow us to move date range behaviors into their own class.

Go to after "Transaction"

#|en| Let's begin with creating a simple range class.

Print:
```


class DateRange {
  private $start; // DateTime
  private $end; // DateTime

  public function __construct(DateTime $start, DateTime $end) {
    $this->start = $start;
    $this->end = $end;
  }
  public function getStart() {
    return $this->start;
  }
  public function getEnd() {
    return $this->end;
  }
}
```

Select "private" in "DateRange"

#|en| The class will be immutable: the dates of the range cannot be changed after it is created, since the date fields are declared as private and we did not create setters for them.

#|en| This way you could avoid many errors related to passing objects in method parameters via references.

Set step 2

Go to the parameters end of "getFlowBetween"

#|en| Now we can add a range parameter to the method for getting the transaction total.

Print ", DateRange $range"

Wait 500ms

Select "$account->|||getFlowBetween|||"

#|en| Now, let's find all places where the method is called. In these calls, add a new parameter – specifically, an object created from the dates already given to the method.

Go to ", $endDate|||"

Print ", new DateRange($startDate, $endDate)"

Set step 3

Select "|||$startDate, $endDate|||,"

#|en| Then we can get rid of the old date parameters.

Select "DateTime $start" in parameters of "getFlowBetween"

#|en| First take care of the <code>start</code> parameter.

Select "$start" in body of "getFlowBetween"

Replace "$range->getStart()"

Select "DateTime $start, " in parameters of "getFlowBetween"

#|en| After replacements in the method body, the parameter can be removed from the signature and calls of the method.

Remove selected

Wait 500ms

Select "getFlowBetween(|||$startDate, |||"

Remove selected

Wait 500ms

Select "DateTime $end" in parameters of "getFlowBetween"

#|en| Now for the remaining parameter.

Select "$end" in body of "getFlowBetween"

Replace "$range->getEnd()"

Wait 500ms

Select "DateTime $end, " in parameters of "getFlowBetween"

Remove selected

Wait 500ms

Select "getFlowBetween(|||$endDate, |||"

Remove selected

Wait 500ms

#C|ru| После всех переносов можно запустить авто-тесты.
#S Отлично, все работает, продолжаем!

#C|en| After performing all the moves, we can run autotests.
#S Everything is good! Let's continue.

#C|uk| Після всіх переносів можна запустити авто-тести.
#S Супер, все працює, продовжуємо.


Set step 4

#|en| After all the necessary parameters were removed, we can start thinking about moving appropriate behaviors to the parameter object.

Select "$transaction->getDate() >= $range->getStart() && $transaction->getDate() <= $range->getEnd()"

#|en| In our case, we can move a check to see if a date is within a range. This gets rid of this code inside <code>getFlowBetween</code>.

Go to the end of "DateRange"

Print:
```

  public function includes(DateTime $arg) {
    return ($arg >= $this->start && $arg <= $this->end);
  }
```

Wait 500ms

Select "$transaction->getDate() >= $range->getStart() && $transaction->getDate() <= $range->getEnd()"

Replace "$range->includes($transaction->getDate())"

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
