# kip8-desktop — КИПиА (ПРОДАКШН) Desktop

Десктопная **боевая** сборка приложения «КИПиА — справочник инженера» на Electron.

## Назначение

Этот репозиторий содержит десктопную (Electron) сборку боевого приложения.
Мобильная PWA-версия живёт в [`kip8`](https://github.com/bloknett-design/kip8).
Тестовая десктоп-сборка — в [`kip8test-desktop`](https://github.com/bloknett-design/kip8test-desktop).

## Связи с kip8

- `index.html` **синхронизируется автоматически** из `kip8` при каждом его обновлении
  (GitHub Action `sync-to-desktop.yml` в репозитории kip8, секрет `DESKTOP_SYNC_TOKEN`).
- `sw.js`, `scripts/*.gs`, `tests/` — синхронизируются автоматически из `kip8`
  (контентные файлы для полного паритета тестов: 1433 теста); в Electron-сборку
  НЕ попадают (files в package.json: index.html/images/data/electron).
- `data/`, `images/` — статические копии из kip8 для офлайн-fallback
  (обновляются вручную при переносе/релизе).
- Приложение при запуске грузит живую страницу https://bloknett-design.github.io/kip8/
  (fallback — локальные файлы `app://` при отсутствии сети).
- `cleanCacheOnStartup` очищает SW/кэш при каждом запуске — пользователю достаточно
  перезапустить приложение, чтобы получить свежий контент (без переустановки).
- `electron-updater` проверяет GitHub Releases этого репозитория и предлагает
  обновление при выходе новой версии. С версии 2.1.8 — две Windows-сборки
  (x64 и ia32/Win32), каждая обновляется своим файлом из общего `latest.yml`.

## Структура

```
kip8-desktop/
├── index.html              # Тот же код, что в kip8/index.html
│                           # (синхронизируется автоматически через GitHub Action)
├── electron/
│   └── main.js             # Точка входа Electron (REMOTE_APP_URL = kip8)
├── package.json            # electron-builder конфиг (appId com.bloknett.kipia)
├── package-lock.json
├── sw.js                  # Копия PWA service worker из kip8 (фикстура тестов;
│                           # в Electron-сборку НЕ попадает)
├── scripts/               # Apps Script .gs из kip8 (фикстуры тестов, 8 файлов;
│                           # сервер бэкенда НЕ задеплоен отсюда)
├── data/                   # Статическая копия данных (синхронизируется при релизе)
├── images/                 # Иконки, логотипы, иллюстрации
├── tests/                  # Тесты — байт-в-байт из kip8 (1433 passed / 0 failed)
└── .github/workflows/
    ├── build-desktop.yml   # Сборка под Windows/Linux/macOS + релиз на тег v*
    └── ci.yml              # Тесты при каждом пуше
```

## Сборка и релизы

При пуше тега `v*` (например `v2.1.8`) автоматически собираются:

- **Windows 64-бит** — `KIPiA-Setup-2.1.8-x64.exe` (NSIS-установщик, русский язык)
- **Windows 32-бит (Win32)** — `KIPiA-Setup-2.1.8-ia32.exe` — отдельная сборка
  для 32-битных систем (старые ПК/ноутбуки с 32-битной Windows); начиная с 2.1.8
- **Linux** — `KIPiA-2.1.8.AppImage` + `KIPiA-2.1.8.deb`
- **macOS** — `KIPiA-2.1.8.dmg`

Все артефакты публикуются в [Releases](https://github.com/bloknett-design/kip8-desktop/releases).

### Как выбрать установщик Windows

- Не знаете разрядность — почти все современные ПК **64-бит** → `KIPiA-Setup-*-x64.exe`.
- 32-битная Windows (Win32) → `KIPiA-Setup-*-ia32.exe`.
- Автообновление независимое: 64-битные и 32-битные установки проверяют один
  `latest.yml` и автоматически выбирают файл своей разрядности
  (`KIPiA-Setup-*-x64.exe` / `KIPiA-Setup-*-ia32.exe`).

## Когда нужен новый релиз (переустановка пользователями)

Только при изменениях в `electron/main.js` или `package.json` (логика окна,
автообновление, зависимости). Обновления `index.html` приходят пользователям
автоматически при перезапуске приложения — релиз не нужен.

## Локальный запуск

```bash
npm install
npm start        # electron .
```
