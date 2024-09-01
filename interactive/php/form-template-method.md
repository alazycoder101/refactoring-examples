form-template-method:php

###

1.en. Split algorithms in the subclasses into their constituent parts described in separate methods. <a href="/extract-method">Extract Method</a> can help with this.

2.en. The resulting methods that are identical for all subclasses can be moved to a superclass via <a href="/pull-up-method">Pull Up Method</a>.

3.en. The non-similar methods can be given consistent names via <a href="/rename-method">Rename Method</a>.

4.en. Move the signatures of non-similar methods to a superclass as abstract ones by using <a href="/pull-up-method">Pull Up Method</a>. Leave their implementations in the subclasses.

5.en. And finally, pull up the main method of the algorithm to the superclass. Now it should work with the method steps described in the superclass, both real and abstract.



###

```
class Article {
  // ...
  public function getTitle() { /* … */ }
  public function getIntro() { /* … */ }
  public function getBody() { /* … */ }
  public function getAuthor() { /* … */ }
  public function getDate() { /* … */ }

  public function markdownView() {
    $output = "# " . $this->getTitle() . "\n\n";
    $output .= "> " . $this->getIntro() . "\n\n";
    $output .= $this->getBody() . "\n\n";
    $output .= "_Written by " . $this->getAuthor() . " on " . date("m/d/Y", $this->getDate()) . "_";
    return $output;
  }
  public function htmlView() {
    $output = "<h2>" . $this->getTitle() . "</h2>" . "\n";
    $output .= "<blockquote>" . $this->getIntro() . "</blockquote>" . "\n";
    $output .= "<p>" . $this->getBody() . "</p>" . "\n";
    $output .= "<em>Written by " . $this->getAuthor() . " on " . date("m/d/Y", $this->getDate()) . "</em>";
    return $output;
  }
}
```

###

```
class Article {
  // ...
  public function getTitle() { /* … */ }
  public function getIntro() { /* … */ }
  public function getBody() { /* … */ }
  public function getAuthor() { /* … */ }
  public function getDate() { /* … */ }

  public function markdownView() {
    $view = new ArticleMarkdown($this);
    return $view->view();
  }
  public function htmlView() {
    $view = new ArticleHtml($this);
    return $view->view();
  }
}

abstract class ArticleView {
  protected $article;
  protected function __construct(Article $article) {
    $this->article = $article;
  }
  protected abstract function title();
  protected abstract function intro();
  protected abstract function body();
  protected abstract function footer();
  public function view() {
    return $this->title()
      . $this->intro()
      . $this->body()
      . $this->footer();
  }
}

class ArticleMarkdown extends ArticleView {
  public function __construct(Article $article) {
    parent::__construct($article);
  }
  protected function title() {
    return "# " . $this->article->getTitle() . "\n\n";
  }
  protected function intro() {
    return "> " . $this->article->getIntro() . "\n\n";
  }
  protected function body() {
    return $this->article->getBody() . "\n\n";
  }
  protected function footer() {
    return "_Written by " . $this->article->getAuthor() . " on " . date("m/d/Y", $this->article->getDate()) . "_";
  }
}

class ArticleHtml extends ArticleView {
  public function __construct(Article $article) {
    parent::__construct($article);
  }
  protected function title() {
    return "<h2>" . $this->article->getTitle() . "</h2>" . "\n";
  }
  protected function intro() {
    return "<blockquote>" . $this->article->getIntro() . "</blockquote>" . "\n";
  }
  protected function body() {
    return "<p>" . $this->article->getBody() . "</p>" . "\n";
  }
  protected function footer() {
    return "<em>Written by " . $this->article->getAuthor() . " on " . date("m/d/Y", $this->article->getDate()) . "</em>";
  }
}
```

###

Set step 1

Select name of "Article"

#|en|+ Let's look at this refactoring using the example of an article that can be displayed in two formats…

Select name of "markdownView"

#|en|<+ …in Markdown plain text…

Select name of "htmlView"

#|en|<= …and in HTML.

#|en| Before starting the refactoring per se, we should arrange things so that these two methods appear in the subclasses of some shared parent class.

Select whole "markdownView"
+Select whole "htmlView"

#|en| To do this, we create a <a href="/replace-method-with-method-object">simple method object</a> by moving both methods to it.

Go to after "Article"

Print:
```


class ArticleView {
  protected $article;
  public function __construct(Article $article) {
    $this->article = $article;
  }
  public function markdownView() {
    $output = "# " . $this->article->getTitle() . "\n\n";
    $output .= "> " . $this->article->getIntro() . "\n\n";
    $output .= $this->article->getBody() . "\n\n";
    $output .= "_Written by " . $this->article->getAuthor() . " on " . date("m/d/Y", $this->article->getDate()) . "_";
    return $output;
  }
  public function htmlView() {
    $output = "<h2>" . $this->article->getTitle() . "</h2>" . "\n";
    $output .= "<blockquote>" . $this->article->getIntro() . "</blockquote>" . "\n";
    $output .= "<p>" . $this->article->getBody() . "</p>" . "\n";
    $output .= "<em>Written by " . $this->article->getAuthor() . " on " . date("m/d/Y", $this->article->getDate()) . "</em>";
    return $output;
  }
}
```

Wait 500ms

Select body of "markdownView"
+ Select body of "htmlView"

#|en| Now the bodies of the original methods can be replaced with calls to the <code>ArticleView</code> methods.

Select body of "markdownView"

Replace:
```
    $view = new ArticleView($this);
    return $view->markdownView();
```

Wait 500ms

Select body of "htmlView"

Replace:
```
    $view = new ArticleView($this);
    return $view->htmlView();
```

Wait 500ms

Select name of "ArticleView"

#|en| Then from <code>ArticleView</code> we can extract two subclasses, <code>ArticleMarkdown</code> and <code>ArticleHtml</code>, by moving the corresponding methods to them.

Go to after "ArticleView"

#|en| Let's create a <code>ArticleMarkdown</code> class.

Print:
```


class ArticleMarkdown extends ArticleView {
  public function __construct(Article $article) {
    parent::__construct($article);
  }
}
```

Wait 500ms

Select whole of "markdownView" in "ArticleView"

#|en| Move corresponding method to it.

Remove selected

Wait 500ms

Go to end of "ArticleMarkdown"

Print:
```

  public function markdownView() {
    $output = "# " . $this->article->getTitle() . "\n\n";
    $output .= "> " . $this->article->getIntro() . "\n\n";
    $output .= $this->article->getBody() . "\n\n";
    $output .= "_Written by " . $this->article->getAuthor() . " on " . date("m/d/Y", $this->article->getDate()) . "_";
    return $output;
  }
```

Wait 500ms

Select "ArticleView" in "markdownView" of "Article"

Replace "ArticleMarkdown"

Wait 500ms

Go to after "ArticleMarkdown"

#|en| Now let's create a <code>ArticleHtml</code> class.

Print:
```


class ArticleHtml extends ArticleView {
  public function __construct(Article $article) {
    parent::__construct($article);
  }
}
```

Wait 500ms

Select whole of "htmlView" in "ArticleView"

#|en| And similarly move the remaining method to it.

Remove selected

Wait 500ms

Go to end of "ArticleHtml"

Print:
```

  public function htmlView() {
    $output = "<h2>" . $this->article->getTitle() . "</h2>" . "\n";
    $output .= "<blockquote>" . $this->article->getIntro() . "</blockquote>" . "\n";
    $output .= "<p>" . $this->article->getBody() . "</p>" . "\n";
    $output .= "<em>Written by " . $this->article->getAuthor() . " on " . date("m/d/Y", $this->article->getDate()) . "</em>";
    return $output;
  }
```

Wait 500ms

Select "ArticleView" in "htmlView" of "Article"

Replace "ArticleHtml"

Wait 500ms

Select name of "markdownView" in "ArticleMarkdown"
+ Select name of "htmlView" in "ArticleHtml"

#|en| Since the methods are now located in different classes, we can give them identical names.

Replace "view"

Wait 500ms

Select "markdownView" in body of "markdownView" of "Article"
+ Select "htmlView" in body of "htmlView" of "Article"

Replace "view"

Select name of "ArticleHtml"
+ Select name of "ArticleMarkdown"

#C|ru| Запустим тестирование, чтобы убедиться в отсутствии ошибок.
#S Всё работает, можем продолжать.

#C|en| Let's launch autotests to check for errors in the code.
#S Everything is OK! We can keep going.

#C|uk| Запустимо тестування, щоб переконатися у відсутності помилок.
#S Все працює, можемо продовжувати.

#|en| Finally, everything is ready to start the refactoring itself.

#|en| First split the <code>view</code> methods in both steps to their constituent steps. Defining the steps is rather easy in our case – these are parts of the printed article.

Select name of "ArticleMarkdown"

#|en| Start with the <code>ArticleMarkdown</code> class.

Go to end of "ArticleMarkdown"

Print:
```

  private function title() {
    return "# " . $this->article->getTitle() . "\n\n";
  }
  private function intro() {
    return "> " . $this->article->getIntro() . "\n\n";
  }
  private function body() {
    return $this->article->getBody() . "\n\n";
  }
  private function footer() {
    return "_Written by " . $this->article->getAuthor() . " on " . date("m/d/Y", $this->article->getDate()) . "_";
  }
```

Select body of "view" in "ArticleMarkdown"

#|en| Now we can replace parts of the <code>view</code> method with calls to the new methods.

Print:
```
    return $this->title()
      . $this->intro()
      . $this->body()
      . $this->footer();
```

Select name of "ArticleHtml"

#|en| Do all of this for the <code>ArticleHtml</code> class.

Go to end of "ArticleHtml"

Print:
```

  private function title() {
    return "<h2>" . $this->article->getTitle() . "</h2>" . "\n";
  }
  private function intro() {
    return "<blockquote>" . $this->article->getIntro() . "</blockquote>" . "\n";
  }
  private function body() {
    return "<p>" . $this->article->getBody() . "</p>" . "\n";
  }
  private function footer() {
    return "<em>Written by " . $this->article->getAuthor() . " on " . date("m/d/Y", $this->article->getDate()) . "</em>";
  }
```

Select body of "view" in "ArticleHtml"

#|en| Now we can replace parts of the <code>view</code> method with calls to the new methods.

Print:
```
    return $this->title()
      . $this->intro()
      . $this->body()
      . $this->footer();
```

Set step 4

Go to type "ArticleView"

#|en| As you can see, the two classes have many identical steps. We should move the identical steps as abstract methods to the superclass.

Print "abstract "

Wait 500ms

Select visibility of "__construct" in "ArticleView"

Replace "protected"

Wait 500ms

Go to end of "ArticleView"

Print:
```

  protected abstract function title();
  protected abstract function intro();
  protected abstract function body();
  protected abstract function footer();
```

Wait 500ms

Select "|||private||| function"

Replace "protected"

Wait 500ms

Select whole of "view" in "ArticleMarkdown"
+ Select whole of "view" in "ArticleHtml"

Set step 5

#|en| Now we can freely extract the identical <code>view</code> methods to the superclass.

Remove selected

Go to end of "ArticleView"

Print:
```

  public function view() {
    return $this->title()
      . $this->intro()
      . $this->body()
      . $this->footer();
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
