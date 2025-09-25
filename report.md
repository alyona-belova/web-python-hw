# Создание и развертывание статического сайта
## Создание виртуального окружения и установка зависимостей
```bash
virtualenv env
. env/bin/activate
pip3 install mkdocs
pip3 install mkdocs-material 
pip3 install pymdown-extensions
```

## Локальный запуск и сборка сайта
```bash
mkdocs serve
mkdocs build
```

## Настройка GitHub Actions (`.github/workflows/deploy.yml`)
```yaml
name: Deploy static site
on:
  push:
    branches: [main]
permissions:
  contents: read
  pages: write
  id-token: write
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: "3.x"
      - name: Install dependencies
        run: pip install mkdocs mkdocs-material pymdown-extensions
      - name: Build site
        run: mkdocs build
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./site
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```
* `on: push: branches: [main]` — workflow запускается каждый раз, когда есть push в ветку main
* `contents: read` — позволяет считывать содержимое репозитория
* `pages: write` — разрешает публиковать на GitHub Pages
* `actions/checkout@v3` — клонирует репозиторий, чтобы workflow имел доступ к файлам
* `actions/setup-python@v4` — устанавливает Python 3 на виртуальной машине
* `pip install mkdocs mkdocs-material pymdown-extensions` — устанавливает необходимые библиотеки для сборки сайта
* `mkdocs build` — собирает статический сайт в  папку `site/`
* `actions/upload-pages-artifact@v3` — передаёт собранный сайт в job деплоя
* `deploy-pages@v4` — action GitHub для публикации Pages

## Подключение Actions к Pages
1. Создать workflow (`.github/workflows/deploy.yml`) в репозитории
2. Commit и push изменения на ветку `main`
3. В Settings → Pages источником выбрать GitHub Actions

---
# Ответы на вопросы
### 1. Возможности использования отечественных CDN для ускорения доставки контента.
Отечественные CDN:
- [Ngenix](https://ngenix.net) – российский провайдер облачных сервисов. Входит в группу компаний «Ростелеком — центры обработки данных».
- [Selectel](https://selectel.ru) – отечественный облачный провайдер.
- [Web Support Revolution](https://w.tools/ru/) – российская компания, CDN-провайдер.
- [CDNvideo](https://www.cdnvideo.ru) – оператор сети доставки контента в России и странах СНГ. Входит в холдинг Билайн.

### 2. Возможности Gitverse для реализации CI/CD.
- Workflow — файл с последовательностью задач, размещается в `.gitverse/workflows/` и запускается автоматически при push.  
- Раннеры — среда выполнения workflow: облачные (ubuntu-cloud-runner), Docker-контейнеры или локальные бинарные раннеры.  
- Секреты — безопасное хранение ключей, токенов и паролей для использования в workflow.  
- Шаблоны — можно создать готовый workflow через интерфейс GitVerse CI/CD.  
- Совместимость с GitHub Actions позволяет использовать уже существующие `.github/workflows/` файлы.

### 3. Какие существуют варианты деплоя статического сайта в продакшен среду и какие технические инструменты для этого могут потребоваться, приведите примеры.
1. GitHub Pages / GitLab Pages
- простая интеграция с репозиторием
- CI/CD можно настроить через Actions (GitHub) или GitLab CI
- бесплатно для небольших проектов
2. CDN или облачные хранилища
- примеры: Яндекс.Облако Object Storage, AWS S3, Google Cloud Storage
- можно использовать CDN для ускорения доставки
- технические инструменты: aws-cli, gsutil, интеграция с CI/CD для автоматической загрузки файлов
3. Собственный сервер или VPS
- размещение сайта на Nginx/Apache
- можно автоматизировать деплой через rsync, scp или Docker
- CI/CD инструменты: GitHub Actions, GitLab CI, Jenkins, Gitverse
4. Специализированные платформы для статических сайтов
- примеры: Netlify, Vercel, Cloudflare Pages
- автоматический билд и деплой при push в репозиторий
- поддержка пользовательских доменов, HTTPS, редиректов

