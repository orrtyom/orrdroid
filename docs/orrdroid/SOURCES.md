# Источники и границы выводов

Основа: техническое задание пользователя, уточнения в текущем чате и чтение открытых исходников. Ни один проект не был установлен или испытан на устройстве пользователя при подготовке плана. Оценки трудозатрат, пороги и архитектура Orrdroid — предложения, а не свойства существующих приложений.

## Актуализация от 12 ноября 2026

Для редакции 0.5 проверены Keqdroid v0.36.3, Karing v1.2.26.2929 и Farvater v1.0.31. Их SHA, сравнение и ссылки на конкретные файлы собраны в [CURRENT_CAPABILITIES_2026-11-12.md](CURRENT_CAPABILITIES_2026-11-12.md). Выбранная база импорта Keqdroid — `7a334bcd040e36e8506970958659990b1cc260c1`.

Предыдущие исследования ниже сохранены как история. Ссылки v0.36.0/v0.36.1 фиксируют состояние на момент наблюдения. Их нельзя читать как утверждение о текущем релизе. Исторические backup/issue-замеры не заменяют испытания актуальных версий.

## Keqdroid — исходное исследование v0.36.0/v0.36.1

- **S1.** [Релиз v0.36.0](https://github.com/Lemonochka/keqdroid/releases/tag/v0.36.0), commit `f8865fb9144f570931f477c15b57e30928882205`. Источник сведений об уже заявленных изменениях безопасности, DNS и правил.
- **S2.** [auto_server_select.dart](https://github.com/Lemonochka/keqdroid/blob/v0.36.0/lib/services/auto_server_select.dart) и [auto_select_watchdog.dart](https://github.com/Lemonochka/keqdroid/blob/v0.36.0/lib/services/auto_select_watchdog.dart). Область подписки, соседи, история, удержание и ограничения переходов.
- **S3.** [EphemeralXrayPing.kt](https://github.com/Lemonochka/keqdroid/blob/v0.36.0/android/app/src/main/kotlin/com/keqdroid/keqdroid/EphemeralXrayPing.kt). GET, время до заголовков, дочитывание в runCatching и отдельная download-проба.
- **S4.** [traffic_routes.dart](https://github.com/Lemonochka/keqdroid/blob/v0.36.0/lib/utils/traffic_routes.dart). Порядок разделов/целей маршрутизации.
- **S5.** [proxy_chain.dart](https://github.com/Lemonochka/keqdroid/blob/v0.36.0/lib/utils/proxy_chain.dart). Уже существующая модель статической цепочки, ссылки на серверы и ограничение длины.
- **S6.** [rule_lists.dart](https://github.com/Lemonochka/keqdroid/blob/v0.36.0/lib/utils/rule_lists.dart), [rule_lists_provider.dart](https://github.com/Lemonochka/keqdroid/blob/v0.36.0/lib/providers/rule_lists_provider.dart), [KeqdisVpnService.kt](https://github.com/Lemonochka/keqdroid/blob/v0.36.0/android/app/src/main/kotlin/com/keqdroid/keqdroid/KeqdisVpnService.kt). Парсинг списков, refresh и Android lifecycle.
- **S15.** [vpn_core_support.dart](https://github.com/Lemonochka/keqdroid/blob/v0.36.0/lib/utils/vpn_core_support.dart) и [AWG в fixture цепочки](https://github.com/Lemonochka/keqdroid/blob/v0.36.0/test/fixtures/mihomo_gen/chain-reality-awg-hy2.json). AWG относится к mihomo; цепочка требует общего поддерживаемого ядра. Fixture не заменяет сетевой тест реального peer.

## Ограничения Android и Tailnet

- **S7.** [Android VPN guide](https://developer.android.com/develop/connectivity/vpn) — один активный VPN-сервис на пользователя/профиль. [Tailscale: other VPNs](https://tailscale.com/docs/reference/faq/other-vpns) — ограничения совместной работы.
- **S8.** [ConnectivityManager.getConnectionOwnerUid](https://developer.android.com/reference/android/net/ConnectivityManager#getConnectionOwnerUid(int,java.net.InetSocketAddress,java.net.InetSocketAddress)) — API 29+, TCP/UDP, ограничения видимости и INVALID_UID.
- **S13.** [tsnet исходники](https://github.com/tailscale/tailscale/blob/main/tsnet/tsnet.go) — встроенный userspace TCP/IP и Dial/Listen. [Kernel vs netstack](https://tailscale.com/docs/reference/kernel-vs-userspace-routers) — контекст архитектуры. Варианты userspace не означают автоматического паритета с полным мобильным VPN.
- **S14.** [AmneziaWG documentation](https://docs.amnezia.org/documentation/amnezia-wg/) — развитие версий обфускации. Поддержку каждой версии в Keqdroid/mihomo проверять отдельно.

## RKNHardering

- **S9.** [README](https://github.com/xtclovver/RKNHardering/blob/main/README.md) — каталог проверок и различение проверенных/сообщённых/заявленных сведений.
- **S10.** [Локальные сигналы](https://github.com/xtclovver/RKNHardering/blob/main/docs/help/ru/anti-detection/local-signals.md).
- **S11.** [Профили](https://github.com/xtclovver/RKNHardering/blob/main/docs/help/ru/anti-detection/profiles.md).
- **S12.** [Сетевой путь](https://github.com/xtclovver/RKNHardering/blob/main/docs/help/ru/anti-detection/network-path.md).

Эти материалы описывают признаки обнаружения, включая системные свойства, которые обычное VPN-приложение не может гарантированно скрыть. План не трактует каждый признак как уязвимость и не обещает прохождения всех детекторов.

## Готовая интеграция Tailscale + AWG

- **S16.** [LiuTangLei/tailscale-awg-installer](https://github.com/LiuTangLei/tailscale-awg-installer) — установщики и актуальное описание режимов; [manual install](https://github.com/LiuTangLei/tailscale-awg-installer/blob/main/doc/manual-install.md).
- Реальное ядро: [релиз tailscale v1.104.4](https://github.com/LiuTangLei/tailscale/releases/tag/v1.104.4), дерево `f58b730c8c1eb30c00127e6a2ebe4fa0540d29f1`; [go.mod](https://github.com/LiuTangLei/tailscale/blob/v1.104.4/go.mod), [README-AMNEZIA](https://github.com/LiuTangLei/tailscale/blob/v1.104.4/README-AMNEZIA.md), [compatibility check](https://github.com/LiuTangLei/tailscale/blob/v1.104.4/docs/awg-compatibility-check-20260906.md).
- Мобильная оболочка: [Android v1.104.4](https://github.com/LiuTangLei/tailscale-android/releases/tag/v1.104.4), дерево `8520521009454ce6e95aa186d2502409e21a46ab`.

README-AMNEZIA внутри тега содержит устаревшую фразу о подготовке 1.102.6, тогда как опубликован релиз 1.104.4. Поэтому фиксировать код по тегу/commit, а заявления проверять испытаниями. Приведённый автором compatibility check имеет ограниченную область: не доказывает совместимость со всеми внешними реализациями. В `main` при чтении не было соответствующего набора AWG-файлов; использовать release tree.

## Karing и Farvater

- **S17.** Karing [setting_manager.dart](https://github.com/KaringX/karing/blob/v1.2.26.2925/lib/app/modules/setting_manager.dart): `SettingConfigItemUITailscale`, поле `auth_key`; [settings_screen.dart](https://github.com/KaringX/karing/blob/v1.2.26.2925/lib/screens/settings_screen.dart): ввод ключа; [my_profiles_screen.dart](https://github.com/KaringX/karing/blob/v1.2.26.2925/lib/screens/my_profiles_screen.dart): `SingboxOutboundTailscaleOptions`. Подтверждено наличие настройки/типа, но полный путь исполнения не проверялся сборкой в этом исследовании.
- Farvater [репозиторий](https://github.com/farvater-app/farvater) и [релиз v1.0.31](https://github.com/farvater-app/farvater/releases/tag/v1.0.31): идеи определения сети и поиска с реальной передачей данных изучались ранее в этом чате. План предлагает собственные критерии, не объявляет перенос готовым.

Конкретные исходники Farvater: `NetModeDetector.kt`, `Pathfinder.kt`, `NodeTester.kt` в `app/src/main/java/app/farvater/engine/`. Для переноса кода потребуется повторная фиксация версии и проверка лицензии. Аналогично интерфейс Karing не означает, что sing-box-компонент можно напрямую вставить в Xray/mihomo.


## Дополнение 0.2: изоляция TUN

Получен материал пользователя «Изоляция VPN на Android: CalyxOS, GrapheneOS и утечка через tun0» от 17.10.2026. Проверенные первичные источники, существующие патчи Keqdroid и границы выводов собраны в [SECURITY_ISOLATION.md](SECURITY_ISOLATION.md). Выводы о полноте защиты остаются задачей испытаний; исходный скан с адресами не публикуется.


## Дополнение 0.3: Keqdroid v0.36.1

**S18.** [Релиз v0.36.1](https://github.com/Lemonochka/keqdroid/releases/tag/v0.36.1), commit `eca4c926f096731fec3f6746c15625c53b1c6573`, опубликован 11.11.2026 в 04:41 МСК. [Сравнение с v0.36.0](https://github.com/Lemonochka/keqdroid/compare/v0.36.0...v0.36.1): 6 коммитов, 45 файлов. Изменения: ручной порядок маршрутов всех приложений, соответствующая генерация правил/DNS; установщик Windows и его обновление/удаление; права Linux-пакетов.

Проверены diff `traffic_route.dart`, `routes_provider.dart`, `traffic_routes.dart`, `app_routes.dart`, `route_servers.dart`, генераторов Xray/mihomo, `xray_core_settings.dart` и `package_linux.sh`. Механизмы Auto/пинга и owner-патчи в сравнении не меняются. Ссылки на v0.36.0 выше сохраняются как след исходного аудита, а не указание старой базы для импорта. Сетевые и установочные тесты новой версии здесь не запускались.
