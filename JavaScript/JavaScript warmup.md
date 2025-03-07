 لغة برمجه صممت من قبل بريىددان آتش، مخصصه للتعامل مع الويب وهي لغة قوية جدا وصديقة للمبتدئين.
جافاسكريت نفسها مضغوطه نسبيا لكنا مرنه جدا والمطورين قدروا انهم يبنوا فوق اللغة نفسها مجموعة من الأدوات اللي تساعد في انها تخلي الشغل اسرع واسهل
### أول كود ليك "مرحبا أيها العالم!"
```javascript
const myHeading = document.querySelector("h1");
myHeading.textContent = "Hello world!"
```
هنا عملنا متغير جديد اسمه `myHeading` وخلناه يختار عنصر من عناصر كود `html` وبعدين يغير محتواه ل hello world!.
### المتغيرات
المتغيرات هي عباة عن عناصر بيتم تخزين فيها قيم معينه من انو البيانات المعروفة في اللغة 
في اكتر من طريقة ننشئ بيها متغير 
#### var:
طريقة قديمة عشان ننشئ بيها متغير وللأسف مشاكلها كتير منها انك تقدر تنشي بيها اكتر من متغير بنفس الأسم ,
#### let: 
الكلمه دي بعدها اسم المتغير بعملك متغير جديد حلت كتير من المشاكل الخاصة ب `var`
#### const:
طريقة تنشي بيها متغير ثابت غير قابل لتغيير قيمته



| Feature       | var      | let   | const |
| ------------- | -------- | ----- | ----- |
| scope         | Function | Block | Block |
| Hoisted?      | Yes      | Yes   | Yes   |
| Reassignment  | Yes      | Yes   | NO    |
| Redeclarition | Yes      | NO    | No    |

### أنواع البيانات

| المتغير | الشرح                                                 | مثال                                      |
| ------- | ----------------------------------------------------- | ----------------------------------------- |
| string  | مجموعة من الحروف تشكل نص وبتتميز بعلامات التنصيص      | `myvar = 'hoba';`                         |
| Number  | عبارة عن رقم ومفهوش تنصيص                             | `myvar = 10;`                             |
| Boolean | ممكن يحتمل قيميتين `ture` او `false`                  | `myvar = true;`                           |
| Array   | مصفوفة تسمحلك انك تخزن فيها اكتر من نوع من البيانات   | `my var = [1, 'hoba', true]`              |
| Object  | هو عبارة عن اي وكل حاجه في اللعة وممكن تخزنها ف متغير | `myvar = document.query sellector('h1');` |
اللغة دي `dynamic types` يعني منتاش محتاج تحدد نوع الييانات اللي هتتحط في المتغير(اي متغير يقبل يتحط فيه اي نوع من البيانات ويتعين لاي نوع تاني)
```js
let foo = 42; // foo is now a number
foo = "bar"; // foo is now a string
foo = true; // foo is now a boolean
```
كمان هي لغة `weakly typed` يعني هي عادي تسمح انها تعمل عمليات علي انواع غير متطابقة بدل ماترمي خطأ  في الكود 
```js
const foo = 42; // foo is a number
const result = foo + "1"; // JavaScript coerces foo to a string, so it can be concatenated with the other operand
console.log(result); // 421
```
كل القيم البدائية تقدر تختبرها نوعا ب `typeof` معادا القيمة الخاصة `null` هترجعلك `object`

|Type|`typeof` return value|Object wrapper|
|---|---|---|
|[Null](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#null_type)|`"object"`|N/A|
|[Undefined](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#undefined_type)|`"undefined"`|N/A|
|[Boolean](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#boolean_type)|`"boolean"`|[`Boolean`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean)|
|[Number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#number_type)|`"number"`|[`Number`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)|
|[BigInt](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#bigint_type)|`"bigint"`|[`BigInt`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt)|
|[String](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#string_type)|`"string"`|[`String`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)|
|[Symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#symbol_type)|`"symbol"`|[`Symbol`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)|


### التعليق
نفس ستايل تعليق الC
```javascript
// this is a comment
/*
This is a multi
line comment
*/
```

### العمليات

| العمليه        | الشرح                                                                                | الرمز          |
| -------------- | ------------------------------------------------------------------------------------ | -------------- |
| الجمع          | جمع رقمين او نصين مع بعض                                                             | `+`            |
| طرح وقسمه وضرب | نفس العمليات الرياضيه                                                                | `-` ,`*` , `/` |
| تعيين          | تعيين قيمه للمتغير                                                                   | `=`            |
| مساواة صارمه   | دي بتتأكد اذا قيمتين متساوين في نفس القيم ونوع البيانات وبترجع اما `ture` او `false` | `===`          |
| ليس و لا يساوي | دي بترجع عكس القيمة المنطقية يعني الصح غلط والغلط صح                                 | `!`, `!==`     |

### الجمل الشرطيه
الجمل الشرطيه بتستعمل في انها تشغل كود معين لو الشرط اللي موجود تحقق
```javascript
let iceCream = "chocolate";
if (iceCream === "chocolate") {
  alert("Yay, I love chocolate ice cream!");
} else if (iceCream === "vanilla") {
  alert("Yay, I like vanilla too");
} else {
  alert("Awwww, but chocolate is my favorite…");
}
```

### الدوال
الدوال هي عبارة عن طريقة بنجمع بيها قطع من الكود حابين نستعمله اكتر من مرة وهي طريقة حلوه في انك متكتبش نفس الكود اكتر من مره
```js
function multiply(num1, num2) {
  let result = num1 * num2;
  return result;
}
multiply(1, 5);
```

في انواع من الدوال اسمها   `anonymous function`  وهي نوع من الفانكشنز ملهاش اسم )
```js
textBox.addEventListener("keydown", function (event) {
  console.log(`You pressed "${event.key}".`);
});
```
في طريقة تاني نكتبها بيها وهي اسمها `arrow function` 
```js
document.querySelector("html").addEventListener("click", () => {
  alert("Ouch! Stop poking me!");
});
```

ولو الفانكشن بتاخد بالظبط بارميتير واحد تقدر تشيل القواس
```js
textBox.addEventListener("keydown", event => {
  console.log(`You pressed "${event.key}".`);
});
```
اخيرا لو الفاكشن بتاعك عبارن عن سطر واحد بس تقدر تشيل الأقواس بتاعتها 
```js
const originals = [1, 2, 3];

const doubled = originals.map(item => item * 2);

console.log(doubled); // [2, 4, 6]
```
### الأحداث
التفاعليه الفعليه للغة تكمن هنا الأحداث هي عبارة عن قطع من الكود بتنتظر حصول حدث معين (مثال ضغطه بالماوس علي كائن معين) ولما يحصل بتشغل قطع معينه من الكود
```js
document.querySelector("html").addEventListener("click", function () {
  alert("Ouch! Stop poking me!");
});
```
في اكتر من طريقة تعمل بيها حدث, هنا احنا بنختار الصفحه كلها اللي موجوده في ملف ال`html` ونحط وبننادي علي الافينيت بتاعها ولو حصل وانها الايفينت حصل هيتم تشغيل الفانكسن اللي اتبعتتلها (الفانكشن اللي اتعتت هنا عبارة عن `anonymous function`  وهي نوع من الفانكشنز ملهاش اسم )
في طريقة تاني نكتبها بيها وهي اسمها `arrow function` 
```js
document.querySelector("html").addEventListener("click", () => {
  alert("Ouch! Stop poking me!");
});
```
### القيمة null , undifiend
 القيمة `undified` قمية تدل علي  انعدام القيمة للمتغير , ولكن `null` تدل علي انعدام وجود كائن
 ```js
> let myvar;
> myvar;
 undifiend
```

### الجملfinally, try, catch, throw
تقدر تستعملهم  `try`, `catch` داخل بلوك عشان تمسك الاخطاء اللي ممكن تحصل وتتفاداها
تقدر ترمي خطا عن `trow` 
واخيرا تقدر تنفذ كود بعد بلوك ال `catch`  
```js
openMyFile();
try {
  writeMyFile(theData); // This may throw an error
} catch (e) {
  handleError(e); // If an error occurred, handle it
} finally {
  closeMyFile(); // Always close the resource
}
```

### الprototype chain:
اي حاجه في اللغة عبارة عن كائن والكائن دا بيتكون من عدة خواط وقيم , فأي كائن انت هتنشئة ميعرفش حاجه عن الخاصية اللي انت طالبها منه فبيروح يشوف الكائن اللي ينتمي اليه هل هو عنده او لا ... وهكذا لحد منوصل للقيمة null
```js
const myObject = {
  city: "Madrid",
  greet() {
    console.log(`Greetings from ${this.city}`);
  },
};

myObject.greet(); // Greetings from Madrid
```

### الclasses
تقدر تعمل كلاس من خلال الكلمه `class`
```js
class Person {
  name;

  constructor(name) {
    this.name = name;
  }

  introduceSelf() {
    console.log(`Hi! I'm ${this.name}`);
  }
}
```
وتقدر تعمل فيها ميثود اسمها `constructor` ودي هتشتغل بمجرد مايتم انشاء كائن من الكلاس
```js
const giles = new Person("Giles");

giles.introduceSelf(); // Hi! I'm Giles
```
وتقدر تنشئ كائن من الكلاس بتسعمال الكلمه `new`
#### الوراثة
بنستعمل الكلمه `extends` عشان نخلي الكلاس وارث من كالس تاني
```js
class Professor extends Person {
  teaches;

  constructor(name, teaches) {
    super(name);
    this.teaches = teaches;
  }

  introduceSelf() {
    console.log(
      `My name is ${this.name}, and I will be your ${this.teaches} professor.`,
    );
  }

  grade(paper) {
    const grade = Math.floor(Math.random() * (5 - 1) + 1);
    console.log(grade);
  }
}
```
وتقدر تنادي من السبكلاس علي ميثود ال `contructor` بتاعت البيرنت كلاس من خلال `super()` وبتاصيلها اي بارميتر البيرنت كلاس متوقعها منك

اول توصل لأي ستاتيك ميثود خاصه بالكلاس 
```js
class Rectangle {
  static logNbSides() {
    return "I have 4 sides";
  }
}

class Square extends Rectangle {
  static logDescription() {
    return `${super.logNbSides()} which are all equal`;
  }
}
Square.logDescription(); // 'I have 4 sides which are all equal'
```

#### التغليق
تقدر تخلي اتربيوتس الكائن المنشي من الكلاس مخفية لما تحط قبلها #
```js
class Student extends Person {
  #year;

  constructor(name, year) {
    super(name);
    this.#year = year;
  }

  introduceSelf() {
    console.log(`Hi! I'm ${this.name}, and I'm in year ${this.#year}.`);
  }

  canStudyArchery() {
    return this.#year > 1;
  }
}
```

او تقدر تعمل جيتر ميثود من خلال الكلمه `get`
```js
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
  // Getter
  get area() {
    return this.calcArea();
  }
  // Method
  calcArea() {
    return this.height * this.width;
  }
  *getSides() {
    yield this.height;
    yield this.width;
    yield this.height;
    yield this.width;
  }
}

const square = new Rectangle(10, 10);

console.log(square.area); // 100
console.log([...square.getSides()]); // [10, 10, 10, 10]
```

#### الثوابت
الثوابت هي عبارة عن اتربيوتس او ميثودز مختصه باكلاس نفسه بدل الكائنات التابعه ليه
```js
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  static displayName = "Point";
  static distance(a, b) {
    const dx = a.x - b.x;
    const dy = a.y - b.y;

    return Math.hypot(dx, dy);
  }
}

const p1 = new Point(5, 5);
const p2 = new Point(10, 10);
p1.displayName; // undefined
p1.distance; // undefined
p2.displayName; // undefined
p2.distance; // undefined

console.log(Point.displayName); // "Point"
console.log(Point.distance(p1, p2)); // 7.0710678118654755
```

### الModules
ببساطه الموديل هو مجرد فايل تقدر تعمله استيراد من فايل تاني عشان تستخدمه في الفايل التاني دا.
اول حاجه تعملها عشان تعرف تستورد موديل هو انك تصدره بستعمال الكلمه `export`
ابسط طريقة لاستعمالها هي انك تحطها قدام العنصر اللي عايز تصدرة برا الموديل
```js
export const name = "square";

export function draw(ctx, length, x, y, color) {
  ctx.fillStyle = color;
  ctx.fillRect(x, y, length, length);

  return { length, x, y, color };
}
```
تقدر تصدر دوال او متغيرات او كائنات وكمان كلاسات لو حابب
ولكنك محتاج تخليها في اعلي طبقة من الكود (يعني مينفعش تستعملها جوا فانكشن)
طريقة الأكثر راحه هي انك تستعمل الكلمه `export` في نهاية الموديل بتاعك وتعمل وراها ليست بكل العناصر اللي عايز تصدرها
```js
export { name, draw, reportArea, reportPerimeter };
```
#### استيراد العناصر :
تقدر تسورد كل العناصر اللي انت عايزها من فايل تاني من خلال الكلمه `import-from` وتحط بينهم العناصر اللي عايز تستوردها وفي النهاية المكان اللي هتستورد منه
```js
import { name, draw, reportArea, reportPerimeter } from "./modules/square.js";
```

