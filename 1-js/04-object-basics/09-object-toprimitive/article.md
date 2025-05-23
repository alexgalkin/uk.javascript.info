
# Перетворення об’єктів в примітиви

Що відбувається, коли об’єкти додаються `obj1 + obj2`, віднімаються `obj1 - obj2` або друкуються за допомогою `alert(obj)`?

JavaScript не дозволяє налаштувати те, як працюють оператори з об’єктами. На відміну від деяких інших мов програмування, таких як Ruby або C++, ми не можемо реалізувати спеціальний об’єктний метод для обробки додавання (або інших операторів).

У разі таких операцій, об’єкти автоматично перетворюються на примітиви, а потім операція здійснюється над цими примітивами та повертає результат у вигляді примітивного значення.

Це важливе обмеження, оскільки результат `obj1 + obj2` (або інша математична операція) не може бути іншим об’єктом!

Наприклад, ми не можемо зробити об’єкти, що представляють вектори або матриці (або досягнення або що завгодно) та додати їх і очікувати, що "сплюсованим" результатом буде об’єкт. Автоматично такі архітектурні особливості недоступні.

Отже, оскільки ми не можемо технічно нічого з цим зробити, у реальних проектах немає жодних математичних дій з об’єктами. Коли це трапляється за рідкісними винятками, це через помилку кодування.

У цьому розділі ми розглянемо те, як об’єкти перетворюється на примітиви і як налаштувати це.

У нас є дві цілі:

1. Це дозволить нам зрозуміти, що відбувається у випадку помилок коду, коли така операція відбулася випадково.
2. Є винятки, де такі операції можливі та доцільні. Наприклад, віднімання або порівняння дати (`Date` об’єкти). Ми будемо зустрічатися з ними пізніше.

## Правила перетворення

У розділі <info:type-conversions> ми розглянули правила числових, рядкових та логічних перетворень примітивів. Але ми залишили прогалину для об'єктів. Тепер, коли ми знаємо про методи і символи, стає можливим заповнити її.

1. Немає ніякого перетворення в тип Boolean. Всі об'єкти є істинними (true) в булевому контексті, ось так просто. Існують лише числові та рядкові перетворення.
2. Числове перетворення відбувається, коли ми віднімаємо об’єкти або застосовуємо математичні функції. Наприклад, `Date` об’єкти (розглянуті в розділі <info:date>) можуть відніматися, і результат `date1 - date2` -- це різниця у часі між двома датами.
3. Що стосується перетворення рядків -- це зазвичай відбувається, коли ми виводимо об’єкт, наприклад `alert(obj)` і в подібних контекстах.
   
Ми можемо реалізувати перетворення рядків і чисел самостійно, використовуючи спеціальні методи об’єкта.

Тепер давайте перейдемо до технічних деталей, тому що це єдиний спосіб глибоко розкрити тему.

## Підказки

Як JavaScript вирішує, яке перетворення застосувати?

Є три варіанти перетворення типів, що відбуваються в різних ситуаціях. Вони називаються "підказками" ("hints"), і описані в [специфікації](https://tc39.github.io/ecma262/#sec-toprimitive):

`"string"`
: Перетворення об’єкта в рядок відбувається коли ми виконуємо операцію над об'єктом, який очікує рядок. Наприклад, `alert`:

    ```js
    // вивід
    alert(obj);

    // використання об’єкта як ключа властивості об’єкта
    anotherObj[obj] = 123;
    ```

`"number"`
: Перетворення об’єкта в число, коли ми робимо математичні операції:

    ```js
    // явне перетворення
    let num = Number(obj);

    // математичні операції (крім бінарного додавання)
    let n = +obj; // унарне додавання
    let delta = date1 - date2;

    // порівняння менше/більше
    let greater = user1 > user2;
    ```

    Більшість вбудованих математичних функцій також включають таке перетворення.

`"default"`
: Виникає в рідкісних випадках, коли оператор "не впевнений", який тип очікується.

    Наприклад, бінарний плюс `+` може працювати як з рядками (об’єднує їх), так і з цифрами (додає їх), тому обидва випадки -- рядки та цифри -- будуть працювати. Отже, якщо бінарний плюс отримує об’єкт як аргумент, він використовує підказку `"default"` для його перетворення.

    Також, якщо об’єкт порівнюється за допомогою (`==`) з рядком, числом чи символом, також незрозуміло, яке перетворення слід виконати, тому використовується підказка `"default"`.

    ```js
    // бінарний плюс використовує підказку "default"
    let total = obj1 + obj2;

    // порівняння obj == число використовує підказку "default"
    if (user == 1) { ... };
    ```

    Оператори порівняння більше та менше, такі як `<` `>`, також можуть працювати як з рядками, так і з числами. Проте, вони з історичних причин використовують підказку `"number"` , а не `"default"`.

Але на практиці все трохи простіше.
    
Всі вбудовані об’єкти, крім одного випадку (об’єкт `Date`, ми дізнаємося пізніше) реалізовують перетворення `"default"` так само як `"number"`. І нам, мабуть, слід робити так само.

Проте важливо знати про всі три підказки, незабаром ми побачимо, чому.

** Щоб зробити перетворення, JavaScript намагається знайти та викликати три методи об’єкта: **

1. Викликати `obj[Symbol.toPrimitive](hint)` -- метод з символьним ключем `Symbol.toPrimitive` (системний символ), якщо такий метод існує,
2. Інакше, якщо підказка - це `"string"`
    - спробує викликати `obj.toString()` або `obj.valueOf()` -- залежно від того, що існує.
3. Інакше, якщо підказка має значення - `"число"` або `"default"`
    - спробує викликати `obj.valueOf()` або `obj.toString()` -- залежно від того, що існує.

## Symbol.toPrimitive

Почнемо з першого методу. Є вбудований символ під назвою `Symbol.toPrimitive`, який слід використовувати для назви методу перетворення, як, наприклад:

```js
obj[Symbol.toPrimitive] = function(hint) {
  // тут йде код, щоб перетворити цей об’єкт в примітив
  // він повинен повернути примітивне значення
  // hint = один з "string", "number", "default"
};
```

Якщо метод `symbol.toPrimitive` існує, він використовується для всіх підказок, і не потрібно більше методів.

Наприклад, тут об’єкт `user` реалізує його:

```js run
let user = {
  name: "Іван",
  money: 1000,

  [Symbol.toPrimitive](hint) {
    alert(`hint: ${hint}`);
    return hint == "string" ? `{name: "${this.name}"}` : this.money;
  }
};

// демонстрація перетворення:
alert(user); // hint: string -> {name: "Іван"}
alert(+user); // hint: number -> 1000
alert(user + 500); // hint: default -> 1500
```

Як ми бачимо з коду, `user` стає само описаним рядком або грошовою сумою залежно від перетворення. Єдиний метод `[Symbol.toPrimitive]` об’єкту `user` обробляє всі випадки перетворення.

## toString/valueOf

Якщо немає `Symbol.toPrimitive` тоді JavaScript намагається знайти методи `toString` і `valueOf`:

- Підказка для `"string"`: виклик методу `toString`, і якщо цей метод не існує або якщо він повертає об'єкт замість примітивного значення, тоді викликати `valueOf` (таким чином `toString` має пріоритет при перетворенні в рядок).
- Для інших підказок: `valueOf`, і якщо це не існує або якщо він повертає об'єкт замість примітивного значення, тоді викликати `toString` (таким чином `valueOf` має пріоритет для математичних дій).

Методи `toString` і `valueOf` походять з давніх часів. Вони не є символами (багато часу назад символи не існували), а скоріше є "звичайними" методами, що названі за допомогою рядків. Вони надають альтернативний «старомодний» спосіб реалізації перетворення.

Ці методи повинні повертати примітивне значення. Якщо `toString` чи `valueOf` повертає об’єкт, то він ігнорується (так само, якби цього методу не існувало).

За замовчуванням, звичайний об’єкт має наступні методи `toString` та `valueOf`:

- Метод `toString` повертає рядок `"[object Object]"`.
- Метод `valueOf` повертає сам об’єкт.

Ось демо:

```js run
let user = {name: "Іван"};

alert(user); // [object Object]
alert(user.valueOf() === user); // true
```

Отже, якщо ми спробуємо використовувати об’єкт як рядок, наприклад в `alert` та ін., то за замовчуванням ми побачимо `[object Object]`.

За замовчуванням метод `valueOf` згадується тут лише заради повноти картини, щоб уникнути будь-якої плутанини. Як бачите, він повертає сам об’єкт, і тому ігнорується з історичних причин. Тож ми можемо припустити, що його не існує.

Давайте реалізуємо ці методи, щоб налаштувати перетворення.

Наприклад, тут `user` робить те ж саме, що й вище, використовуючи комбінацію `toString` і `valueOf` замість `Symbol.toPrimitive`:

```js run
let user = {
  name: "Іван",
  money: 1000,

  // для hint="string"
  toString() {
    return `{name: "${this.name}"}`;
  },

  // для hint="number" чи "default"
  valueOf() {
    return this.money;
  }

};

alert(user); // toString -> {name: "Іван"}
alert(+user); // valueOf -> 1000
alert(user + 500); // valueOf -> 1500
```

Як бачимо, поведінка така ж, як і в попередньому прикладі з `Symbol.toPrimitive`.

Часто нам потрібне єдине «універсальне» місце для обробки всіх перетворень примітивів. У цьому випадку ми можемо реалізувати тільки `toString`, ось так:

```js run
let user = {
  name: "Іван",

  toString() {
    return this.name;
  }
};

alert(user); // toString -> Іван
alert(user + 500); // toString -> Іван500
```

За відсутності `Symbol.toPrimitive` і `valueOf`, `toString` буде обробляти всі перетворення примітивів.

### Перетворення може повернути будь-який примітивний тип

Важливо знати про всі методи перетворення примітивів, що вони не обов’язково повертають "підказаний" примітив.

Немає контролю, чи повертає `toString` саме рядок, або чи `symbol.toprimitive` метод повертає число для підказки `"number"`.

Єдина обов’язкова річ: ці методи повинні повертати примітивний тип, а не об’єкт.

```smart header="Історичні нотатки"
З історичних причин, якщо `toString` чи `valueOf` повертає об’єкт,це не є помилкою, але таке значення ігнорується (так само, якби цей метод не існував). Це тому, що в давнину в JavaScript не було хорошого концепту "помилка".

На противагу цьому, `Symbol.toPrimitive` суворіший, він *повинен* повернути примітив, інакше буде помилка.
```

## Подальші перетворення

Як ми вже знаємо, багато операторів та функцій виконують перетворення типів, наприклад, множення `*` перетворює операнди в числа.

Якщо ми передамо об’єкт як аргумент, то відбувається два етапи обчислень:
1. Об’єкт перетворюється на примітив (використовуючи правила, описані вище).
2. Якщо це необхідно для подальших обчислень, то отриманий примітив також перетворюється.

Наприклад:

```js run
let obj = {
  // toString обробляє всі перетворення за відсутності інших методів
  toString() {
    return "2";
  }
};

alert(obj * 2); // 4, об’єкт перетворено на примітив "2", потім множенням отримано число
```

1. Множення `obj * 2` спочатку перетворює об’єкт в примітив (це рядок `"2"`).
2. Тоді `"2" * 2` стає `2 * 2` (рядок перетворюється на число).

Бінарний плюс буде об’єднувати рядки в такій же ситуації, оскільки він з радістю приймає рядки:

```js run
let obj = {
  toString() {
    return "2";
  }
};

alert(obj + 2); // 22 ("2" + 2), перетворення до примітиву повернуло рядок => Конкатенація
```

## Підсумки

Перетворення об’єкта на примітив викликається автоматично багатьма вбудованими функціями та операторами, які очікують примітив як значення.

Існує 3 типи (підказки) цього перетворення:
- `"string"` (для `alert` та інших операцій, які потребують рядка)
- `"number"` (для математичних операцій)
- `"default"` (мало операторів, зазвичай об’єкти реалізують це так само як і `"number"`.)

Специфікація явно описує, який оператор використовує яку підказку.

Алгоритм перетворення наступний:

1. Викликати `obj[Symbol.toPrimitive](hint)`, якщо метод існує,
2. Інакше, якщо підказка -- має значення `"string"`
    - спробувати викликати `obj.toString()` або `obj.valueOf()`, залежно від того, що існує.
3. Інакше, якщо підказка -- має значення `"number"` чи `"default"`
    - спробувати викликати `obj.valueOf()` або `obj.toString()`, залежно від того, що існує.

Усі ці методи повинні повертати примітив (якщо визначені).

На практиці часто достатньо реалізувати лише `obj.toString()` як універсальний метод для перетворення рядків, який повинен повернути "читабельне для людини" представлення об’єкта, для цілей логування або пошуку помилок.
