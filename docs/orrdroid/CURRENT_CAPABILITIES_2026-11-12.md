# Проверка возможностей клиентов от 12 ноября 2026

Проверены опубликованные релизы и исходники. Приложения на устройствах и в сети пользователя в этой работе не запускались. «Подтверждено» ниже означает наличие реализации или документированной возможности. Это не результат сравнительного теста стабильности.

| Проект | Проверенная версия | Зафиксированный commit |
|---|---|---|
| [Keqdroid](https://github.com/Lemonochka/keqdroid/releases/tag/v0.36.3) | v0.36.3 | `7a334bcd040e36e8506970958659990b1cc260c1` |
| [Karing](https://github.com/KaringX/karing/releases/tag/v1.2.26.2929) | v1.2.26.2929 | `94ca093898289bcdbb485c2c2651e67989e07ccf` |
| [Farvater](https://github.com/farvater-app/farvater/releases/tag/v1.0.31) | v1.0.31 | `50636ce4afd93b72281e6bedd0f4d3e4ecf170a3` |

## Сравнение по основным функциям

| Функция | Keqdroid | Karing | Farvater | Вывод для Orrdroid |
|---|---|---|---|---|
| Ядро | Xray и mihomo [K1] | Модифицированный sing-box [C1] | Xray [F1] | Сохранить два адаптера Keqdroid |
| Пользовательский URL проверки | HTTPS-пресет и свой URL [K2] | Пользовательские URL, таймаут и параллелизм [C2] | Фиксированные цели детектора режима [F2] | Сохранить настройку Keqdroid |
| Проверка передачи данных | Есть отдельная проверка скорости. В быстрой Android URL-пробе ошибка чтения тела подавляется [K3] | Наличие URLTest и его настроек подтверждено. Эквивалентная валидация тела в поставляемом ядре здесь не установлена [C2] | Детектор ЧС требует 2xx и минимум 32 КиБ [F2] | Разделить быстрый ответ и подтверждение передачи |
| Автовыбор | История, разброс, отказы, выбор внутри подписки и резерв соседних подписок [K4] | Пользовательские URLTest-группы с узлами включённых источников [C3] | Выбор и восстановление с учётом режима и сохранённых узлов [F3] | Общий пул GlobalAuto поверх имеющегося контроллера |
| БС/ЧС | В модели маршрута нет условия режима [K5] | Эквивалент детектора Farvater в проверенных материалах не установлен | Авто/ручной режим, сохранение последнего известного режима, фильтр источников [F2–F4] | Перенести полный сценарий Farvater и интегрировать с маршрутами |
| Цепочки | Статические цепочки. Адаптер отдельной цели штатного формата цепочки уже допускает mihomo [K6] | Пользовательская URLTest-группа имеет preProxy [C3] | Эквивалент редактора произвольных цепочек в этом исследовании не установлен | Явный выбор сохранённой цепочки в «Куда» на первом этапе |
| Правила и списки | DIRECT/BLOCK/текущий/отдельный сервер, URL/hosts/AdBlock, ru-whitelist и Re:filter [K1, K5, K7] | Группы правил и узлов, GeoIP/GeoSite/наборы правил [C1] | Есть DIRECT_DOMAINS, а БС/ЧС также маркирует источники узлов [F4, F5] | Источник узлов и список назначений хранить раздельно |
| Страна фактического выхода | Cloudflare trace через локальный proxy, поля ip/loc [K8] | Полный эквивалент контракта проверки конкретной цепочки здесь не проверялся | Полный эквивалент не проверялся | Расширить Keqdroid до проверки назначенного пути |
| LAN-прокси | SOCKS5/HTTP, логин/пароль, копирование IP:port. Пустые реквизиты допускают анонимный LAN-доступ [K9] | В этой проверке не оценивался | В этой проверке не оценивался | Обязательные реквизиты в UI и генераторах, URI/QR |
| Backup и синхронизация | Backup/restore [K1] | ZIP, LAN, WebDAV, iCloud на поддерживаемых платформах [C1] | Для этой задачи не оценивались | На первом этапе расширить backup Keqdroid. Межустройственную синхронизацию оставить дальше |
| AWG и Tailnet | AWG через mihomo [K1] | В настройках есть Tailscale Auth key [C4] | Для этой задачи не оценивались | AWG переиспользовать. Встроенный Tailnet требует отдельной работы |

Пробел в сравнении означает границу проведённой проверки. По нему нельзя заключать, что у клиента отсутствует возможность.

## Уточнения, влияющие на план

1. **Новая база.** После указанной пользователем v0.36.2 вышла v0.36.3. В ней исправлена передача пароля работающего локального proxy запросам самого приложения, в том числе загрузке обновлений и подписок. В diff также есть выделение Android-логики для JVM/Robolectric-тестов и запуск этих тестов в CI. Это сохраняется при импорте. [Релиз](https://github.com/Lemonochka/keqdroid/releases/tag/v0.36.3), [сравнение](https://github.com/Lemonochka/keqdroid/compare/v0.36.2...v0.36.3).
2. **Цепочка уже проходит проверку адаптера mihomo.** Расширение касается явного выбора в редакторе, сохранения ссылки, диагностики и поведения при отказе. Xray поддерживает совместимые статические цепочки как основной выход, но отдельная цель в штатном формате цепочки ограничена mihomo. Импортированный Xray JSON имеет отдельный путь обработки. Нельзя обобщать это до «Xray не поддерживает цепочки». [K6]
3. **Отсутствующая цель сейчас подменяется VPN.** Комментарий и логика `routeServerOutbounds` описывают уход через текущий VPN при пропавшем/неподдерживаемом выходе. Для правила с обязательной страной или цепочкой Orrdroid должен применять только явно допустимый резерв либо BLOCK. [K6]
4. **Два разных смысла географических списков.** «Заблокировано для РФ» предназначено для сервисов, ограничивающих доступ из РФ/с российских IP. `ru-available-only-inside` означает доступность только внутри РФ и требует противоположного выхода. Пользовательский [список](sources/russia-geoblocked-services.txt) содержит также ограничения функций и аккаунтов. Из него нужно получить проверенные домены/IP с областью действия, датой и источником, а не загружать весь справочный текст как готовый ruleset.
5. **Farvater переносится как сценарий.** Ручной выбор имеет приоритет, неизвестный результат сохраняет последний известный режим, при чистой установке допускаются все разрешённые источники. Проверки выполняются без proxy. В Orrdroid требуется привязать их к физической сети и исключить влияние собственного VPN. Таймеры смены режима и восстановление узла согласуются с существующим Android-сервисом. [F2–F4]
6. **Фоновая работа требует приёмки.** У Farvater в service loop при выключенном экране есть ожидание screenOn. Это нельзя выдавать за готовый непрерывный фоновый контроль Orrdroid. Сохранить сценарий определения режима и отдельно проверить мониторинг Android/Doze. [F3]
7. **Karing — пример модели групп.** Экран пользовательской URLTest-группы позволяет выбирать узлы из включённых источников и задавать preProxy. Это ориентир для последующего этапа независимых Auto-групп и динамических цепочек. Перенос в Xray/mihomo требует собственных адаптеров. В релизе 2929 исправлена конвертация VLESS uTLS-REALITY, поэтому старые backup-замеры не характеризуют текущую версию. [C3, релиз Karing](https://github.com/KaringX/karing/releases/tag/v1.2.26.2929)

Дополнительная проверка Auto: `AutoServerSelect.eligible()` возвращает всю подписку, если все её узлы имеют `autoSkip`. Поэтому строгое соблюдение исключений в GlobalAuto требует изменения этой семантики и явной миграции, а не только объединения списков кандидатов. [K4]

## Первичные источники

- **K1** — [README Keqdroid v0.36.3](https://github.com/Lemonochka/keqdroid/blob/v0.36.3/README.ru.md).
- **K2** — [ping_test_config.dart](https://github.com/Lemonochka/keqdroid/blob/v0.36.3/lib/models/ping_test_config.dart), [пакетные проверки](https://github.com/Lemonochka/keqdroid/blob/v0.36.3/lib/providers/servers_provider.dart).
- **K3** — [EphemeralXrayPing.kt](https://github.com/Lemonochka/keqdroid/blob/v0.36.3/android/app/src/main/kotlin/com/keqdroid/keqdroid/EphemeralXrayPing.kt), функции `httpProbeViaSocks` и проверки скорости. Несмотря на имя файла, быстрый SOCKS-тест используется и с mihomo.
- **K4** — [auto_server_select.dart](https://github.com/Lemonochka/keqdroid/blob/v0.36.3/lib/services/auto_server_select.dart).
- **K5** — [traffic_route.dart](https://github.com/Lemonochka/keqdroid/blob/v0.36.3/lib/models/traffic_route.dart).
- **K6** — [route_servers.dart](https://github.com/Lemonochka/keqdroid/blob/v0.36.3/lib/utils/route_servers.dart), [генератор Xray](https://github.com/Lemonochka/keqdroid/blob/v0.36.3/lib/utils/config_gen.dart), [генератор mihomo](https://github.com/Lemonochka/keqdroid/blob/v0.36.3/lib/utils/mihomo_config_gen.dart).
- **K7** — [routing_presets.dart](https://github.com/Lemonochka/keqdroid/blob/v0.36.3/lib/utils/routing_presets.dart).
- **K8** — [exit_ip_service.dart](https://github.com/Lemonochka/keqdroid/blob/v0.36.3/lib/services/exit_ip_service.dart).
- **K9** — [LAN UI](https://github.com/Lemonochka/keqdroid/blob/v0.36.3/lib/screens/settings/lan_sharing.dart) и генераторы K6. Авторизация внутреннего proxy и реквизиты LAN — разные настройки.
- **C1** — [README Karing v1.2.26.2929](https://github.com/KaringX/karing/blob/v1.2.26.2929/README.md).
- **C2** — [URLTest settings](https://github.com/KaringX/karing/blob/v1.2.26.2929/lib/screens/urltest_settings_screen.dart), [setting_manager.dart](https://github.com/KaringX/karing/blob/v1.2.26.2929/lib/app/modules/setting_manager.dart).
- **C3** — [urltest_group_custom_screen.dart](https://github.com/KaringX/karing/blob/v1.2.26.2929/lib/screens/urltest_group_custom_screen.dart).
- **C4** — [SettingConfigItemUITailscale](https://github.com/KaringX/karing/blob/v1.2.26.2929/lib/app/modules/setting_manager.dart). Auth key регистрирует устройство и сам по себе не реализует синхронизацию настроек.
- **F1** — [релиз Farvater](https://github.com/farvater-app/farvater/releases/tag/v1.0.31).
- **F2** — [NetModeDetector.kt](https://github.com/farvater-app/farvater/blob/v1.0.31/app/src/main/java/app/farvater/engine/NetModeDetector.kt).
- **F3** — [MainViewModel.kt](https://github.com/farvater-app/farvater/blob/v1.0.31/app/src/main/java/app/farvater/ui/MainViewModel.kt), [FarvaterVpnService.kt](https://github.com/farvater-app/farvater/blob/v1.0.31/app/src/main/java/app/farvater/vpn/FarvaterVpnService.kt), [ModeSwitch.kt](https://github.com/farvater-app/farvater/blob/v1.0.31/app/src/main/java/app/farvater/engine/ModeSwitch.kt).
- **F4** — [NetMode.kt](https://github.com/farvater-app/farvater/blob/v1.0.31/app/src/main/java/app/farvater/core/model/NetMode.kt).
- **F5** — [XrayConfigBuilder.kt](https://github.com/farvater-app/farvater/blob/v1.0.31/app/src/main/java/app/farvater/core/xray/XrayConfigBuilder.kt).
