### What is urllib:
هي عبارة عن مكتبه مشهورة في بايثون بتستعمل في إرسال طلبات وإستقبال ردود  ال `HTTP` للخوادم .
##### ازاي اعمل استيراد للمكتبه؟
من خلال اللاين دا.
```python
import urllib
```
##### إرسال طلب
من خلال المكتبه هنبعت طلب لموقعنا اللطيف `guideck.tech` واللي في وضعه الحالي هو فقط عبارة عن ملف بحتوي كلمه "Hello World!\\n" .
```python
>>> from urllib import request
>>> with request.urlopen("http://guideck.tech") as respond:
...     body = respond.read()
...
>>> body[0:]
b'Hello World!\n'
```
لاحظ أننا أسعملنا `context mangaer` عشان منوجعش دماغنا بأننا نقفل ال`URL` اللي فتحناه من شويه في تاني سطر من الكود.
كمان بعد ماطبعنا الرد بتاع السيرفر في حاجه غريبه كدا مكتوب b قبل النص، هنبقي نعرف الموضوع دا قدام.
##### نوع الرد
لما بتاخد رد من السيرفر بعد ماتعمل طلب ب`request.urlopen()` الميثود بترجعلك `object`  للرد الي جالها من السيرفير، تعال نستكشف الميثودز اللي بيوفرها الكائن دا.
```python
>>> with request.urlopen("http://guideck.tech") as respond:
...     for method in dir(respond):
...             print(method)
...

__abstractmethods__
__class__
__del__
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
__gt__
__hash__
__init__
__iter__
__le__
__lt__
__module__
__ne__
__new__
__next__
__reduce__
__reduce_ex__
__repr__
__setattr__
__sizeof__
__str__
__subclasshook__
_abc_cache
_abc_negative_cache
_abc_negative_cache_version
_abc_registry
_checkClosed
_checkReadable
_checkSeekable
_checkWritable
_check_close
_close_conn
_method
_read_and_discard_trailer
_read_next_chunk_size
_read_status
_readall_chunked
_readinto_chunked
_safe_read
_safe_readinto
begin
chunk_left
chunked
close
closed
code
debuglevel
fileno
flush
fp
getcode
getheader
getheaders
geturl
headers
info
isatty
isclosed
length
msg
read
readable
readall
readinto
readline
readlines
reason
seek
seekable
status
tell
truncate
url
version
will_close
writable
writelines
```
وه واضح انو الموضوع كبير طحن، متخفش هنبقي نتعرف علي أهم الميثودز دي واللي هنحتاجها منهم.
##### التعامل مع رؤوس الرد
عشان أعرض الرؤوس بتاعت الرد هستعمل الميثود `headers` ودا هيرجعلي كائن من نوع `HTTPMessage` واللي اقدر اتعامل معاه زي القاموس `dictionary`
```python
>>> from urllib.request import urlopen
>>> respond =urlopen("http://guideck.tech") :
>>> respond.headers
<http.client.HTTPMessage object at 0x7fc38bd698d0>
>>> respond.headers.items()
[('Server', 'nginx/1.18.0 (Ubuntu)'), ('Date', 'Fri, 16 Aug 2024 14:37:36 GMT'), ('Content-Type', 'text/html'), ('Content-Length', '13'), ('Last-Modified', 'Thu, 15 Aug 2024 16:10:05 GMT'), ('Connection', 'close'), ('ETag', '"66be285d-d"'), ('X-Served-By', '486841-web-02'), ('Accept-Ranges', 'bytes')]
>>> print(respond.headers)
Server: nginx/1.18.0 (Ubuntu)
Date: Fri, 16 Aug 2024 14:37:36 GMT
Content-Type: text/html
Content-Length: 13
Last-Modified: Thu, 15 Aug 2024 16:10:05 GMT
Connection: close
ETag: "66be285d-d"
X-Served-By: 486841-web-02
Accept-Ranges: bytes


>>>
```
*طب لو أنا عايز رأس معين بس أعمل إيه؟*
هنا يجي دول الميثود `get_header()` أو ببساطه أستعمل ال [] عشان ترجع قيمه معينه من القاموس.
```python
>>> respond.getheader("X-Served-By")
'486841-web-02'
>>> respond.headers["X-Served-By"]
'486841-web-02'
```
لاحظ انو احنا مقفلناش الrespond حتي الان فيلا نقفله
```python
>>> respond.close()
```


في مشكله ممكن تكون غريبه
```python
>>> from urllib.request import urlopen
>>> with urlopen("http://guideck.tech") as respond:
...     respond.read(5)
...
b'Hello'
>>> respond.read(7)
b''
```
بسبب أنو ال `read` برا ال`scope` بتاع `with` فهنا أحنا بنحاول نقرا من رد اتقفل بالفعل با `close` ولكن بالرغم من ذالك المتغير `respond` لازال موجود ولكنه مش بيرجع أي حاجه.

ال`read` ميثود عليها زي  `cursor` بيقرا البيتات أول بأول وبتالي لو البيتات خلصت..
```python
>>> with urlopen("http://guideck.tech") as respond:
...     respond.read(5)
...     respond.read(8)
...     respond.read()
...

b'Hello'
b' World!\n'
b''
```
مش هيرجع حاجه من ال`body`، وللأسف مفيش طريقه تقدر ترجع بيها الداتا اللي اتقرت إلا أنك تعمل طلب جديد.
والسبب في كدا أنو زي ماقلنا حرف ال`b` هنا وظيفته انو يفهمك أنك بتقرا من بايتات مش نص وبتالي لازم عشان تحتفظ بالرد انك تحوله لنص.

##### التحويل من بايت لنص
عشان تحول البيتات لنص لازم تبقي عارف أنت بتتعامل مع أنهو `charcter set` في البيتات اللي معاك ولحن الحظ غالبا أنت هتكون بتتعامل مع `UTF-8` ، فيلا بينا نحول البيتات لنص .
```python
>>> with urlopen("http://guideck.tech") as respond:
...     body = respond.read()
...     decoded_body = body.decode('utf-8')
...
>>> print(decoded_body)
Hello World!
```

تقدر دلوقتي تلاحظ اننا حولنا جسم الرد لنص
إحطياطا برضو عشان تبقي عارف أنت بتتعامل مع أنو `charcter set` هتجيب المعلومه دي من رأس في رؤوس الرد بتاعك.
```python
>>> with urlopen("http://guideck.tech") as respond:
...     body = respond.read()
...     charset = respond.headers.get_content_charset()
...
>>> charset
'utf-8'
>>> body.decode(charset)
'Hello World!\n'
```
##### كتابته البيتات علي ملف
في طريقتين عشان تكتب البيتات بتاعت الجسم بتاع الرد علي فايل 
1. **عن طريق فتح الفايل في وضع `binary mode` والكتابه مباشرة** 
```python
>>> from urllib.request import urlopen
>>> with urlopen("https://www.example.com") as response:
...     body = response.read()
...
>>> with open("example.html", mode="wb") as html_file:
...     html_file.write(body)
...
1256
```


2. **انك تحول البيتات لنص  وبعدين تكتب النص علي الفايل**
ودا مفيد في أنك بعدين تكتب علي الفايل ب`charset` مختلف.
```python
>>> from urllib.request import urlopen
>>> with urlopen("https://www.google.com") as response:
...     body = response.read()
...
>>> character_set = response.headers.get_content_charset()
>>> character_set
'ISO-8859-1'
>>> content = body.decode(character_set)
>>> with open("google.html", encoding="utf-8", mode="w") as file:
...     file.write(content)
...
14066
```
وهنا احنا كتبنا علي الفايل ب`utf-8` 

##### إرسال  الرؤوس في الطلب

الطلب الإفتراضي اللي بتبعته المكتبه بيكون كالاتي:

```text
GET https://httpstat.us/403 HTTP/1.1
Accept-Encoding: identity
Host: httpstat.us
User-Agent: Python-urllib/3.10
Connection: close
```
الروؤس بتتبعت في شكل `dictionary` وعشان أعرف ابعت رؤوس مع طلب محتاج أختصر الجزء بتاع إرسال الطلب في `function` الأول
```python
from urllib.error import HTTPError, URLError
from urllib.request import urlopen

def make_request(url):
    try:
        with urlopen(url, timeout=10) as response:
            print(response.status)
            return response.read(), response
    except HTTPError as error:
        print(error.status, error.reason)
    except URLError as error:
        print(error.reason)
    except TimeoutError:
        print("Request timed out")
```

كل اللي هتعمل الداله دي انها بس تبعت طلب للسيرفر ولو نجح هتبطلع كود الرد  وترجع الرد والبيتات اللي جواه ، وإلا لو حصل خطأ هتطبع رساله خطأ والسبب بتاعها.
```python
>>> make_request("https://guideck.tech")
200
(b'200 OK', <http.client.HTTPResponse object at 0x0000023D612660B0>)
>>> make_request("https://gudeck.tech/fuck")
404 NotFound
```



حلو بينا بقا نعدل الداله ونخليها تستقبل قاموس من الرؤوس
```python

from urllib.error import HTTPError, URLError
from urllib.request import urlopen, Request

def make_request(url, headers_dict=None):
    request = Request(url, headers=headers_dict or {})
     try:
        with urlopen(request, timeout=10) as response:
             print(response.status)
             return response.read(), response
     except HTTPError as error:
         print(error.status, error.reason)
     except URLError as error:
         print(error.reason)
     except TimeoutError:
         print("Request timed out")
```
هنا انت بدل مابتاخد ال`URL` وتحطه في `urlopen` مباشرة، هنعمل قبلها خطوه وهي نعمل كائن نجمع فيه ال`URL` وال `Headers` من خلال الميثود `Request` ونعمل بيه طلب بعدين.
```python
>>> body, response = make_request(
...             "https://www.httpbin.org/user-agent",
...             {"User-Agent": "Yo ana header"})
200
>>> body
b'{\n  "user-agent": "Yo ana header"\n}\n'
```
هنا احنا بعتنا طلب لموقع بيهوست REST API وكل اللي بيرجعه عبارة عن ملف `json` بيرحع بيانات الرأس اللي اتبعتله بتاعت `user-agent` زي ماموضح تحت.
```bash
zerobors@Manjaro ~ » curl -s https://www.httpbin.org/user-agent
{
  "user-agent": "curl/8.8.0"
}
```
 ![[urllib_header_send.png]]
 