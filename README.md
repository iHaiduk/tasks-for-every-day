## Задачі
- Принципи
    - [Задача 15 (принцип YAGNI)](#задача-15-принцип-yagni)
    - [Задача 14 (принцип DRY)](#задача-14-принцип-dry)
    - [Задача 13 (принцип KISS)](#задача-13-принцип-kiss)
- [Задача 12](#задача-12)
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
Ви працюєте над проєктом для обробки замовлень у невеликому онлайн-магазині. Наразі єдина вимога — генерувати унікальний код замовлення на основі дати та порядкового номера замовлення за день (наприклад, "20231015-001").

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
 Скоро
</details>

---

### Задача 14 (принцип DRY)
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
Правильна відповідь: 2. Одна функція format усуває дублювання коду (виконуючи принцип DRY), оскільки однакова логіка застосовується до будь-якого об’єкта з полями name та age. Вона проста і зрозуміла (що зберігає KISS), бо не додає зайвих конструкцій чи умов.
<br/><br/>
Чому інші варіанти не підходять:

- Відповідь 1. Порушує DRY, бо однакова логіка повторюється в двох функціях. Якщо потрібно змінити форматування, доведеться редагувати обидві, що ускладнює підтримку.
- Відповідь 3. Порушує KISS, бо вводить складну структуру (клас) для простої задачі. Логіка все ще дублюється в методах, тож DRY не дотримується.
- Відповідь 4. Порушує KISS через зайвий параметр role і умову, які не потрібні, адже логіка однакова для всіх. Це додає складність без користі.
</details>

---

### Задача 13 (принцип KISS)
У вас є функція, яка обчислює суму чисел у масиві. Ваша мета — реалізувати її якнайпростіше, дотримуючись принципу KISS

Який із наведених варіантів є найкращим застосуванням цього принципу?

Відповідь 1:
```js
function sum(arr) {
  return arr.reduce((total, num) => total + num, 0);
}
```

Відповідь 2:
```js
function sum(arr) {
  let total = 0;
  if (Array.isArray(arr)) {
    for (let i = 0; i < arr.length; i++) {
      if (typeof arr[i] === 'number') {
        total += arr[i];
      } else {
        total += Number(arr[i]);
      }
    }
    return total;
  } else {
    return 0;
  }
}
```

Відповідь 3:
```js
function sum(arr) {
  let total = 0;
  arr.forEach(function(item) {
    total = total + item;
  });
  return total;
}
```

Відповідь 4:
```js
function sum(arr) {
  return arr.length ? sum(arr.slice(1)) + arr[0] : 0;
}
```

<details>
  <summary><strong>Відповідь</strong></summary>
Щоб мати гарний та зрозумілий код треба використовувати певні принципи. Один з них це - KISS.
<br><br>
KISS (Keep It Simple, Stupid) — закликає писати максимально прості рішення, без зайвої абстракції, перевірок чи оптимізацій "на майбутнє". Це знижує кількість помилок, спрощує читання, тестування та підтримку коду. Простий код — це код, який легше зрозуміти, змінити й масштабувати.
Під ций прицип підпадає відповідь - 1
<br><br>
Щодо варіантів А та В.
<br><br>
KISS — це не про «найбільш безпечний варіант за будь-яких обставин», а про рішення, яке виконує рівно те, що сказано в умові, без зайвого ускладнення. Варіант B — намагається вирішити гіпотетичні проблеми, яких у задачі не поставлено. В задачі чітко сказано — “масив чисел”.
Також маємо розуміти, що sum не має відповідати за валідність аргументів, якщо вона — частина контрольованого внутрішнього середовища і тим паче вирішувати, що повертати у випадку не валідних даних.
<br><br>
Але у випадку sum, що ця функція є частиною публічного API або буде викликатись із непередбачуваних джерел, то KISS не забороняє перевірки і може бути частиною бізнес-логіки, а не зайвим ускладненням.
В такому випадку кращим кроком буде повідомити про невідповідність даних на вході, замість прийняття рішення повернення 0. Оскільки "0" в такому випадку може бути результат суми чи помилки і функціє вирішує більше ніж від неї необхідно.
<br><br>

```js
function sum(arr) {
  if (!Array.isArray(arr)) throw new TypeError('Expected an array of numbers');
  return arr.reduce((sum, num) => sum + num, 0);
}
```

</details>

---