## Stack
HTML/CSS/JS (single file index.html), nginx:alpine (Docker), Cloudinary (media storage, cloud: dckjt7i6f, preset: mihey_upload), GitHub API (data storage), Cloudflare Workers (OAuth proxy)

## Architecture
- `index.html` — весь сайт: CSS + HTML + JS в одном файле
- `admin/index.html` — кастомная админка (пароль: passwordmihey)
- `data/projects.json` — проекты
- `data/tags.json` — теги
- `data/settings.json` — настройки (контакты, about текст, i18n)
- `data/uploads/` — обложки проектов (Cloudinary URLs)
- `Dockerfile` — FROM nginx:alpine, COPY . /usr/share/nginx/html
- `default.conf` — nginx конфиг с SPA роутингом и /en роутом
- `amvera.yaml` — конфиг Amvera (static_web, npm)
- `package.json` — минимальный, без сборки

## Current state
Working:
- Сайт на Amvera: miheysound.ru + miheysound-mikheysound-1.amvera.io
- Автодеплой: GitHub main → Amvera webhook → пересборка
- Сохранение из админки: OAuth воркер → GitHub → Amvera
- SPA роутинг через nginx try_files
- RU/EN переключатель в nav (RU справа вверху)
- About блок с 3 главами (интерактивный)
- CSS Grid сетка проектов с aspect-ratio 3/4
- Вертикальные видео в натуральных пропорциях

Broken/In progress:
- /en роут — nginx редиректит обратно на /
- Nav links по центру вместо правого угла (CSS конфликт)
- applyLang() не переключает тексты (LANG всегда 'ru')
- SSL сертификат на miheysound.ru ещё не выпустился
- OAuth воркер (mihey-oauth) — новые деплои падают, старая версия живёт

## Active task
Переключатель языка RU/EN:
- URL-based: / = RU, /en = EN
- Меняет: nav (Обо мне/About, Кейсы/Work, Связаться/Contact), About блок (3 главы)
- Тексты редактируются в админке → settings.json → i18n объект
- Проблема: /en редиректит на /, LANG всегда 'ru', тексты не меняются

## Key decisions made
- Хостинг: Amvera (Москва) вместо Cloudflare Pages — из-за блокировки РКН
- Deploy flow: git push origin main → GitHub webhook → Amvera пересборка (~2 мин)
- Данные: JSON файлы в GitHub репо (не БД)
- Медиа: Cloudinary (не локально)
- Язык: URL-based (/en), не localStorage
- Nav: logo слева, links + RU/EN справа
- About: 3 интерактивные главы, тексты в settings.json
- Карточки проектов: CSS Grid 4 колонки, aspect-ratio 3/4, object-fit contain, прозрачный фон

## Env & deploy
Hosting: cloud.amvera.ru, аккаунт mikheysound-1, приложение miheysound
Domain: miheysound.ru (DNS через Cloudflare, A запись → 158.160.116.199)
GitHub: github.com/egorasus/mihey-portfolio, ветка main
Amvera git: git.msk0.amvera.ru/mikheysound-1/miheysound, ветка master
OAuth Worker: miheysound-oauth.egorkurakinwork.workers.dev
Deploy: git push origin main (Amvera подхватывает через webhook)
Push to Amvera: git push https://mikheysound-1@git.msk0.amvera.ru/mikheysound-1/miheysound main:master
