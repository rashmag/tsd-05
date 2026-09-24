# WMS ТСД

Android-приложение для терминала сбора данных. Кладовщик авторизуется, получает задачу и работает со штрихкодами: сборка заказа, приёмка, перемещение, инвентаризация, возвраты и отгрузка.

Релизы для установки на ТСД публикуются в [rashmag/tsd-05](https://github.com/rashmag/tsd-05). Release-сборка при старте проверяет последний релиз этого репозитория.

## Стек

- Clean Architecture: у фичи слои `data`, `domain`, `presentation`
- Kotlin, Coroutines, MVVM, LiveData
- Dagger Hilt
- Навигация: [Alligator](https://github.com/aartikov/Alligator)
- Сеть: Retrofit, OkHttp, Moshi
- minSdk 24, compileSdk 35

## Сборка

Нужны JDK 11 и Android SDK.

В `local.properties` рядом с `sdk.dir` должны быть:

```properties
GITHUB_TOKEN=
LOGIN_ANALYTICS=
PASSWORD_ANALYTICS=
```

`GITHUB_TOKEN` нужен приложению, чтобы скачать APK из GitHub Release. Токен в репозиторий не коммитится.

Сборка release APK для ТСД:

```bash
./gradlew assembleAppCenterRelease
```

Готовый файл: `app/build/outputs/apk/appCenter/release/wms-<version>-release.apk`.

Другие варианты: flavor `appCenter` или `googlePlay`, тип сборки `debug`, `stage`, `release`.

## Релиз на GitHub

Новый релиз создаётся в [rashmag/tsd-05](https://github.com/rashmag/tsd-05/releases). К релизу прикладывается APK.

Версия задаётся **tag**. Принудительность задаётся **Release title**, не tag.

| | Tag | Release title |
|---|---|---|
| Можно пропустить | `v5.4.9` | `v5.4.9` |
| Нельзя пропустить | `v5.4.9` | `v5.4.9 [force]` |

`[force]` можно поставить в любом месте заголовка, регистр не важен: `[force] v5.4.9` и `v5.4.9 [FORCE]` работают так же. В tag маркер не добавляется: по tag приложение сравнивает версии.

Без `[force]` на ТСД есть кнопки «Может позже» и «Не интересно». С `[force]` остаётся только «Обновиться сейчас», окно нельзя закрыть. Проверка обновления выполняется только в release-сборке.

## Фичи

- `auth` — авторизация
- `account` — личный кабинет, если у кладовщика нет задачи
- `task` — сборка заказа
- `unloading` — сканирование ячеек разгрузки перед завершением заказа
- `scanner` — штрихкод со сканера попадает в `BarcodeCache`, фичи читают его через `GetBarcodeUseCase`
- `pause` — пауза сборки с указанием причины
- `problem` — выбор типа проблемы по товару
- `connection` — состояние сети, логика в `PingTimer`
- `notification` — пуши о новом заказе и отмене
- `task_acceptance`, `task_acceptance_labeling` — приёмка и маркировка
- `task_moving`, `relocate_product` — перемещение
- `task_inventory` — инвентаризация
- `task_cancellation`, `task_return_goods` — отмена и возврат товара
- `refund_to_supplier` — возврат поставщику
- `task_transportation` — транспортировка и отгрузка
- `collection_salvage` — сбор утиля
- `rescan` — пересканирование

Логика основных классов описана в самих классах. Порядок этапов сборки лучше сначала разобрать с продакт-менеджером.
