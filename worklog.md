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

---
Task ID: 190
Agent: AI Assistant (GLM)
Task: Синхронизация с kip8@bfcacbb (kipia-v393, Tasks 180-189) + фикс устаревшего devices-table-desktop.js (пропущены Tasks 163-176)

Work Log:
- Автосинк index.html: e95bc37 «auto: sync index.html from kip8@672fa8d»
  (правки Tasks 180-189: строка крошек, шеврон flowmeter-data, три точки
  на разделителе, мобильное «Избранное», секретные кнопки)
- Найден и устранён пропуск синхронизации: в kip8-desktop был
  devices-table-desktop.js от v371 (360 строк, Task 148) — без фильтров
  по колонкам, изменения ширины мышью, клавиатуры, виртуального
  скролла, CSV-экспорта и статистики (Tasks 163-176). Причина:
  sync-to-desktop.yml в kip8 синхронизировал ТОЛЬКО index.html, тогда
  как в kip8test workflow синхронизирует 3 файла (index.html,
  charts-desktop.js, devices-table-desktop.js)
- devices-table-desktop.js и charts-desktop.js скопированы из kip8
  (1627 строк / 496 строк — актуальные версии)
- sync-to-desktop.yml в kip8 исправлен: теперь синхронизирует все
  3 файла, как в kip8test (коммит в kip8)
- tests/test-role-access.js синхронизирован из kip8: 498 тестов
  (было 446 с устаревшим файлом; в kip8-desktop прогоне —
  498 passed, 0 failed после обновления модуля)
- Системный промт обновлён до post-Task 189: версии кэшей
  (kipia-test-v455 / kipia-v393), 498 тестов, раздел
  «Десктоп: строка крошек и разделитель панелей (Tasks 180-189)»

Stage Summary:
- kip8-desktop полностью синхронен с kip8: index.html (v393),
  devices-table-desktop.js (Tasks 163-176), charts-desktop.js,
  тесты (498)
- Фикс workflow гарантирует: при изменении десктоп-модулей в kip8
  они автоматически попадают в kip8-desktop (раньше — только вручную)
- Пользователям десктопа: перезапустить приложение — таблица приборов
  получит фильтры/ширину колонок/клавиатуру/CSV; релиз 2.1.7 остаётся
  актуальным (изменения только в контенте)
- Следующий Task ID — 191
