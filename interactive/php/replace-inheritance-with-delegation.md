replace-inheritance-with-delegation:php

###

1.en. Create a field in the subclass for holding the superclass. During the initial stage, place the current object in it.

2.en. Change the subclass methods so that they use the superclass object instead of <code>this</code>.

3.en. Remove the inheritance declaration from the subclass.

4.en. Change the initialization code of the field in which the former superclass is stored by creating a new object.



###

```
class Engine {
  //…
  private $fuel;
  private $CV;

  public function getFuel() {
    return $this->fuel;
  }
  public function setFuel($fuel) {
    $this->fuel = $fuel;
  }
  public function getCV() {
    return $this->CV;
  }
  public function setCV($cv) {
    $this->CV = $cv;
  }
}

class Car extends Engine {
  // ...
  private $brand;
  private $model;

  public function getName() {
    return $this->brand . ' ' . $this->model . ' (' . $this->getCV() . 'CV)';
  }
  public function getModel() {
    return $this->model;
  }
  public function setModel($model) {
    $this->model = $model;
  }
  public function getBrand() {
    return $this->brand;
  }
  public function setBrand($brand) {
    $this->brand = $brand;
  }
}
```

###

```
class Engine {
  //…
  private $fuel;
  private $CV;

  public function getFuel() {
    return $this->fuel;
  }
  public function setFuel($fuel) {
    $this->fuel = $fuel;
  }
  public function getCV() {
    return $this->CV;
  }
  public function setCV($cv) {
    $this->CV = $cv;
  }
}

class Car {
  // ...
  private $brand;
  private $model;
  protected $engine;

  public function __construct() {
    $this->engine = new Engine();
  }
  public function getName() {
    return $this->brand . ' ' . $this->model . ' (' . $this->engine->getCV() . 'CV)';
  }
  public function getModel() {
    return $this->model;
  }
  public function setModel($model) {
    $this->model = $model;
  }
  public function getBrand() {
    return $this->brand;
  }
  public function setBrand($brand) {
    $this->brand = $brand;
  }
}
```

###

Set step 1

#|en| Let's try out one more refactoring using a <code>Car</code> class that is inherited from the <code>Engine</code> as our example.

Select "$this->getCV()" in "Car"

#|en| At first, inheritance seemed a good and noble idea… But later we found that cars use only one engine's property (volume, to be precise).

Go to the start of "Car"

#|en| So it would have been more efficient to delegate to the <code>Engine</code> class for getting the necessary properties or methods.

#|en| Let's start refactoring by creating a field for storing a reference to an engine object.

Go to "private $model;|||"

Print:
```

  protected $engine;
```

Select "|||$engine|||;"

#|en| For now we will fill this field with the current object (this can be done in the constructor).

Go to before "getName"

Print:
```

  public function __construct() {
    $this->engine = $this;
  }
```

Set step 2

Select "$this->getCV()"

#|en| Then we should change all access points to the Engine's fields and methods so that they go through the newly created field. In our case, this happens in only one place. 

Print "$this->engine->getCV()"

Set step 3

Select " extends Engine"

#|en| Now we can remove the inheritance declaration from the <code>Car</code> class.

Remove selected

Set step 4

Select "engine = |||$this|||"

#|en| All that's left to do is create a new engine object for filling the field of the associated object.

Replace "new Engine()"

#C|ru| Запускаем финальное тестирование.
#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#C|uk| Запускаємо фінальне тестування.
#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
