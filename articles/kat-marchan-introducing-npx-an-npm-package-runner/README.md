# Представляем npx: утилиту для запуска npm-пакетов
*Перевод статьи [Kat Marchán](https://github.com/zkat): [Introducing npx: an npm package runner](https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b).*


Те из вас, кто обновил npm до последней версии, [`npm@5.2.0`](https://github.com/npm/npm/releases/tag/v5.2.0), могли заметить, что вместе с привычным бинарным файлом `npm` появился новый: [`npx`](https://npm.im/npx).

`npx` — инструмент, предназначенный для того, чтобы помочь стандартизировать опыт использования npm-пакетов: так же, как и npm упрощает установку и управление зависимостями, размещенными в реестре, npx упрощает использование CLI-утилит и других исполняемых файлов. Это значительно упрощает ряд вещей, которые мы раньше делали с помощью обычного npm.

## Использование локально установленных утилит без npm run-script

![](https://cdn-images-1.medium.com/max/800/1*A4HJT1FHQA_1_z3aMBc5mg.gif)

*Установка cowsay в качестве локальной devDependency и его запуск с помощью `$ npx cowsay`*

В последние годы экосистема npm все больше и больше продвигалась к установке инструментов в качестве локальных `devDependencies` проекта, вместо того, чтобы требовать от пользователей устанавливать их глобально. Это означает, что инструменты, такие как [`mocha`](https://npm.im/mocha), [`grunt`](https://npm.im/grunt-cli) и [`bower`](https://npm.im/bower), которые когда-то были впервые установлены на глобальном уровне в системе, теперь могут использовать свою версию на основе каждого проекта. Это также означает, что все, что вам нужно сделать, чтобы запустить проект на основе npm: убедиться, что у вас есть node и npm в вашей системе, склонировать git-репозиторий и вызвать `npm it` для запуска `install` и `test`. Поскольку `npm run-script` добавляет локальные бинарные файлы в `path`, это работает отлично!

Недостатком является то, что это не даёт вам быстрого и удобного способа интерактивного вызова локальных бинарных файлов. Есть несколько способов сделать это, и все они вызывают раздражение: вы можете добавить эти инструменты в  `scripts`, но тогда вам необходимо помнить, что передавать аргументы нужно с помощью `--`, вы можете делать трюки с `shell`, такие как `alias npmx=PATH=$(npm bin):$PATH`, или вы можете просто запустить бинарные файлы вручную с помощью `./node_modules/.bin/mocha`. Все эти способы работают, но ни один из них не является идеальным.

`npx` даёт вам то, что я считаю лучшим решением: `$ npx mocha` - это все, что вам нужно сделать, чтобы использовать локально установленную `mocha`. Если вы выполните дополнительный шаг и настройте [автофолбэк в `shell`](https://www.npmjs.com/package/npx#shell-auto-fallback) (подробнее об этом ниже), тогда `$ mocha` внутри каталога проекта выполнится без дополнительных команд!

В качестве бонуса [`npx` не приносит оверхэда](https://twitter.com/maybekatz/status/877444832494596096) при вызове уже установленного бинарного файла - он достаточно умен, чтобы загрузить код для инструмента непосредственно в текущий процесс `node`! Это работает быстро настолько, насколько вообще можно, и делает `npx` очень удобным инструментом для запуска скриптов.

## Исполнение команд, вызываемых однократно

![](https://cdn-images-1.medium.com/max/800/1*OlIRsvVO5aK7ja9HmwXz_Q.gif)

*`$ npx create-react-app my-cool-new-app` временно устанавливает create-react-app и вызывает его, без замусоривания глобально установленных пакетов и делает это за один шаг!*

Вы когда-нибудь сталкивались с ситуацией, когда вы хотите попробовать какой-то CLI-инструмент, но не хочется устанавливать в глобал то, что нужно один раз? `npx` отлично подходит для этого. Вызов `npx <command>`, когда `<command>` ещё не находится в вашем `$PATH`, автоматически установит пакет с этим именем из npm и вызовет его. Когда это будет сделано, установленный пакет никак не затронет глобал, поэтому вам не придётся беспокоиться о загрязнении глобала в долгосрочной перспективе.

Эта функция идеально подходит для таких вещей, как генераторы. Инструменты, подобные [`yoman`](https://npm.im/yo) или [`create-react-app`](https://npm.im/create-react-app), вызываются раз в году. К тому времени, когда вы запустите их снова, они уже будут очень устаревшими, поэтому вам придётся запускать инсталляцию каждый раз, когда вы захотите их использовать.

Как мейнтернеру утилит, мне очень нравится эта функция, потому что это означает, что я могу написать `$ npx my-tool` в README.md, вместо того, чтобы заставлять людей делать лишние шаги для её фактической установки. Честно говоря, инструкция «о, просто скопипасть эту команду, больше ничего не требуется» более приемлема для пользователей, которые не уверены в том, использовать ли инструмент или нет.

Вот некоторые интересные пакеты, которые вы можете попробовать использовать с npx: [happy-birthday](https://npm.im/happy-birthday), [benny-hill](https://npm.im/benny-hill), [workin-hard](https://npm.im/workin-hard), [cowsay](https://npm.im/cowsay), [yo](https://npm.im/yo), [create-react-app](https://npm.im/create-react-app), [npm-check](https://npm.im/npm-check). Вперёд! Команда для получения [полноценного локального REST-сервера](https://twitter.com/maybekatz/status/878926190064668672), достаточно мала, чтобы вписаться в твит.

## Запуск команд с различными версиями Node.js

![](https://cdn-images-1.medium.com/max/800/1*cfjXl2hTKW7czetTfNGYbA.png)

*`npx -p node-bin@<version> node -v` может использоваться для однократного запуска различных версий node.*

Как оказалось, в npm есть классный пакет под названием [node-bin](https://npm.im/node-bin). Это означает, что вы можете очень легко попробовать команды node с использованием разных версий, не имея необходимости использовать диспетчер версий, такой как [nvm](http://nvm.sh/), [nave](https://npm.im/nave) или [n](https://npm.im/n). Все, что вам нужно - это установленный `npm@5.2.0`!

Опция `-p` для npx позволяет вам указывать пакеты для установки и добавления в запущенный `$PATH`, поэтому это означает, что вы можете делать такие забавные вещи, как: `$ npx -p node-bin@6 npm it`, чтобы установить и протестировать npm пакет, как если бы вы запускали `node@6` глобально. Я использую это постоянно - и даже недавно пришлось очень много использовать этот трюк с одним проектом из-за того, что одна из моих тестовых библиотек ломалась под `node@8`. Это была реальная экономия времени, и я нашла этот способ гораздо проще в использовании для такого рода прецедентов, чем менеджеры версий, так как я всегда нахожу способ их сломать или неправильно сконфигурировать.

*Примечание: `node-bin` работает только на \*nix платформах . Это отличная работа [Арьи Стюарт](https://medium.com/@aredridel). В будущем этот пакет будет доступен как просто `node`, так что вы сможете напрямую вызывать `$ npx node@6 ...` даже на Windows.*

## Разработка интерактивных npm-скриптов

![](https://cdn-images-1.medium.com/max/800/1*JqCC1irC-XxXAWiThpOUiw.gif)

*`$ npx -p cowsay -p lolcatjs -c ‘echo “$npm_package_name@$npm_package_version” | cowsay | lolcatjs’` устанавливает как cowsay, так и lolcatjs, и даёт скрипту доступ к множеству переменных `$npm_` из запущенных скриптов.*

Многие пользователи npm в наши дни используют действительно классную функцию [`запуска npm-скриптов`](https://docs.npmjs.com/misc/scripts). Мало того, что они организуют ваш `$PATH` так, чтобы локальные бинарные файлы были доступны, но они также добавляют целый набор переменных окружения, доступ к которым вы можете получить в этих сценариях! Вы можете исследовать эти переменные с `$ npm run env | Grep npm_`.

Это может затруднить разработку и тестирование таких скриптов - и это означает, что даже с помощью трюков, таких как `$(npm bin)/some-bin`, вы всё равно не будете иметь доступ к этим волшебным переменным окружения при работе в интерактивном режиме.

Но постойте! *npx* имеет ещё один трюк: когда вы используете параметр `-c`, скрипт, написанный внутри строкового аргумента, будет иметь полный доступ к тем же переменным `env`, что и обычный npm-скрипт! Вы даже можете использовать пайпинг и вызывать несколько команд с одним вызовом `npx`!

## Поделитесь своими скриптами с друзьями и близкими!

Стало привычным использовать [gist.github.com](https://gist.github.com/) для обмена всеми видами служебных скриптов, вместо того, чтобы настраивать репозитории git, выпускать новые инструменты и так далее.

С `npx` вы можете пойти дальше: поскольку npx принимает любой спецификатор, принимающий сам npm, вы можете создать gist, который люди могут вызывать напрямую, с помощью одной команды!

Попробуйте сами [https://gist.github.com/zkat/4bc19503fe9e9309e2bfaa2c58074d32](https://gist.github.com/zkat/4bc19503fe9e9309e2bfaa2c58074d32)!

*Примечание: Помните о безопасности! Всегда читайте gists перед их исполнением, так, как если бы вы запускали .sh скрипты!*

## Бонус: автофолбэк для shell

![](https://cdn-images-1.medium.com/max/600/1*MXY3EyWrnjdrv6gjDLgznw.png)

*Добавление автофолбэка для npx в .zshrc означает, что вы можете сделать `$ ember-cli@latest ...` без ссылки на npx вообще!*

Эта потрясающая функция, добавленная [Félix Saparelli](https://medium.com/@passcod), означает, что во многих случаях вам даже не нужно напрямую обращаться к npx! Основное различие между регулярным использованием npx и фолбэком заключается в том, что фолбэк не устанавливает новые пакеты, если вы не используете синтаксис `pkg@version`: защита от потенциально опасного [тайпсквоттинга](https://ru.wikipedia.org/wiki/Тайпсквоттинг).

Настройка автофолбэка очень проста: просмотрите в документации npx  [команду, используемую для вашего текущего шелла](https://www.npmjs.com/package/npx#for-bash), добавьте её в `.bashrc` / `.zshrc` / `.fishrc`, затем перезапустите свой шелл (или используйте `source` или какой-либо другой механизм для обновления шелла).

Теперь вы можете делать такие вещи, как `$ standard@8 -version`, чтобы опробовать разные версии утилит, а если вы находитесь в npm-проекте, `$ mocha` автоматически обратится к локально установленной версии `mocha`, если она не установлена глобально.

## Сделайте это!
Вы можете получить npx уже сейчас, установив `npm@5.2.0` или новее, или, если вы не хотите использовать npm, вы можете установить [автономную версию npx](https://npm.im/npx)! Он полностью совместим с другими менеджерами пакетов, поскольку любое использование npm выполняется только для внутренних операций. О, и он доступен на [10 разных языках](https://github.com/zkat/npx/tree/latest/locales) благодаря [вкладу группы ранних пользователей](https://github.com/zkat/npx/pulls?q=is%3Apr+is%3Aclosed+label%3Ai18n) со всего мира. `--help` и все системные сообщения переведены и автоматически доступны на основе выбранного языка системы! Также есть репозиторий [awesome-npx](https://github.com/js-n/awesome-npx) с примерами вещей, отлично работающими с npx!

У вас есть любимая функция? Вы уже использовали её? Если у вас есть что-то классное, о чём можно рассказать, и что я не перечислила здесь, поделитесь этим в комментариях! Мне бы хотелось услышать, что думают другие люди!

---

*Слушайте наш подкаст в [iTunes](https://itunes.apple.com/ru/podcast/девшахта/id1226773343) и [SoundCloud](https://soundcloud.com/devschacht), читайте нас на [Medium](https://medium.com/devschacht), контрибьютьте на [GitHub](https://github.com/devSchacht), общайтесь в [группе Telegram](https://t.me/devSchacht), следите в [Twitter](https://twitter.com/DevSchacht) и [канале Telegram](https://t.me/devSchachtChannel), рекомендуйте в [VK](https://vk.com/devschacht) и [Facebook](https://www.facebook.com/devSchacht).*

[Статья на Medium](https://medium.com/devschacht/introducing-npx-an-npm-package-runner-a72a658cd9e6)
