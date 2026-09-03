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
  обновление при выходе новой версии.

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

При пуше тега `v*` (например `v2.1.7`) автоматически собираются:

- **Windows** — `KIPiA-Setup-2.1.7.exe` (NSIS-установщик, русский язык)
- **Linux** — `KIPiA-2.1.7.AppImage` + `KIPiA-2.1.7.deb`
- **macOS** — `KIPiA-2.1.7.dmg`

Все артефакты публикуются в [Releases](https://github.com/bloknett-design/kip8-desktop/releases).

## Когда нужен новый релиз (переустановка пользователями)

Только при изменениях в `electron/main.js` или `package.json` (логика окна,
автообновление, зависимости). Обновления `index.html` приходят пользователям
автоматически при перезапуске приложения — релиз не нужен.

## Локальный запуск

```bash
npm install
npm start        # electron .
```
