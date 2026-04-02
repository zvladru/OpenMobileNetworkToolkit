# Codex и GitHub: подготовка окружения для Android-сборки

Этот документ закрывает проблему вида:

```
SDK location not found. Define a valid SDK location with an ANDROID_HOME environment variable
or by setting the sdk.dir path in local.properties
```

## 1) Что нужно для локального запуска в Codex

### Обязательные компоненты
- JDK 17
- Android SDK Command-line tools
- Android Platform + Build-tools нужной версии
- Файл `local.properties` в корне проекта

### Быстрая настройка
1. Скопируйте шаблон:
   ```bash
   cp local.properties.example local.properties
   ```
2. Укажите путь к SDK в `local.properties`:
   ```properties
   sdk.dir=/absolute/path/to/Android/Sdk
   ```
3. Если используете переменные окружения, задайте одну из них:
   - `ANDROID_SDK_ROOT`
   - `ANDROID_HOME`

> `local.properties` не нужно коммитить в репозиторий.

### Проверка окружения
```bash
echo "$ANDROID_SDK_ROOT"
echo "$ANDROID_HOME"
cat local.properties
./gradlew :app:compileDebugJavaWithJavac
```

## 2) Что нужно для GitHub Actions

В workflow важно:
1. Явно установить Android SDK (`android-actions/setup-android`).
2. Установить требуемые пакеты через `sdkmanager`.
3. Принять лицензии SDK.
4. Только после этого запускать Gradle-задачи.

В репозитории это уже учтено в `.github/workflows/android.yml`.

## 3) Секреты GitHub (для release-подписи)

Если собираете release APK в CI, должны быть заданы:
- `KEYSTORE`
- `STOREPASSWORD`
- `KEYALIAS`
- `KEYPASSWORD`

Без них release-часть workflow может падать.

## 4) Рекомендуемая диагностика при падении CI

1. Проверить шаг `Set up Android SDK`.
2. Проверить шаг установки пакетов SDK (`sdkmanager`).
3. Проверить `./gradlew --version` и версию Java (должна быть 17).
4. Проверить, что проблема не в keystore-secrets, если падает только release.
