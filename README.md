# Практическая работа 1.1

## Информация о студенте  
Шарманов Даниил Андреевич, ИВТ 1-1

## Комментарий о проделанной работе
В рамках данного задания была изучена и практически реализована процедура развертывания статического сайта на базе Hugo. Основные этапы работы включали:

  * Установку и настройку Hugo на локальной машине.
  * Инициализацию проекта и подключение современной темы оформления Blowfish.
  * Создание структуры сайта и наполнение его контентом: подготовлена главная страница, а также разделы «Обо мне» и «Мои проекты».
  * Настройку системы контроля версий Git и интеграцию локального репозитория с удаленным на GitHub.
  * Автоматизацию процесса публикации (CI/CD) с использованием GitHub Actions.

**Ссылка на развёрнутый сайт (GitHub Pages)**: [Портфолио](https://d4shsk.github.io/web-portfolio/)

## Скрипт развертывания GitHub Actions
Для автоматизации сборки и публикации был создан файл .github/workflows/hugo.yaml со следующим содержимым:

```yaml
name: Deploy Hugo site to Pages

on:
  push:
    branches: ["main"]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: 'latest'
          extended: true
      - name: Build
        run: hugo --minify
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

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

## Описание работы скрипта  
Скрипт разделен на две логические части:

  * **Build:**
    *  Checkout: копирует исходный код из репозитория на виртуальную машину GitHub, включая тему оформления (через параметр submodules: recursive).
    *  Setup Hugo: устанавливает последнюю расширенную (extended) версию Hugo, необходимую для обработки SCSS/SASS в теме Blowfish.
    *  Build: запускает команду hugo --minify, которая генерирует статические HTML-файлы в папку `public` и оптимизирует их размер.
    *  Upload artifact: упаковывает содержимое папки public и сохраняет его для следующего этапа.
  * **Deploy:**
    *  Берет подготовленный артефакт и публикует его на хостинг GitHub Pages, делая сайт доступным в сети. Процесс запускается автоматически при каждом обновлении кода в ветке main.
