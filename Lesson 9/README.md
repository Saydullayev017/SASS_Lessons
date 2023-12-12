#### Родительский селектор, Интерполяция

Родительский селектор & — это специальный селектор, изобретенный Sass, который используется во вложенных селекторах для обратитесь к внешнему селектору. Это позволяет повторно использовать внешний селектор более сложными способами, например добавить псевдокласс или добавить селектор перед родителем.

Когда родительский селектор используется во внутреннем селекторе, он заменяется соответствующий внешний селектор. Это происходит вместо обычного поведения вложенности.


```
.alert {
  // The parent selector can be used to add pseudo-classes to the outer
  // selector.
  &:hover {
    font-weight: bold;
  }

  // It can also be used to style the outer selector in a certain context, such
  // as a body set to use a right-to-left language.
  [dir=rtl] & {
    margin-left: 0;
    margin-right: 10px;
  }

  // You can even use it as an argument to pseudo-class selectors.
  :not(&) {
    opacity: 0.8;
  }
}
```

⚠️ Внимание!
Поскольку родительский селектор можно заменить селектором типа, например h1, это разрешено только в начале составных селекторов, где селектор типа тоже будет разрешено. Например, span& не разрешено.

Однако мы рассматриваем возможность ослабления этого ограничения. Если вы хотите помочь чтобы это произошло, ознакомьтесь с этой проблемой на GitHub .

#### Добавление суффиксов
Добавление суффиксов, постоянная ссылка
Вы также можете использовать родительский селектор, чтобы добавить дополнительные суффиксы к внешнему элементу. селектор. Это особенно полезно при использовании такой методологии, как BEM, которая использует высокоструктурированные имена классов. Пока внешний селектор заканчивается буквенно-цифровое имя (например, класс, ID и селекторы элементов), вы можете использовать родительский селектор для добавления дополнительного текста.

```
.accordion {
  max-width: 600px;
  margin: 4rem auto;
  width: 90%;
  font-family: "Raleway", sans-serif;
  background: #f4f4f4;

  &__copy {
    display: none;
    padding: 1rem 1.5rem 2rem 1.5rem;
    color: gray;
    line-height: 1.6;
    font-size: 14px;
    font-weight: 500;

    &--open {
      display: block;
    }
  }
}
```

#### В постоянной ссылке SassScript
Родительский селектор также можно использовать в SassScript. Это особенный выражение, которое возвращает текущий родительский селектор в том же формате, который используется функции выбора: список, разделенный запятыми (список выбора), который содержит списки, разделенные пробелами (комплексные селекторы), содержащие строки без кавычек (комплексные селекторы). составные селекторы).

```
.main aside:hover,
.sidebar p {
  parent-selector: &;
  // => ((unquote(".main") unquote("aside:hover")),
  //     (unquote(".sidebar") unquote("p")))
}
```

Если выражение & используется вне каких-либо правил стиля, оно возвращает null. С null является falsey, это означает, что вы можете легко использовать его, чтобы определить, является ли миксин вызывается в правиле стиля или нет.

```
@mixin app-background($color) {
  #{if(&, '&.app-background', '.app-background')} {
    background-color: $color;
    color: rgba(#fff, 0.75);
  }
}

@include app-background(#036);

.sidebar {
  @include app-background(#c6538c);
}
```

#### Расширенное вложение
Постоянная ссылка с расширенным вложением
Вы можете использовать & как обычное выражение SassScript, что означает, что вы можете его передать к функциям или включать их в интерполяцию — даже в других селекторах! Используя его в сочетание с функциями выбора и правилом позволяет вам вкладывать селекторы очень эффективнымиспособами.@at-root 

Например, предположим, что вы хотите написать селектор, соответствующий внешнему селектор и селектор элемента. Вы могли бы написать такой миксин, который использует функцию для объединения с пользовательскимселектором.selector.unify()& 

```
@use "sass:selector";

@mixin unify-parent($child) {
  @at-root #{selector.unify(&, $child)} {
    @content;
  }
}

.wrapper .field {
  @include unify-parent("input") {
    /* ... */
  }
  @include unify-parent("select") {
    /* ... */
  }
}
```

⚠️ Внимание!
Когда Sass вкладывает селекторы, он не знает, для чего использовалась интерполяция генерировать их. Это означает, что он автоматически добавит внешний селектор в внутренний селектор даже если вы использовали & в качестве выражения SassScript. Вот почему вам нужно явно использовать правило , чтобы указать Sass не включать внешнийселектор.@at-root 



#### Интерполяция

Интерполяцию можно использовать практически в любом месте таблицы стилей Sass для встраивания результата выражения SassScript в фрагмент < a i=3>CSS. Просто оберните выражение в в любом из следующихмест:#{} 
### scss
```
@mixin corner-icon($name, $top-or-bottom, $left-or-right) {
  .icon-#{$name} {
    background-image: url("/icons/#{$name}.svg");
    position: absolute;
    #{$top-or-bottom}: 0;
    #{$left-or-right}: 0;
  }
}

@include corner-icon("mail", top, left);
```
### css
```
.icon-mail {
  background-image: url("/icons/mail.svg");
  position: absolute;
  top: 0;
  left: 0;
}
```