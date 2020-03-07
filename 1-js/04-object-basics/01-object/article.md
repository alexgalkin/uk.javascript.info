
# Об'єкти

Як нам відомо з глави <info:types>, є сім типів даних в JavaScript. Шість з них називається "примітивами", тому що вони можуть мати лише один тип значення (чи то рядок, чи число, чи ще щось).

На відміну від цього, об'єкти використовуються для зберігання набору ключів з різними даними і складніших сутностей. У JavaScript об'єкти проникають майже в кожен аспект мови. Тому ми повинні спочатку їх розібрати, перш ніж поглибитись далі.

Об'єкт можна створити за допомогою фігурних дужок `{…}` з необов'язковим списком *властивостей*. Властивість - це пара "ключ: значення", де `ключем` є рядок (який називають "назвою властивості"), і будь чим є `значення`.

Ми можемо уявити об'єкт шафою із підписаними файлами. Кожен фрагмент даних зберігається у своєму файлі за вказаним ключем. Легко знайти файл за його назвою або додати / видалити файл.

![](object.svg)

Порожній об'єкт ("порожню шафу") можна створити за допомогою одного з двох синтаксисів:

```js
let user = new Object(); // синтаксис "конструктора об’єкта"
let user = {};  // синтаксис "об'єктного літералу"
```

![](object-user-empty.svg)

Зазвичай використовують фігурні дужки `{...}`. Таке оголошення називається *об'єктним літералом*.

## Літерали і властивості

Ми можемо відразу вказати деякі властивості в `{...}` за допомогою пар "ключ: значення":

```js
let user = {     // об'єкт
  name: "John",  // ім'я "John" зберігається за ключем "name"
  age: 30        // 30 зберігається за ключем "age"
};
```

Властивість має ключ (також відомий, як "ім'я" або "ідентифікатор") перед двокрапкою `":"` і значення після неї.

Об'єкт `user` має дві властивості:

1. Перша властивість має ім'я `"name"` і значення `"John"`.
2. Друга властивість має ім'я `"age"` і значення `30`.

Готовий об'єкт можна уявити шафою з двома підписаними файлами, які мають назву "name" та "age".

![user object](object-user.svg)

Ми можемо додавати, видаляти і читати файли з цієї шафи у будь-який час.

Для доступу до значень властивостей використовується крапка:

```js
// отримати значення властивостей об'єкта:
alert( user.name ); // John
alert( user.age ); // 30
```

Значення може бути будь-якого типу. Додамо булеве значення:

```js
user.isAdmin = true;
```

![user object 2](object-user-isadmin.svg)

Для видалення властивості ми може використовувати оператор `delete`:

```js
delete user.age;
```

![user object 3](object-user-delete.svg)

Ми також можемо використовувати кілька слів, але тоді нам потрібно брати їх у лапки:

```js
let user = {
  name: "John",
  age: 30,
  "likes birds": true  // властивість з кількома словами взята у лапки
};
```

![](object-user-props.svg)


Остання властивість у списку може закінчуватись комою:
```js
let user = {
  name: "John",
  age: 30*!*,*/!*
}
```
Вона називається "останньою" або "висячою" комою. Її використання робить додавання/видалення/переміщення властивостей легшим, тому що структура рядків залишається однаковою.

## Квадратні дужки

Для властивостей, що містять кілька слів, доступ за допомогою крапки не працює:

```js run
// це призведе до синтаксичної помилки
user.likes birds = true
```

JavaScript такого не розуміє. Він гадає, що ми звертаємось до `user.likes`, і повідомляє про синтаксичну помилку, коли далі неочікувано зустрічає `birds`.

Використання крапки вимагає коректного ідентифікатору змінної. Це означає: жодних пробілів, не починати назву з числа і не використовувати спеціальні символи (дозволяється використовувати `$` та `_`).

Існує альтернативний спосіб з "квадратними дужками", який працює з будь-яким рядком:

```js run
let user = {};

// встановити
user["likes birds"] = true;

// отримати
alert(user["likes birds"]); // true

// видалити
delete user["likes birds"];
```

Тепер все добре. Зверніть увагу, що рядок у дужках взятий у лапки належним чином (підійде будь-який тип лапок).

Квадратні дужки також дозволяють отримати ім'я властивості у якості результату будь-якого виразу - на відміну від літерального рядка - наприклад, із змінної:

```js
let key = "likes birds";

// так само, як user["likes birds"] = true;
user[key] = true;
```

Тут змінна `key` може бути обчислена під час виконання або залежати від введення користувача. А потім ми використовуємо її для доступу до властивості. Це дає нам велику гнучкість.

Наприклад:

```js run
let user = {
  name: "John",
  age: 30
};

let key = prompt("Що ви бажаєте дізнатись про користувача?", "name");

// доступ за допомогою змінної
alert( user[key] ); // John (якщо ми ввели "name")
```

Спосіб з крапкою не можна так само використати:

```js run
let user = {
  name: "John",
  age: 30
};

let key = "name";
alert( user.key ) // undefined
```

### Обчислені властивості

Ми можемо використовувати квадратні дужки в об'єктному літералі. Властивості в такому випадку називають *обчисленими*.

Наприклад:

```js run
let fruit = prompt("Який фрукт купити?", "apple");

let bag = {
*!*
  [fruit]: 5, // ім'я властивості береться зі змінної fruit
*/!*
};

alert( bag.apple ); // 5 якщо fruit="apple"
```

Значення обчислених властивостей просте: `[fruit]` означає, що ім'я властивості треба брати зі змінної `fruit`.

Таким чином, якщо користувач введе `"apple"`, `bag` буде мати`{apple: 5}`.

По суті, це працює так само, як:
```js run
let fruit = prompt("Який фрукт купити?", "apple");
let bag = {};

// взяти ім'я властивості зі змінної fruit
bag[fruit] = 5;
```

...Але має краще вигляд.

Ми можемо використовувати складніші вирази всередині квадратних дужок:

```js
let fruit = 'apple';
let bag = {
  [fruit + 'Computers']: 5 // bag.appleComputers = 5
};
```

Square brackets are much more powerful than the dot notation. They allow any property names and variables. But they are also more cumbersome to write.

So most of the time, when property names are known and simple, the dot is used. And if we need something more complex, then we switch to square brackets.

## Property value shorthand

In real code we often use existing variables as values for property names.

For instance:

```js run
function makeUser(name, age) {
  return {
    name: name,
    age: age
    // ...other properties
  };
}

let user = makeUser("John", 30);
alert(user.name); // John
```

In the example above, properties have the same names as variables. The use-case of making a property from a variable is so common, that there's a special *property value shorthand* to make it shorter.

Instead of `name:name` we can just write `name`, like this:

```js
function makeUser(name, age) {
*!*
  return {
    name, // same as name: name
    age   // same as age: age
    // ...
  };
*/!*
}
```

We can use both normal properties and shorthands in the same object:

```js
let user = {
  name,  // same as name:name
  age: 30
};
```

## Property names limitations

Property names (keys) must be either strings or symbols (a special type for identifiers, to be covered later).

Other types are automatically converted to strings.

For instance, a number `0` becomes a string `"0"` when used as a property key:

```js run
let obj = {
  0: "test" // same as "0": "test"
};

// both alerts access the same property (the number 0 is converted to string "0")
alert( obj["0"] ); // test
alert( obj[0] ); // test (same property)
```

**Reserved words are allowed as property names.**

As we already know, a variable cannot have a name equal to one of language-reserved words like "for", "let", "return" etc.

But for an object property, there's no such restriction. Any name is fine:

```js run
let obj = {
  for: 1,
  let: 2,
  return: 3
};

alert( obj.for + obj.let + obj.return );  // 6
```

We can use any string as a key, but there's a special property named `__proto__` that gets special treatment for historical reasons.

For instance, we can't set it to a non-object value:

```js run
let obj = {};
obj.__proto__ = 5; // assign a number
alert(obj.__proto__); // [object Object] - the value is an object, didn't work as intended
```

As we see from the code, the assignment to a primitive `5` is ignored.

The nature of `__proto__` will be revealed in detail later in the chapter [](info:prototype-inheritance).

As for now, it's important to know that such behavior of `__proto__` can become a source of bugs and even vulnerabilities if we intend to store user-provided keys in an object.

The problem is that a visitor may choose `__proto__` as the key, and the assignment logic will be ruined (as shown above).

There are two workarounds for the problem:
1. Modify the object's behavior to treat `__proto__` as a regular property. We'll learn how to do it in the chapter [](info:prototype-methods).
2. Using [Map](info:map-set) data structure which supports arbitrary keys. We'll learn it in the chapter <info:map-set>.

## Property existence test, "in" operator

A notable objects feature is that it's possible to access any property. There will be no error if the property doesn't exist! Accessing a non-existing property just returns `undefined`. It provides a very common way to test whether the property exists -- to get it and compare vs undefined:

```js run
let user = {};

alert( user.noSuchProperty === undefined ); // true means "no such property"
```

There also exists a special operator `"in"` to check for the existence of a property.

The syntax is:
```js
"key" in object
```

For instance:

```js run
let user = { name: "John", age: 30 };

alert( "age" in user ); // true, user.age exists
alert( "blabla" in user ); // false, user.blabla doesn't exist
```

Please note that on the left side of `in` there must be a *property name*. That's usually a quoted string.

If we omit quotes, that would mean a variable containing the actual name will be tested. For instance:

```js run
let user = { age: 30 };

let key = "age";
alert( *!*key*/!* in user ); // true, takes the name from key and checks for such property
```

````smart header="Using \"in\" for properties that store `undefined`"
Usually, the strict comparison `"=== undefined"` check the property existence just fine. But there's a special case when it fails, but `"in"` works correctly.

It's when an object property exists, but stores `undefined`:

```js run
let obj = {
  test: undefined
};

alert( obj.test ); // it's undefined, so - no such property?

alert( "test" in obj ); // true, the property does exist!
```


In the code above, the property `obj.test` technically exists. So the `in` operator works right.

Situations like this happen very rarely, because `undefined` is usually not assigned. We mostly use `null` for "unknown" or "empty" values. So the `in` operator is an exotic guest in the code.
````

## The "for..in" loop

To walk over all keys of an object, there exists a special form of the loop: `for..in`. This is a completely different thing from the `for(;;)` construct that we studied before.

The syntax:

```js
for (key in object) {
  // executes the body for each key among object properties
}
```

For instance, let's output all properties of `user`:

```js run
let user = {
  name: "John",
  age: 30,
  isAdmin: true
};

for (let key in user) {
  // keys
  alert( key );  // name, age, isAdmin
  // values for the keys
  alert( user[key] ); // John, 30, true
}
```

Note that all "for" constructs allow us to declare the looping variable inside the loop, like `let key` here.

Also, we could use another variable name here instead of `key`. For instance, `"for (let prop in obj)"` is also widely used.


### Ordered like an object

Are objects ordered? In other words, if we loop over an object, do we get all properties in the same order they were added? Can we rely on this?

The short answer is: "ordered in a special fashion": integer properties are sorted, others appear in creation order. The details follow.

As an example, let's consider an object with the phone codes:

```js run
let codes = {
  "49": "Germany",
  "41": "Switzerland",
  "44": "Great Britain",
  // ..,
  "1": "USA"
};

*!*
for (let code in codes) {
  alert(code); // 1, 41, 44, 49
}
*/!*
```

The object may be used to suggest a list of options to the user. If we're making a site mainly for German audience then we probably want `49` to be the first.

But if we run the code, we see a totally different picture:

- USA (1) goes first
- then Switzerland (41) and so on.

The phone codes go in the ascending sorted order, because they are integers. So we see `1, 41, 44, 49`.

````smart header="Integer properties? What's that?"
The "integer property" term here means a string that can be converted to-and-from an integer without a change.

So, "49" is an integer property name, because when it's transformed to an integer number and back, it's still the same. But "+49" and "1.2" are not:

```js run
// Math.trunc is a built-in function that removes the decimal part
alert( String(Math.trunc(Number("49"))) ); // "49", same, integer property
alert( String(Math.trunc(Number("+49"))) ); // "49", not same "+49" ⇒ not integer property
alert( String(Math.trunc(Number("1.2"))) ); // "1", not same "1.2" ⇒ not integer property
```
````

...On the other hand, if the keys are non-integer, then they are listed in the creation order, for instance:

```js run
let user = {
  name: "John",
  surname: "Smith"
};
user.age = 25; // add one more

*!*
// non-integer properties are listed in the creation order
*/!*
for (let prop in user) {
  alert( prop ); // name, surname, age
}
```

So, to fix the issue with the phone codes, we can "cheat" by making the codes non-integer. Adding a plus `"+"` sign before each code is enough.

Like this:

```js run
let codes = {
  "+49": "Germany",
  "+41": "Switzerland",
  "+44": "Great Britain",
  // ..,
  "+1": "USA"
};

for (let code in codes) {
  alert( +code ); // 49, 41, 44, 1
}
```

Now it works as intended.

## Copying by reference

One of the fundamental differences of objects vs primitives is that they are stored and copied "by reference".

Primitive values: strings, numbers, booleans -- are assigned/copied "as a whole value".

For instance:

```js
let message = "Hello!";
let phrase = message;
```

As a result we have two independent variables, each one is storing the string `"Hello!"`.

![](variable-copy-value.svg)

Objects are not like that.

**A variable stores not the object itself, but its "address in memory", in other words "a reference" to it.**

Here's the picture for the object:

```js
let user = {
  name: "John"
};
```

![](variable-contains-reference.svg)

Here, the object is stored somewhere in memory. And the variable `user` has a "reference" to it.

**When an object variable is copied -- the reference is copied, the object is not duplicated.**

If we imagine an object as a cabinet, then a variable is a key to it. Copying a variable duplicates the key, but not the cabinet itself.

For instance:

```js no-beautify
let user = { name: "John" };

let admin = user; // copy the reference
```

Now we have two variables, each one with the reference to the same object:

![](variable-copy-reference.svg)

We can use any variable to access the cabinet and modify its contents:

```js run
let user = { name: 'John' };

let admin = user;

*!*
admin.name = 'Pete'; // changed by the "admin" reference
*/!*

alert(*!*user.name*/!*); // 'Pete', changes are seen from the "user" reference
```

The example above demonstrates that there is only one object. As if we had a cabinet with two keys and used one of them (`admin`) to get into it. Then, if we later use the other key (`user`) we would see changes.

### Comparison by reference

The equality `==` and strict equality `===` operators for objects work exactly the same.

**Two objects are equal only if they are the same object.**

For instance, if two variables reference the same object, they are equal:

```js run
let a = {};
let b = a; // copy the reference

alert( a == b ); // true, both variables reference the same object
alert( a === b ); // true
```

And here two independent objects are not equal, even though both are empty:

```js run
let a = {};
let b = {}; // two independent objects

alert( a == b ); // false
```

For comparisons like `obj1 > obj2` or for a comparison against a primitive `obj == 5`, objects are converted to primitives. We'll study how object conversions work very soon, but to tell the truth, such comparisons are necessary very rarely and usually are a result of a coding mistake.

### Const object

An object declared as `const` *can* be changed.

For instance:

```js run
const user = {
  name: "John"
};

*!*
user.age = 25; // (*)
*/!*

alert(user.age); // 25
```

It might seem that the line `(*)` would cause an error, but no, there's totally no problem. That's because `const` fixes only value of `user` itself. And here `user` stores the reference to the same object all the time. The line `(*)` goes *inside* the object, it doesn't reassign `user`.

The `const` would give an error if we try to set `user` to something else, for instance:

```js run
const user = {
  name: "John"
};

*!*
// Error (can't reassign user)
*/!*
user = {
  name: "Pete"
};
```

...But what if we want to make constant object properties? So that `user.age = 25` would give an error. That's possible too. We'll cover it in the chapter <info:property-descriptors>.

## Cloning and merging, Object.assign

So, copying an object variable creates one more reference to the same object.

But what if we need to duplicate an object? Create an independent copy, a clone?

That's also doable, but a little bit more difficult, because there's no built-in method for that in JavaScript. Actually, that's rarely needed. Copying by reference is good most of the time.

But if we really want that, then we need to create a new object and replicate the structure of the existing one by iterating over its properties and copying them on the primitive level.

Like this:

```js run
let user = {
  name: "John",
  age: 30
};

*!*
let clone = {}; // the new empty object

// let's copy all user properties into it
for (let key in user) {
  clone[key] = user[key];
}
*/!*

// now clone is a fully independent clone
clone.name = "Pete"; // changed the data in it

alert( user.name ); // still John in the original object
```

Also we can use the method [Object.assign](mdn:js/Object/assign) for that.

The syntax is:

```js
Object.assign(dest, [src1, src2, src3...])
```

- Arguments `dest`, and `src1, ..., srcN` (can be as many as needed) are objects.
- It copies the properties of all objects `src1, ..., srcN` into `dest`. In other words, properties of all arguments starting from the 2nd are copied into the 1st. Then it returns `dest`.

For instance, we can use it to merge several objects into one:
```js
let user = { name: "John" };

let permissions1 = { canView: true };
let permissions2 = { canEdit: true };

*!*
// copies all properties from permissions1 and permissions2 into user
Object.assign(user, permissions1, permissions2);
*/!*

// now user = { name: "John", canView: true, canEdit: true }
```

If the receiving object (`user`) already has the same named property, it will be overwritten:

```js
let user = { name: "John" };

// overwrite name, add isAdmin
Object.assign(user, { name: "Pete", isAdmin: true });

// now user = { name: "Pete", isAdmin: true }
```

We also can use `Object.assign` to replace the loop for simple cloning:

```js
let user = {
  name: "John",
  age: 30
};

*!*
let clone = Object.assign({}, user);
*/!*
```

It copies all properties of `user` into the empty object and returns it. Actually, the same as the loop, but shorter.

Until now we assumed that all properties of `user` are primitive. But properties can be references to other objects. What to do with them?

Like this:
```js run
let user = {
  name: "John",
  sizes: {
    height: 182,
    width: 50
  }
};

alert( user.sizes.height ); // 182
```

Now it's not enough to copy `clone.sizes = user.sizes`, because the `user.sizes` is an object, it will be copied by reference. So `clone` and `user` will share the same sizes:

Like this:
```js run
let user = {
  name: "John",
  sizes: {
    height: 182,
    width: 50
  }
};

let clone = Object.assign({}, user);

alert( user.sizes === clone.sizes ); // true, same object

// user and clone share sizes
user.sizes.width++;       // change a property from one place
alert(clone.sizes.width); // 51, see the result from the other one
```

To fix that, we should use the cloning loop that examines each value of `user[key]` and, if it's an object, then replicate its structure as well. That is called a "deep cloning".

There's a standard algorithm for deep cloning that handles the case above and more complex cases, called the [Structured cloning algorithm](https://html.spec.whatwg.org/multipage/structured-data.html#safe-passing-of-structured-data). In order not to reinvent the wheel, we can use a working implementation of it from the JavaScript library [lodash](https://lodash.com), the method is called [_.cloneDeep(obj)](https://lodash.com/docs#cloneDeep).



## Summary

Objects are associative arrays with several special features.

They store properties (key-value pairs), where:
- Property keys must be strings or symbols (usually strings).
- Values can be of any type.

To access a property, we can use:
- The dot notation: `obj.property`.
- Square brackets notation `obj["property"]`. Square brackets allow to take the key from a variable, like `obj[varWithKey]`.

Additional operators:
- To delete a property: `delete obj.prop`.
- To check if a property with the given key exists: `"key" in obj`.
- To iterate over an object: `for (let key in obj)` loop.

Objects are assigned and copied by reference. In other words, a variable stores not the "object value", but a "reference" (address in memory) for the value. So copying such a variable or passing it as a function argument copies that reference, not the object. All operations via copied references (like adding/removing properties) are performed on the same single object.

To make a "real copy" (a clone) we can use `Object.assign` or  [_.cloneDeep(obj)](https://lodash.com/docs#cloneDeep).

What we've studied in this chapter is called a "plain object", or just `Object`.

There are many other kinds of objects in JavaScript:

- `Array` to store ordered data collections,
- `Date` to store the information about the date and time,
- `Error` to store the information about an error.
- ...And so on.

They have their special features that we'll study later. Sometimes people say something like "Array type" or "Date type", but formally they are not types of their own, but belong to a single "object" data type. And they extend it in various ways.

Objects in JavaScript are very powerful. Here we've just scratched the surface of a topic that is really huge. We'll be closely working with objects and learning more about them in further parts of the tutorial.
