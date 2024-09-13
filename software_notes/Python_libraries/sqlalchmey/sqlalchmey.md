مكتبه بايثون بتتعامل مع قواعد البيانات  عن طريق أكواد بايثون.
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
 عشان تستعمل `sqlalchemy` لازم أولا  تعمل `Engine` ودا هو بداية  كل عمليه علي أي قاعدة بيانات.
 ال`Engin` عباره عن كائن بيتصل بقاعدة بيانات معيه فقط وبيكون جواه مجموعه من ال `connection pool` مخصصه للتعامل مع قاعدة البيانات دي فقط.
 نقدر نعمل الكائن دا من خلال الفانشكن `create_engine()`  ودا هترجعلنا `instance` من الكائن `Engine`
 ```python
 from sqlalchemy import create_engine
 engine = create_engine("sqlite+pysqlite:///:memory:", echo=True))
```
السطر  `sqlite+pysqlite:///:memory:` بيدلنا علي تلات حجات مهمين
1. ايه نوع قاعدة البيانات اللي بنتعامل معاها، وهنا احنا كتبنا `sqlite` 
2. إيه نوع ال `DPAPI` اللي بنتصل بيه، وهنا حددنا انه `pysqlite` وهو نوع معين من ال `DPAPI` اللي بيستعمل بين بايثون و`sqlite`
3. ازاي نحدد نوقع قاعده البيانات، وهنا احتبنا `memory` عشان نخليها مباشرة علي الذاكرة من غير ماانعمل أي فايلات ودا مناسب للتجارب.
وأخيرا احنا خلينا `echo` عشان بايثون تروينا الكود الأصلي اللي بيتبعت ل `sql`
القاعدة الأصليه لبناء أي `url` إتصال هي: `dialect+driver://username:password@host:port/database`

### التعامل مع قاعدة البيانات
من بعد ماجهزنا الكائن `engine` كدا احنا بقينا جاهزين اننا نفعل `connection` علي قاعدة البيانات وبعدين نتعامل معاها
وعشان نبداء نفعل `connection` علي ال `engine` هنستعمل `context_maneger` بحيث منوجعش دماغنا باننا نقفله بعدين.

```python
>>> with engine.connect() as conn:
...     result = conn.execute(text("select 'hello world'"))
...     print(result.all())
...
```

```output
 INFO sqlalchemy.engine.Engine BEGIN (implicit)
 INFO sqlalchemy.engine.Engine select 'hello world'
 INFO sqlalchemy.engine.Engine [generated in 0.00033s] ()
[('hello world',)]
 INFO sqlalchemy.engine.Engine ROLLBACK
```
كل اللي عملناه هنا ببساطه اننا بعتنا `qurey` مجرده لقاعده البيانات ورجعلنا اللي طلبنا في صيغه `list` ولكن لاحظ بسبب ال`echo=True` اللي حطناها واحنا بنععمل ال `engine` الداتا بتظهرلنا في الاوتبوت باللي بيبعته المحرك للقاعدة البيانات
ووظيفه ال `ROLLBACK` في اخر سطر انه مش بيعمل `commit` للداتا وبالتالي مش بتتحفظ في قاعدة البيانات
لو كنا عايزين نعمل `commit` للبيانات، فتعال نجرب نعمل جدول وبعدين نعمله `commit`
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

الكائن `result` عباره عن `tuple` بكل البيانات اللي رجعتها ال `exec`، وفي طرق كتير نقدر نستخرج بيها البيانات من الكائن دا منها:
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
عشان نعرف نكون قاعدة البيانات بيستعمال ال `ORM` ، عادة ماهنوصف الجداول والأعمده اللي مفروض  في هيئة `classes` 
والمهمه دي عادة هتم من خلال إنشاء `instance` من ال `declartive_base` والتطبيق غالبا مش هيحتاح غير عنصر واحد بس منه، وهتورث منه عدد من ال `classes` تساوي عدد الجداول اللي عايز تعملها.
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
id = Column(Integer, primary_key=True, autoincrement=True)
name = Column(String, nullable=False)
email = Column(String, unique=True)
status = Column(String, default='active')
```
وأخيرا ال `__reper__`  برتجعلنا وصف لكل كائن من الكلاس، بمعني اخر قيم كل صف في الجول

بما أنو الجدول اللي عرفناه للتو وارث من ال `Base` كلاس ففي أي وقت نقدر نوصفه
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

بعد تكوين الجداول والأعمده حان وقت السحر
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
الكائن `Session` مربوط باقاعدة البيانات بتاعتنا وكل لما بنيجي نستنسخ منه بيرجع `connection` ويفضل متمسك بيها لحد ما نعمل `commit` أون ننهي الكائن `session` عن طريق `session.close()`

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
للعلم `sqlalchemy` بمجرد مابتتعرف علي كائن في ال `session` مينقعش يتكرر، ودا عشان `sqlalchemjy` مراقبه كل الكائنات اللي عندها ولو حصل أي إضافة أو تغيير بتعرف علي طول
```python
session.add_all([
    User(name='wendy', fullname='Wendy Williams', nickname='windy'),
    User(name='mary', fullname='Mary Contrary', nickname='mary'),
    User(name='fred', fullname='Fred Flintstone', nickname='freddy')])
```
هنا ضفنا شويه كائنات جديد من جدولنا لل `session` وكماان غيرنا قيمه  `nickname` للكائن القديم بتاعنا
```python
ed_user.nickname = 'eddie'
```
لاحظ أنو علي طول ال `session` لاحظت التغيرات
```python
>>> session.dirty
IdentitySet([<User(name='ed', fullname='Ed Jones', nickname='eddie')>])
>>> session.new  
IdentitySet([<User(name='wendy', fullname='Wendy Williams', nickname='windy')>,
<User(name='mary', fullname='Mary Contrary', nickname='mary')>,
<User(name='fred', fullname='Fred Flintstone', nickname='freddy')>])
```
ويلا بينا ن `commit` التعديلات والإضافات الجديدة
```python
sesseion.commit()
```
### البحث  عن البيانات
اي كويري هتعملها هتستعمل فيها الميثود `query()` بالإضافة إلي بعض ميثود التصفيات والترتيب
 في `query()` هتذكر **البيانات اللي انت عايز ترجعها** أو اسم **الجدول لو انت عايز البيانات بالكامل**
في الحاله الأولي الفانكشن هترجع `tuple` بالبيانات وفي الحاله التانيه الفانكشن هترجع الكائن كلو
```python
>>> for instance in session.query(User).order_by(User.id):
...     print(instance.name, instance.fullname)

ed Ed Jones
wendy Wendy Williams
mary Mary Contrary
fred Fred Flintstone
```

```python
>>> for name, fullname in session.query(User.name, User.fullname):
...     print(name, fullname)

SELECT users.name AS users_name,
        users.fullname AS users_fullname
FROM users
()

ed Ed Jones
wendy Wendy Williams
mary Mary Contrary
fred Fred Flintstone
```
وتقدر تصفي النتائج بستعمال `filter()` 
```python
>>> for name, in session.query(User.name).\
...             filter(User.fullname=='Ed Jones'):
...    print(name)
```
وتقدر تصفي اكتر من مره بناء علي اكتر من عامل
```python
>>> for user in session.query(User).\
...          filter(User.name=='ed').\
...          filter(User.fullname=='Ed Jones'):
...    print(user)
```

```python
query.filter(User.name == 'ed', User.fullname == 'Ed Jones')
```
وتقدر كمان تصفي بناء علي اكتر من قيمه لنفس العامل بإستعمال `in_()`
```python
query.filter(User.name.in_(['ed', 'wendy', 'jack']))

# تقدر كمان تباصي جواها كيوري
query.filter(User.name.in_(
    session.query(User.name).filter(User.name.like('%ed%'))
))
```
تقدر بقا بعدين تقرر انت عايز تستلم كام قيمه من النتيجه بإستعمال:
1. لو عايز كل القيم 
```python
>>> query = session.query(User).filter(User.name.like('%ed')).order_by(User.id)
>>> query.all()
```
2.  لو عايز أول قيمه بس
```python
query.first()
```
3. لو عايز عدد معين من القيم
```python
query.all()[1:3]
```
####  أنواع الكويري:
في مجموعه من الحجات اللي ممكن تضيفها للميثود `query`  شبه اللي في `mysql`:

1. ال count()
شبهه ال `count()` في  `mysql` ودي بترجع عدد ال `objects` اللي رجعت في الكويري
2. ال  delete()
ودي بتمسح كل الكائنات اللي رجعت في الكويري من القاعدة `session.query(Table_bname).delete()` 
3. ال filter() و ال order_by()
ودي سبق ذكرها فوق
4. ال join()
 ودي صيغتها كالتالي `session.query.join(target, *props, **kwargs)`
### تطبيق عملي
في التطبيق العملي دا هنعمل داتا بيز بسيطه مكونه من تلات جداول عن عالم الألعاب
#### إنشاء كلاسات الجداول:
هنبداء نعمل داتا بيز متكامله وهنعمل عليها معظم ال`queris` اللي احنا عرفينا
كالعادة نبداء بستيراد المكتبة 
```python
from sqlalchemy import create_engine
from sqlalchemy.orm import declarative_base, sessionmaker
```
ومن ثم ننشء نسخه من الكلاس `declarative_base` ونورث من كل الجداول بتاعتنا .
```python
Base = declarative_base()

# Define classes aka Tables
class Games(Base):
    __tablename__ = 'games'

class Players(Base):
    __tablename__ = 'players'

class Records(Base):
    __tablename__ = 'records'
```
هنعمل تلات جداول:
1. جدول `players` هنخزن فيه بيانات الاعبين:
	- عمود `id` رقم برايمري كاي ومينفعش يبفي ب `null` و بيزيد اوتوماتيكلي
	- عمود `name` نص عبارة عن 128 حرف ومنفعش يبفي ب `null`
	- عمود `age` رقم مينفعش يبقي ب `null`

2. جدول `games` هنخزن فيه بيانات الألعاب:
	- عمود `id` رقم برايمري كي ومينفعش يبقي ب `null` وبزيد اوتوماتيكلي
	- عمود `name` نص عبارة عن 128 حرف ومنفعش يبفي ب `null`

3. جدول `records` هنخزن فيه بيانات الاعبين والألعاب اللي بيلعبوها
	- عمود `player_id` رقم عبارة عن `foregin key` من العمود  `players.id`
	- عمود `game_id` رقم عبارة عن `foregin key` من العمود `game_id`
 ```python
from sqlalchemy.orm import declarative_base
from sqlalchemy import Column, Integer, String, ForeignKey

Base = declarative_base()


class Games(Base):
    __tablename__ = 'games'

    id = Column(Integer, primary_key=True, autoincrement=True)
    name = Column(String(128), nullable=False)

    def __repr__(self):
        return "[<class>:{}, id: {}, name: {}]".format(
                self.__class__.__name__, self.id, self.name)


class Players(Base):
    __tablename__ = 'players'

    id = Column(Integer, primary_key=True, autoincrement=True)
    name = Column(String(128), nullable=False)
    age = Column(Integer, nullable=False)

    def __repr__(self):
        return "[<class>:{}, id: {}, name: {}, age: {}]".format(
                self.__class__.__name__, self.id, self.name, self.age)


class Records(Base):
    __tablename__ = 'records'

    id = Column(Integer, primary_key=True, autoincrement=True)
    player_id = Column(Integer, ForeignKey('players.id'))
    game_id = Column(Integer, ForeignKey('games.id'))
```
نقدر دلوقتي ناخد الكلام دا في فايل ويكون اسمه مثلا `mapped_models.py` 

#### مسخدم جديد وقاعدة بيانات جديده:
حتي هذه المرحله احنا كدا خلصنا البيانات الأساسه وعملنالها `mapp` للكلاسات ودلوقتي جه وقت انو يبقي ليها لازمه.
في `mysql` هخش وأعمل قاعدة بيانات جديدة أسمها `gaming` كمان هعمل مستخدم جديد وهديله باسورد كل الصلاحيات علي القاعدة دي .
```mysql
CREATE DATABASE IF NOT EXISTS gaming;
CREATE USER IF NOT EXISTS 'ehab'@'localhost' IDENTIFIED BY 'ehabw57';
GRANT ALL PRIVILEGES ON gaming.* TO 'ehab'@'localhost';
GRANT SELECT ON performance_schema.* TO 'ehab'@'localhost';
```
هنجفظ دول في ملف ونسميه `setup.sql` وبعدين نبعت الفايل ل `mysql`
```bash
root@6508b8926b79:/home/ehabw57/games_dev >> cat setup.sql | mysql
root@6508b8926b79:/home/ehabw57/games_dev >> echo "SHOW DATABASES;" | mysql | grep gaming
gaming
root@6508b8926b79:/home/ehabw57/games_dev >> echo "SHOW GRANTS FOR 'ehab'@'localhost';" | mysql
Grants for ehab@localhost
GRANT USAGE ON *.* TO `ehab`@`localhost`
GRANT ALL PRIVILEGES ON `gaming`.* TO `ehab`@`localhost`
GRANT SELECT ON `performance_schema`.* TO `ehab`@`localhost`
root@6508b8926b79:/home/ehabw57/games_dev >>
```

#### إنشاء المحرك engine:
ودلوقتي هنشغل `python3`:
نستورد كل الكلاسات اللي عملناها في الملف `mapped_models` 
والفانكشن `create_engine`
وننشي ال `engine` الللطيف بتاعنا ببيانات المستخدم الليي عملناه 
وأخيرا ننشئ الجداول  بتاعتنا .
```python
>>> from mapped_models import *
>>> from sqlalchemy import create_engine
>>> engine = create_engine("mysql://ehab:ehabw57@localhost/gaming")
>>> engine
Engine(mysql://ehab:***@localhost/gaming)
>>> Base.metadata.create_all(engine)
```
ودلوقتي خلينا نرجع نتأكد أنو الجداول بتاعتنا اتعملت فعلا
```bash
root@6508b8926b79:/home/ehabw57/games_dev >> echo "SHOW TABLES;" | mysql gaming
Tables_in_gaming
games
players
records
root@6508b8926b79:/home/ehabw57/games_dev >>
```
**ملحوظه**: لو شغلت `Base.metadata.create_all(engine)` والجداول بالفعل معموله، فمش هيكون ليها أي تأثير وهيتم تجاهلها.
#### إنشاء صفوف جديدة
علي قد ما أنا حابب المرحله اللي وصلناها ولكن مش مبسوط انو جداولنا فاضيه فتعال نضيف صفوف فيها من خلال اننا نعمل كائنات من الكلاسات بتاعتنا
في بايثون هبدأ أعمل كائانات من الكلاسات بتاعتنا.
```python
>>> from mapped_models import Players, Games, Records
>>> ehab = Players(name='Ehab', age=24)
>>> nutty = Players(name='Nutty', age=25)
>>> layla = Players(name='Layla', age=21)
>>> ahmed = Players(name='Ahmed', age=19)
```
ومن ثم بعدها هعمل ال `engine` وبعدين أعمل الكائن `session` وأربطه با `engine` بتاعي
```python
>>> from sqlalchemy import create_engine
>>> engine = create_engine("mysql://ehab:ehabw57@localhost/gaming")
>>> from sqlalchemy.orm import sessionmaker
>>> Session = sessionmaker(bind=engine)
>>> session = Session()
```
نضيف بقا كل الكائنات اللي عملناها لل `session` علي هيئة `list`
```python
>>> session.add_all([ehab, nutty, layla, ahmed])
```
ومن ثم نعمل `commit` عشان نبعت البيانات للقاعدة 
```python
>>> session.commit()
```
واخيرا نتأكد انو البيانات أضافت للقاعدة
```bash
root@6508b8926b79:/home/ehabw57/games_dev $ echo "SELECT * FROM gaming.players;" | mysql
id	name	age
1	Ehab	24
2	Nutty	25
3	Layla	21
4	Ahmed	19
root@6508b8926b79:/home/ehabw57/games_dev $
```
وبما انو احنا فيها يلا نضيف شويه ألعاب للجدول `games`
```python
>>> session.add_all([Games(name='Genshin_impact'),
...             Games(name='Mobile_legends'),
...             Games(name="Pubge"),
...             Games(name="Leauge_of_legends"),
...             Games(name="Among_us")])
>>> session.commit()
>>>
```

```bash
root@6508b8926b79:/home/ehabw57/games_dev $ echo "SELECT * FROM gaming.games;" | mysql
id	name
1	Genshin_impact
2	Mobile_legends
3	Pubge
4	Leauge_of_legends
5	Among_us
root@6508b8926b79:/home/ehabw57/games_dev $
```
ويلا نضيف شويه قيم عشوائيه كمان لل `records`
**ملاحظه**: وانت بضيف قيم ال `game_id` وال `player_id` إتأكد انها مابين 1 إلي 4  و ال 1 إلي 5 عشان الجدول دا أعمدته عبارة عن `forigen key` من الجدولين التانيين
```python
>>> session.add_all([
...          Records(game_id=2, player_id=4),
...          Records(game_id=1, player_id=3),
...          Records(game_id=4, player_id=3),
...          Records(game_id=4, player_id=1),
...          Records(game_id=4, player_id=3)])
>>> session.commit()
```

```bash
root@6508b8926b79:/home/ehabw57/games_dev $ echo "SELECT * FROM gaming.records;" | mysql
id	player_id	game_id
1	4	2
2	3	1
3	3	4
4	1	4
5	3	4
root@6508b8926b79:/home/ehabw57/games_dev $
```
#### إسترداد البيانات
بإعتبار اننا قفلنا جلسه بايثون اللي كنا فاتحينها، هنبداء نعمل واحدة جديدة
```bash
root@6508b8926b79:/home/ehabw57 $ python3
```
هنبداء نعمل نفس خطواتنا القديمه من أننا نستورد المودلز و نعمل ال`engine` وال `session`
```python
>>> from sqlalchemy import create_engine
>>> engine = create_engine("mysql://ehab:ehabw57@localhsot/gaming")
>>> from mapped_models import *
>>> from sqlalchemy.orm import sessionmaker
>>> Session = sessionmaker(bind=engine)
>>> session = Session()
```
خلي في بالك انو حتي الان دي الهيئة الي وصلتلها جداولنا
1. جدول `players`:

| id  | name  | age |
| :-: | :---: | :-: |
|  1  | Ehab  | 24  |
|  2  | Nutty | 25  |
|  3  | Layla | 21  |
|  4  | Ahmed | 19  |
2. جدول ال `games`:


| id  |       name        |
| :-: | :---------------: |
|  1  |  Genshin_impact   |
|  2  |  Mobile_legends   |
|  3  |       Pubge       |
|  4  | Leauge_of_legends |
|  5  |     Among_us      |
3. جدول ال  `records`

| id  | player_id | game_id |
| :-: | :-------: | :-----: |
|  1  |     4     |    2    |
|  2  |     3     |    1    |
|  3  |     3     |    4    |
|  4  |     1     |    4    |
|  5  |     3     |    4    |
1   جيب كل الصفوف اللي في جدول الاعبين
2  جيب أسماء الاعبين اللي عمرهم اكبر من 21 سنه
3 جيب أسماء الألعاب اللي اخر كلمه فيها `legends`
4 جيب الاعبين وأسماء الالعاب اللي بيلعبوها
5 جيب أسماء الاعبين وعدد الألعاب اللي بيلعبوها
6 جيب أسماء الألعاب وعدد الاعبين اللي بيلعبوها ( حتي الألعاب اللي محدش بيلعبها )






ودلوقتي هنسترد شويه بيانات:
1 - عايزين نجيب كل الصفوف اللي في جدول الاعبين
```python
>>> result = session.query(Players).all()
>>> for obj in result:
...     print(obj)
...
[<class>:Players, id: 1, name: Ehab, age: 24]
[<class>:Players, id: 2, name: Nutty, age: 25]
[<class>:Players, id: 3, name: Layla, age: 21]
[<class>:Players, id: 4, name: Ahmed, age: 19]
```
2. نجيب أسماء الاعبين اللي عمرهم اكبر من 21 سنه
```python
>>> result = session.query(Players).filter(Players.age > 21).all()
>>> for obj in result:
...     print(obj)
...
[<class>:Players, id: 1, name: Ehab, age: 24]
[<class>:Players, id: 2, name: Nutty, age: 25]
```
3. نجيب أسماء الألعاب اللي اخر كلمه فيها `legends`
```python
>>> result = session.query(Games).filter(Games.name.like("%legends")).all()
>>> for obj in result:
...     print(obj)
...
[<class>:Games, id: 2, name: Mobile_legends]
[<class>:Games, id: 4, name: Leauge_of_legends]
```
4. نجيب الاعبين وأسماء الالعاب اللي بيلعبوها
```python
>>> result = session.query(Players.name, Games.name, Players.age)\
... .join(Records, Players.id == Records.player_id)\
... .join(Games, Records.game_id == Games.id).all()
>>> for obj in result:
...     print(obj)
...
('Ahmed', 'Mobile_legends', 19)
('Layla', 'Genshin_impact', 21)
('Layla', 'Leauge_of_legends', 21)
('Ehab', 'Leauge_of_legends', 24)
('Layla', 'Leauge_of_legends', 21)
```
5. نجيب أسماء الاعبين وعدد الألعاب اللي بيلعبوها
```python
>>> from sqlalhcemy import func
>>> result = session.query(Players.name, func.count(Records.game_id))\
 		.join(Players, Records.player_id == Players.id).group_by(Records.player_id).all()
>>> for re in result:
...     print(re)
...
('Ahmed', 1)
('Layla', 3)
('Ehab', 1)
```
6. نجيب أسماء الألعاب وعدد الاعبين اللي بيلعبوها حتي الألعاب اللي محدش بيلعبها 
```python
>>> result = session.query(Games.name, func.count(Records.player_id))\
 		.outerjoin(Records, Games.id == Records.game_id).group_by(Games.name).all()
>>> for re in result:
...     print(re)
...
('Genshin_impact', 1)
('Mobile_legends', 1)
('Pubge', 0)
('Leauge_of_legends', 3)
('Among_us', 0)
```