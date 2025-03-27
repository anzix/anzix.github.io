+++
title = "!Мои заметки при создании сайта используя zola"
date = 2023-06-13
draft = false
[taxonomies]
categories = []
tags = []
+++

zola - это генератор статического сайта написанный на Rust


## Этапы создания сайта Github

Перед этим импортируем конфиги git

```sh
stow -vt ~ git
```

1. Создать пустое репо github `git init`
2. Вводим команды для добавления удалённого репозитория

   ```sh
   git remote add origin [url]
   git branch -M main
   git push -u origin main
   ```

3. Создание токена, переходим по [ссылке для создания токена](https://github.com/settings/tokens/new?scopes=public_repo)

   После создания копируем его

   Заходим в настройки репозитория **Settings** - **Secrets and
   variables** - **Actions** - жмём на **New repository secret**

   И вставляем скопированный токен

4. На главной странице репозитория сайта жмём на **Action** - и на "**set
   up a workflow yourself**"

   Вставляем то что предлагает zola в документации по [ссылке](https://www.getzola.org/documentation/deployment/github-pages/)
   и стартуем

> Если ветка не **main** а **master** тогда редактируем этот параметр в yml файле
> (не проверено)

Финальный шаг: После в репо **website** в **Settings** - **Pages** - выставляем
ветку вместо **main** на **gh-pages**, и сайт готов.

## Как использовать свою собственную тему для синтаксиса кода?

Создаём папку

```sh
mkdir site/syntaxes
```

Вставляем в эту папку свою темку, например `cobalt2.tmTheme`

Чтобы применить тему открываем `config.toml` и вставляем

```toml
# Чтобы подхватить кастомную тему синтаксиса
extra_syntaxes_and_themes = ["syntaxes"]
highlight_theme = "cobalt2"
```

## Как добавить якорь (anchor) в zola?

Создаём `site/templates/anchor-link.html` с данным содержанием

```html
<!-- # <- можно поменять на что угодно -->
<a class="anchor" href="#{{ id }}">#</a>&nbsp;
```

В `_index.md` самый конец вставляем содержимое

```md
+++
insert_anchor_links = "left"
+++
```

И последнее редактируем `style.css` чтобы придать вид якорю

```css
h1,
h2,
h3,
h4,
h5,
h6 {
  .anchor {
    text-decoration: none;
    border-bottom-color: transparent;
    cursor: pointer;
    padding-left: 1rem;
  }
}
```

## !Как использовать переменные для расширения функционала страницы используя js? (???)

Например добавить возможность копировать код одной кнопкой

Добавляем if (если) statement (утверждение) в `base.html`

```html
{% if page.extra.copy_code %}
<script src="/assets/js/copy.js"></script>
{% else %} {% if section.extra.copy_code %}
<script src="/assets/js/copy.js"></script>
{% endif %} {% endif %}
```

> Пояснение: Т.е если на какой-либо страничке `page.html` (`*.md`)
> или в секции `section.html` присутствует блок [extra]
> с данными аргументом переведённым в состояние **true**
> тогда будет выполнятся javascript файл

```txt
[extra]
copy_code = true
```

## !Как указать последнее изменение статьи и списке постов?

Добавляем в `page.html`

```html
...
<li>
  {{[ page.date | date(format="%F") ]}}<a href="{{ page.permalink | safe }}">
    {{ page.title }} </a
  >(last edited: {{ page.date }})
</li>
...
```

## !Как добавить поиск на веб странице в zola? (Работает)

Редактируем `config.toml` и меняем язык на `en` т.к я пока не знаю как сделать
разделение на два языка, и чтобы это всё работало

```toml
defualt_language = "en"
```

Там же добавляем данные опции чтобы генерировать индексацию поиска

```toml
[search]
index_format = "elasticlunr_json"

# Если установлено значение "true", поисковый индекс строится из содержимого страниц
# и раздела для `default_language`.
build_search_index = true
```

Создаём папку (в url это будет отображаться так `http://127.0.0.1:1111/pages/search/`)

```sh
mkdir site/content/pages/
nvim site/content/pages/search.md
```

И в `search.md` вставляем шаблон страницы поиска

```md
+++
title = "Поиск"
template = "search.html"
+++
```

Затем создаём необходимый html шаблон поиска в template который мы указали в `search.md`

```sh
nvim site/template/search.html
```

```html
{% extends "base.html" %} {% block content %}
<article>
  <header>
    <!-- Имя заголовка отражается из содержимого content/pages/search.md -->
    <h1>{{ page.title }}</h1>
  </header>
  <section class="modal-card-body">
    <div class="field mb-2">
      <div class="control">
        <input
          class="input"
          id="search"
          placeholder="🔎  поиск"
          type="search"
        />
      </div>
    </div>
    <!--Вывод выхлопа результата из search.js-->
    <div class="search-results">
      <ul class="search-results__items"></ul>
    </div>
  </section>
  <!--Здесь должны быть скрипты, но из-за бага в zola я не могу их вписать-->
</article>
{% endblock content %}
```

Скрипт `search.js` я взял из [исходников документации zola](https://github.com/getzola/zola/blob/master/docs/static/search.js),
кладём его по пути `site/js/search.js`

Также в `style.css` я добавил отступ списка

```css
.search-results__item {
  margin-bottom: 9px;
}
```

Поиск готов!

## Как добавить нумерацию страниц?

Указываем в `_index.md` число страниц которые будут появляться в нумерации, без
этого у нас будет просто пустой список страниц

```md
...
paginate_by = 30
```

Далее редактируем `posts.html` (или `blogs.html`) добавляя саму нумерацию

В функции **for** вместо **section** меняем на **paginate**. И добавляем
навигационный тег **nav**

```html
{% for page in paginate.pages %} ...
<nav class="navigation">
  {% if paginator.previous %}
  <a class="navigation-prev" href="{{ paginator.previous }}">‹ Предыдущая</a>
  {% endif %} {% if paginator.next %}
  <a class="navigation-next" href="{{ paginator.next }}">Следующая ›</a>
  {% endif %}
</nav>
```

## Как добавить в страницу (page.html) показатель тегов?

Добавляем функцию в `page.html`

```html
...
{% if page.taxonomies.tags %}
    {% for tag in page.taxonomies.tags %}
         <a href="{{ get_taxonomy_url(kind="tags", name=tag) }}">#{{ tag }}</a>
{% endfor %}
<br>
<br>
...
```

## При padding'е в блоке code на первой строчке появляется пустой пробел

- [Источник](https://stackoverflow.com/questions/35999212/css-padding-left-value-of-code-is-applied-for-only-the-first-line)

![image](/images/my-site-on-zola/Screenshot_20230612_135739.png)

РЕШЕНИЕ: Необходимо было добавить `display: inline-block;`
