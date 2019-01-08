# Стартовый проект с gulp  ![Test Status](https://travis-ci.org/nicothin/NTH-start-project.svg?branch=master) [![devDependencies Status](https://david-dm.org/nicothin/NTH-start-project/dev-status.svg)](https://david-dm.org/nicothin/NTH-start-project?type=dev) [![dependencies Status](https://david-dm.org/nicothin/NTH-start-project/status.svg)](https://david-dm.org/nicothin/NTH-start-project)


## Парадигма

- Именование классов по БЭМ, разметка в [pug](https://pugjs.org/) и стилизация [Sass](http://sass-lang.com/). См. [Как работать с CSS-препроцессорами и БЭМ](http://nicothin.github.io/idiomatic-pre-CSS/)
- Каждый БЭМ-блок в своей папке внутри `src/blocks/`.
- Список использованных в проекте доп. файлов указан в `config.js`.
- Есть глобальные файлы: стилевые (стили печати), js (по умолчанию пуст), шрифты, картинки.
- Список pug-примесей `src/pug/mixins.pug` генерируется автоматически, содержит `include` всех существующих pug-файлов блоков.
- Диспетчер подключения стилей `src/scss/style.scss` генерируется автоматически.
- Входная точка обработки js (`src/js/entry.js`) генерируется автоматически.
- Перед созданием коммита запускается проверка файлов, входящих в коммит. При наличии ошибок коммит не происходит, ошибки выводятся в терминал.
- Есть механизм быстрого создания нового блока: `node createBlock.js new-block` (создаёт папки и scss-файл). После имени нового блока можно дописать нужные расширения.


## Команды

`npm start` — запуск сервера разработки (сборка **без библиотеки блоков**).

`npm run wlib` — запуск сервера разработки (сборка проекта с библиотекой блоков).

`npm run deploy` — cборка проекта (с библиотекой) и отправка содержимого папки сборки на gh-pages (не сработает, если проект не имеет репозитория на github.com).


## Структура

```bash
build/               # Папка сборки
src/                 # Исходники
  blocks/            # Блоки
  favicon/           # Фавиконки
  fonts/             # Шрифты
  img/               # Доп. изображения
  pages/             # Страницы проекта
  pug/               # Служебные pug-файлы
  scss/              # Служебные стилевые файлы
```


## Как начать новый проект c этим репозиторием

```bash
# Клонировать в новую папку
git clone https://github.com/nicothin/NTH-start-project.git new-project
# Зайти в папку
cd new-project
# Убрать историю разработки
rm -rf .git
# Отредактировать config.js, LICENSE, README.md, package.json
# Установить зависимость
npm i
# Запустить сервер разработки
npm start
```


## Как это работает

При `npm start` запускается дефолтная задача gulp:

1. Очищается папка сборки (`build/`).
5. Записывается файл `src/pug/mixins.pug` со всеми pug-файлами блоков.
5. Компилируются файлы страниц (`src/pages/**/*.pug`).
5. Из скомпилированных html-файлов извлекаются все классы уровня БЭМ-блока. На основании этого списка будут построены диспетчер подключения стилей и список всех js-файлов проекта.
5. Генерируются спрайты, в папку сборки копируются картинки блоков и доп. файлы из секции `addAssets` файла `config.js`.
5. Записывается диспетчер подключения стилей `src/scss/style.scss`, в котором:
    - Импорты файлов из секции `addStyleBefore` файла `config.js`. По умолчанию — SCSS-переменные и примеси.
    - Импорты файлов БЭМ-блоков, использующихся на проекте.
    - Импорты файлов БЭМ-блоков, упомянутых в секции `alwaysAddBlocks` файла `config.js`. Таким образом, можно взять в сборку любой блок, даже если его css-класс не упоминается в разметке страниц.
    - Импорты файлов из секции `addStyleAfter` файла `config.js`. По умолчанию — стили для печати страницы.
5. Записывается диспетчер подключений скриптов `src/js/entry.js`, в котором:
    - `require` файлов из секции `addJsBefore` файла `config.js`.
    - `require` файлов БЭМ-блоков, использующихся на проекте.
    - `require` файлов БЭМ-блоков, упомянутых в секции `alwaysAddBlocks` файла `config.js`.
    - `require` файлов из секции `addJsAfter` файла `config.js`.
5. Компилируются и обрабатываются PostCSS-ом стили (`src/scss/style.scss`).
6. Javascript (`src/js/entry.js`) собирается webpack-ом.
5. Запускается локальный сервер и слежение за файлами для пересборки.


## Блоки

Каждый блок лежит в `src/blocks/` в своей папке.

Возможное содержимое блока:

```bash
demo-block/               # Папка блока.
  bg-img/                 # Изображения для использования в стилях (не обрабатываются автоматикой).
  img/                    # Изображения, используемые блоком (копируются в папку сборки).
  demo-block.pug          # Разметка (pug-примесь, отдающая разметку блока, описание API примеси).
  demo-block.scss         # Стилевой файл блока.
  demo-block.js           # js-файл блока.
  readme.md               # Описание для документации, подсказки.
```


### Удобное создание нового блока

```bash
# формат: node createBlock.js ИМЯБЛОКА [доп. расширения через пробел]
node createBlock.js demo-block        # создаст папку блока, demo-block.scss, подпапки img/ и bg-img/ для этого блока
node createBlock.js demo-block pug js # создаст папку блока, demo-block.scss, demo-block.pug, demo-block.js, подпапки img/ и bg-img/ для этого блока
```

Если блок уже существует, файлы не будут затёрты, но создадутся те файлы, которые ещё не существуют.


## Разметка

Используется [pug](https://pugjs.org/api/getting-started.html).

Все страницы (см. `src/pages/index.pug`) являются расширениями шаблонов из `src/pug` (см. [наследование шаблонов](https://pugjs.org/language/inheritance.html)), в страницах описывается только содержимое «шапки», «подвала» и контентной области (см. [блоки](https://pugjs.org/language/inheritance.html#block-append-prepend)).

Каждый блок (в `src/blocks/`) может содержать одноимённый pug-файл с одноименной примесью, который при старте сервера разработки будет взят includ-ом в файл `src/pug/mixins.pug`.


## Стили

Диспетчер подключений (`src/scss/style.scss`) формируется автоматически при старте сервера разработки.

Каждый блок (в `src/blocks/`) может содержать одноимённый scss-файл со стилями этого блока. Если блок используется в разметке (или упомянут в `config.js#alwaysAddBlocks`), его scss-файл будет взят в сборку стилей.

Используемый постпроцессинг:

1. [autoprefixer](https://github.com/postcss/autoprefixer)
2. [css-mqpacker](https://github.com/hail2u/node-css-mqpacker)
3. [postcss-import](https://github.com/postcss/postcss-import)
4. [postcss-inline-svg](https://github.com/TrySound/postcss-inline-svg)
6. [postcss-object-fit-images](https://github.com/ronik-design/postcss-object-fit-images) (в паре с [полифилом](https://github.com/bfred-it/object-fit-images))


## Скрипты

Точка входа (`src/js/entry.js`) формируется автоматически при старте сервера разработки. Точка входа обрабатывается webpack-ом (с babel-loader).

Для глобальных действий предусмотрен `src/js/script.js` (см. `config.js#addJsAfter` и `config.js#addJsBefore`).

Каждый блок (в `src/blocks/`) может содержать одноимённый js-файл. Если блок используется в разметке (или упомянут в `config.js#alwaysAddBlocks`), его js-файл будет взят в сборку стилей.


## Модульная сетка (flexbox)

По умолчанию в сборку берётся [файл с примесями](https://github.com/nicothin/NTH-start-project/blob/master/src/scss/mixins/grid-mixins.scss), возвращающими правила модульной сетки. Cелекторов в CSS не добавляет, нужно писать семантические селекторы и вызывать примеси. Настройки по умолчанию вынесены в переменные (`$grid-columns: 12;` и `$grid-gutter-width: 30px;`).

- Любое кол-во колонок, в т.ч. разная колоночность на разных ширинах вьюпорта.
- Любые промежутки между колонками, в т.ч. разные на разных ширинах вьюпорта.

```scss
.promo {

  &__inner {
    @include container(); // это контейнер, padding по умолчанию (из переменной)
  }

  &__grid {
    @include row();// это прямой родитель сетки, margin по умолчанию (из переменной)
  }

  &__img {
    @include col();      // узкий вьюпорт, 100% ширина и padding по умолчанию
    @include col(md, 5); // MD-вьюпорт (786 по умолчанию) ширина 5 колонок и padding по умолчанию
  }

  &__text {
    @include col();      // узкий вьюпорт, 100% ширина и padding по умолчанию
    @include col(md, 7); // MD-вьюпорт (786 по умолчанию) ширина 7 колонок и padding по умолчанию
  }
}

```

```scss
.row
  // Родитель. Указаны промежутки между ячейками: XS|SM|MD|LG|XL|XXL;
  @include row($grid-gutter-width, 10px, 12px, 16px, 20px, 26px);

  &__element {
    @include col(); // малые ширины — 12 из 12, промежутки по умолчанию
    @include col(sm, 6, $grid-columns, 10px);  // SM (480 по умолчанию) — 6 из 12, промежутки 10px
    @include col(md, 4, $grid-columns, 12px);  // MD (768 по умолчанию) — 4 из 12, промежутки 12px
    @include col(lg, 3, $grid-columns, 16px);  // LG (992 по умолчанию) — 3 из 12, промежутки 16px
    @include col(xl, 2, $grid-columns, 20px);  // XL (1200 по умолчанию) — 2 из 12, промежутки 20px
    @include col(xxl, 1, $grid-columns, 26px); // XXL (1800 по умолчанию) — 1 из 12, промежутки 26px
  }
}
```

Посмотреть примеры и попробовать вживую можно в [этом примере с codepen.io](https://codepen.io/nicothin/pen/aJEOwE?editors=1100).


## Нравится проект?

Ставьте звезду в верхнем правом углу и/или [угостите меня кофе](https://money.yandex.ru/to/41001252765094), переведя сколь угодно символическую сумму.
