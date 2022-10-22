Задача добавить новые интересные карты в карточную игру.

0. «Осмотр реквизита»
   Для того, чтобы запустить игру, понадобится веб-сервер. Воспользуемся веб-сервером для Node.js.
   Необходимые настройки уже сделаны в `package.json`, поэтому все, что нужно сделать:

- Иметь установленную Node.js.
- Перейти в консоли в папку с `package.json` и выполнить команду `npm install`.
  Для этого можно воспользоваться встроенным в IDE терминалом (верно для WebStorm и Visual Studio Code).
  Произойдет скачивание веб-сервера и его зависимостей в папку `node_modules`.
- Выполнить команду `npm start` для запуска веб-сервера.
  После запуска веб-сервер выведет в консоль свой адрес.
  Один из адресов должен быть http://localhost:8080, по нему и доступна игра.
  `localhost` - стандартное доменное имя для хоста, на котором расположена программа-клиент, в нашем случае браузер.
  `8080` - порт веб-сервера, который часто используется в разработке (в продакшене обычно используется порт `80`).

Для начала осмотрись:

- Запусти `index.html` и понаблюдай как игра играет сама в себя.
- Посмотри `index.js`. В этом файле создаются колоды карты и запускается игра.
- Загляни в `Game.js`, обрати внимания на стадии хода.
- Изучи `Card.js`, чтобы разобраться, какие действия происходят с картой, какие возможности по расширению заложены.

Браузер имеет правильное свойство кэшировать все, что скачивает: скрипты, стили, картинки.
Правда в разработке это может мешать: только что внесенные изменения не будут появляться при обновлении страницы.
Для перезагрузки страницы без кэша можно использовать сочетание Control+F5.
А в Chrome можно полностью отключить кэширование в `Developer Tools`:

- Открой `Developer Tools`
- Перейди во вкладку `Network`
- Установи флажок `Disable cache`

Изначально в скриптах на JavaScript нельзя было импортировать код из других файлов.
Если нужно было добавить на страницу несколько скриптов, они добавлялись отдельными тэгами `script` на страницу.
Chrome уже поддерживает использование модулей ECMAScript из коробки.
Чтобы это работало в файле `index.html`, основной скрипт подключается так:
`<script type="module" src="index.js"></script>`
Кроме того, в каждом из файлов `TaskQueue.js`, `Game.js`, `Card.js`, `CardView.js`, `Player.js`, `PlayerView.js`
, `SpeedRate.js`
используются директивы `export` или `export default`, чтобы экспортировать определенные в файле сущности.
И в этих же файлах, а еще в `index.js` используются директивы `import`, чтобы подключать сущности из других файлов.
В результате каждый скрипт подключает только то, что нужно ему, дерево зависимостей скриптов строится динамически
и не надо прописывать пути до всех скриптов в `index.html`.

Инструктаж окончен. Твоя задача - создавать новые типы карт, наследующиеся от Card. Можешь приступать к решению задачи!

1. «Классы»
   Во всем коде типы определены по-старому, через прототипы.
   Изоляция кода при этом обеспечивает за счет техники IIFE:
   https://developer.mozilla.org/ru/docs/%D0%A1%D0%BB%D0%BE%D0%B2%D0%B0%D1%80%D1%8C/IIFE

Благодаря использованию модулей ECMAScript каждый файл изолирован и IIFE больше не нужны,
ведь снаружи будет видно только то, что явным образом экспортируется.

Сами типы можно определять с помощью новой инструкции `class`.
Результат будет тот же, но запись будет более лаконичной.

Чтобы лучше понять разницу между старым и новым синтаксисом перепиши `TaskQueue` с использованием `class` и без IIFE:

- Убери обрамляющую определение `TaskQueue` самовызывающуюся функцию.
- Перенеси вверх функцию `runNextTask`, потому что это не метод `TaskQueue`.
- Объяви тип `TaskQueue` с помощью инструкции `class`, определи constructor и все методы.
- `export default` поставь сразу перед инструкции `class`.

Пример перехода от старого синтаксиса к новому:

```js
// до переработки, старый синтаксис
const Bar = function() {
    function BarInternal(a, b) {
        this.a = a;
        this.b = b;
    }

    BarInternal.prototype.do = function(c) {
        this.a += secret(c);
    }

    function secret(value) {
        return 2 * value;
    }

    return BarInternal;
}();

// после переработки, новый синтаксис
class Bar {
    constructor(a, b) {
        this.a = a;
        this.b = b;
    }

    do(c) {
        this.a += secret(c);
    }
};

// эта функция доступна только внутри модуля
function secret(value) {
    return 2 * value;
}
```

2. «Утки против собак»
   Создай в `index.js` две новые карты, используй `class` и унаследовав их от `Card`:

- `Duck` с именем «Мирная утка» и силой 2
- `Dog` с именем «Пес-бандит» и силой 3

Посмотреть, как унаследовать один тип от другого с помощью классов можно тут:
https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Classes#%D0%9D%D0%B0%D1%81%D0%BB%D0%B5%D0%B4%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5_%D0%BA%D0%BB%D0%B0%D1%81%D1%81%D0%BE%D0%B2_%D1%81_%D0%BF%D0%BE%D0%BC%D0%BE%D1%89%D1%8C%D1%8E_extends

`Card` при этом не нужно переводить на современный синтаксис с `class`,
можно просто создавать новые типы в новом синтаксисе.

Не забудь вызвать базовый конструктор `Card` с помощью `super`!

- Новые карты должны создаваться, даже если им не передать параметров: `new Duck()` и `new Dog()`.
- Методы `quacks` и `swims` утки должны создаваться на уровне класса, а не добавляться в конструкторе.
- После добавления новых типов замени карты в колоде шерифа на уток, а в колоде бандита - на собак.
- Функция `isDuck` должна возвращать `true` для утки, а функция `isDog` — для собаки.
- Если все сделано правильно, то внизу карты утки должен быть текст Duck➔ Card, а у собаки Dog➔ Card.

Колоды для проверки:

```js
const seriffStartDeck = [
    new Duck(),
    new Duck(),
    new Duck(),
];
const banditStartDeck = [
    new Dog(),
];
```

3. «Утка или собака?»
   Метод `getDescriptions` в `Card` создан для того, чтобы на картах появлялась дополнительная информация.
   Его функционал хочется расширить. Причем так, чтобы это работало и для уток, и для собак,
   и для всех остальных существ, которые будут добавляться.

- Создай новый тип `Creature` и унаследуй его от `Card`.
- Сделай так, чтобы `Duck` и `Dog` наследовались от `Creature`.
- Переопредели в классе `Creature` реализацию `getDescriptions` на новую.
  Теперь в ней должны возвращаться две строки описания в виде массива.
  Первая из них — из функции `getCreatureDescription`, которая определена в `index.js`.
  Вторая — из `getDescriptions` в `Card`.
  Для этого надо вызывать эту базовую версию функции `getDescriptions` из прототипа `Card`.
  В классах это делается просто: `super.getDescriptions()`;

  Заметь, что `getDescriptions` должен возвращать массив строк, а не просто строку!
  Верный признак, что ты напутал что-то с возвращаемым значением - вертикальная надпись на карте:
  У
  т
  к
  а
  Используй `spread-оператор` (так: `[newValue, ...values]`) или метод `unshift` массива,
  чтобы дополнить возвращаемый базовой версией `getDescriptions` массив новыми значениями.

Используя те же колоды, убедись, что у уток появилась надпись «Утка» над строкой с цепочкой наследования,
а у собак надпись «Собака» над цепочкой наследования.

4. «Громила»
   Для уток все становится плохо, когда в рядах бандитов появляется Громила.

Добавь карту `Trasher`:

- называется Громила, сила 5, наследуется от `Dog`.
- если Громилу атакуют, то он получает на 1 меньше урона.

Подсказки:

- переопредели метод `modifyTakenDamage`, чтобы уменшать урон
- `this.view.signalAbility` — используй, чтобы при применении способности карта мигала
  Работает это так:
  `this.view.signalAbility(() => { // то, что надо сделать сразу после мигания. }`

Обрати внимание, что если Громиле нанести 2 урона,
то он сначала должен мигнуть «белым», потому что применилась способность и урон уменьшился на 1,
а затем мигнуть «красным» так как он все же получит единицу урона.
За красное мигание отвечает `signalDamage`, и он будет вызван сам в методе `takeDamage` в `Card.js`.

Переопредели `getDescriptions`, чтобы на лицевой стороне карты выводилось краткое описание способности Громилы.
Не забудь вызвать реализацию из базового типа, чтобы информация «Утка или Собака» никуда не делась!

Колоды для проверки:

```js
const seriffStartDeck = [
    new Duck(),
    new Duck(),
    new Duck(),
    new Duck(),
];
const banditStartDeck = [
    new Trasher(),
];
```

5. «Гатлинг»
   Нехорошо нападать на мирных жителей. Это еще может быть опасно, если в сарае припрятан Гатлинг.

Добавь карту `Gatling`:

- называется Гатлинг, сила 6, наследуется от `Creature`.
- при атаке наносит 2 урона по очереди всем картам противника на столе, но не атакует игрока-противника.
  Таким образом урон сначала получает самая левая карта противника, затем вторая слева и так далее.
  Урон не должен наноситься одновременно.

Подсказки:

- переопредели метод `attack` так, чтобы урон наносился всем картам противника
- список карт противника можно получить через `gameContext.oppositePlayer.table`
- в качестве примера выполнения действий над несколькими картами можешь использовать `applyCards` из `Player.js`

Колоды для проверки:

```js
const seriffStartDeck = [
    new Duck(),
    new Duck(),
    new Duck(),
    new Gatling(),
];
const banditStartDeck = [
    new Trasher(),
    new Dog(),
    new Dog(),
];
```

6. «Братки»
   Чем их больше, тем они сильнее.

Добавь карту `Lad`:

- называется Браток, сила 2, наследуется от `Dog`.
- чем больше братков находится в игре, тем больше урона без потерь поглощается
  и больше урона по картам наносится каждым из них.

Защита от урона = количество * (количество + 1) / 2
Дополнительный урон = количество * (количество + 1) / 2

Подсказки:

- текущее количество братков в игре надо где-то хранить, свойство в функции-конструкторе `Lad` — подходящее место.
  Заведи для этого пару методов:
  `static getInGameCount() { return this.inGameCount || 0; }`
  `static setInGameCount(value) { this.inGameCount = value; }`
  Хоть свойство `inGameCount` в функции `Lad`, другими словами статическое свойство класса `Lad`, явно не объявляется,
  при первом вызове `setInGameCount`, оно будет создано со значением `value`.
- чтобы обновлять количество братков в игре переопредели методы `doAfterComingIntoPlay`, `doBeforeRemoving`
- чтобы рассчитывать бонус к урону и защите стоит завести статический метод в классе `Lad`.
  Выглядеть будет как-то так: `static getBonus() { ... }`
  Чтобы в `getBonus` обращаться к другим статическим методам используй `this`, а не имя класса `Lad`.
  Вспомни, почему в статических методах в качестве `this` передается `Lad`.
- переопредели методы `modifyDealtDamageToCreature` и `modifyTakenDamage`, чтобы они использовали бонус.

Добавь в описание карты «Чем их больше, тем они сильнее».
Этот текст должен появляться только если непосредственно у братков (т.е. в `Lad.prototype`)
переопределены методы `modifyDealtDamageToCreature` или `modifyTakenDamage`.
Проверка на наличие свойства непосредственно у объекта выполняется с помщью метода `hasOwnProperty`.
Проверка наличия метода `modifyDealtDamageToCreature` у братков выглядит так:
`Lad.prototype.hasOwnProperty('modifyDealtDamageToCreature')`
Эта особенность понадобится на следующем шаге.
Как видишь, даже при использовании `class` весь функционал прототипов доступен и работает.

Колоды для проверки:

```js
const seriffStartDeck = [
    new Duck(),
    new Duck(),
    new Duck(),
];
const banditStartDeck = [
    new Lad(),
    new Lad(),
];
```

7*. «Изгой»
От него все бегут, потому что он приходит и отнимает силы...

Добавь карту `Rogue`:

- называется Изгой, сила 2, наследуется от `Creature`.
- перед атакой на карту забирает у нее все способности к увеличению наносимого урона или уменьшению получаемого урона.
  Одновременно эти способности забираются у всех карт того же типа, но не у других типов карт.
  Изгой получает эти способности, но не передает их другим Изгоям.

Подсказки:

- Изгой похищает эти способности: `modifyDealtDamageToCreature`, `modifyDealtDamageToPlayer`, `modifyTakenDamage`
- Чтобы похитить способности у всех карт некоторого типа, надо взять их из прототипа
- Получить доступ к прототипу некоторой карты можно так: `Object.getPrototypeOf(card)`
- Чтобы не похищать способности у других типов, нельзя задевать прототип прототипа
- `Object.getOwnPropertyNames` и `obj.hasOwnProperty` позволяют получать только собственные свойства объекта
- Удалить свойство из объекта можно с помощью оператора `delete` так: `delete obj[propName]`
  Это не то же самое, что `obj[propName] = undefined`
- После похищения стоит обновить вид всех объектов игры. `updateView` из `gameContext` поможет это сделать.

Колоды для проверки:

```js
const seriffStartDeck = [
    new Duck(),
    new Duck(),
    new Duck(),
    new Rogue(),
];
const banditStartDeck = [
    new Lad(),
    new Lad(),
    new Lad(),
];
```

8*. «Пивовар»
Живительное пиво помогает уткам творить невозможное!

Добавь карту `Brewer`:

- называется Пивовар, сила 2, наследуется от `Duck`.
- перед атакой на карту Пивовар раздает пиво,
  которое изменяет максимальную силу карты на +1, а затем текущую силу на +2.
- Пивовар угощает пивом все карты на столе: и текущего игрока и игрока-противника,
  но только с утками, проверяя их с помощью `isDuck`.
- Пивовар само собой утка, поэтому его сила тоже возврастает.

Подсказки:

- Все карты на столе можно получить из `gameContext` так: `currentPlayer.table.concat(oppositePlayer.table)`.
- `this.view.signalHeal` — используй, чтобы подсветить карту, у которой увеличилась сила.
- `card.updateView()` — используй, чтобы обновлять вид карт, у которых увеличилась сила.

Важно, чтобы при увеличении текущей силы она не превышала максимальную.
Добиться этого можно по разному, но решить этот вопрос раз и навсегда иначе определив свойство `currentPower` в `Card`.

Сейчас оно определяется в конструкторе `Card` довольно просто:
`this.currentPower = maxPower`
Пусть так и определяется.

А вот в `Creature` определи заново свойство `currentPower` через `get` и `set`.
Геттер должен просто возвращать текущее значение, а сеттер не давать устанавливать значение выше, чем `this.maxPower`.

Подробнее про `get` и `set` тут:
https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Functions/get
https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Functions/set

Колоды для проверки:

```js
const seriffStartDeck = [
    new Duck(),
    new Brewer(),
];
const banditStartDeck = [
    new Dog(),
    new Dog(),
    new Dog(),
    new Dog(),
];
```

9*. «Псевдоутка»
Чтобы получить доступ к живительному напитку надо всего лишь
выглядеть как утка, плавать как утка и крякать как утка!

Добавь карту `PseudoDuck`:

- называется Псевдоутка, сила 3, наследуется от `Dog`.
- Псевдоутка — это обычный пес, но еще умеет крякать и плавать, поэтому легко проходит проверку на утиность `isDuck`.

Подсказки:

- чтобы быть похожим на утку надо всего лишь реализовывать пару методов: `quacks` и `swims`
- убедись, что в описании Псевдоутки присутстует «Утка-Собака».

Колоды для проверки:

```js
const seriffStartDeck = [
    new Duck(),
    new Brewer(),
];
const banditStartDeck = [
    new Dog(),
    new PseudoDuck(),
    new Dog(),
];
```

10*. «Немо»
«The one without a name without an honest heart as compass»

Добавь карту `Nemo`:

- называется Немо, сила 4, наследуется от `Creature`.
- перед атакой на карту крадет ее прототип и назначает себе, получая все ее способности,
  но вместе со своим старым прототипом теряет способность красть.
- если карта, у которой был украден прототип, обладает способностями, выполняющимися перед атакой,
  то они должны быть выполнены сразу после кражи прототипа.

Подсказки:

- `updateView` из `gameContext` позволяет обновить вид всех объектов игры.
- `Object.getPrototypeOf(obj)` позволяет получить прототип объекта.
- `Object.setPrototypeOf(obj, proto)` позволяет задать протип объекту.
- функция `doBeforeAttack` из прототипа должна быть вызвана сразу после кражи прототипа.

Колоды для проверки:

```js
const seriffStartDeck = [
    new Nemo(),
];
const banditStartDeck = [
    new Brewer(),
    new Brewer(),
];
```
