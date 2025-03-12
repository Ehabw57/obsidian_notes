هي عبارة عن إطار عمل مبنيه علي لغة جافا سكريبت وظيفتها انها تساعدنا نصمم واجهات المستخدم بستعمال ال `HTML, CSS` العاديين
### تسطيب اطار العمل
```bash
npm create vue@latest
```
 دا هينزلك الباكدجز المطلوبه وهيديك شوية خيارات تختار منها عشان تنزل باكدحز اضافية لمشروعك 
```bash
zerobors@void ~/Projects/somthig » npm create vue@latest
Need to install the following packages:
create-vue@3.15.1
Ok to proceed? (y) y


> npx
> create-vue

┌  Vue.js - The Progressive JavaScript Framework
│
◇  Project name (target directory):
│  firstVueTime
│
◇  Package name:
│  firstvuetime
│
◇  Select features to include in your project: (↑/↓ to navigate, space to select, a to toggle all, enter to confirm)
│  none

Scaffolding project in /home/zerobors/Projects/somthig/firstVueTime...
│
└  Done. Now run:

   cd firstVueTime
   npm install
   npm run dev

zerobors@void ~/Projects/somthig »
``` 
تقدر بعدها تدخل علي البروجيكت بتاعك وتشغل `npm run dev` وسيرفر الفرونت انت هيشتغل معاك 

### الخطوة الأولي
من المعروف انو اي فريم وورك عشان تشتغل بيها لازم تعمل انستانس من الكلاس بتاعها وهنا في فيو هنعمل انستن من الفانكشن `createApp`
```js
import { createApp } from 'vue'

const app = createApp({
  /* root component options */
})
```
هو أيه اصلا الرووت كمبوننت؟
كل ابلكيشن فيو محتاج نقطه بداية تكون هي الرووت بتاعته الرووت كمبوننت هو اعلي كمبونتت في الشجرة اللي بتتنادي منه باقي الكمبوننتس

#### ماونت الابلكيشن 
ال `app` انتستانس مش هيرندر اي حاجه من غير متتنادي الميثود  `mount()` بتاعته واللي بيتباصالها عنصر DOM وال العنصر دا هو اللي هيترندر جواه الرروت كمبوننت
 ```html
 <div id="app"></div>
```

```js
app.mount('#app')
```
المثود دي بتتنادي بعد ماتكون خلصت كل الكونفقريشن بتاعت الابلكيشن بتاعك وبتالي بمجرد ما هتناديها هترجلك انستانس من الرووت كمبوننت بدل الانستانس بتاعت الابلكيشن اللي رجعتها `createApp`

#### فيو syntax

فيو بتستعمل سنتاكس شبيهه ب`html` جدا وحاجه كدا زي `jinja2` 
اعمل اتنين اقواس متعرجه وجط جواهم اسم المتغير بتاعك عشان يظهر في ال`dom` 
```vue
<span>the msg is {{ msg }}</span>
```
السنتاكس دي اسمها `text interpolation`  او `mustash` ودي بترجم نص حرفي مش كود  `html` لكن لو عايز تترجم كود `html` استعمل ال `v-html` دايركتف
```html
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```
#### Attribute binding
السنمتاكس اللي فاات مش هتشتغل غير علي الكونتنت ولكن لو حبيت تشغلها علي اتربيوت لكائن `html` استعمل الدايركتف `v-bind` 
```vue
<div v-bind:id="dynamicId"></div>
```
هنا الدايركتف دا بيقول لفيو انها تخلي بالها من قيمه `dynamicId` الخاصه بالكمبوننت ولو اتغيرت غير معاها قيمه ال`id` اتربيوت بتاعت الكائن

وعشان الديركتف دا هنستعمله كتير فعملوله اختصارة ظريفة
```vue
<div :id="dynamicId"></div>
```
ممكن تختصرالكلام دا اكتر لو كان اسم البروبرتي هو نفس اسم الاتربيوت ==ولكنها لللاسف مش متاحه غير في النسخه 3.4==
```vue
<!-- same as :id="id" -->
<div :id></div>

<!-- this also works -->
<div v-bind:id></div>
```

وخد عندك الأظرف لو عندك كائن جافا سكريبت الاتربيوتس بتاعته هي نفس الاتبربيوتس بتاعت الالييمنت تقدر مباشرة تعملها بايندنغ

```js
const objectOfAttrs = {
  id: 'container',
  class: 'wrapper',
  style: 'background-color:green'
}
```


```vue
<div v-bind="objectOfAttrs"></div>
```

### جافا سكريبت وفيو
في الواقع لسا مكلمتكش انو فيو بتدعم القوة الكامله للغة جافا سكريبت داخل المستاش  
```vue
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div :id="`list-${id}`"></div>
```
 ==ولكنن للأسف== كل بايندغ يقدر يتحط جواه تعبير واحد فقط بستثناء دول
  ```vue
  <!-- this is a statement, not an expression: -->
{{ var a = 1 }}

<!-- flow control won't work either, use ternary expressions -->
{{ if (ok) { return message } }}
```


### Directives
هي عبارة عن اتربيوتس مميزة بتقدمها فيو ودا بتبداء ب `v-` وبنستعملها عشان نحدث اي تغيرر يحصل في ال`dom`
خد عندك كمثال ال `v-if` 
```vue
<p v-if="seen">Now you see me</p>
```
ودي دورها انها تعرض العنصر `p` في حاله فقط انه قيمه `seen` كانت ب `true`

#### Directives args
بعض الداريكتفس بتحتاج تاخد ارغيومنس ودي بتتحط بعد علامه الكولم `:` وشفنا مثال منها وهي ال `v-bind` 
```vue
<a v-bind:href="url"> ... </a>

<!-- shorthand -->
<a :href="url"> ... </a>
```
مثال تاني وهو الدايركتف `v-on` ودا بيستمع لايفينت معين لما يحصل علي العنصر وهو دا الارجيومنت بتاعه
```vue
<a v-on:click="doSomething"> ... </a>

<!-- shorthand -->
<a @click="doSomething"> ... </a>
```

##### Dynamic args
الظريف انك برضو هنا تقدر تستعمل تعبير جافا سكريبت لو جطيت الارغيومنت بين `[ ]`
```vue
<a v-on:[eventName]="doSomething"> ... </a>

<!-- shorthand -->
<a @[eventName]="doSomething"> ... </a>
```
 هننا كمثال لو كان الكمبونتت بتاعك فيه  قيمة `eventName` ب `foucs` وقتها التعبير دا خيكون صح ولما تتغير لاي قيمة تاني برضو هيتم تغيرها في التعبير دا

##### بعص القيود
 ==هام: القيم النهائية المتوقعه لاي ارجيومنت هيتولد من الاكسبرشن لازم تكون نص وإلا لو كانت قيمتها ب `null` فوقتها هيتم الغاء الاتربيوت كله==
 كمان مينفعش برضو تحط تعبير كمبيوتد 
 ```vue
 <!-- This will trigger a compiler warning. -->
<a :['foo' + bar]="value"> ... </a>
```
برضو حاول تتفادي انك تخلي المتغيرات فيها حروف كابيتال عشان البراوزر هيجبرها انها تبقي سمول
```vue
<a :[someAttr]="value"> ... </a>
```
يعني دا للاسف هيتحول ل `someattr` جوا ال `Dom` ووقتها كودك مش هيشتغل لوكمت حاطط جوا الكمبوننت داتا باسم `someAttr`

#### Modifiers
واخيرا وليس اخرا معانا المعدلات ودي وظيفتها انها بتعدل علي سلوك الدايركتف بشكل او باخر
 ```vue
 <form @submit.prevent="onSubmit">...</form>
```
