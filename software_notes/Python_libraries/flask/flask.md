فلاسك هي عباه عن `web_framework` مصممه للغه بايثون عشان نبني بيها `web_application`
### تشغيل المكتبه
كل اللي محتاحه تعمله هي انك تستورد الكلاس `Flask` من المكتبه 
```python
from flask import Flask
```
### برنامج بسيط 
```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
	return "fuck_world"
```
شغل الابلكيشن من خلال `flask --app <file_name> run` 