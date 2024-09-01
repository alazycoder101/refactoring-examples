add-parameter:php

###

1.ru. Проверьте, не определён ли метод в суперклассе или подклассе. Если метод в них присутствует, нужно будет повторить все шаги также в этих классах.
1.en. See whether the method is defined in a superclass or subclass. If the method is present in them, you will need to repeat all the steps in these classes as well.
1.uk. Перевірте, чи не є метод вже визначеним в суперкласі або підкласі. Якщо метод в них вже присутній, треба буде повторити усі кроки також в цих класах.

2.ru. Следующий шаг важен, чтобы сохранить работоспособность программы во время рефакторинга. Итак, создайте новый метод, скопировав старый, и добавьте в него требуемый параметр. Замените код старого метода вызовом нового метода. Вы можете подставить любое значение в новый параметр (например <code>null</code> для объектов или ноль для чисел).
2.en. The following step is critical for keeping your program functional during the refactoring process. Create a new method by copying the old one and add the necessary parameter to it. Replace the code for the old method with a call to the new method. You can plug in any value to the new parameter (such as <code>null</code> for objects or a zero for numbers).
2.uk. Наступний крок важливий для того, щоб зберегти працездатність програми під час рефакторингу. Отже, створіть новий метод, скопіювавши старий, і додайте в нього необхідний параметр. Замініть код старого методу викликом нового методу. Ви можете підставити будь-яке значення в новий параметр (наприклад <code>null</code> для об'єктів або нуль для чисел).

3.ru. Найдите все обращения к старому методу и замените их обращениями к новому методу.
3.en. Find all references to the old method and replace them with references to the new method.
3.uk. Знайдіть усі звернення до старого методу і замініть їх зверненнями до нового методу.

4.ru. Удалите старый метод. Этот шаг неосуществим, если старый метод является частью публичного интерфейса. В этом случае старый метод нужно пометить как устаревший (<code>deprecated</code>).
4.en. Delete the old method. Deletion is not possible if the old method is part of the public interface. If that is the case, mark the old method as deprecated.
4.uk. Видаліть старий метод. Цей крок стає неможливим, якщо старий метод є частиною публічного інтерфейсу. В цьому випадку старий метод треба помітити як застарілий (<code>deprecated</code>).



###

```
class Calendar {
  // ...
  private $appointments; // array
  public function findAppointment(DateTime $date) {
    $result = array();
    foreach ($this->appointments as $each) {
      if ($date->format('Y-m-d') == $each->date->format('Y-m-d')) {
        $result[] = $date;
      }
    }
    return $result;
  }
}

// Somewhere in client code
$today = new DateTime();
$appointments = $calendar->findAppointment($today);
```

###

```
class Calendar {
  // ...
  private $appointments; // array
  public function findAppointmentByDateAndName(DateTime $date, $name) {
    $result = array();
    foreach ($this->appointments as $each) {
      if ($date->format('Y-m-d') == $each->date->format('Y-m-d')) {
        if ($name == null || ($name != null && $name == $each->name)) {
          $result[] = $date;
        }
      }
    }
    return $result;
  }
}

// Somewhere in client code
$today = new DateTime();
$appointments = $calendar->findAppointmentByDateAndName($today, null);
```

###

Set step 1

#|en| Let's say we have a <code>Calendar</code> class that stores records about planned meetings.

Select name of "findAppointment"

#|en| There's a method in this class returns the values of meetings for a particular date.

#|en| It would be great if this method could filter visitors by their names as well.

Set step 2

#|en| We could simply add a new parameter to the method signature, but that would cause a large risk of breaking some existing code that has this method's calls.

Go to the end of "Calendar"

#|en| So we need to proceed very carefully. Therefore we start by creating a new method with the desired parameter. Then, we place a copy of the existing method in its body.

#|en| Since PHP does not support function overloading, we give the method a new name.

Print:
```

  public function findAppointmentByDateAndName(DateTime $date, $name) {
    $result = array();
    foreach ($this->appointments as $each) {
      if ($date->format('Y-m-d') == $each->date->format('Y-m-d')) {
        $result[] = $date;
      }
    }
    return $result;
  }
```

Select 2nd "        $result[] = $date;"

#|en| Then we change the method body as needed for the new method.

Print:
```
        if ($name == null || ($name != null && $name == $each->name)) {
          $result[] = $date;
        }
```

Select body of "findAppointment"

#|en| Now the body of the old method can be replaced with the new method's call.

Print:
```
    findAppointmentByDateAndName($date, null);
```

Set step 3

Select name of "findAppointment"

#|en| Then we need to find all calls to the old method and replace them with calls to the new one.

Select "$calendar->findAppointment($today);"

#|en| Here is one of them. Since we have nothing to pass to the new parameter, we use the <code>null</code> value.

Select "$calendar->|||findAppointment|||($today);"

Replace "findAppointmentByDateAndName"

Go to "$calendar->findAppointmentByDateAndName($today|||);"

Print ", null"

Set step 4

Select whole "findAppointment"

#|en| After all changes have been made, go ahead and delete the old method.

Remove selected

#S Отлично, все работает!

#C|en| Let's start the final testing.
#S Wonderful, it's all working!

#S Супер, все працює.

Set final step

#|en|Q The refactoring is complete! You can compare the old and new code if you like.
