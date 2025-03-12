الجاسون يعين java script object notation وهو عبارة عن طريقة تمثيل بيانات جافا سكريبت نصيًا وبتستخدم لتبادل البيانات عبر الشبكات (غالبا بين السرفر والكلاينت) اختراعها واحد اسمها دوجلاس كروكفورد, واحيانا بتتخزن في ملف امتداده `.json`
### تخظيظ json:
الجيسون بتحاكي طريقة جافا سكريب في عرض البيانات وتقدر تخزن فيها نوع من انواع بيانات جافا سكريبت
```json
{
  "squadName": "Super hero squad",
  "homeTown": "Metro City",
  "formed": 2016,
  "secretBase": "Super tower",
  "active": true,
  "members": [
    {
      "name": "Molecule Man",
      "age": 29,
      "secretIdentity": "Dan Jukes",
      "powers": ["Radiation resistance", "Turning tiny", "Radiation blast"]
    },
    {
      "name": "Madame Uppercut",
      "age": 39,
      "secretIdentity": "Jane Wilson",
      "powers": [
        "Million tonne punch",
        "Damage resistance",
        "Superhuman reflexes"
      ]
    },
    {
      "name": "Eternal Flame",
      "age": 1000000,
      "secretIdentity": "Unknown",
      "powers": [
        "Immortality",
        "Heat Immunity",
        "Inferno",
        "Teleportation",
        "Interdimensional travel"
      ]
    }
  ]
}
```
لو خزنا النص دا في متغير جافاسكريب  وعملناله لو هنقدر نأكسس بكل سهوله علي بيانات وكأنه اوبجيكت فعلي في الكود

### مثال عملي
تعالو نجيب الداتا اللي كانت فوق من الرابط اللي موجود قدامنا 
```js
async function populate() {
  const requestURL =
    "https://mdn.github.io/learning-area/javascript/oojs/json/superheroes.json";
  const request = new Request(requestURL);

  const response = await fetch(request);
  const superHeroes = await response.json();
}
```
  عشان نستلم جيسون بنبعت ريكوست لأي سرفر عن طريق الالفانكش `fetch` الرابط رجعلنا نص باينري  واحنا حولناه لنص جيسون 
  ولكن المشكله احيانا بتكون اننا بنستلم نص خام لجيسون وعايزين نحوله مباشرة لكائن جافاسكربت
  او العكس
  `parse()`: بتاخد نص بتخطيط جيسون وبترجع كائن جدافاسكربت
  `strigify()`: بتحول كائن جافا سكريبت لنص جيسون
  ```js
  async function populate() {
  const requestURL =
    "https://mdn.github.io/learning-area/javascript/oojs/json/superheroes.json";
  const request = new Request(requestURL);

  const response = await fetch(request);
  const superHeroesText = await response.text();

  const superHeroes = JSON.parse(superHeroesText);
  populateHeader(superHeroes);
  populateHeroes(superHeroes);
}
```
المره دي هنحول الداتا الباينري اللي استلمنها مباشرة لنص والنص طبيعي بكون بتخطيط جيسون ثم بعدها عملنا الكائن من النص بتسخدام `JSON.parse(text)`

أو ممكن نعمل العكس 
```js
let myObj = { name: "Chris", age: 38 };
myObj;
let myString = JSON.stringify(myObj);
myString;
console.log(myString); // "{"name": "Chris", "age": 38}"
```
