replace-array-with-object:php

###

1.en. Create the new class that will contain the data from the array. Place the array itself in the class as a public field.

2.en. Create a field for storing the object of this class in the original class.

3.en. In the new class, create access methods one by one for all elements of the array.

4.en. When access methods have been created for all elements, make the array private.

5.en. For each element of the array, create a private field in the class and then change the access methods so that they use this field instead of the array.

6.en. When this is done, delete the array.



###

```
class Tournament {
  public $row = array();
  
  public function __construct() {
    $this->row[0] = "Liverpool";
    $this->row[1] = "15";
  }
  public function displayScore() {
    $name = $this->row[0];
    $score = intval($this->row[1]);
    // ...
  }
}
```

###

```
class Tournament {
  public row; // Performance
  
  public function __construct() {
    $this->row = new Performance();
    $this->row->setName("Liverpool");
    $this->row->setScore("15");
  }
  public function displayScore() {
    $name = $this->row->getName();
    $score = $this->row->getScore();
    // ...
  }
}

class Performance {
  private $name;
  private $score;

  public function getName() {
    return $this->name;
  }
  public void setName($arg) {
    $this->name = $arg;
  }
  public function getScore() {
    return $this->score;
  }
  public function setScore($arg) {
    $this->score = intval($arg);
  }
}
```

###

Set step 1

#|en| Let's look at <i>Replace Array with Object</i>, using a class that stores the name of an athletic team, number of wins and losses as our example.

Go to the end of file

#|en| Converting an array to an object starts with creating a class.

Print:
```


class Performance {
}
```

Go to the start of "Performance"

#|en| Then, we add the same array field to the new class as in the original. Don't worry, this is a temporary measure that we will take care of later.

Print:
```

  public $data = array();
```

Set step 2

Select name of "Tournament"

#|en| Now we should find all code, which works with the array and replace it with calls to your new class.

Select "public $row = array();"

#|en| Create the instance of our data class in the place where the array had been initialized.

Print:
```
public row; // Performance
```

Go to start of "__construct"

Print:
```

    $this->row = new Performance();
```

Select "|||row|||[" in "__construct"
+Select "|||row|||[" in "displayScore"

#|en| Now replace the code that uses the array.

Replace "row->data"

Set step 3

Go to the end of "Performance"

#|en| Add getters and setters with more self-explanatory names to the data class. Start with the field containing the team name.

Print:
```


  public function getName() {
    return $this->data[0];
  }
  public void setName($arg) {
    $this->data[0] = $arg;
  }
```

Select "$this->row->data[0] = "Liverpool""
+ Select "$name = $this->row->data[0]"

#|en| Now we need to replace the code of assignment values to array elements with appropriate setters of the <code>Performance</code> class.

Select "$this->row->data[0] = "Liverpool""

Replace "$this->row->setName("Liverpool")"

Wait 500ms

Select "$this->row->data[0]"

Replace "$this->row->getName()"

Go to the end of "Performance"

#|en| Do the same with the second element.

Print:
```

  public function getScore() {
    return intval($this->data[1]);
  }
  public function setScore($arg) {
    $this->data[1] = $arg;
  }
```


Select "$this->row->data[1] = "15""

Replace "$this->row->setScore("15")"

Wait 500ms

Select "intval($this->row->data[1])"

Replace "$this->row->getScore()"

Set step 4

Select "|||public||| $data"

#|en| Having done so for all elements, we can declare the array private.

Replace "private"

Set step 5

#|en| The main part of this refactoring – replacing the interface – is now complete. But it will also be useful to replace the array inside the data class.

Select name of "getName"
+ Select name of "setName"

#|en| To do this, we add fields for all array elements and reorient the access methods to use them. First convert the team name field.

Go to "array();|||"

Print:
```

  private $name;
```

Select "data[0]"

Replace "name"

Select name of "getScore"
+ Select name of "setScore"

#|en| Then convert the field that stores the team win/loss history.

Go to "$name;|||"

Print:
```

  private $score;
```

Select "intval($this->data[1])"

Replace "$this->score"

Select "$this->data[1] = $arg"

Replace "$this->score = intval($arg)"

Set step 6

Select:
```
  private $data = array();

```
#|en| After finishing replacements for all the elements of the array, we can remove the array declaration from the class.

Remove selected

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
