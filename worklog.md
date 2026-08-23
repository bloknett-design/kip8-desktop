# Worklog — kip8-desktop (боевая десктоп-сборка КИПиА)

---
Task ID: 1
Agent: AI Assistant (GLM)
Task: Создание репозитория kip8-desktop (рабочее десктопное приложение) по образцу kip8test-desktop, наладка связей с kip8

Что сделано:
- Репозиторий https://github.com/bloknett-design/kip8-desktop создан (public)
- Структура скопирована из kip8test-desktop и адаптирована:
  * electron/main.js: REMOTE_APP_URL -> https://bloknett-design.github.io/kip8/
    (cleanCacheOnStartup origin общий — без изменений)
  * package.json: name kipia-desktop, appId com.bloknett.kipia,
    productName KIPiA, publish repo kip8-desktop, ярлык «КИПиА» (без Test),
    artifactName KIPiA-* (без Test), version 2.1.7
  * index.html / data/ / images/ / tests/ — из kip8@2f27982 (v371, перенос
    Task 143): 251 тест проходят
  * README.md — адаптирован под продакшн
  * workflows: build-desktop.yml (сборка Win/Linux/macOS на тег v*) + ci.yml
- НЕ переносится из kip8test-desktop: Системный_промт (специфика kip8test),
  build.mjs/vite.config.mjs (не используются workflow), bun.lock

Связи с kip8 (по образцу тестовых версий):
- В kip8 добавлен .github/workflows/sync-to-desktop.yml — при пуше в main,
  меняющем index.html, автоматически коммитит его в kip8-desktop
  (секрет DESKTOP_SYNC_TOKEN, см. Task 2 в worklog kip8test)
- Приложение грузит живую страницу kip8 (GitHub Pages), fallback — локальные
  файлы app:// (офлайн)
- cleanCacheOnStartup: свежий контент при каждом перезапуске (без релиза)
- electron-updater: обновления из Releases этого репозитория

Stage Summary:
- kip8-desktop готов к сборке релизов (тег v2.1.7 -> установщики
  Win/Linux/macOS в Releases)
- Первый релиз: v2.1.7 (соответствует функциональности kip8@v371)
