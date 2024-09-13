فلاسك هي عباه عن `web_framework` مصممه للغه بايثون عشان نبني بيها `web_application`
### تشغيل المكتبه
كل اللي محتاحه تعمله هي انك تستورد الكلاس `Flask` من المكتبه 
```python
from flask import Flask
```
#### برنامج بسيط 
```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
	return "Hello_world!"
```
شغل الابلكيشن من خلال `flask --app <file_name> run`        
```bash
$ flask --app hello run
 * Serving Flask app 'hello'
 * Running on http://127.0.0.1:5000 (Press CTRL+C to quit)
```
الابليكشن كدا بيسيرف علي ال `localhost` بورت `5000` وبما انو انا الماشين بتاعتي مش `ubuntu` فخليني اسحب نسخه `ubuntu` من دوكر ونبداء نشغل عليه
```bash
$ docker run -it -p 5000:5000 --name Flask_web -v <mounting_path>
```
هتحتاج تضيف في `hello` ابلكيشن فايل الاين دا 
```python
if __name__ == '__main__':
	app.run(host='0.0.0.0')
```
ودا مطلوب طول مانت هتشتغل علي دوكر عشان الابليكشن يسرف علي نفس ال `localhost` بتاع الماشين
كمان ممكن تشغل ال `debug mode` عشان متحتاحش مع كل تغيير في ملف تعمل ريستارت ل `flask`
```python
if __name__ == '__main__':
	app.run(host='0.0.0.0', debug=True)
```

#### المتغيرات في الراوت
تقدر تخلي الابليكشن مستني طلب معين مخزن في متغير الطلب دا ممكن يكون أي حاجه نص، رقم، كسر، فقرة كتالي

| string | \|(default) accepts any text without a slash\| |
| ------ | ---------------------------------------------- |
| int    | \|accepts positive integers                    |
| float  | accepts positive floating point values         |
| path   | like `string` but also accepts slashes         |
 والصي

#### تخطي HTML escaping
عشان تمنع انو المستخدم يعملك `HTML injection` لازم تعمل `HTML escaping` ودا بيتم من خلال 
```python
from markupsafe import escape

@app.route("/<name>")
def hello(name):
    return f"Hello, {escape(name)}!"
```
هنا الراوت هيكون أي كلام بعد ال `/` يعني كمثال لو كتبت في المتصفح `localhost:5000/Ehab` دا هيرجع الصفحه دي 
![[image1.png]]
ولكن لاحظ اني لو شلت ال `escape` المتصفح مباشرة هيحاول يرندر أي حاجه تدخله ككود `HTML`
```python
from flask import Flask
from markupsafe import escape

app = Flask(__name__)


@app.route("/<name>")
def hello(name):
    return f"Hello, {name}!"


if __name__ == '__main__':
    app.run(host='0.0.0.0', debug=True)
```
هندخل `localhost:5000/<h1>Ehab`
![[image2.png]]
اه اللي حصل مصيبه المتصفح حاول يرندر الصفحه كلها بالكود اللي جاله 

#### تخصيص طلب الHTTP
تقدر تخصص نوع معين يستقبله ال`path` من ال `HTTP methods` من خلال انك تدي معطيات للبارميتر `methods` 
```python
from flask import request

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        return do_the_login()
    else:
        return show_the_login_form()
```
كمان تقدر تشوف نوع الطلب اللي تم من خلال العنصر `request` في المكتبه 
وتقدر تفصل كل نوع من الطلبات في `decrator` لوحده 
```python
@app.get('/login')
def login_get():
    return show_the_login_form()

@app.post('/login')
def login_post():
    return do_the_login()
```

#### عرض الصفحات 
تطبيقًا عشان تخلي فلاسك تعرضلك اي صفحه هتستعمل الفانكشن `render_template()` ولازم تكون مخزن الملف اللي هيتعرض في مجلد `templates` لانو دا فلاسك اللي افتراضيا بتدور فيه عن الملف
```python
from flask import render_template

@app.route('/hello/')
@app.route('/hello/<name>')
def hello(name=None):
    return render_template('hello.html', name=name)
```
جوا الملف تقدر تستغمل `Jinja2` وتستغلها في عرض صفحاتك
```html
<!doctype html>
<title>Hello from Flask</title>
{% if name %}
  <h1>Hello {{ name }}!</h1>
{% else %}
  <h1>Hello, World!</h1>
{% endif %}
```

#### الوصول لبيانات الطلب
كل بيانات الطلب متخزنه في متغير عام اسمه `request` 
```python 
from flask import request
```
الكائن دا متخزن فيه بيانات الطلب ونوعه وحجات تاني نقدر نستفيد منها
```python
@app.route('/login', methods=['POST', 'GET'])
def login():
    error = None
    if request.method == 'POST':
        if valid_login(request.form['username'],
                       request.form['password']):
            return log_the_user_in(request.form['username'])
        else:
            error = 'Invalid username/password'
    # the code below is executed if the request method
    # was GET or the credentials were invalid
    return render_template('login.html', error=error)
```
طبعا لو لاحظت انو ال `form` دا عباه عن قاموس فبتالي الوصول للبيانانت هيكون اسهل بكتير
طيب ولول ال `key` اللي في القاموس `form` مش موجود؟؟ 
وقتها اكسبشن هيترفع من نوع `KeyError` عادي ولو مهندلتش الموضوع الابليكشن هيرجع للكلاينت `ERROR 400 bad request`
والاحسن انك تجيب اي بيانات بإستعمال الطريقه دي
```python
searchword = request.args.get('key', '')
```
ابقي اقرا اكتر عن الكائن `request` [هنا](https://flask.palletsprojects.com/en/2.3.x/api/#flask.Request)

#### رفع الملفات
الموضوع اسهل مما تتخيل مع الكائن `request` الفكره بس متنساش تحط `enctype="multipart/form-data"` في ملف ال `html` وانت بتكون الفورم بتاعتك
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>File Upload</title>
</head>
<body>
    <h1>Upload a File</h1>
    <form action="/upload" method="POST" enctype="multipart/form-data">
        <label for="file">Choose file:</label>
        <input type="file" id="file" name="file" required><br><br>
        <input type="submit" value="Upload">
    </form>
</body>
</html>
```

اي ملف بترفع بيكون كائن بايثون عادي في القاموس `files` المتواجد في الكائن `request` وتقدر تحفظه عادي ب `save`
```python
from flask import request

@app.route('/upload', methods=['GET', 'POST'])
def upload_file():
    if request.method == 'POST':
        f = request.files['the_file']
        f.save('/var/www/uploads/uploaded_file.txt')
    ...
```

#### البسواكيت والكحك
عشان تجيب قيمه اي كحكاية  هتستغمل `cookies` اتربيوت اللي قاعد في الكائن `request`
```python
from flask import request

@app.route('/')
def index():
    username = request.cookies.get('username')
    # use cookies.get(key) instead of cookies[key] to not get a
    # KeyError if the cookie is missing.
```
وعشان تعمل كحك هتشغل `set_cookie`  علي الكائن `resp` اللي اتعمل من `make respond` وهترجعها هي للكلاينت بدل ماترجع ال `render_tem` مباشرة
```python
from flask import make_response

@app.route('/')
def index():
    resp = make_response(render_template(...))
    resp.set_cookie('username', 'the username')
    return resp
```

#### إعادة التوجيه والأخطاء
عشان تعمل إعادة توجيه لاي مكان تاني `redirect` هنا عشان تنقذ الموقف، ولو في مره مش عجبك الكلاينت خلص عليه ب `abort`
```python
from flask import abort, redirect, url_for

@app.route('/')
def index():
    return redirect(url_for('login'))

@app.route('/login')
def login():
    abort(401)
    this_is_never_executed()
```
ولو في يوم فكرت تعدل الديفولت صفحه الايرور كود 
```python
from flask import render_template

@app.errorhandler(404)
def page_not_found(error):
    return render_template('page_not_found.html'), 404:w
```

#### الجلسات
بالإضافه للكائن الروش `request` عندنا كائن اروش اسمه `session`
```python
from flask import session
```
الكائن بسستعمل عشان نخزن فيه بيانات الجلسه والجلسه دي بنستعملها عشان نقدر ننقل البيانات معانا لاي راوت عايزينيه، بمعني اخر هي طريقه عشان نحفظ بيها ال`state` الحاليه للكلاينت
بس هووب استني لازم الاول تعمل سيكريت كي لليسشن عشان محدش يجيب اي بينات منها
```python
app.secret_key = 'your_secret_key'

```
ومن ثم بعدها نبداء نتعامل مع السيشن ونضيف ونمسح بيانات منها عادي في اي ريكويست، وبيانات الريكويست دي كلها متخزنه عند الكلاينت في البراوسزر بتاعه
```python
from flask import Flask, session, redirect, url_for, request

app = Flask(__name__)

@app.route('/')
def index():
    if 'username' in session:
        username = session['username']
        return f'Logged in as {username}'
    return 'You are not logged in'


@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        session['username'] = request.form['username']
        return redirect(url_for('index'))
    if 'username' in session:
        return redirect(url_for('index'))
    else:
        return '''
            <form method="post">
                <p><input type="text" name="username" placeholder="Enter your username">
                <p><input type="submit" value="Login">
            </form>
        '''


@app.route('/logout')
def logout():
    session.pop('username', None)  # Remove the username from the session
    return redirect(url_for('index'))


if __name__ == '__main__':
    app.run(debug=True)

```