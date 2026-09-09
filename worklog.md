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
---
Task ID: 339 (десктоп)
Agent: Z.ai Code (главная сессия)
Task: Заявка пользователя: «Нужно сделать отдельную сборку
      десктопной версии для Win32» — 32-битный Windows-инсталлятор
      в дополнение к x64 (kip8-desktop, ПРОДАКШН).

Work Log:
- ДИАГНОСТИКА: win-цель electron-builder была только x64 →
  на 32-битных Windows приложение не ставится вообще.
  Проверено по ИСХОДНИКАМ зафиксированных версий (package-lock:
  app-builder-lib 26.15.3, electron-updater 6.8.9, electron
  35.7.5 — бинарник electron-v35.7.5-win32-ia32.zip существует):
  1) один прогон electron-builder с arch [x64, ia32] пишет ОДИН
     общий latest.yml (Windows — без arch-суффикса), files[]
     обоих инсталляторов (PR electron-builder#2994);
  2) electron-updater NsisUpdater.findFile(files, "exe") ищет
     в URL файла process.arch («x64»/«ia32»), при отсутствии —
     первый (там ia32) → имена артефактов ОБЯЗАНЫ содержать arch.
- РЕАЛИЗАЦИЯ (package.json): win.target.arch ["x64","ia32"];
  win.artifactName KIPiA-Setup-${version}.${ext} →
  KIPiA-Setup-${version}-${arch}.${ext} (→ KIPiA-Setup-2.1.8-
  x64.exe + KIPiA-Setup-2.1.8-ia32.exe); version 2.1.7 → 2.1.8.
  electron/main.js НЕ тронут (нативных модулей нет, контент —
  живой с Pages; ia32-сборка функционально идентична x64).
- .github/workflows/build-desktop.yml: шаг build-win —
  «NSIS, x64 + ia32/Win32», аплоад «installers (x64 + Win32)»
  (dist/*.exe — оба), dist/latest*.yml — общий; текст релиза —
  раздельные строки Windows 64-бит (x64.exe) / Windows 32-бит
  Win32 (ia32.exe) + пояснение про независимое автообновление.
- README.md: секция «Сборка и релизы» — два установщика,
  НОВАЯ подсекция «Как выбрать установщик Windows» (правило
  выбора + механизм автообновления), примечание в «Связи с kip8».
- РЕЛИЗ: коммит в main (CI BuildDesktop собирает ОБЕ арх —
  валидация), затем тег v2.1.8 → GitHub Release с
  KIPiA-Setup-2.1.8-x64.exe (~87 МБ) + KIPiA-Setup-2.1.8-ia32.exe
  + общий latest.yml (files: ia32, x64 — каждой разрядности свой
  файл) + blockmap-ы; существующие 64-битные установки 2.1.7
  предложат обновление до 2.1.8 и скачают x64-файл.
- ПАРИТЕТ: те же правки конфига — kip8 (стейджинг-сборка,
  version 1.0.0→1.1.1) и kip8test-desktop (тест, KIPiA-Test-*,
  2.1.7→2.1.8) — без тегов.

Stage Summary:
- kip8-desktop v2.1.8: ОТДЕЛЬНАЯ Win32-сборка ГОТОВА — релиз
  v2.1.8 содержит KIPiA-Setup-2.1.8-ia32.exe (32-бит) и
  KIPiA-Setup-2.1.8-x64.exe (64-бит); автообновление
  разрядностезависимое, единый latest.yml.
- 32-битным пользователям: скачать KIPiA-Setup-2.1.8-ia32.exe
  из Releases и установить. 64-битным: как обычно (x64-файл или
  автообновление).
- Контент/сервер/листы/Apps Script НЕ тронуты (только
  сборочная конфигурация).
- Локальная дата: 2026-09-07 (Asia/Novosibirsk, UTC+07:00).

УТОЧНЕНИЕ (Task 339, по фактам CI-прогона 34cbc1d):
- electron-builder 26.15.3 с arch ["x64","ia32"] + ${arch} в
  artifactName собирает ТРИ установщика (NsisTarget.finishBuild:
  builds = универсальный + по одному на arch, т.к. шаблон имени
  содержит ${arch}): KIPiA-Setup-2.1.8.exe (УНИВЕРСАЛЬНЫЙ —
  обе разрядности, сам выбирает при установке; arch=null →
  ${arch} опускается, имя без суффикса, как у 2.1.7) +
  KIPiA-Setup-2.1.8-x64.exe + KIPiA-Setup-2.1.8-ia32.exe.
  CI подтвердил: артефакт KIPiA-win-setup 338.9 МБ (≈170+87+85),
  лог build-win: «building target=nsis file=dist\KIPiA-Setup-
  2.1.8.exe archs=x64, ia32» → «-x64.exe archs=x64» → «-ia32.exe
  archs=ia32»; blockmap-ы для всех трёх.
- latest.yml: files[] = [универсальный, ia32, x64] (сортировка:
  arch=null первым); NsisUpdater.findFile у 64-битного приложения
  берёт файл с «x64» в URL, у 32-битного — «ia32»; fallback —
  универсальный. Автообновление корректно для обеих разрядностей.
- README/worklog/текст релиза обновлены: ТРИ Windows-файла
  (универсальный + x64 + ia32), правило выбора упрощено
  («не знаете разрядность — качайте KIPiA-Setup-<версия>.exe»).
- Аналогичный CI-успех (main, без тега): kip8@f192fd6
  (KIPiA-win-setup 339.0 МБ) и kip8test-desktop@888fcae
  (337.4 МБ; build-mac там упал на ECONNRESET при npm ci —
  сетевой флэйк раннера, build-win/build-linux success).

---

## Task 347 — догон справочников (синхрон с kip8, 2026-09-08)

Заявка: «проверить, чтобы в репозиториях проектов все файлы были
актуальными» (сопровождение Tasks 346/347).

- scripts/Code.gs — догон до байт-в-байт ≡ kip8: фикс роутера Task 346
  (`Auth.verifyOTP(…, payload)` — иначе политика «1 моб + 1 десктоп»
  неактивна) + вызов `Utils.cleanupStaleSessions()` в hourlyCleanup
  (Task 347);
- scripts/RoleMatrixGate.gs + scripts/RoleMatrixTask340Init.gs — НОВЫЕ
  справочники из kip8: авто-синк принёс tests/test-task340.js, но НЕ
  файлы → CI был красный (2286/2: ENOENT RoleMatrixGate.gs, «файл
  init-скрипта существует» = false);
- Тесты: **2288/0** (было 2286/2). Релиз НЕ нужен: electron/main.js,
  package.json и контент не менялись — только справочники scripts/.
- Догон №2 (после ребейза на origin/6e8a9d6): авто-синк kip8@7ceed82
  принёс в tests/ ПОЛНЫЙ прогон (вкл. Task 346/347), которому нужны
  справочники Auth.gs, Sessions.gs, SessionsDevicePolicy.gs, Utils.gs
  и DEPLOY-Task346-доки — до догона run-all.js падал ENOENT на
  scripts/Sessions.gs (репо на GitHub было сломано). Скопированы из
  kip8 → **2338/0 = полный паритет тестов с kip8**.

Следующий номер задачи: 348.

---

## Task 348 — синхрон справочников с kip8 (2026-09-09)

- `scripts/Utils.gs`, `scripts/Sessions.gs`, `scripts/Auth.gs`,
  DEPLOY-Task348-uuid-lockservice.md — байт-в-байт из kip8
  (getUuid вместо Math.random; Utils.withLock от гонок);
- tests/test-task348.js + test-task346.js + test-task347.js +
  run-all.js — синхрон из kip8;
- Тесты: **2366/0 = полный паритет с kip8** (было 2338). Релиз НЕ
  нужен: менялись только справочники scripts/ и тесты.

Следующий номер задачи: 349.

## Task 349 — синхрон справочников с kip8 (2026-09-09)

- `scripts/Utils.gs`, `scripts/Sessions.gs`, `scripts/Auth.gs`,
  DEPLOY-Task349-updaterole-evict-naming-ip-cleanup.md — байт-в-байт
  из kip8 (мёртвый IP-код удалён; SESSION_CLEANUP_ORPHAN;
  updateRole: замок + снапшот sessions!D + мгновенная выгонка при
  «Запрет»);
- tests/test-task349.js + run-all.js — синхрон из kip8;
- Тесты: **2394/0 = полный паритет с kip8** (было 2366). Релиз НЕ
  нужен: менялись только справочники scripts/ и тесты.

Следующий номер задачи: 350.

---
## Task 350 (09.09.2026) — sync: справочники Utils.gs/Auth.gs из kip8

Мёртвых изменений клиента НЕТ (только scripts/ и tests/). Байт-в-байт:
Utils.gs, Auth.gs, DEPLOY-Task350-locks-cleanup-batch.md,
test-task350.js, test-task348.js (актуализирован), run-all.js.
Тесты **2427/0** = полный паритет с kip8. Релиз desktop-приложения
не нужен (сеть/клиент не менялись — только серверные справочники).

Следующий номер задачи: 351.

## Task 351 (09.09.2026) — sync: deleteUser + кэш чтений + listLogs-хвост + config

Справочники Utils.gs/Code.gs/Sessions.gs + DEPLOY-дока
байт-в-байт из kip8 (Admin.deleteUser с гардами; кэш чтений
_rowsCache/beginExecution/инвалидация; getLastRows-хвост для
listLogs; устойчивый getConfig; setCell-переводы heartbeat/
getCurrentUser/updateRole). Тесты синхронизированы (test-task351
+46, test-task349/350 актуализированы) → **2473/0** = полный
паритет с kip8. Релиз desktop-приложения НЕ нужен (только
scripts/ и tests/ — серверные справочники).

Следующий номер задачи: 352.

## Task 354 (09.09.2026) — легаси-сборка для Windows 7/8.1 + Electron 22-совместимость main.js

Заявка: на старом 32-битном Windows при запуске установленного
KIPiA-Setup-2.1.8-ia32.exe — «KIPiA.exe не является приложением Win32».

Диагноз (разбор PE-заголовков релиза v2.1.8 локально): установщик
корректен (все бинарники i386), но Electron 35 (Chromium 134)
собран с MajorOperatingSystemVersion = 10.00 → загрузчик Win7/8.1
отклоняет образ (ERROR_BAD_EXE_FORMAT). ia32 = разрядность, не
совместимость со старыми ОС: обычная ia32-сборка — для 32-битной
Windows 10+. Решение: легаси-линейка на Electron 22.3.27
(последняя с поддержкой Win7/8.1, PE OS 5.01; проверено: e22=5.01,
e23=5.01, e35=10.00; Chromium 108-совместимость контента — JS 0
преград, CSS только scrollbar-width с webkit-фолбэками).

- electron-builder-legacy.yml — НОВЫЙ: electronVersion 22.3.27,
  ia32+x64, artifactName KIPiA-Setup-${version}-win7-${arch}.${ext},
  extraMetadata { kipiaWin7Legacy: true, version: 2.1.8-lts },
  output dist-legacy, publish НЕТ (без latest.yml-канала).
- .github/workflows/build-legacy-win7.yml — НОВЫЙ: тег legacy-v* /
  workflow_dispatch; сборка на windows-latest (electron-builder на
  Linux требует wine для rcedit — на Windows-раннере нативно);
  verify-pe джоба на ubuntu (7z + python: machine 0x014c/0x8664 +
  PE OS 5.01 — страховка от Win10-бинарников); релиз legacy-v* —
  PRERELEASE (не «latest», не мешает каналу автообновления), только
  *.exe.
- electron/main.js — совместимость с Electron 22 при сохранении
  поведения на 35: протокол двойной путь (protocol.handle для 25+ /
  registerBufferProtocol для 22, общая resolveFile); isRemoteAvailable
  через модуль net (Node 16 без global fetch; единый путь, системный
  прокси); автообновление отключается флагом kipiaWin7Legacy
  (ленивый require electron-updater, no-op checkForUpdates, диалог в
  меню); setTimeout(checkForUpdates) под гвардом.
- Локальная верификация в песочнице: electron-builder скачал
  e22.3.27, win-ia32-unpacked/KIPiA.exe = i386 + OS 5.01, x64 = OS
  5.02, asar { version 2.1.8-lts, kipiaWin7Legacy true, весь новый
  main.js }; финальная NSIS-упаковка локально упёрлась в wine —
  перенесена на windows-latest (паттерн проверенного build-win).
- .gitignore: dist-legacy/.
- README: раздел «Легаси-сборка для Windows 7/8.1» + предупреждение
  в «Как выбрать установщик» (основным нужно Win10+, в т.ч. ia32).
- tests/test-task354.js +30 (SRC: флаг, ленивый require, гварды,
  двойной протокол, нет fetch/AbortController, yml-конфиг, workflow
  триггеры/windows-latest/PE-гварды/prerelease/только-exe, gitignore;
  VM: isRemoteAvailable 7 сценариев на моке net, resolveFile 4 —
  файлы с MIME, 404). Моки БЕЗ фазы таймеров (queueMicrotask):
  async-тесты будили блуждающие setTimeout-focus из eval-скриптов
  старых тестов → уронили прогон (318, 322) → flake-fix: моки focus
  в test-task318.js (по прецеденту Task 325) + микротасковые моки.
- Зеркала: тесты в kip8/kip8test (синк-инвариант: ассерты на
  desktop-файлы за existsSync-гардами, в прочих репо — заглушка);
  main.js в kip8test-desktop (свой REMOTE_APP_URL; тест-изоляция
  Task 345 проходит, diff = ровно 2 строки URL/комментарий).
- Тесты: kip8-desktop **2546/0**, kip8 2518/0, kip8test 2512/0,
  kip8test-desktop 218/0. node --check main.js OK.
- Пуш: kip8-desktop + kip8 + kip8test + kip8test-desktop, затем тег
  legacy-v2.1.8 → релиз KIPiA-Setup-2.1.8-lts-win7-{ia32,x64}.exe +
  универсальный (prerelease). Выдача: download/task354/
  NOTICE-Task354-win7-legacy.md.

Осознанные НЕ-правки: kip8/electron/main.js не тронут (стейджинг-копия
устарела ещё до Task 129 — синк electron/ никогда не шёл); kip8test-desktop
без legacy-workflow (тестовый канал, пользователей Win7 нет); основная
сборка/канал latest.yml не менялись (на Win10+ всё как было).

Следующий номер задачи: 355.
