## Задачі
- TypeScript
  - [Задача №21 (Безпечне типізування)](#задача-21)
- [Задача №20 (імутабельність)](#задача-20)
- [Задача №19 (каррування)](#задача-19)
- [Задача №18 (композиція)](#задача-18)
- Принципи
    - [Задача №17 (принцип Кента Бека)](#задача-17)
    - [Задача №16 (принцип YAGNI)](#задача-16)
    - [Задача №15 (принцип DRY)](#задача-15)
    - [Задача №14 (принцип KISS)](#задача-14)
- [Задача №13 (про оператори та їх пріоритет)](#задача-13)
- [Задача №12 (про браузер та id)](#задача-12)
- [Задача №11 (про генератори)](#задача-11)
- [Задача №10 (про замикання)](#задача-10)
- [Задача №9 (про чисті словники)](#задача-9)
- [Задача №8 (про асинхроннe програмування частина 3)](#задача-8)
- [Задача №7 (про IIFE)](#задача-7)
- [Задача №6 (про рест-параметри)](#задача-6)
- [Задача №5 (про асинхроннe програмування частина 2)](#задача-5)
- [Задача №4 (про асинхроннe програмування частина 1)](#задача-4)
- [Задача №3 (про області видимості змінних)](#задача-3)
- [Задача №2 (про this)](#задача-2)
- [Задача №1 (про підняття / hoisting)](#задача-1)

---

### Задача 21
У нас є функція getPrice котра ламається, якщо price не число. Ми потребуємо певного рішення, щоб типізувати getPrice, щоб уникнути помилок?

Виберіть варіант, який забезпечує типобезпеку.

Приклад даних:
```js
function getPrice(item) {
  return item.price;
}
const item1 = { price: 10 };
const item2 = { price: "10" };
```

Відповідь 1.
```js
interface Item {
  price: number;
}
const getPrice = (item: Item): number => item.price;

const item1: Item = { price: 10 };
const item2 = { price: "10" };
```

Відповідь 2.
```js
const getPrice = (item: any): number => item.price;

const item1 = { price: 10 };
const item2 = { price: "10" };
```

Відповідь 3.
```js
const getPrice = (item: { price: any }): number => item.price;

const item1 = { price: 10 };
const item2 = { price: "10" };
```

Відповідь 4.
```js
const getPrice = (item: object): number => {
  return typeof item.price === "number" ? item.price : 0;
};

const item1 = { price: 10 };
const item2 = { price: "10" };
```

<details>
  <summary><strong>Відповідь</strong></summary>

TypeScript додає статичну типізацію, виявляючи помилки на етапі компіляції. Варіант 1 використовує інтерфейс Item для суворого визначення price як числа, запобігаючи некоректним даним (наприклад, price: "`10`"). Це простий вступ до TypeScript, що показує типобезпеку, зрозумілий новачкам і корисний для досвідчених.

Чому інші варіанти гірші:

- Варіант 2: `any` втрачає переваги TypeScript, дозволяючи будь-які дані.
- Варіант 3: price: `any` не захищає від нечислових значень.
- Варіант 4: Перевірка під час виконання суперечить меті TypeScript — виявляти помилки на компіляції.

</details>
---

### Задача 20
У системі бронювання готелів масив reservations містить інформацію про бронювання. Користувач хоче змінити статус бронювання з id: 101 на "Confirmed". Пряме оновлення даних у масиві часто викликає помилки: наприклад, інші функції, що відображають статистику, бачать неочікувані зміни, а скасування операції стає складним через втрату початкового стану.

Як реалізувати функцію, щоб безпечно оновити статус?

Приклад даних:
```js
const reservations = [
  { id: 101, room: "A1", status: "Pending" },
  { id: 102, room: "B2", status: "Confirmed" }
];
```

Відповідь 1.
```js
const updateReservationStatus = (reservations, id, newStatus) => {
  return reservations.map(res => 
    res.id === id ? { ...res, status: newStatus } : res
  );
};
const updatedReservations = updateReservationStatus(reservations, 101, "Confirmed");
```

Відповідь 2.
```js
const updateReservationStatus = (reservations, id, newStatus) => {
  const res = reservations.find(res => res.id === id);
  if (res) res.status = newStatus;
  return reservations;
};
const updatedReservations = updateReservationStatus(reservations, 101, "Confirmed");
```

Відповідь 3.
```js
const updateReservationStatus = (reservations, id, newStatus) => {
  const newReservations = reservations.slice();
  const res = newReservations.find(res => res.id === id);
  if (res) res.status = newStatus;
  return newReservations;
};
const updatedReservations = updateReservationStatus(reservations, 101, "Confirmed");
```

Відповідь 4.
```js
const updateReservationStatus = (reservations, id, newStatus) => {
  const index = reservations.findIndex(res => res.id === id);
  if (index !== -1) {
    reservations[index].status = newStatus;
  }
  return reservations;
};
const updatedReservations = updateReservationStatus(reservations, 101, "Confirmed");
```

<details>
  <summary><strong>Відповідь</strong></summary>

Імутабельність у JavaScript — це створення нових даних без зміни оригіналу, що забезпечує стабільність і уникає побічних ефектів. У варіанті 1 метод map генерує новий масив, а spread-оператор (`...`) копіює об’єкт із новим статусом. Це декларативний підхід, який не мутує початковий стан, що ідеально для React чи Redux. Код читабельний, простий і підходить для масштабування.

Чому інші варіанти гірші:

- Варіант 2: Мутує об’єкт напряму (`res.status = newStatus`), порушуючи імутабельність.
- Варіант 3: Копіює масив (`slice`), але мутує об’єкт усередині, що не відповідає принципу.
- Варіант 4: Змінює оригінальний масив через індекс, ламаючи стабільність даних.

</details>

---

### Задача 19
У веб-магазині потрібно форматувати ціну: додати валюту, округлити до двох знаків, застосувати знижку. Вимога: створити гнучку функцію formatPrice для "`$90.00`" з `100`, знижки `0.1`, валюти "`$`". 

Як реалізувати, щоб функція дозволяла поетапно задавати параметри, забезпечувала модульність і гнучкість?

Відповідь 1. Композиція чистих функцій:
```js
const applyDiscount = (price, discount) => price * (1 - discount);
const formatCurrency = (price, currency) => `${currency}${price.toFixed(2)}`;

const compose = (...fns) => x => fns.reduceRight((acc, fn) => fn(acc), x);
const formatPrice = compose(
  price => formatCurrency(price, "$"),
  price => applyDiscount(price, 0.1)
);

const result = formatPrice(100); // "$90.00"
```

Відповідь 2. Каррування:
```js
const formatPrice = currency => discount => price => {
  const discounted = price * (1 - discount);
  return `${currency}${discounted.toFixed(2)}`;
};
const result = formatPrice("$")(0.1)(100); // "$90.00"
```

Відповідь 3. Одна функція з усією логікою:
```js
const formatPrice = (price, discount, currency) => {
  const discounted = price * (1 - discount);
  return `${currency}${discounted.toFixed(2)}`;
};
const result = formatPrice(100, 0.1, "$"); // "$90.00"
```

Відповідь 4. Часткове застосування:
```js
const formatPrice = (currency, discount, price) => {
  const discounted = price * (1 - discount);
  return `${currency}${discounted.toFixed(2)}`;
};
const formatPriceUSD = formatPrice.bind(null, "$", 0.1);
const result = formatPriceUSD(100); // "$90.00"
```

<details>
  <summary><strong>Відповідь</strong></summary>

Каррування розбиває функцію з кількома аргументами на послідовність функцій з одним аргументом, забезпечуючи гнучке часткове застосування. Варіант 2 реалізує `formatPrice` як каррувану функцію, дозволяючи поетапно задавати `currency`, `discount`, `price`. Це підвищує модульність і повторне використання (наприклад, `formatPrice("$")`). На відміну від композиції, каррування фокусується на аргументах, а не потоці даних.

Чому інші варіанти не відповідають:

Варіант 1: Застосовує композицію, комбінуючи функції для обробки потоку даних, але не дозволяє поетапно задавати аргументи, як каррування.
Варіант 3: Монолітна функція приймає всі аргументи одразу, втрачаючи гнучкість і модульність каррування.
Варіант 4: Часткове застосування через bind фіксує аргументи, але не створює послідовність функцій, обмежуючи гнучкість каррування.

Різниця між карруванням і композицією:

Каррування: Перетворює `f(a, b, c)` у `f(a)(b)(c)`, дозволяючи створювати спеціалізовані функції поетапно (наприклад, `formatPrice("$")(0.1)`).
Композиція: Об’єднує функції, де вихід однієї є входом іншої (варіант 1), фокусуючись на трансформації даних, а не на аргументах.

</details>

<details>
  <summary><strong>Додатково</strong></summary>

Каррування та композиція — потужні інструменти функціонального програмування, але застосовуються в різних сценаріях. Каррування оптимальне для створення спеціалізованих функцій шляхом поетапного задання аргументів. Композиція краще підходить для послідовної трансформації даних через об’єднання функцій. Вибір залежить від задачі: гнучкість аргументів (каррування) чи потік обробки (композиція).

Коли використовувати каррування:
- Спеціалізація функцій: Якщо потрібно створювати функції з фіксованими параметрами для повторного використання. Наприклад, у веб-додатку для форматування цін каррування дозволяє створити `formatUSD = formatPrice("$")`, щоб фіксувати валюту, а потім застосовувати різні знижки чи ціни.
- API з налаштуваннями: У бібліотеках, де користувач задає параметри поетапно, як у конфігураціях логерів (`createLogger("DEBUG")("console")`).
- Гнучкість у динамічних контекстах: Наприклад, у обробці подій, де параметри (наприклад, тип події) задаються поступово.

Коли використовувати композицію:
- Послідовна обробка даних: Якщо потрібно виконати кілька трансформацій над даними, де вихід однієї функції є входом іншої. Наприклад, у обробці тексту: `compose(addPrefix, capitalize, trim)` для форматування імені.
- Пайплайн даних: У обробці потоків, як у UI-рендерингу, де дані проходять через фільтри, мапінг і форматування (наприклад, `compose(render, filter, sort)`).
- Декларативні трансформації: Для чіткого опису ланцюжка обробки, як у парсингу JSON чи підготовці даних для API.

Практичні рекомендації:
- Використовуйте каррування, якщо задача вимагає створення сімейства функцій із заздалегідь заданими параметрами, особливо в сценаріях із динамічними конфігураціями.
- Використовуйте композицію, коли є чіткий потік даних, і потрібно поєднати кілька операцій у логічний ланцюжок.
- Комбінуйте за потреби: У складних системах каррування може створювати спеціалізовані функції, які потім комбінуються через композицію. Наприклад, каррувана функція для логування комбінується з функцією форматування виводів.

</details>

---

### Задача 18
У веб-додатку потрібно форматувати дані користувача для відображення: обрізати пробіли в імені, зробити першу літеру великою, додати префікс "User:". 

Як реалізувати formatUserName для `" john " → "User: John"`? Виберіть варіант, який найкраще застосовує композицію чистих функцій, забезпечуючи модульність і простоту.

Відповідь 1. Композиція чистих функцій:
```js
const trim = str => str.trim();
const capitalize = str => str.charAt(0).toUpperCase() + str.slice(1).toLowerCase();
const addPrefix = str => `User: ${str}`;
const compose = (...fns) => x => fns.reduceRight((acc, fn) => fn(acc), x);
const formatUserName = compose(addPrefix, capitalize, trim);
```

Відповідь 2. Одна функція з усією логікою:
```js
const formatUserName = str => {
  const trimmed = str.trim();
  const capitalized = trimmed.charAt(0).toUpperCase() + trimmed.slice(1).toLowerCase();
  return `User: ${capitalized}`;
};
```

Відповідь 3. Чисті функції з послідовним викликом:
```js
const trim = str => str.trim();
const capitalize = str => str.charAt(0).toUpperCase() + str.slice(1).toLowerCase();
const addPrefix = str => `User: ${str}`;
const formatUserName = str => addPrefix(capitalize(trim(str)));
```

Відповідь 4. Функція з валідацією:
```js
const trim = str => str.trim();
const capitalize = str => str.charAt(0).toUpperCase() + str.slice(1).toLowerCase();
const addPrefix = str => (str ? `User: ${str}` : "User: Unknown");
const formatUserName = str => {
  const trimmed = trim(str);
  return trimmed ? addPrefix(capitalize(trimmed)) : addPrefix("");
};
```

<details>
  <summary><strong>Відповідь</strong></summary>

Композиція чистих функцій поєднує незалежні, детерміновані функції для створення модульного коду. Варіант 1 використовує compose для об’єднання `trim`, `capitalize` та `addPrefix`, кожна з яких виконує єдину задачу. Це забезпечує декларативність, повторне використання та легке тестування. Код інтуїтивний для новачків і відповідає функціональній парадигмі для досвідчених розробників.

Чому інші варіанти не відповідають:

- Варіант 2: Монолітна функція втрачає модульність, ускладнює повторне використання та тестування.
- Варіант 3: Послідовний виклик чистих функцій не використовує композицію, знижуючи абстракцію та гнучкість.
- Варіант 4: Валідація для порожніх рядків додає надлишкову логіку, не передбачену вимогою, порушуючи простоту.

</details>

---

### Задача 17
Ви працюєте над системою управління задачами для невеликої команди. Поточна вимога: вивести список із назвою та статусом (наприклад, "Write report (To Do)"). Вимога проста, але команда пропонує додати сортування, фільтри чи класи "на майбутнє". Як реалізувати displayTasks, щоб відповідати принципу Кента Бека? 

Початковий код:
```js
const tasks = [
  { id: 1, title: "Write report", status: "To Do" },
  { id: 2, title: "Review code", status: "Done" },
  { id: 3, title: "Test feature", status: "To Do" }
];
```

Відповідь 1. Базова функція з форматуванням:
```js
function displayTasks(tasks) {
  return tasks.map(task => `${task.title} - ${task.status}`).join('\n');
}
```

Відповідь 2. Функція з виправленням статусу:
```js
function displayTasks(tasks) {
  return tasks.map(task => {
    const status = task.status === "To Do" ? "Pending" : "Completed";
    return `${task.title} - ${status}`;
  }).join('\n');
}
```

Відповідь 3. Функція з підтримкою порожнього списку:
```js
function displayTasks(tasks) {
  if (!tasks.length) return "No tasks available";
  return tasks.map(task => `${task.title} - ${task.status}`).join('\n');
}
```

Відповідь 4. Функція з додаванням ID:
```js
function displayTasks(tasks) {
  return tasks.map(task => `[${task.id}] ${task.title} - ${task.status}`).join('\n');
}
```

<details>
  <summary><strong>Відповідь</strong></summary>

Принцип Кента Бека "Do the Simplest Thing That Could Possibly Work" вимагає мінімального рішення, яке виконує специфікацію. Варіант 1 відображає назву та статус задач, використовуючи `map` для форматування та `join` для об’єднання рядків. Це лаконічний, декларативний код, що відповідає вимозі без надлишкової логіки, забезпечуючи легкість розуміння та модифікації для розробників будь-якого рівня.

Чому інші варіанти не відповідають:

- Варіант 2: Трансформація статусів (`To Do → Pending, Done → Completed`) додає логіку, не передбачену вимогою, ускладнюючи код та порушуючи простоту.
- Варіант 3: Обробка порожнього списку повертає повідомлення, але це не вимагається, що вводить зайву умовну логіку.
- Варіант 4: Включення `ID` у вивід передбачає додаткову потребу, не вказану в специфікації, додаючи непотрібне форматування.

</details>

---

### Задача 16
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

### Задача 15
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

### Задача 14
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


### Задача 13

У вас є наступний JS-код:

```js
let a = 5;
const b = ++a / a++;

console.log(a, b);
```

Варіанти відповідей:

- A) 6, 1
- B) 7, 0.71428....
- C) 7, 1
- D) 6, 0.71428....

<details>
  <summary><strong>Відповідь</strong></summary>
 
`++a` — префікс: спочатку збільшує a до `6`, потім повертає `6`.
`a++` — постфікс: повертає `6`, але збільшує a до `7` після.
Тобто: `b = 6 / 6`, результат — `1`.
Після виконання: `a = 7, b = 1`. ВІдповідь - С)

В JavaScript порядок обчислення виразів з інкрементами та побічними ефектами критично впливає на результат. Такі конструкції краще уникати для читабельності та відсутності багів. Це гарний приклад, як тонкощі семантики можуть спричинити несподівану поведінку навіть у простому коді.
</details>

---

### Задача 12

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

### Задача 11
А що ви знаєте про генератори і коли варто їх використовувати?

У вас є наступний JS-код:

```js
function fetchPage(page) {
  return new Promise(res =>
    setTimeout(() => res({ data: `Page ${page}`, hasMore: page < 2 }), 100)
  );
}

function* loader() {
  let page = 1;
  while (true) {
    const result = yield fetchPage(page);
    if (!result.hasMore) return `Loaded ${page} pages`;
    page++;
  }
}

const gen = loader();
gen.next().value
  .then(r1 => {
    console.log(r1.data);              // ?
    return gen.next(r1).value;
  })
  .then(r2 => {
    console.log(r2.data);              // ?
    console.log(gen.next(r2).value);   // ?
  });
```

Яка відповідь правильна:

- A) Page 1, Page 2, Loaded 2 pages
- B) Page 1, Loaded 2 pages, undefined
- C) Page 1, Page 2, undefined
- D) Нічого

<details>
  <summary><strong>Відповідь</strong></summary>

Правильна відповідь: A
Генератор «`loader`» `yield`-ить проміс для завантаження сторінок. Перший виклик gen.next() повертає обіцянку з першою сторінкою ("Page 1"). Після резолву результат передається в генератор через `next(r1)`, який `yield`-ить другий проміс з даними "Page 2". Після резолву другого промісу, генератор бачить, що `hasMore` стає `false`, і повертає рядок "Loaded 2 pages". 

Генератори корисні, коли потрібно контролювати послідовність виконання та зберігати стан між ітераціями, особливо для складних або нескінченних процесів. Сучасний `async/await` добре підходить для простих сценаріїв, але генератори дозволяють "припиняти" виконання та відновлювати його за запитом.

Також незамінний для ситуацій, коли потрібно поступово генерувати дані за вимогою, контролюючи потік обчислень. Наприклад унікальні ідентифікатори без повторень
</details>

---

### Задача 10
А що ви знаєте про генератори і коли варто їх використовувати?

У вас є наступний JS-код:

```js
function fetchPage(page) {
  return new Promise(res =>
    setTimeout(() => res({ data: `Page ${page}`, hasMore: page < 2 }), 100)
  );
}

function* loader() {
  let page = 1;
  while (true) {
    const result = yield fetchPage(page);
    if (!result.hasMore) return `Loaded ${page} pages`;
    page++;
  }
}

const gen = loader();
gen.next().value
  .then(r1 => {
    console.log(r1.data);              // ?
    return gen.next(r1).value;
  })
  .then(r2 => {
    console.log(r2.data);              // ?
    console.log(gen.next(r2).value);   // ?
  });
```

Яка відповідь правильна:

- A) Page 1, Page 2, Loaded 2 pages
- B) Page 1, Loaded 2 pages, undefined
- C) Page 1, Page 2, undefined
- D) Нічого

<details>
  <summary><strong>Відповідь</strong></summary>

Правильна відповідь: A
Генератор «`loader`» `yield`-ить проміс для завантаження сторінок. Перший виклик gen.next() повертає обіцянку з першою сторінкою ("Page 1"). Після резолву результат передається в генератор через `next(r1)`, який `yield`-ить другий проміс з даними "Page 2". Після резолву другого промісу, генератор бачить, що `hasMore` стає `false`, і повертає рядок "Loaded 2 pages". 

Генератори корисні, коли потрібно контролювати послідовність виконання та зберігати стан між ітераціями, особливо для складних або нескінченних процесів. Сучасний `async/await` добре підходить для простих сценаріїв, але генератори дозволяють "припиняти" виконання та відновлювати його за запитом.

Також незамінний для ситуацій, коли потрібно поступово генерувати дані за вимогою, контролюючи потік обчислень. Наприклад унікальні ідентифікатори без повторень
</details>

---

### Задача 9

У вас є наступний JS-код:

```js
Object.create(null).hasOwnProperty('toString');
```

Що ми отримаємо?

- A) true
- B) false
- C) undefined
- D) TypeError

<details>
  <summary><strong>Відповідь</strong></summary>

На перший погляд здається, що будь-який об'єкт у JS має базові методи, як-от `toString` чи `hasOwnProperty`, що є частиною прототипу `Object.prototype`, однак `Object.create(null)` — виняток.

Але коли ми викликаємо `Object.create(null)`, ми створюємо об'єкт без прототипу. У нього немає методів з `Object.prototype`, тому виклик o`bj.hasOwnProperty(...)` призводить до `TypeError`

Такі об'єкти часто використовують для створення чистих словників (dictionary) без конфлікту з ключами-прототипами. 

Такий виняток ми можемо отримати у випадку коли потрібно створити об'єкт, який успадковує властивості та методи від існуючого об'єкта, але ми передали null. Зазвичай це відбувається через помилку очікування, що typeof нам пропустить тільки типи `object`. Але `typeof null == 'object'` і цьому випадку ми можемо потрапити в пастку.
</details>

---

### Задача 8

У вас є наступний JS-код:

```js
console.log("1");

setTimeout(() => console.log("2"), 0);

Promise.resolve()
    .then(() => console.log("3"))
    .then(() => {
        console.log("4");
        queueMicrotask(() => console.log("5"));
    });

Promise.resolve().then(() => console.log("6"));

queueMicrotask(() => console.log("7"));

console.log("8");
```

Що ми отримаємо?

- A) 1, 8, 3, 6, 7, 4, 5, 2
- B) 1, 8, 3, 6, 4, 5, 7, 2
- C) 1, 8, 7, 3, 6, 4, 5, 2
- D) 1, 8, 3, 4, 6, 7, 5, 2

<details>
  <summary><strong>Відповідь</strong></summary>

Правильна відповідь - А)

Задача демонструє пріоритетність різних типів задач у JavaScript Event Loop: синхронні (Call Stack) → мікрозадачі (MicroTask Queue) → макрозадачі (Task Queue).

Спочатку виконуються синхронні (1, 8), потім мікрозадачі (проміси та queueMicrotask: 3, 6, 7, 4, 5 у порядку постановки, оскільки мікрозадачі так само працюють за приниципом FIFO), а після них уже макрозадачі (setTimeout: 2)

Важливо пам'ятати, що мікрозадачі працюють аналогічно до макрозадачі, але мають вищий пріорітет виконання.
</details>

---


### Задача 7

У вас є наступний JS-код:

```js
function sayHi() {
    return (() => 0)();
}

typeof sayHi();
```

Що ми отримаємо?

- A) "object"
- B) "number"
- C) "function"
- D) "undefined"

<details>
  <summary><strong>Відповідь</strong></summary>

Функція `sayHi` повертає результат виконання IIFE — стрілочної функції `(() => 0)()`, яка одразу викликається й повертає 0. Отже, `sayHi()` повертає `0`, а `typeof sayHi()` — це `typeof 0`, тобто "`number`". Це демонструє, як працюють IIFE та як `typeof` визначає тип значення. Важливо: функції в JS мають тип "`function`", але технічно це підтип "`object`", згідно з внутрішньою структурою мови.

Правильна відповідь - B)
</details>

---

У вас є наступний JS-код:

```js
console.log("1");

setTimeout(() => console.log("2"), 0);

Promise.resolve()
    .then(() => console.log("3"))
    .then(() => {
        console.log("4");
        queueMicrotask(() => console.log("5"));
    });

Promise.resolve().then(() => console.log("6"));

queueMicrotask(() => console.log("7"));

console.log("8");
```

Що ми отримаємо?

A) 1, 8, 3, 6, 7, 4, 5, 2
B) 1, 8, 3, 6, 4, 5, 7, 2
C) 1, 8, 7, 3, 6, 4, 5, 2
D) 1, 8, 3, 4, 6, 7, 5, 2

<details>
  <summary><strong>Відповідь</strong></summary>

Правильна відповідь - А)

Задача демонструє пріоритетність різних типів задач у JavaScript Event Loop: синхронні (Call Stack) → мікрозадачі (MicroTask Queue) → макрозадачі (Task Queue).

Спочатку виконуються синхронні (1, 8), потім мікрозадачі (проміси та queueMicrotask: 3, 6, 7, 4, 5 у порядку постановки, оскільки мікрозадачі так само працюють за приниципом FIFO), а після них уже макрозадачі (setTimeout: 2)

Важливо пам'ятати, що мікрозадачі працюють аналогічно до макрозадачі, але мають вищий пріорітет виконання.
</details>

---


### Задача 6

У вас є наступний JS-код:

```js
function getAge(...args) {
    console.log(typeof args);
}

getAge(21);
```

Що ми отримаємо?

- A) "number"
- B) "array"
- C) "object"
- D) "NaN"

<details>
  <summary><strong>Відповідь</strong></summary>

Правильна відповідь - С

`...args` — це rest-параметр, який збирає всі передані аргументи у масив. Але в JavaScript масиви — це спеціальний тип об'єкта, тому `typeof args` повертає "`object`". Це часто плутає розробників, адже `Array.isArray(args)` поверне `true`, але `typeof` не розпізнає масиви окремо. Цей приклад підкреслює важливість розуміння типів у JS і того, як працює typeof на низькому рівні. 
</details>

---

### Задача 5

У вас є наступний JS-код:

```js
function first() {
    second();
    setTimeout(function secondT() { console.log('4') }, 0);
    console.log('1');
}

function second() {
    setTimeout(function thirdT() { console.log('5') }, 0);
    console.log('2');
}

setTimeout(function firstT() { console.log('3') }, 0);

first()
```

Що ми отримаємо?

- A) 1 → 2 → 3 → 4 → 5
- B) 5 → 2 → 4 → 1 → 3
- C) 3 → 4 → 2 → 5 → 1
- D) 2 → 1 → 3 → 5 → 4

<details>
  <summary><strong>Відповідь</strong></summary>

Першим виконується `first()`, який викликає `second()`. У `second()` одразу логується '`2`', а `setTimeout` ставить '`5`' у Task Queue. Потім у `first()` логується '`1`', а ще один `setTimeout` ставить '`4`' у чергу.

До виклику `first()`, ще один `setTimeout` вже поставив '`3`' у чергу.

Call Stack (LIFO) завершує синхронний код (2, 1), а потім Event Loop виконує асинхронні колбеки (3, 5, 4) у Task Queue (FIFO).

Результат: 2 → 1 → 3 → 5 → 4. А правильна відповідь - `D`

LIFO (Last In, First Out) — це принцип, за яким останній доданий елемент обробляється першим. Call Stack працює за цим принципом, щоб коректно виконувати вкладені функції. Якщо A викликає B, а B — C, то C має завершитись першою, і лише потім — B і A. Це забезпечує правильне збереження контексту виконання та повернення до попередньої точки виклику, що критично для стабільної роботи програми.

FIFO (First In, First Out) — це принцип, за яким першим обробляється те, що надійшло першим. Task Queue (черга завдань) працює саме так, щоб асинхронні операції виконувалися у тому порядку, в якому були поставлені. Це гарантує передбачуваність — наприклад, якщо два setTimeout додані послідовно, то й виконаються вони у тому ж порядку, зберігаючи логіку програми та уникнення хаотичної поведінки.

Ці моделі були обрані для JavaScript через його однопоточну природу та орієнтацію на інтерактивність. `LIFO` у стеку дозволяє миттєво обробляти вкладені виклики, зменшуючи накладні витрати на зберігання стану. `FIFO` у чергах — ключ до передбачуваного асинхронного виконання: події, таймери та колбеки не блокують головний потік, але все ж виконуються у порядку, зручному для розробника та безпечному для UX. 
</details>

---

### Задача 4

У вас є наступний JS-код:

```js
const foo = () => console.log("1");
const bar = () => setTimeout(() => console.log("2"));
const baz = () => console.log("3");

bar();
foo();
baz();
```

Що ми отримаємо?

- A) 1 2 3
- B) 1 3 2
- C) 2 1 3
- D) 2 3 1

<details>
  <summary><strong>Відповідь</strong></summary>

Функція `bar` викликає `setTimeout`, який ставить колбек у Task Queue. Він виконається лише після завершення всього синхронного коду в Call Stack.

Після виклику `bar()`, виконуються `foo()` та `baz(`) — обидві синхронні, тому одразу потрапляють у стек і виконуються: виводиться 1 і 3.

Лише після цього Event Loop бере з Task Queue функцію `() => console.log("2")`, і вона виводиться останньою.

Тому результат: 1 3 2 і правильна відповідь B  
</details>

---

### Задача 3

У вас є наступний JS-код:

```js
for (var i = 0; i < 3; i++) {
    setTimeout(() => console.log(i), 1);
}

for (let i = 0; i < 3; i++) {
    setTimeout(() => console.log(i), 1);
}
```

Що ми отримаємо?

- A) 0 1 2 та 0 1 2
- B) 0 1 2 та 3 3 3
- C) 3 3 3 та 0 1 2

<details>
  <summary><strong>Відповідь</strong></summary>

Правильна відповідь - С

У першому циклі (`var i`), змінна i має функціональну область видимості, тобто однакова для всіх ітерацій. `setTimeout` виконується після завершення циклу, коли i вже дорівнює 3, тому кожен виклик `console.log(i)` виводить 3.

У другому циклі `(let i)`, i має блокову область видимості. Для кожної ітерації створюється новий контекст змінної, що замикає поточне значення i. Тому `console.log(i)` виведе 0, 1, 2.

Про Call Stack та Task Queue

Call Stack — це структура даних LIFO, де виконуються функції. Спочатку синхронний код додається в стек і виконується. При виклику setTimeout, його зворотний виклик передається в Task Queue після завершення таймера, а цикл продовжується.

Task Queue — це спецальна черга, що містить відкладені колбеки (setTimeout, setInterval, setImmediate) та працює за принципом FIFO. Вони виконуються лише після очищення Call Stack, коли головний потік готовий їх обробити.
</details>

---

### Задача 2

У вас є наступний JS-код:

```js
const shape = {
    radius: 10,
    diameter() {
        return this.radius * 2;
    },
    perimeter: () => 2 * Math.PI * this.radius
};

shape.diameter();
shape.perimeter();
```

Що ми отримаємо?

- A) 20 та 62.83185307179586
- B) 20 та NaN
- C) 20 та 63
- D) NaN та 63

<details>
  <summary><strong>Відповідь</strong></summary>

В JavaScript існують глобальна, функціональна, блокова, лексична області видимості та контекст виконання (`this`). Глобальна область охоплює весь код, функціональна обмежує змінні межами функції, блокова — скобками `{}` (тільки для `let` і `const`). Лексична область визначає доступність змінних за місцем оголошення. this динамічно змінюється залежно від виклику, а в стрілкових функціях наслідує значення з лексичного контексту.

У методі diameter використовується звичайна функція, яка бере `this` з контексту об'єкта `shape`, тому `this.radius` дорівнює 10, і результат — 20. У `perimeter` використана стрілкова функція, яка не має власного `this` і бере його зі зовнішнього лексичного контексту (глобального об'єкта window або `globalThis`). У цьому контексті `radius` не визначено, тому `this.radius` дає `undefined`, а вираз `2 * Math.PI * undefined` повертає `NaN`.
</details>

---

### Задача 1

У вас є наступний JS-код:

```js
function sayHi() {
    console.log(name);
    console.log(age);
    var name = "My Name";
    let age = 21;
}

sayHi();
```

Що ми отримаємо?

- A) My Name та undefined
- B) My Name та ReferenceError
- C) ReferenceError та 21
- D) undefined та ReferenceError 

<details>
  <summary><strong>Відповідь</strong></summary>

При виклику `sayHi()` змінна name, оголошена через `var`, піднімається (`hoisting`) і має значення `undefined` до ініціалізації, тому `console.log(name)` виведе `undefined`. Натомість age, оголошена через let, також піднімається, але залишається в "тимчасовій мертвій зоні" (TDZ) до її ініціалізації. Спроба доступу до `age` до її оголошення викликає `ReferenceError`. Це демонструє різницю між `var` і `let` у контексті `hoisting` та `TDZ`.

 А отже правильна відповідь - D.
</details>

---
