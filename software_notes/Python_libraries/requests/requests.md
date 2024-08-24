هي مكتبه خارجيه في بايثون بتستعمل لإرسال طلبات بروتوكول `HTTP` والتعامل مع الردود .

#### تنزيل وإستيراد المكتبة:
للأسف المكتبة مش موحودة إفتراضيا مع بايثون لذا لازم تنزلها يدويا
```bash
pip3 install requests
```
او لو أنت علي Arch
```bash
sudo pacman -S python-requests
```
ومن ثم 
```python
import requests 
```
#### إرسال الطلبات:
المكتبه بتدعم جميع أنواع ال `HTTP request` من `GET` لحد `OPTIONS` لذا خلينا نبعت أول طلب لموقعنا الجامد `http://guideck.tech`
```python
>>> import requests
>>> requests.get("http://guideck.tech")
<Response [200]>
>>>
```
مبروك كدا أحنا بعتنا أول طلب من المكتبه ودلوقتي هنتعلم أزاي نتعامل مع الرد.
#### التعامل مع الرد:
الرد اللي بيرجع من الطلب عبارة عن كائن قوي جدا، خلينا نشوف كل الميثودز بتاعته ب `dir`، فتعال نعمل طلب تاني ونخزنه في متغير
```python
>>> respond = requests.get("http://guideck.tech")
>>> for methond in dir(respond):
...     print(methond)
...
__attrs__
__bool__
__class__
__delattr__
__dict__
__dir__
__doc__
__enter__
__eq__
__exit__
__format__
__ge__
__getattribute__
__getstate__
__gt__
__hash__
__init__
__init_subclass__
__iter__
__le__
__lt__
__module__
__ne__
__new__
__nonzero__
__reduce__
__reduce_ex__
__repr__
__setattr__
__setstate__
__sizeof__
__str__
__subclasshook__
__weakref__
_content
_content_consumed
_next
apparent_encoding
close
connection
content
cookies
elapsed
encoding
headers
history
is_permanent_redirect
is_redirect
iter_content
iter_lines
json
links
next
ok
raise_for_status
raw
reason
request
status_code
text
url
>>>
```
الموضوع كبير قوي يامحسن فتعال نفهم شويه من الميثودز دي .
##### كود الرد:
كود الرد أو `status code` نقدر نجيبها بالطريقة دي 
```python
>>> respond.status_code
200
```
 هيكون من البسيط جدا أنك تطلع كود رد السيرفر لما تستعمل `if` `else` بلوك.
 ```python
 if response:
    print("Success!")
else:
    raise Exception(f"Non-success status code: {response.status_code}")
```
في الكود دا احنا بنتحقق إذا كان الرد بين 200 ل 399 وإلا فهنرفع `ecxeption`
الجميل أنك مش محتاج تبني الكلام دا بنفسك، المكتبة بتوفرلك ميثودز تقدر من خلال ترفع بيها `excption`
```bash
zerobors@Manjaro ~ » cat request.py
#!/usr/bin/python
import requests
from requests.exceptions import HTTPError

URLS = ["https://guideck.tech", "https://guideck.tech/Not_found"]

for url in URLS:
    try:
        response = requests.get(url)
        response.raise_for_status()
    except HTTPError as http_err:
        print(f"HTTP error occurred: {http_err}")
    except Exception as err:
        print(f"Other error occurred: {err}")
    else:
        print("Success!")
zerobors@Manjaro ~ » ./request.py
Success!
HTTP error occurred: 404 Client Error: Not Found for url: https://api.github.com/invalid
zerobors@Manjaro ~ »
```

 الفكرة هنا أنك لما تشغل `raise_for_status` علي أي  رد، لو كان ال `stauts code` بتاع الرد بين 400 ل 600 هيرفع `exception` .
 
##### محتوي الرد:
عشان تشوف محتوي الرد هتستعمل `content` ودا هيورينا طبعا محتوي الرد بال`byte` .
```python
>>> import requests
>>> request = requests.get("http://guideck.tech")
>>> request.content
b'Hello World!\n'
>>> request.content
b'Hello World!\n'
```
 الجميل هنا مش زي `urllib` لما كنا بنشغل `read` فبتالي انا أقدر احتقظ بالبيتات قد مانا عايز.
ولكن ماذا لو أنا عايز احول لنص؟؟.

##### التحويل لنص:
ببساطه هتشغل علي الرد `.text` ودا هيرجعلك مباشرة التمثيل النصي للبيتات، وهنا المكتبه بتحاول تكتشف ال chart set من خلال رؤوس الرد، ولو مش متوفره هتستعمل ال `utf-8` إفتراضيًا.
```python
>>> request.encoding
'ISO-8859-1'
>>> request.text
'Hello World!\n'
>>>
```
كمان لو هتكلم سيرفر بيهوست `REST API`تقدر تحول الرد مباشرة ل `json` من خلال الميثود دي 
```python
>>> import requests

>>> response = requests.get("https://api.github.com")
>>> response.content
b'{"current_user_url":"https://api.github.com/user", ...}'
>>> response.json()
{'current_user_url': 'https://api.github.com/user', ...}

>>> type(response.json())
<class 'dict'>
```
بالحديث بقا عن الرؤوس يلا بينا نعرف ازاي نتعامل معاها

#### التعامل مع رؤوس الرد:
الموضوع هنا أبسط مما تتخيل، الميثود `headers()` هترجعلك الردود في كائن شبيه بالقاموس في بايثون.
```python
>>> respond = requests.get("http://guideck.tech")
>>> type(respond.header)
<class 'requests.structures.CaseInsensitiveDict'>
>>> for head, value  in respond.headers.items():
...     print("{}: {}".format(head, value))
...
Server: nginx/1.18.0 (Ubuntu)
Date: Sat, 17 Aug 2024 07:45:00 GMT
Content-Type: text/html
Content-Length: 13
Last-Modified: Thu, 15 Aug 2024 16:10:05 GMT
Connection: keep-alive
ETag: "66be285d-d"
X-Served-By: 486841-web-01
Accept-Ranges: bytes
>>>
>>> header = respond.headers
>>> header["X-Served-By"]
>>> header["x-served-by"]
'486841-web-01'
>>>
```
لاحظ أنو القاموس دا مش `case senstive` وبتالي مش هتقلق من أسم الهيدر اللي عايز تجيب قيمته.

#### ارسال الرؤوس في الطلب
عشان تبعت رؤوس في الطلب هتبعتها في شكل قاموس  مع `get` في باراميتر اسمو `headers`، وعشان تبعت جسم للطلب هتبعته في بارميتر اسمه `params` برضو علي شكل قاموس
```python
# user.py

#!/usr/bin/python
import requests

response = requests.get(
    "https://api.github.com/search/users",
    params={"q": '"Ehabw57"'},
    headers={"Accept": "application/vnd.github.text-match+json"},
)

json_response = response.json()
first_user = json_response["items"][0]
for key, value in first_user.items():
    print("{}: {}".format(key, value))
```
ولما نشعل الاسكربت دا الاوت بتاعنا.

```bash
zerobors@Manjaro ~ » ./users.py
login: Ehabw57
id: 136044546
node_id: U_kgDOCBvgAg
avatar_url: https://avatars.githubusercontent.com/u/136044546?v=4
gravatar_id:
url: https://api.github.com/users/Ehabw57
html_url: https://github.com/Ehabw57
followers_url: https://api.github.com/users/Ehabw57/followers
following_url: https://api.github.com/users/Ehabw57/following{/other_user}
gists_url: https://api.github.com/users/Ehabw57/gists{/gist_id}
starred_url: https://api.github.com/users/Ehabw57/starred{/owner}{/repo}
subscriptions_url: https://api.github.com/users/Ehabw57/subscriptions
organizations_url: https://api.github.com/users/Ehabw57/orgs
repos_url: https://api.github.com/users/Ehabw57/repos
events_url: https://api.github.com/users/Ehabw57/events{/privacy}
received_events_url: https://api.github.com/users/Ehabw57/received_events
type: User
site_admin: False
score: 1.0
```

#### إرسال باقي أنواع الطلبات
بعيدا عن `GET` في طلبات تاني ينفع تتبعت من خلال المكتبة، وزي بالظبط الميثود بتاعت `get` هتلاقي برضو لكل طلب ميثود بأسمه `post`, `options`, `put`, `delete`, ..etc
```python
>>> import requests

>>> requests.get("https://httpbin.org/get")
<Response [200]>
>>> requests.post("https://httpbin.org/post", data={"key": "value"})
<Response [200]>
>>> requests.put("https://httpbin.org/put", data={"key": "value"})
<Response [200]>
>>> requests.delete("https://httpbin.org/delete")
<Response [200]>
>>> requests.head("https://httpbin.org/get")
<Response [200]>
>>> requests.patch("https://httpbin.org/patch", data={"key": "value"})
<Response [200]>
>>> requests.options("https://httpbin.org/get")
<Response [200]>
```
##### جسم الطلب:
من المعروف أنو `post`, `put` بيبعتوا مع طلباتهم بيانات في جسم الطلب ودي هنعملها من خلال البارميتر `data` وهنبعتله البيانات برضو في شكل قاموس .
```python
>>> import requests

>>> requests.post("https://httpbin.org/post", data={"key": "value"})
<Response [200]>
```
وبرضو للتسهل تقدر تبعتها ك `list of tuples`
```python
requests.post("https://httpbin.org/post", data=[("key", "value")])
<Response [200]>
```
 وكمان تقدر تبعنها ك `json`
 ```python
>>> response = requests.post("https://httpbin.org/post", json={"key": "value"})

>>> json_response = response.json()
>>> json_response["data"]
'{"key": "value"}'
>>> json_response["headers"]["Content-Type"]
'application/json'
```
تقدر تتأكد انو الداتا بتاعتك اتبعتت في جسم الطلب وانها بصيغة `json` من خلال انك تشوف جسم ورأس الطلب.
```python
>>> response = requests.post("https://httpbin.org/post", json={"key":"value"})

>>> response.request.headers["Content-Type"]
'application/json'
>>> response.request.url
'https://httpbin.org/post'
>>> response.request.body
b'{"key": "value"}'
```
