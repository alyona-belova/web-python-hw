## Создание кастомной темы

- В папке `theme/` создан шаблон `main.html` с использованием Jinja:

  - Кастомныt **header** и **footer**
  - `css/custom.css` для оформления всех страниц

- Структура темы:

```
theme/
├─ main.html
├─ components/
│  ├─ header.html
│  └─ footer.html
├─ css/
│  └─ custom.css
```

- В `main.html` добавлены метаданные сайта:

```html
<meta name="description" content="{{ config.site_description }}" />
<meta name="author" content="{{ config.site_author }}" />
<title>
  {{ config.site_name }}{% if page.title %} - {{ page.title }}{% endif %}
</title>
```

## Настройка GitHub Actions (`.github/workflows/deploy.yml`)

```yaml
- uses: actions/setup-node@v3
  with:
    node-version: "20"
```

- Устанавливает **Node.js версии 20**
- Необходим для работы с `PostCSS`, `npm` и утилитами для минификации CSS/JS

```yaml
- name: Install Node dependencies
  run: |
    npm install -g postcss postcss-cli autoprefixer cssnano html-minifier terser html-validator-cli
```

- Устанавливает Node-инструменты:

  - `postcss` + `postcss-cli` — для обработки CSS
  - `autoprefixer` — добавляет в CSS префиксы для разных браузеров
  - `cssnano` — минификация CSS
  - `html-minifier` — минификация HTML
  - `terser` — минификация JS
  - `html-validator-cli` — проверка валидности HTML

```yaml
- name: Validate HTML
  run: npx html-validator-cli --file=site/index.html --verbose
```

- Проверяет HTML на корректность
- `npx` запускает установленный CLI без глобальной установки
- `--verbose` выводит подробные ошибки и предупреждения

```yaml
- name: Minify HTML
  run: |
    find site -name '*.html' -exec html-minifier --collapse-whitespace --remove-comments --minify-css true --minify-js true -o {} {} \;
```

- Ищет все HTML-файлы в папке `site/` и минифицирует их
- Опции:

  - `--collapse-whitespace` — убирает лишние пробелы
  - `--remove-comments` — убирает комментарии
  - `--minify-css true` — минифицирует встроенный CSS
  - `--minify-js true` — минифицирует встроенный JS

```yaml
- name: Process CSS with PostCSS
  run: |
    npx postcss site/css/*.css --use autoprefixer cssnano -d site/css/
```

- Берёт все CSS-файлы и обрабатывает их:

  - `autoprefixer` добавляет префиксы для браузеров
  - `cssnano` минифицирует CSS

- `-d site/css/` — сохраняет результат в ту же папку
