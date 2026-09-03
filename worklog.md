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
---
Task ID: 240-241 (перенос из kip8test-desktop — частичный)
Agent: main (Super Z)
Task: Перенос в боевой десктоп kip8-desktop изменений Tasks 240+241
      (через GitHub Action sync-to-desktop.yml из kip8). Task 241 зебра
      применена; sidebar-move часть НЕ перенесена (требует модуль
      WorkSchedule, которого в kip8/kip8-desktop ещё нет).

Work Log:
- Источник: kip8@<commit после kipia-v394 push> (Tasks 240+241 — частичный
  перенос из kip8test@96039d0: только зебра, без sidebar-move).
- index.html (~line 3037-3038): светлая тема зебры карточек расходомеров —
  odd-ряд потемнее (rgba(243,233,223,0.96) vs было 248,242,238),
  even без изменений. Разница R-канала 4 → 9. CSS-комментарий с
  пояснением про sidebar-move deferred.
- index.html: sidebar-item «График работы» ВНУТРИ группы docs-ios
  НЕ добавлен — требует модуля WorkSchedule (Tasks 201-239).
- sw.js: в kip8-desktop НЕТ (Electron). Версия PWA в kip8:
  kipia-v393 → kipia-v394.

Stage Summary:
- В kip8-desktop применена ТОЛЬКО зебра-часть Task 241.
- Sidebar-move — ОТЛОЖЕНО (как и в kip8). Требуется предварительный
  перенос модуля WorkSchedule (Tasks 201-239) из kip8test.
- Источник: auto-sync из kip8@<commit> (GitHub Action).
- Файлы изменены: index.html (только зебра). sw.js отсутствует.
- Версия PWA в kip8: kipia-v394. Версия десктопа: 2.1.7.
- Пользователю: после пересборки Electron-приложения в светлой теме
  на странице расходомеров зебра карточек станет немного контрастней.

---
Task ID: 241 финал (auto-sync из kip8@5edaeac — модуль WorkSchedule Tasks 201-239 + sidebar-move)
Agent: main (Super Z)
Task: Финальный перенос в боевой десктоп kip8-desktop изменений Task 241
      из kip8 (commit 5edaeac). В kip8 завершён перенос модуля WorkSchedule
      (Tasks 201-239) + Task 241 sidebar-move. В kip8-desktop: index.html
      обновлён автоматически через GitHub Action sync-to-desktop.yml.

Work Log:
- Источник: kip8@5edaeac (Task 241 финал: перенос модуля WorkSchedule
  Tasks 201-239 + sidebar-move в kip8).
- index.html: auto-sync из kip8@5edaeac через GitHub Action
  sync-to-desktop.yml (триггер — push в kip8/index.html на ветке main).
  После sync: kip8-desktop/index.html содержит весь WorkSchedule-стек:
  - CSS-блок .ws-* (279 строк) — тулбар, сетка шахматки, легенда,
    карточки сотрудников и инструктажей, светлая тема;
  - HTML 3 страницы (#page-work-schedule/-employees/-trainings);
  - 3 bottom-sheet-а (wsCellOverlay/wsEmpOverlay/wsTrOverlay);
  - Кнопка меню workScheduleMenuBtn в .kip-ios-block страницы Документация ИОС;
  - JS-модуль var WorkSchedule = {...} (770 строк) — клиентский модуль;
  - 3 init-блока в navigateTo() (WorkSchedule.init/initEmployeesPage/initTrainingsPage);
  - _WORK_SCHEDULE_PAGES в role config (3 страницы);
  - Task 241 sidebar-move: sidebar-item-extra sidebarWorkScheduleBtn внутри
    сворачиваемой группы docs-ios сайдбара, счётчик «2».
- sw.js: в kip8-desktop НЕТ (Electron). Версия PWA в kip8: kipia-v394 → v395.
- Версия десктопа: 2.1.7 (без изменений — Electron-сборка не требует бампа
  для контентных обновлений, но пользователю нужно пересобрать/обновить
  приложение, чтобы получить новый index.html).

Stage Summary:
- kip8-desktop полностью синхронен с kip8@5edaeac: index.html содержит
  модуль WorkSchedule (Tasks 201-239) + Task 241 sidebar-move.
- После пересборки/перезапуска Electron-приложения под ролью «Админ» в
  сайдбаре внутри группы «Документация ИОС» появится пункт «График работы»
  (оранжевый, navigateTo('work-schedule')), при клике откроется шахматка
  сменного и дневного персонала ИОС.
- Допущения (как в kip8): Apps Script production deployment должен включать
  WorkSchedule.gs и обновлённые роуты Code.gs. До этого график работы
  возвращает no_session/unknown_action.
- Файлы изменены: index.html (auto-sync из kip8). worklog.md и
  Системный_промт_для_приложения_КИПиА.md — обновлены вручную.
- Локальная дата: 2026-08-29 18:07:29 UTC+07:00 (Asia/Novosibirsk).
---
Task ID: 306-перенос (десктоп)
Agent: main (Super Z)
Task: По указанию пользователя «Так же перенеси все последние
      изменения в боевой kip8-desktop» — доводка боевого десктопа
      до полного паритета с kip8@f43012f (партия Tasks 298-306).

Work Log:
- Проверка автосинка: пуш kip8@f43012f уже синхронизировал
  index.html + charts-desktop.js + devices-table-desktop.js
  (коммит ba23818 «auto: sync index.html from kip8@f43012f»,
  все 4 workflow kip8 — success). Верификация байт-в-байт: все
  3 файла IDENTICAL; images/ — идентичны.
- Найдена и устранена проблема: CI Tests в kip8-desktop ПАДАЛ
  (3 фейла в test-role-access.js — устаревший список от Task 190
  не знал новые страницы «Отпуска»/«График работы»;红了 3 пуша
  подряд: ba23818/04dc468/55de39a).
- tests/ — полный синк байт-в-байт из kip8@f43012f: 27 файлов
  (было 12): +15 новых (test-work-schedule, test-vacations-*
  ×5, test-work-events, test-tab-numbers, test-task306,
  test-deploy-url, test-flow-period-input, test-flowmeter-
  validation/-comment, test-prod-calendar, test-minesweeper),
  обновлены extract-functions/run-all/test-role-access.
- НОВОЕ для полного прогона тестов (фикстуры, в Electron-сборку
  НЕ попадают — files: index.html/images/data/electron):
  sw.js (32 КБ) + scripts/ 8×.gs (Code, Flowmeter,
  FlowmeterArchive, TabNumbersFix, VacationsDiagnose,
  VacationsInit, ValidationRules, WorkSchedule) — байт-в-байт
  из kip8. .gitignore: + scripts/.gscheck-code.js (врем. файл
  test-deploy-url).
- data/ офлайн-fallback: devices.json (1 695 988), lockouts.json
  (431 576), projects.json (176 687) — актуальные авто-коммиты
  Google Sheets из kip8 (70f8e40/562f7cc); остальные 6 файлов
  уже были идентичны.
- Системный_промт — копия kip8@f43012b post-Task 306 (166 453
  байта; было 77 030 — уровень Task 190).
- README.md — актуализированы секции «Связи с kip8» и «Структура»
  (sw.js/scripts/tests — автосинк-фикстуры; data/images — вручную).
- Локальная верификация: node tests/run-all.js → 1433 passed /
  0 failed (942 мс) — ПОЛНЫЙ ПАРИТЕТ с kip8@f43012f и
  kip8test@4fc48bc.
- Коммит + push (PAT-протокол: URL с токеном → push → сброс URL).

Stage Summary:
- kip8-desktop полностью синхронен с kip8@f43012f (партия Tasks
  298-306): контент (авто-синк ba23818) + тесты 1433/0 + data +
  промт. CI починен (было 495+3 фейла → 1433/0).
- ПОЛЬЗОВАТЕЛЮ НИЧЕГО НЕ НУЖНО: новый релиз НЕ требуется
  (electron/main.js и package.json не менялись; приложение грузит
  живой контент с GitHub Pages — пользователям достаточно
  перезапустить приложение; cleanCacheOnStartup подхватит kipia-v411).
  Apps Script и листы — уже задеплоены для kip8 в Task 306-перенос
  (ОДИН бэкенд на всё).
- Следующий номер в kip8: 307.
- Локальная дата: 2026-09-03 (Asia/Novosibirsk, UTC+07:00).
