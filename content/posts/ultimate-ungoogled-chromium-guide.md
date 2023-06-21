+++
title = "!Ультимативный Ungoogled Chromium гайд для Arch Linux"
date = 2023-03-12T01:07:49+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Это тот же chromium но с полностью выпиленными гугл сервисами

[Флаги](/posts/google-chrome-and-chromium-on-archlinux/#%D0%BD%D0%B5%D0%BE%D0%B1%D1%85%D0%BE%D0%B4%D0%B8%D0%BC%D1%8B%D0%B5-%D0%B8-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D1%84%D0%BB%D0%B0%D0%B3%D0%B8) применяются как и в обычном chromium'е, расположенном `~/.config/chromium-flags.conf`

## Установка

```sh
yay -S ungoogled-chromium-bin
```

(Альтернатива) Обновляемое офф репо специально для Arch

* https://github.com/ungoogled-software/ungoogled-chromium-archlinux

```sh
curl -s 'https://download.opensuse.org/repositories/home:/ungoogled_chromium/Arch/x86_64/home_ungoogled_chromium_Arch.key' | sudo pacman-key -a -
echo '
[home_ungoogled_chromium_Arch]
SigLevel = Required TrustAll
Server = https://download.opensuse.org/repositories/home:/ungoogled_chromium/Arch/$arch' | sudo tee --append /etc/pacman.conf
sudo pacman -Sy
```

### Настройки браузера

<span style="color:green">Описание:</span>: **Всегда использовать безопасные соединения HTTPS и DNS Cloudflare**

Настройки - Конфиденциальность и безопасность - Безопасность. Включаем "Всегда использовать безопасные соединения"

Тут же включаем Использовать безопасный DNS-сервер. В списке выбираем Cloudflare (1.1.1.1)

<span style="color:green">Описание:</span> **Блокировать сторонние файлы cookie**

Настройки - Конфиденциальность и безопасность - Файлы cookie и других данных сайтов. Включаем "Блокировать сторонние файлы cookie"

> Примечание: Если вы качаете что-либо с Google Drive необходимо добавить URL в "Сайты, которые всегда могут использовать файлы cookie"

![](/images/ultimate-ungoogled-chromium-guide/google-drive-cookie.png)

<span style="color:green">Описание:</span>: **Не отслеживать наши данные веб сайтам**

Включаем "Отправлять Do Not Track в запросах веб-страниц"

<span style="color:green">Описание:</span>: **Запрещаем отслеживание геолокации для сайтов**

Настройки - Конфиденциальность и безопасность - Настройки сайтов - секция "Разрешения" - Геоданные. Ставим "Запретить сайтам доступ к местоположению"

### Добавление магазина расширений

В этом нам поможет [chromium-web-store](https://github.com/NeverDecaf/chromium-web-store)

Добавляем в `~/.config/chromium-flags.conf`
```sh
--extension-mime-request-handling=always-prompt-for-install
```

Перезагружаем браузер

Переходим в **chrome://extensions** и включаем режим разработчика

[Скачиваем](https://github.com/NeverDecaf/chromium-web-store/releases/latest) Chromium.Web.Store.crx вас сразу предложат установить магазин, соглашаемся с установкой

![](/images/ultimate-ungoogled-chromium-guide/chromium-web-store.png)

Теперь можно закачивать расширения из магазина и обновлять их этим расширением

### !Расширения

* [KeePassXC-Browser](https://chrome.google.com/webstore/detail/keepassxc-browser/oboonakemofpalcgghocfoadofidjkkk?hl=ru) (Прекрасно работает с данным браузером)
* ([Удалённый](/posts/google-chrome-and-chromium-on-archlinux/#%D1%83%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0-%D1%83%D0%B4%D0%B0%D0%BB%D1%91%D0%BD%D0%BD%D1%8B%D1%85-%D1%80%D0%B0%D1%81%D1%88%D0%B8%D1%80%D0%B5%D0%BD%D0%B8%D0%B9)) [TMS - Поиск торрентов](https://github.com/Feverqwe/tSearch)
* (Удалённый) [cookies.txt](https://github.com/daftano/cookies.txt) - Экспортирует куки Chrome (закинуть только src)
* (Выключен) [Vimium](https://chrome.google.com/webstore/detail/vimium/dbepggeogbaibhgnhhndojpepiihcmeb) - браузить веб страницы vim клавишами
* [Return YouTube Dislike](https://chrome.google.com/webstore/detail/return-youtube-dislike/gebbhagfogifgggkldgodflihgfeippi)
* [Ublock Origin](https://chrome.google.com/webstore/detail/ublock-origin/cjpalhdlnbpafiamejdnhcphjbkeiagm) + [Доп пользовательские фильтры](https://filterlists.com/)
1. Настройки uBo:
	* Я - опытный пользователь - Вкл
2. Вкл доп фильтры:
	* (встроенные) uBlock filters - 6шт
	* Easy List
	* Easy List Cookie
	* EasyPrivacy
	* Block Outsider Intrusion into LAN
	* Online Malicious URL Blocklist
	* uBlock filters – Annoyances
	* Peter Lowe’s Ad and tracking server list
	* EasyList Cookie
	* RUS: RU AdList
	* (через импорт) [RU AdList: Counters](https://easylist-downloads.adblockplus.org/cntblock.txt) (достаточно его одного)
	* (через импорт) [RU AdList: BitBlock](https://easylist-downloads.adblockplus.org/bitblock.txt)
	* [Web Annoyances Ultralist](https://github.com/yourduskquibbles/webannoyances)
	* [ClearURLs for uBo](https://filterlists.com/lists/clearurls-for-ublock-origin)
	* [uBlock-Origin-dev-filter – Google – Dev](https://github.com/quenhus/uBlock-Origin-dev-filter)
3. Экспортирован backup настроек
* [Dark Reader](https://chrome.google.com/webstore/detail/dark-reader/eimadpbcbfnmbkopoojfekhnkhdbieeh)
* [S3.Переводчик](https://chrome.google.com/webstore/detail/s3translator/debnnjfbneojbmioajinefnflopdohjk)
* [Steam Inventory Helper](https://chrome.google.com/webstore/detail/steam-inventory-helper/cmeakgjggjdlcpncigglobpjbkabhmjl?hl=ru)
* [enhanced-h264ify](https://chrome.google.com/webstore/detail/enhanced-h264ify/omkfmpieigblcllmkgbflkikinpkodlk) - Для аппаратного кодирования
* [FrankerFaceZ](https://chrome.google.com/webstore/detail/frankerfacez/fadndhdgpmmaapbmfcknlfgcflmmmieb)
1. Включённые дополнения:
	* 7TV Emotes
	* BetterTTV Emotes
	* Chat Repetition Detector
	* First Message Highlight
* [SponsorBlock](https://chrome.google.com/webstore/detail/sponsorblock-for-youtube/mnjggcdmjocbbbhaepdhchncahnbgone)
	* Самореклама/рекомендация - Показывать в полосе прокрутки
	* Напоминание о взаимодействии (подписка) - Авто пропуск
	* Важное - Показывать в полосе прокрутки
	* Пауза/интро - Авто пропуск
	* Конечная заставка/титры - Авто пропуск
	* Предпросмотр/краткое содержание - Показывать в полосе прокрутки
	* Музыка: Сегмент без музыки - Показывать в полосе прокрутки
* [SteamDB](https://chrome.google.com/webstore/detail/steamdb/kdbmhfkmnlmbkgbabkdealhhbfhlmmon)
* [Обход блокировок Рунета](https://chrome.google.com/webstore/detail/%D0%BE%D0%B1%D1%85%D0%BE%D0%B4-%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BE%D0%BA-%D1%80%D1%83%D0%BD%D0%B5%D1%82%D0%B0/npgcnondjocldhldegnakemclmfkngch) [+Tor прокси .onion доменов](/posts/tor-archlinux/#%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D0%B0-tor-%D0%B2%D0%BC%D0%B5%D1%81%D1%82%D0%B5-%D1%81-%D0%BE%D0%B1%D1%85%D0%BE%D0%B4-%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BE%D0%BA-%D1%80%D1%83%D0%BD%D0%B5%D1%82%D0%B0-%D0%BF%D1%80%D0%BE%D0%BA%D1%81%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D1%82%D1%8C-%D0%B4%D0%BE%D0%BC%D0%B5%D0%BD%D1%8B-onion)
* [Cute Save Button](https://chrome.google.com/webstore/detail/cute-save-button/foblnmhpgiilabdcbnfgoheplajhompg) - Удобная кнопка скачивания изображения
* [ff2mpv](https://chrome.google.com/webstore/detail/ff2mpv/ephjcajbkgplkjmelpglennepbpmdpjg) ([инструкция по установке](/posts/mpv-and-setup/#ff2mpv))
* [Cookie AutoDelete](https://chrome.google.com/webstore/detail/cookie-autodelete/fhcgjolkccmbidfldomjliifgaodjagh)
* [Proxy SwitchyOmega](https://chrome.google.com/webstore/detail/proxy-switchyomega/padekgcemlokbadohgkifijomclgjgif) с настроенными [.onion](/posts/tor-archlinux/#proxy-switchyomega--tor) и [.i2p](/posts/i2p-archlinux/)
* [Violentmonkey](https://chrome.google.com/webstore/detail/violentmonkey/jinjaccalgkegednnccohejagnlnfdag) (<span style="color:green">Open-Source</span>) или <span style="color:red">Tampermonkey</span> (Проприетарный)
1. Скрипты:
	* [Dollchan Extension Tools](https://github.com/SthephanShinkufag/Dollchan-Extension-Tools) - Расширяет функционал 4chan/2ch
	* [EmuParadise Download Workaround - 1.1.1](https://gist.github.com/Eptun/3fdcc84552e75e452731cd4621c535e9)
	* [Rarbg Enhancer](https://github.com/FarisHijazi/Rarbg-Enhancer-UserScript)
	* [Endless Google [fork]](https://greasyfork.org/en/scripts/41041-endless-google-fork)
	* [Better Google](https://greasyfork.org/en/scripts/395257-better-google)
	* (Для Firefox) [Steam-Economy-Enhancer](https://github.com/Nuklon/Steam-Economy-Enhancer)
	* [@kawai-scripts/soundcloud-downloader](https://openuserjs.org/scripts/KawaiZombi/kawai-scriptssoundcloud-downloader)
	* [Recaptcha Solver (Automatically solves Recaptcha in browser)](https://gist.github.com/origamiofficial/2557dd47fb0aaf08e3c298a236bfa14d)
	* [Direct links out](https://openuserjs.org/scripts/nokeya/Direct_links_out)
	* [Remove web limits(modified)](https://greasyfork.org/en/scripts/28497-%E7%BD%91%E9%A1%B5%E9%99%90%E5%88%B6%E8%A7%A3%E9%99%A4-%E6%94%B9)
	* [Simple YouTube Age Restriction Bypass](https://github.com/zerodytrash/Simple-YouTube-Age-Restriction-Bypass/blob/main/dist/Simple-YouTube-Age-Restriction-Bypass.user.js)
	* [YouTube Livechat GoToChannel](https://github.com/zerodytrash/YouTube-Livechat-GoToChannel)
	* [voice-over-translation](https://github.com/ilyhalight/voice-over-translation) ([для тех кто юзает violentmonkey](https://raw.githubusercontent.com/ilyhalight/voice-over-translation/master/vot-cloudflare.user.js))
	* (Может не работать) [Google Images direct links 2](https://greasyfork.org/ru/scripts/398802-google-images-direct-links-2) - Быстрое перенаправление на картинку
	* [Back2source](https://github.com/vladgba/Back2source) (или https://github.com/quenhus/uBlock-Origin-dev-filter) - решение по вырезанию машинных переводов из поисковика (stackoverflow и т.д) как на пикче
	![](/images/ultimate-ungoogled-chromium-guide/back2source.png)
	* [Privacy Redirector](https://greasyfork.org/en/scripts/436359-privacy-redirector) - перенаправляет на свободные ресурсы уважающие приватность данных
	* [RU AdList JS Fixes](https://greasyfork.org/ru/scripts/19993-ru-adlist-js-fixes) -  (Использовать вместе с uBlock Origin со списком фильтров RU AdList) блокирует рекламу Яндекса, Live Journal и т.д. Вместо этого скрипта можно использовать [RU AdList: Counters](https://easylist-downloads.adblockplus.org/cntblock.txt) и [RU AdList: BitBlock](https://easylist-downloads.adblockplus.org/bitblock.txt) через импорт uBlockOrigin, также и наоборот

* [User-Agent Switcher and Manager](https://chrome.google.com/webstore/detail/user-agent-switcher-and-m/bhchdcejhohfmigjafbampogmaanbfkg)
* [Link Gopher - Extract links](https://chrome.google.com/webstore/detail/link-gopher/bpjdkodgnbfalgghnbeggfbfjpcfamkf)
* (Выключено) [Bookmark Dupes](https://chrome.google.com/webstore/detail/bookmark-dupes/ombpkjoelcapenbepmgifadkgpokfgfd?hl=ru) - поиск дубликатов закладок
* (Выключено) [Redirector](https://chrome.google.com/webstore/detail/redirector/ocgpenflpmgnfapjedencafcfakcekcd) - Пользовательские правила перенаправления
* [Кэшбэк-сервис "Мегабонус"](https://chrome.google.com/webstore/detail/%D0%BA%D1%8D%D1%88%D0%B1%D1%8D%D0%BA-%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81-%D0%BC%D0%B5%D0%B3%D0%B0%D0%B1%D0%BE%D0%BD%D1%83%D1%81/dbfipcjecamggjfabeaclacjoohfjhhn)
* [Browsec VPN - Free VPN for Chrome](https://chrome.google.com/webstore/detail/browsec-vpn-free-vpn-for/omghfjlpggmjjaagoclmmobgdodcjboh)

### Экспорт и Импорт расширений
Тут всё просто. Переходим в настройки расширения Chromium Web Store

Скролим вниз и копируем весь текст и сохраняем его на ПК

Импортирование расширений происходит простым CopyPaste в пустое поле и нажать "Установить всё"

Остальные удалённые устанавливаем вручную

![](/images/ultimate-ungoogled-chromium-guide/1668285495.png)

### Добавление поисковой системы и шорткатов

* https://www.youtube.com/watch?v=gthBmRxDFlo

Стандартно Ungoogled-chromium поставляется с несколькими поисковыми системами но поисковой системы [brave](https://search.brave.com/) и [startpage](https://www.startpage.com/) всё ещё нет. Именно поэтому я хотел рассказать как их добавить.

Заходим в **chrome://settings/searchEngines**, жмём **Добавить** и вставляем

#### Brave search

* Поисковая система: `Brave`
* Быстрая команда: `:br`
* URL с параметром %s вместо запроса: `https://search.brave.com/search?q=%s&source=desktop`

Сохраняем и назначаем по умолчанию

#### Startpage

* Поисковая система: `Startpage`
* Быстрая команда: `:sp`
* URL с параметром %s вместо запроса: `https://www.startpage.com/do/search?q=%s&segment=startpage.brave`

Сохраняем и назначаем по умолчанию

#### Google Search

Источники:
* https://www.youtube.com/watch?v=qV2woVC6aVw
* https://support.google.com/chrome/answer/95426?hl=en&co=GENIE.Platform%3DDesktop#zippy=%2Curl-with-s-in-place-of-query-field:~:text=%22URL%20with%20%25s%20in%20place%20of%20query%22%20field

Вставляем
* Поисковая система: `Google`
* Быстрая команда: `google.com`
* URL с параметром %s вместо запроса: `https://www.google.com/search?q=%s`
* Suggestions URL with %s in place of query: `https://www.google.com/complete/search?client=chrome&q=%s`

#### SearXNG

Источники:
* https://github.com/searxng/searxng

Экземпляры:
* https://paulgo.io/
* https://searx.tiekoetter.com/ - Находится ближе, интерфейс удобнее настроен
* https://searx.be/
* https://searx.bar/

Вставляем:
* Поисковая система: `SearXNG`
* Быстрая команда: `:sx`
* URL с параметром %s вместо запроса: `https://searx.tiekoetter.com/search?q=%s`

### Работа с защищённым контентом DRM (Spotify Netflix и т.д)

Из данного браузера вырезана данная проприетарщина, но её можно вернуть

Способы установки:
1. **(Обновляемый) AUR пакет [chromium-widevine](https://aur.archlinux.org/packages/chromium-widevine)**

Данный пакет автоматизирует вышеперечисленные манипуляции в единый пакет, обновляется прямо после выхода новой версией ungoogled-chromium.
```sh
yay -S chromium-widevine
```
2. **[Офф инструкция](https://ungoogled-software.github.io/ungoogled-chromium-wiki/faq#linux)**

Заработало при помощи офф инструкции.

3. (Может быть устаревшее) **[Скриптом](https://github.com/Empyreann/chromium-widevine)**

Или воспользовавшись данным скриптом

Инструкция:
```sh
git clone https://github.com/Empyreann/chromium-widevine.git
cd chromium-widevine
./use-standalone-widevine.sh
```
Для проверки что Widevine работает можно запустить данный html тест (test-widevine.html) внутри репо.

![](/images/ultimate-ungoogled-chromium-guide/widewine-test.png)

