مكتبه بايثون بتتعامل مع قواعد بيانات `mysql`  عن طريق أكواد بايثون.
### تنزيل وإستيراد المكتبة
هنزل المكتبه من خلال `pip3` كعادة كل مكتبات بايثون
```shell
$ pip3 install SQLAlchemy
```
وتقدر بعدها تفتح بايثون وتستورد المكتبه
```python
>>> import sqlalchemy
>>> sqlalchemy.__version__
'2.0.32'
```
### إنشاء إتصال لقاعدة بيانات
 عشان تستعمل `sqlalchemy` لازم أولا  تعمل `Engine` ودا هو بداية كل كل عمليه علي أي قاعدة بيانات.
 ال`Engin` عباره عن كائن بيتصل بقاعدة بيانات معيه فقط وبيكون جواه مجموعه من ال `connection pool` مخصصه للتعامل مع قاعدة البيانات دي فقط.
 نقدر نعمل الكائن دا من خلال الفانشكن `create_engine()`  ودا هترجعلنا `instance` من الكائن `Engine`
 ```python
 from sqlalchemy import create_engine
 engine = create_engine("sqlite+pysqlite:///:memory:", echo=True))
```
السطر  `sqlite+pysqlite:///:memory:` بيدلنا علي تلات حجات مهمين
1. ايه نوع قاعدة البيانات اللي بنتعامل معاها، وهنا احنا كتبنا `sqlite` 
2. إيه نوع الDPAPI اللي بنتصل بيه، وهنا حددنا انه `pysqlite` وهو نوع معين من ال DPAPI اللي بيستعمل بين بايثون و`sqlite`
3. ازاي نحدد نوقع قاعده البيانات، وهنا احتبنا `memory` عشان نخليها مباشرة علي الذاكرة من غير ماانعمل أي فايلات ودا مناسب للتجارب.
وأخيرا احنا خلينا `echo` عشان بايثون تروينا الكود الأصلي اللي بيتبعت لsql

### التعامل مع قاعدة البيانات
من بعد ماجهزنا الكائن `engine` كدا احنا بقينا جاهزين اننا نفعل `connection` علي قاعدة البيانات وبعدين نتعامل معاها
وعشان نبداء نفعل `connection` علي ال `engine` هنستعمل `context_maneger` بحيث منودعجش دماغنا باننا نقفله بعدين.

```python
>>> with engine.connect() as conn:
...     result = conn.execute(text("select 'hello world'"))
...     print(result.all())
...
```

```output
2024-08-23 18:33:55,343 INFO sqlalchemy.engine.Engine BEGIN (implicit)
2024-08-23 18:33:55,343 INFO sqlalchemy.engine.Engine select 'hello world'
2024-08-23 18:33:55,343 INFO sqlalchemy.engine.Engine [generated in 0.00033s] ()
[('hello world',)]
2024-08-23 18:33:55,345 INFO sqlalchemy.engine.Engine ROLLBACK
```
كل اللي عملناه هنا ببساطه اننا بعتنا كويري مجرده لقاعده البيانات ورجعلنا اللي طلبنا في صيغه `list` ولكن لاحظ بسبب ال`echo=True` اللي حطناها واحنا بنععمل ال `engine` الداتا بتظهرلنا في الاوتبوت باللي بيبعته المحرك للقاعدة البيانات
ووظيفه ال `ROLLBACK` في اخر سطر انه مش بيعمل `commit` للداتا وبالتالي مش بتتحفظ في قاعدة البيانات
لو كنا عايزين نعمل `commit` للبيانات اللي بنعمل، فتعال نجرب نعمل جدول وبعدين نعمله `commit`
```python
>>> with engine.connect() as conn:
...     conn.execute(text("CREATE TABLE some_table (x int, y int)"))
...     conn.execute(
...         text("INSERT INTO some_table (x, y) VALUES (:x, :y)"),
...         [{"x": 1, "y": 1}, {"x": 2, "y": 4}],
...     )
...     conn.commit()
...
```

```output
 INFO sqlalchemy.engine.Engine BEGIN (implicit)
 INFO sqlalchemy.engine.Engine CREATE TABLE some_table (x int, y int)
 INFO sqlalchemy.engine.Engine [generated in 0.00086s] ()
 <sqlalchemy.engine.cursor.CursorResult object at 0x7f4d45e7d580>
 INFO sqlalchemy.engine.Engine INSERT INTO some_table (x, y) VALUES (?, ?)
 INFO sqlalchemy.engine.Engine [generated in 0.00057s] [(1, 1), (2, 4)]
<sqlalchemy.engine.cursor.CursorResult object at 0x7f4d45e7d5e0>
 INFO sqlalchemy.engine.Engine COMMIT
```
كدا احنا عملنا 2 كوماند، الأول عملنا فيه جدول مكون من x و y، وبعدين عبينا في الجدول بيانات بقيم 1،1 و 2،4 وعشان نحفظ البيانات عملنا `commit` عللي ال`conntion
ومن الجميل اننا نذكر انو في طريقه تانيه لحفظ البيانات وهي عن طريق فانكشن `.begin` بدل `connect` 

والمفيد في إستعمال الفانكشن دي انو بمجرد ما ال `connection` يتقفل سواء من ال `context manager` او يدويا ،اوتماتيكي الفانكشن هتعمل `commit` لو مفيش مشكله  او `ROLLBACK` لو في `exception` اترفع.
```python
>>> with engine.begin() as conn:
...     conn.execute(
...         text("INSERT INTO some_table (x, y) VALUES (:x, :y)"),
...         [{"x": 6, "y": 8}, {"x": 9, "y": 10}],
...     )
...
```

```output
 INFO sqlalchemy.engine.Engine BEGIN (implicit)
 INFO sqlalchemy.engine.Engine INSERT INTO some_table (x, y) VALUES (?, ?)
 INFO sqlalchemy.engine.Engine [cached since 497.9s ago] [(6, 8), (9, 10)]
 <sqlalchemy.engine.cursor.CursorResult object at 0x7f4d45e7d760>
 INFO sqlalchemy.engine.Engine COMMIT
```

ومن هذا المنطلق نبداء رحلتنا مع `sqlalchemy` ونشوف ازاي ممكن نسعمل الكائن `result` اللي بنرجعه من الفانكشن `excute` بتضامن مع فانكشن `text` 
```python
>>> with engine.connect() as conn:
...     result = conn.execute(text("SELECT x, y FROM some_table"))
...     for row in result:
...         print(f"x: {row.x}  y: {row.y}")
...
```

```output
 INFO sqlalchemy.engine.Engine BEGIN (implicit)
 INFO sqlalchemy.engine.Engine SELECT x, y FROM some_table
 INFO sqlalchemy.engine.Engine [generated in 0.00090s] ()
x: 1  y: 1
x: 2  y: 4
x: 6  y: 8
x: 9  y: 10
 INFO sqlalchemy.engine.Engine ROLLBACK
```

الكائن `result` عباره عن `tuple` بكل البيانات اللي رجعتها الexec، وفي طرق كتير نقدر نستخرج بيها البيانات من الكائن دا منها:
1. تفكيك ال`tuple` ودا عباره عن انك تفكك التابل في متغيرات وتطبعها:
```python
result = conn.execute(text("select x, y from some_table"))

for x, y in result:
    ...
```
2. طباعه ال`attribute` ودا عباره عن انك بتستخرج قيمه كل عمود في كل صف:
```python
result = conn.execute(text("select x, y from some_table"))
    for row in result:
            print (f"y: {row.y}     x: {row.x}")
```
3. الإستخراج عن طريق ال`key-value` وهنا احنا بنستخرج القيم كأنها قاموس:
```python
result = conn.execute(text("select x, y from some_table"))

for dict_row in result.mappings():
    x = dict_row["x"]
    y = dict_row["y"]
```

ودلوقتي جه دور اننا نقدم الفانكشن `Session()`، وبصراحه مفيش فرق كبير بينها وبين `connect` ولكن هي مصممه انها تتعامل مع ال `ORM` من غير `actual sql qeries` وهنعرف معني الكلام دا قدام.
```python
from sqlalchemy.orm import Session

stmt = text("SELECT x, y FROM some_table WHERE y > :y ORDER BY x, y")
with Session(engine) as session:
    result = session.execute(stmt, {"y": 6})
    for row in result:
        print(f"x: {row.x}  y: {row.y}")
```
لو قارنت المثال دا باللي فوق هتلاقي انو مفيش اي فرق في التكينك بل هي هي نفس الطرق اللي استعملناها حتي في ال `commit`
```python
with Session(engine) as session:
    result = session.execute(
        text("UPDATE some_table SET y=:y WHERE x=:x"),
        [{"x": 9, "y": 11}, {"x": 13, "y": 15}],)
    session.commit()
```
### تكوين قاعدة البيانات
عشان نعرف نكون قاعدة البيانات بيستعمال ال `ORM` ، عادة ماهنوصف الجداول والبيانات اللي مفروض تكون في الصفوف في هيئة `classes` 
والمهمه دي عادة هتم من خلال إنشاء `instance` من ال `declartive_base` والتطبيق غالبا مش هيحتاح غير عنصر واحد بس منه وعدد من ال `classes` تساوي عدد الجداول.
نبداء نستورد الكلاس من خلال.
```python
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()
```
طول ما احنا معانا الكائن `Base` نقدر نعمل عدد لانهائي من الكلاسات واللي بتعبر عن الجداول ولكن هنكتفي بجدول واحد فقط نتمرن عليه.
```python
from sqlalchemy import Column, Integer, String
class User(Base):
    __tablename__ = 'users'
    id = Column(Integer, primary_key=True)
    name = Column(String)
    fullname = Column(String)
    nickname = Column(String)
    def __repr__(self):
       return "<User(name='%s', fullname='%s', nickname='%s')>" % (
                            self.name, self.fullname, self.nickname)
```
الكلاس علي اقل تقدير محتاج اسم ودا اللي بندهولو من خلال القيمه `__tablename__` .
ابدا ما `sqlalchemy` هتحاول تخمن الداتا اللي انت هتحطها في كل عمود من الجدول وعشان كدا لازم توصفها بنفسك من خلال الداتا تايبس اللي موضحه في الجدول التالي.
```python
from sqlalchemy import ( 
	Integer,
	String, 
	Boolean, 
	ForeignKey, 
	DateTime, 
	Sequence, 
	Float )
```
في  قيود ممكن تفعلها في كل `Column` زي `mysql` بالظبط واللي موضحه في الجدول الجاي
```python
id = Column(Integer, primary_key=True)
name = Column(String, nullable=False)
email = Column(String, unique=True)
status = Column(String, default='active')
```
وأخيرا ال `__reper__`  برتجعلنا وصف لكل كائن من الكلاس، بمعني اخر قيم كل صف في الجول

بما أنو الجدول اللي عرفناه للتو وارص من `Base` كلاس فف أي وقت نقدر نوصفه
```python
User.__table__
```

``` output
Table('users', MetaData(bind=None),
            Column('id', Integer(), table=<users>, primary_key=True, nullable=False),
            Column('name', String(), table=<users>),
            Column('fullname', String(), table=<users>),
            Column('nickname', String(), table=<users>), schema=None)
```

بعد وصف الجداول والأعمده حان وقت السحر
```python
Base.metadata.create_all(engine)
```
بإستعمال الكائن `Base` هنأكسس علي ال`metadata` واللي مخزنه جواها كل ال `mapped tables` اللي اتعرفت، وفي حالتنا مفيش غير `table` واحد اللي هو 'users' 
ومن ثم بنفعل الميثود `create_all()` وبنسلمها ال `engine` بتاعنا اللي وصلناه بالقاعدة البيانات 
ووقتها بتنشئ الجداول جوا قاعدة البيانات.

#### إدخال البيانات
بعد ما عملنا الكلاس واللي بتالي انشئ جدول، نقدر دلوقتي ندخل بيانات في الجدول من خلال أننا نعمل `instances` من الكلاس بتاعنا.
```python
>>> ed_user = User(name='ed', fullname='Ed Jones', nickname='edsnickname')
>>> ed_user.name
'ed'
>>> ed_user.nickname
'edsnickname'
>>> str(ed_user.id)
None
```
لاحظ أنو حتي لما محطناش قيمه لل`id` بايثون مرفعتش `AttributeError` ولكن في المقابل رجعت `None` ودا القيمه الإقتراضيه اللي بيرجعها اي كلاس وارث من ال `declarative base`
ودلوقتي احنا جاهزين نتكلم مع قاعدة البيانات
المتحكم الأساسي في إدخال وحذف البيانات من القاعدة هي ال `session` المعموله من ال`engine` المتصل بقاعدة البيانات.
```python
from sqlalchemy.orm import sessionmaker
Session = sessionmaker(bind=engine)
```
وفي حاله انك لسا معملتش `engine` وحابب تعمل `session` ممكن تعملها زي كدا
```python
Session = sessionmaker()
```
وبعدين تربطه بال`engine`
```python
Session.configure(bind=engine)
```
وبمجرد إنشاء الكائن `Session` هتبداء تنشئ كائنات تانيه  مرتببطه بالقاعدة الخاصه بينا، وكل ما نحب نكلم القاعدة هنعمل `instance` من الكائن `Session` .
```python
session = Session()
```
الكائن `Session` مربوط باقاعدة البيانات بتاعتنا وكل لما بنيجي نستنسخ منه بيرجع `connection` ويفضل متمسك بيها لحد ما نعمل `commit` أون ننهي الكائن `session` 

نبداء نضيف نسخنا من الكلاس `users` واللي بتمثل الصفوف إلي ال `session`
```python
ed_user = User(name='ed', fullname='Ed Jones', nickname='edsnickname')
session.add(ed_user)
```
في الوضع الحالي الكائن `ed_user` مش مضاف للقاعدة البيانات، ولكنه مضاف لل`session` وبمجرد مانعمل `commit` او نحاول نعمل `quiry` علي القاعدة هيتعمل مباشرة `flush` ويضاف للقاعدة كصف جديد
```python
>>> our_user = session.query(User).filter_by(name='ed').first() 
```

```output
BEGIN (implicit)
INSERT INTO users (name, fullname, nickname) VALUES (?, ?, ?)
('ed', 'Ed Jones', 'edsnickname')
SELECT users.id AS users_id,
        users.name AS users_name,
        users.fullname AS users_fullname,
        users.nickname AS users_nickname
FROM users
WHERE users.name = ?
 LIMIT ? OFFSET ?
('ed', 1, 0)
```
بعد ما عملنا `query` علي القاعدة لاحظ أنو الكائن مباشرة أتعمله `flush` واضاف كصف جديد وأيضا شوف المفاجأة
```python
>>> our_user
<User(name='ed', fullname='Ed Jones', nickname='edsnickname')>
```
 خلينا ال`query` علي الكلاس اللي عملنا `User` وعملناله `filter_by` علي ال `attribute name` ، واخدنا أول عنصر من ال`query` وخزناه في المتغير `our_user` ،والنتيجه كانت انو ال`session` رجعتلنا نفس الكائن بتاعنا `ed_user`.
 ```python
 >>> ed_user is our_user
True
``` 
