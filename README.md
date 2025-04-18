## Задачі
- Принципи
    - [Задача 15 (принцип YAGNI)](#задача-15-принцип-yagni)
    - [Задача 14 (принцип DRY)](#задача-14-принцип-dry)
    - [Задача 13 (принцип KISS)](#задача-13-принцип-kiss)
- [Задача 12 (про браузер та id)](#задача-12-про-браузер-та-id)
- [Задача 11](#задача-11)
- [Задача 10](#задача-10)
- [Задача 9](#задача-9)
- [Задача 8](#задача-8)
- [Задача 7](#задача-7)
- [Задача 6](#задача-6)
- [Задача 5](#задача-5)
- [Задача 4](#задача-4)
- [Задача 3](#задача-3)
- [Задача 2](#задача-2)
- [Задача 1](#задача-1)
---



### Задача 15 (принцип YAGNI)
Ви працюєте над проєктом для обробки замовлень у невеликому онлайн-магазині. Наразі єдина вимога — генерувати унікальний код замовлення на основі дати та порядкового номера замовлення за день (наприклад, `"20231015-001"`).

Як найкраще написати функцію, щоб дотриматися принципу YAGNI?

Відповідь 1. Проста функція з фіксованим форматом:
```js
function generateOrderCode(date, orderNumber) {
  const dateStr = date.toISOString().slice(0, 10).replace(/-/g, '');
  const paddedNumber = String(orderNumber).padStart(3, '0');

  return `${dateStr}-${paddedNumber}`;
}
```

Відповідь 2. Функція з опціональним форматуванням:
```js
function generateOrderCode(date, orderNumber, format = 'default') {
  const dateStr = date.toISOString().slice(0, 10).replace(/-/g, '');
  const paddedNumber = String(orderNumber).padStart(3, '0');
  if (format === 'short') {
    return `${dateStr.slice(4)}-${paddedNumber}`;
  }
  return `${dateStr}-${paddedNumber}`;
}
```

Відповідь 3. Функція з об’єктом конфігурації:
```js

function generateOrderCode(date, orderNumber, config = {}) {
  const dateStr = date.toISOString().slice(0, 10).replace(/-/g, '');
  const paddedNumber = String(orderNumber).padStart(3, '0');

  const prefix = config.prefix || '';
  const separator = config.separator || '-';

  return `${prefix}${dateStr}${separator}${paddedNumber}`;
}
```

Відповідь 4. Клас для генерації коду:
```js
class OrderCodeGenerator {
    constructor (config = {}) {
        this.prefix = config.prefix ?? '';
        this. separator = config. separator ?? '-';
    }
    generate(date, orderNumber) {
        const dateStr = date. toISOString( ).slice(0, 10). replace(/-/g, '');
        const paddedNumber = String(orderNumber .padStart(3,
        '0' );
        return '${this.prefix}${dateStr}${this.separator}${paddedNumber}';
    }
}
```

<details>
  <summary><strong>Відповідь</strong></summary>

Кращий варіант - Варіант `1`. Проста функція з фіксованим форматом.

Чому в рамках YAGNI це правильно?

Поточна вимога — генерувати код у форматі "YYYYMMDD-NNN" (наприклад, "20231015-001"). Варіант 1 робить саме це, без додавання зайвої складності чи гнучкості, яка наразі не потрібна. Це відповідає принципу YAGNI: "Ви не потребуватимете цього" — не додавайте код, який передбачає майбутні зміни, поки вони не будуть чітко визначені.  

Варіант 2: Додавання параметра format передбачає, що в майбутньому можуть знадобитися різні формати (наприклад, короткий). Але це не потрібно зараз, і така гнучкість лише ускладнює функцію та може призвести до помилок при неправильному використанні.

Варіант 3: Об’єкт конфігурації з prefix і separator дозволяє кастомізувати код, але це надмірно для поточної задачі. Розробник фактично пише "універсальний генератор", якого ніхто не просив.

Варіант 4: Клас додає ще більше складності, вводячи стан і структуру, які не виправдані однією простою вимогою. Це типовий приклад "надінженерії"(оверхед).
</details>

---

### Задача 14 (принцип DRY)
У вас є код, який форматує дані користувача та адміна. Як написати його так, щоб уникнути повторень (`DRY`) і зберегти простоту (`KISS`)?

```js
function formatUser(user) {
  return user.name.toUpperCase() + "!" + user.age;
}
function formatAdmin(admin) {
  return admin.name.toUpperCase() + "!" + admin.age;
}
```

Відповідь 1. Залишити як є:
```js
function formatUser(user) {
  return user.name.toUpperCase() + "!" + user.age;
}
function formatAdmin(admin) {
  return admin.name.toUpperCase() + "!" + admin.age;
}
```

Відповідь 2. Створити одну функцію:
```js
function format(person) {
  return person.name.toUpperCase() + "!" + person.age;
}
```

Відповідь 3. Використати клас:
```js
class Formatter {
  formatUser(user) {
    return user.name.toUpperCase() + "!" + user.age;
  }
  formatAdmin(admin) {
    return admin.name.toUpperCase() + "!" + admin.age;
  }
}
```

Відповідь 4. Додати умовну логіку:
```js
function formatByRole(person, role) {
  if (role === "user" || role === "admin") {
    return person.name.toUpperCase() + "!" + person.age;
  }
}
```

<details>
  <summary><strong>Відповідь</strong></summary>
В рамках DRY та KISS.
<br/><br/>
Правильна відповідь: `2`. Одна функція format усуває дублювання коду (виконуючи принцип `DRY`), оскільки однакова логіка застосовується до будь-якого об’єкта з полями name та age. Вона проста і зрозуміла (що зберігає KISS), бо не додає зайвих конструкцій чи умов.
<br/><br/>
Чому інші варіанти не підходять:

- Відповідь 1. Порушує DRY, бо однакова логіка повторюється в двох функціях. Якщо потрібно змінити форматування, доведеться редагувати обидві, що ускладнює підтримку.
- Відповідь 3. Порушує KISS, бо вводить складну структуру (клас) для простої задачі. Логіка все ще дублюється в методах, тож DRY не дотримується.
- Відповідь 4. Порушує KISS через зайвий параметр role і умову, які не потрібні, адже логіка однакова для всіх. Це додає складність без користі.
</details>

---

### Задача 13 (принцип KISS)
У вас є код, який форматує дані користувача та адміна. Як написати його так, щоб уникнути повторень (DRY) і зберегти простоту (KISS)?

```js
function formatUser(user) {
  return user.name.toUpperCase() + "!" + user.age;
}
function formatAdmin(admin) {
  return admin.name.toUpperCase() + "!" + admin.age;
}
```

Відповідь 1. Залишити як є:
```js
function formatUser(user) {
  return user.name.toUpperCase() + "!" + user.age;
}
function formatAdmin(admin) {
  return admin.name.toUpperCase() + "!" + admin.age;
}
```

Відповідь 2. Створити одну функцію:
```js
function format(person) {
  return person.name.toUpperCase() + "!" + person.age;
}
```

Відповідь 3. Використати клас:
```js
class Formatter {
  formatUser(user) {
    return user.name.toUpperCase() + "!" + user.age;
  }
  formatAdmin(admin) {
    return admin.name.toUpperCase() + "!" + admin.age;
  }
}
```

Відповідь 4. Додати умовну логіку:
```js
function formatByRole(person, role) {
  if (role === "user" || role === "admin") {
    return person.name.toUpperCase() + "!" + person.age;
  }
}
```

<details>
  <summary><strong>Відповідь</strong></summary>
В рамках DRY та KISS.
<br/><br/>

Правильна відповідь: `2`. Одна функція format усуває дублювання коду (виконуючи принцип `DRY`), оскільки однакова логіка застосовується до будь-якого об’єкта з полями name та age. Вона проста і зрозуміла (що зберігає `KISS`), бо не додає зайвих конструкцій чи умов.

Чому інші варіанти не підходять:

- Відповідь 1. Порушує `DRY`, бо однакова логіка повторюється в двох функціях. Якщо потрібно змінити форматування, доведеться редагувати обидві, що ускладнює підтримку.
- Відповідь 3. Порушує `KISS`, бо вводить складну структуру (клас) для простої задачі. Логіка все ще дублюється в методах, тож `DRY` не дотримується.
- Відповідь 4. Порушує `KISS` через зайвий параметр role і умову, які не потрібні, адже логіка однакова для всіх. Це додає складність без користі.
</details>

---

### Задача 12 (про браузер та id)
У вас є наступний HTML-код:

```html
<div id="wrap">
  <h1>Click example</h1>
  <p id="clicks" style="font-size: 32px">0</p>
  <button id="button" style="width: 100px; height: 100px">
    Click to earn
  </button>
</div>
<script>
    let score = 0
    button.addEventListener('click', (e) => {
        score += 1
        clicks. innerText = score
    })
</script>
```

Що буде якщо натиснути на кнопку 1 раз?
- Відповідь 1: `1`
- Відповідь 2: `0`
- Відповідь 3: `ReferenceError: button is not defined`
- Відповідь 4: `TypeError: addEventListener is not a function`

<details>
  <summary><strong>Відповідь</strong></summary>

В даному HTML коді елементи мають `id="button"` і `id="clicks"`. У браузерах, які працюють в режимі `non-strict`, ці елементи стають глобальними змінними, доступними в window. Тому `button.addEventListener(...)` не викликає помилку, а додає обробник події кліку. Після першого кліку змінна score інкрементується й оновлює вміст елемента clicks.

Тому правильна відповідь - `1`

Це неочевидна особливість, бо:
- У JavaScript не рекомендується покладатися на глобальні змінні, створені через id.
- У strict-режимі (`'use strict'`) або при використанні модулів (`type="module"`), такий код впаде з помилкою `ReferenceError`.

Для очевидності завжди використовуйте - `document.getElementById`
</details>

---