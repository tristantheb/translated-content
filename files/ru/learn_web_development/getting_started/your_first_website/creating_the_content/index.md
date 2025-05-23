---
title: Основы HTML
slug: Learn_web_development/Getting_started/Your_first_website/Creating_the_content
---

{{LearnSidebar}}{{PreviousMenuNext("Learn/Getting_started_with_the_web/Dealing_with_files", "Learn/Getting_started_with_the_web/CSS_basics", "Learn/Getting_started_with_the_web")}}

HTML (Hypertext Markup Language) - это код, который используется для структурирования и отображения веб-страницы и её контента. Например, контент может быть структурирован внутри множества параграфов, маркированных списков или с использованием изображений и таблиц данных. Как видно из названия, эта статья даст вам базовое понимание HTML и его функций.

## Что такое HTML на самом деле?

HTML не является языком программирования; это _язык разметки_, и используется, чтобы сообщать вашему браузеру, как отображать веб-страницы, которые вы посещаете. Он может быть сложным или простым, в зависимости от того, как хочет веб-дизайнер. HTML состоит из ряда **{{Glossary("element", "элементов")}}**, которые вы используете, чтобы вкладывать или оборачивать различные части контента, чтобы заставить контент отображаться или действовать определённым образом. Ограждающие {{Glossary("tag", "теги")}} могут сделать слово или изображение ссылкой на что-то ещё, могут сделать слова курсивом, сделать шрифт больше или меньше и так далее. Например, возьмём следующую строку контента:

```
Моя кошка очень раздражена
```

Если бы мы хотели, чтобы строка стояла сама по себе, мы могли бы указать, что это абзац, заключая его в теги абзаца:

```html
<p>Моя кошка очень раздражена</p>
```

### Анатомия HTML элемента

Давайте рассмотрим элемент абзаца более подробно.

![](grumpy-cat-small.png)

Главными частями нашего элемента являются:

1. **Открывающий тег (Opening tag)**: Состоит из имени элемента (в данном случае, "p"), заключённого в открывающие и закрывающие **угловые скобки**. Открывающий тег указывает, где элемент начинается или начинает действовать, в данном случае — где начинается абзац.
2. **Закрывающий тег (Closing tag):** Это то же самое, что и открывающий тег, за исключением того, что он включает в себя косую черту перед именем элемента. Закрывающий элемент указывает, где элемент заканчивается, в данном случае — где заканчивается абзац. Отсутствие закрывающего тега является одной из наиболее распространённых ошибок начинающих и может приводить к странным результатам.
3. **Контент (Content)**: Это контент элемента, который в данном случае является просто текстом.
4. **Элемент(Element)**: Открывающий тег, закрывающий тег и контент вместе составляют элемент.

Элементы также могут иметь атрибуты, которые выглядят так:

![](grumpy-cat-attribute-small.png)

Атрибуты содержат дополнительную информацию об элементе, которую вы не хотите показывать в фактическом контенте. В данном случае, class это _имя_ _атрибута,_ а `editor-note` это _значение атрибута_. Класс позволяет дать элементу идентификационное имя, которое может позже использоваться, чтобы обращаться к элементу с информацией о стиле и прочих вещах.

Атрибут всегда должен иметь:

1. Пробел между ним и именем элемента (или предыдущим атрибутом, если элемент уже имеет один или несколько атрибутов).
2. Имя атрибута, за которым следует знак равенства.
3. Значение атрибута, заключённое с двух сторон в кавычки.

### Вложенные элементы

Вы также можете располагать элементы внутри других элементов — это называется **вложением**. Если мы хотим заявить, что наша кошка **очень** раздражена, мы можем заключить слово "очень" в элемент {{htmlelement("strong")}} , который указывает, что слово должно быть сильно акцентированно:

```html
<p>Моя кошка <strong>очень</strong> раздражена.</p>
```

Вы, однако, должны убедиться, что ваши элементы правильно вложены: в примере выше мы открыли первым элемент {{htmlelement("p")}}, затем элемент {{htmlelement("strong")}}, потом мы должны закрыть сначала элемент {{htmlelement("strong")}}, затем {{htmlelement("p")}}. Приведённое ниже неверно:

```html example-bad
<p>Моя кошка <strong>очень раздражена.</p></strong>
```

Элементы должны открываться и закрываться правильно, поэтому они явно располагаются внутри или снаружи друг друга. Если они перекрываются, как в примере выше, ваш веб-браузер будет пытаться сделать наилучшее предположение на основе того, что вы пытались сказать, что может привести к неожиданным результатам. Так что не стоит этого делать!

### Пустые элементы

Некоторые элементы не имеют контента, и называются **пустыми элементами**. Возьмём элемент {{htmlelement("img")}}, который уже имеется в нашем HTML:

```html
<img src="images/firefox-icon.png" alt="Моё тестовое изображение" />
```

Он содержит два атрибута, но не имеет закрывающего тега `</img>`, и никакого внутреннего контента. Это потому, что элемент изображения не оборачивает контент для влияния на него. Его целью является вставка изображения в HTML страницу в нужном месте.

### Анатомия HTML документа

Мы завершили изучение основ отдельных HTML элементов, но они не очень полезны сами по себе. Теперь мы посмотрим, как отдельные элементы объединяются в целую HTML страницу. Давайте вернёмся к коду, который мы записывали в наш `index.html` (с которым мы впервые встретились в статье [Работа с файлами](/ru/docs/Learn_web_development/Getting_started/Environment_setup/Dealing_with_files)):

```html
<!doctype html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Моя тестовая страница</title>
  </head>
  <body>
    <img src="images/firefox-icon.png" alt="Моё тестовое изображение" />
  </body>
</html>
```

Здесь мы имеем:

- `<!DOCTYPE html>` — доктайп. В прошлом, когда HTML был молод (около 1991/1992), доктайпы должны были выступать в качестве ссылки на набор правил, которым HTML страница должна была следовать, чтобы считаться хорошим HTML, что могло означать автоматическую проверку ошибок и другие полезные вещи. Однако в наши дни, никто не заботится об этом, и они на самом деле просто исторический артефакт, который должен быть включён для того, что бы все работало правильно. На данный момент это все, что вам нужно знать.
- `<html></html>` — элемент {{htmlelement("html")}}. Этот элемент оборачивает весь контент на всей странице, и иногда известен как корневой элемент.
- `<head></head>` — элемент {{htmlelement("head")}}. Этот элемент выступает в качестве контейнера для всего, что вы пожелаете включить на HTML страницу, но _не являющегося_ контентом, который вы показываете пользователям вашей страницы. К ним относятся такие вещи, как ключевые слова и описание страницы, которые будут появляться в результатах поиска, CSS стили нашего контента, кодировка и многое другое.
- `<body></body>` — элемент {{htmlelement("body")}}. В нем содержится _весь_ контент, который вы хотите показывать пользователям, когда они посещают вашу страницу, будь то текст, изображения, видео, игры, проигрываемые аудиодорожки или что-то ещё.
- `<meta charset="utf-8">` — этот элемент устанавливает UTF-8 кодировку вашего документа, которая включает в себя большинство символов из всех известных человечеству языков. По сути, теперь документ может обрабатывать любой текстовый контент, который вы в него вложите. Нет причин не устанавливать её, так как это может помочь избежать некоторых проблем в дальнейшем.
- `<title></title>` — элемент {{htmlelement("title")}}. Этот элемент устанавливает заголовок для вашей страницы, который является названием, появляющимся на вкладке браузера загружаемой страницы, и используется для описания страницы, когда вы добавляете её в закладки/избранное.

## Изображения

Давайте снова обратим наше внимание на элемент {{htmlelement("img", "изображения")}}:

```html
<img src="images/firefox-icon.png" alt="Mоё тестовое изображение" />
```

Как было сказано раньше, код встраивает изображение на нашу страницу в нужном месте. Это делается с помощью атрибута `src` (source, источник), который содержит путь к нашему файлу изображения.

Мы также включили атрибут `alt` (alternative, альтернатива). В этом атрибуте, вы указываете поясняющий текст для пользователей, которые не могут увидеть изображение, возможно, по следующим причинам:

1. У них присутствуют нарушения зрения. Пользователи со значительным нарушением зрения часто используют инструменты, называемые Screen Readers (экранные дикторы), которые читают для них альтернативный текст.
2. Что-то пошло не так, в результате чего изображение не отобразилось. Например, попробуйте намеренно изменить путь в вашем атрибуте `src`, сделав его неверным. Если вы сохраните и перезагрузите страницу, то вы должны увидеть что-то подобное вместо изображения:

![](alt-text-example.png)

Альтернативный текст - это "пояснительный текст". Он должен предоставить читателю достаточно информации, чтобы иметь представление о том, что передаёт изображение. В этом примере наш текст "My test image" ("Моё тестовое изображение") не годится. Намного лучшей альтернативой для нашего логотипа Firefox будет "The Firefox logo: a flaming fox surrounding the Earth" ("Логотип Firefox: огненный Лис вокруг Земли").

Сейчас попробуйте придумать более подходящий альтернативный текст для вашего изображения.

> [!NOTE]
> Узнайте больше о [специальных возможностях](/ru/docs/Web/Accessibility).

## Разметка текста

В этом разделе рассмотрим некоторые из основных HTML элементов, которые вы будете использовать для разметки текста.

### Заголовки

Элементы заголовка позволяют вам указывать определённые части вашего контента в качестве заголовков или подзаголовков. Точно так же, как книга имеет название, названия глав и подзаголовков, HTML документ может содержать то же самое. HTML включает шесть уровней заголовков {{htmlelement("h1")}}–{{htmlelement("h6")}}, хотя обычно вы будете использовать не более 3-4 :

```html
<h1>Мой главный заголовок</h1>
<h2>Мой заголовок верхнего уровня</h2>
<h3>Мой подзаголовок</h3>
<h4>Мой под-подзаголовок</h4>
```

Теперь попробуйте добавить подходящее название для вашей HTML страницы, чуть выше элемента {{htmlelement("img")}}.

### Абзацы

Как было сказано раньше, элемент {{htmlelement("p")}} предназначен для абзацев текста; вы будете использовать их регулярно при разметке текстового контента:

```html
<p>Это одиночный абзац</p>
```

Добавьте свой образец текста (вы создавали его в статье [_Каким должен быть ваш веб-сайт?_](/ru/docs/Learn/Getting_started_with_the_web/What_should_your_web_site_be_like)) в один или несколько абзацев, расположенных прямо под элементом {{htmlelement("img")}}.

### Списки

Большая часть веб-контента является списками и HTML имеет специальные элементы для них. Разметка списка всегда состоит по меньшей мере из двух элементов. Наиболее распространёнными типами списков являются нумерованные и ненумерованные списки:

1. **Ненумерованные списки** - это списки, где порядок пунктов не имеет значения, как в списке покупок. Они оборачиваются в элемент {{htmlelement("ul")}}.
2. **Нумерованные списки -** это списки, где порядок пунктов имеет значение, как в рецепте. Они оборачиваются в элемент {{htmlelement("ol")}}.

Каждый пункт внутри списков располагается внутри элемента {{htmlelement("li")}} (list item, элемент списка).

Например, если мы хотим включить часть следующего фрагмента абзаца в список:

```html
<p>
  Mozilla, мы являемся мировым сообществом технологов, мыслителей и строителей,
  работающих вместе ...
</p>
```

Мы могли бы изменить разметку на эту:

```html
<p>Mozilla, мы являемся мировым сообществом</p>

<ul>
  <li>технологов</li>
  <li>мыслителей</li>
  <li>строителей</li>
</ul>

<p>работающих вместе ...</p>
```

Попробуйте добавить упорядоченный или неупорядоченный список на свою страницу.

## Ссылки

Ссылки очень важны — это то, что делает Интернет Интернетом. Чтобы добавить ссылку, нам нужно использовать простой элемент — {{htmlelement("a")}} — _a_ это сокращение от "anchor" ("якорь"). Чтобы текст в вашем абзаце стал ссылкой, выполните следующие действия:

1. Выберите некоторый текст. Мы выбрали текст "Манифест Mozilla".
2. Оберните текст в элемент {{htmlelement("a")}}, например так:

   ```html
   <a>Манифест Mozilla</a>
   ```

3. Задайте элементу {{htmlelement("a")}} атрибут href, например так:

   ```html
   <a href="">Манифест Mozilla</a>
   ```

4. Заполните значение этого атрибута веб-адресом, на который вы хотите указать ссылку:

   ```html
   <a href="https://www.mozilla.org/ru/about/manifesto/details/"
     >Манифест Mozilla</a
   >
   ```

Вы можете получить неожиданные результаты, если в самом начале веб-адреса вы опустите `https://` или `http://` часть, называемую _протоколом_. После создания ссылки, кликните по ней, чтобы убедиться, что она направляет вас туда, куда вы хотели.

> **Примечание:** `href` сначала может выглядеть довольно непонятым выбором для имени атрибута. Если у вас возникли проблемы с тем, чтобы запомнить его, можете запомнить, что атрибут href образуется как _**h**ypertext **ref**erence_ ("гипертекстовая ссылка").

Теперь добавьте ссылку на вашу страницу, если вы ещё не сделали этого.

## Заключение

Если вы следовали всем инструкциям в этой статье, то вы должны увидеть в конечном итоге страницу, аналогичную рисунку ниже (вы также можете [посмотреть её здесь](https://mdn.github.io/beginner-html-site/)):

![A web page screenshot showing a firefox logo, a heading saying mozilla is cool, and two paragraphs of filler text](finished-test-page-small.png)

Если вы застряли, вы всегда можете сравнить свою работу с нашим [готовым примером кода](https://github.com/mdn/beginner-html-site/blob/gh-pages/index.html) на GitHub.

Здесь вы узнали только самую поверхность HTML. Чтобы узнать больше, перейдите на страницу [Обучение HTML: руководства и уроки](/ru/docs/Learn_web_development/Core/Structuring_content).

{{PreviousMenuNext("Learn/Getting_started_with_the_web/Dealing_with_files", "Learn/Getting_started_with_the_web/CSS_basics", "Learn/Getting_started_with_the_web")}}
