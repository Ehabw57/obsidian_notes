السلام عليكم الداتا في عالم التكنولوجيا عمله مهمه جدا، وللأسف أساس معظم البرامج قائم علي الإحتفاظ بالبيانات، وطبعا لأهميتها أحنا منقدرش نخاطر بيها، فالنهردا هنتعلم أزاي نعمل نسخ احطياتي لبياناتنا علي mysql.
#### طريقه نسخ السيد والعبد :
هنستعمل طريقة النسخ `master-slave replication` واللي هي عبارة عن سرفرين واحد فيهم بينسخ بيانات التاني باستمرار، وبكد لو حصل أي عطل أو البيانات خربت في السرفر الأساسي هنقدر نرجعها من النسخه اللي علي السرفر التابع، وفي نفس الوقت بيبقا عندنا سيرفرين بيتخزن عليهم الداتا ونقدر نقرا منهم في نفس الوقت ودا بيزود سرعه توصيل وإستلام البيانات.

#### أولا:
هنحتاج سيرفرين جاهزين ومتسطب عليهم `mysql` لذا، وهنعتبر أنو عندك السيرفرين دول واحد هنرمزله `master` 
<div class="green_code_block"> <pre>master:~$ </pre> </div>

والتاني `slave`

<div class="Yallow_code_block"> <pre> slave:~$</pre> </div>

##### تعديل إعدادت firewall:
 عشان نعرف نخلي سيرفر العبد ينسخ من سيده لازم نعدل إعدادات ال`firewall` انه يسمح بإتصلات اللي جايه علي بورت 3306 من سيرفر العبد .
 <div class="green_code_block"> <pre>master:~$ sudo ufw allow from <span class="highlight">slave_server_ip</span> to any port 3306</pre> </div>

#### تعديل إعدادت mysql علي السيد:
الخطوه اللي بعد كدا هي تعديل ملف إعدادات `MySQL` بحيث انو احنا نعرف اننا هنخلي الجهاز دا مصدر للنسخ علي جهاز تاني.
ملف تعديل إعدادات `sql` موجود في `etc/mysql/mysql.conf.d/mysql.cnf/` يلا نفتح `vim` ونلعب فيه.
 <div class="green_code_block"> <pre>master:~$ vim /etc/mysql/mysql.conf.d/mydsql.cnf </pre> </div>

دور جوا الفايل علي `bind-address` هتلاقيه بيساوي `127.0.0.1` ودا بيقول ل `mysql` انه ميقبلش أي إتصالات إلا من ال  `localhost` وبتالي مش هتم عمليه النسخ.
```txt
 . . .
bind-address                = 127.0.0.1
 . . .
```

هنا هتغير ال `ip` وتحط مكانه `ip` سيرفر العبد.
 وبعدين دول علي `server-id` جوا الفايل وشيل التعليق من عليه أو علامه `#` وأديله أي رقم، بس حط في الإعتبار انو الرقم دا مينفعش يتكرر علي سيرفر تاني  في مجموعه النسخ اللي انت بتصممها.
 ``` text
 . . . 
 #server-id               = 1
 . . .
 ```
وعشان نخلي الامور بسيطه انا حطيت رقم 1
تحت ال `server-id` هتلاقي `log-bin` ودا لازم برضو تشيل التعليف من عليه عشان `sql` يقرا ويخزن البيانات بتاعتك عليه وبسلمها لسيرفر العبد.
```text
. . .
log_bin                       = /var/log/mysql/mysql-bin.log
. . . 
```
وأخيرا أنزل لأخر الملف تحت هتلاقي `binlog-do-db` شيل برضو التعليق من عليه وضيف أسماء قواعد البيانات اللي حابب السيرفر يسلمها  للعبد.
```text
. . .
binlog_do_db           = <حط هنا اسم قاعدة بيانات>
```
ولو حبيت تنسخ اكتر من قاعدة هتضيف أكتر من `binlog_do_db` في الفايل
```text
binlog_do_db           = db_1
binlog_do_db           = db_2
binlog_do_db           = db_3
binlog_do_db           = db_4
```
وتقدر كمان تحط أسماء قواعد بيانات في `binlog_ignore_db` عشان `sql` يتجاهلها ومينسخهاش
```text
binlog_ignore_db       = ignore_this_db=
```
وبعدين إعمل إعاده تشغيل ل `myslq`
 <div class="green_code_block"> <pre class="green_code_block">master:~$ sudo service mysql restart</pre> </div>

#### تخصيص مستخدم للنسخ:
عشان سيرفر العبد  يعرف ينسخ  محتاج مستخدم معاه الصلاحيات المنسابه  علي قاعدة بيانات سيده فخش أعمل مستخدم جديد علي القاعدة.
 <div class="green_code_block"> <pre>mysql> CREATE USER 'replcation_user'@'slave_server_ip' IDENTIFIED BY 'password'; </pre> </div>
 وبعدها نديله الصلاحيات المنسابة واللي هي `REPLICATION SLAVE`
  <div class="green_code_block"> <pre>mysql> GREANT REPLICATION SLAVE ON *.* TO 'replcation_user'@'slave_server_ip'; </pre> </div>
  بعدها اعمل `flush` عشان نطبق الصلاحيات
   <div class="green_code_block"> <pre>mysql> FLUSH PRIVILEGES ; </pre> </div>
   وبكدا نكون جهزنا سيرفر السيد إنه ينسخ ، باقي بس ناخد منه شويه بيانات مهمه ونسلمها للعبد
#### إستلام إحداثيات النسخ وتسليمها للعبد:
في سبيل أنو العبد يعرف فين مكان ملف `binray_log` بلازم نسلمه إحداثيات الملف.
في الخطوه دي لو أنت فاتح قواعد البيانات بتاعتك والمستخدمين عمالين يكتبو ويقروا منها، لازم تقفلها عشان تتجنب أي تعديل مفاجئ في البيانات.
الأمر دا هيقفل كل الجداول المفتوحه علي القاعدة ودا هيسبب وقف مؤقت لعمليات القراءة والكتابة من الجداول.
   <div class="green_code_block"> <pre>mysql> FLUSH TABLES WITH READ LOCK; </code> </div>
   وبعدين شغل الأمر دا عشان تاخد إحداثيات ملف ال `binary_log`
      <div class="green_code_block"> <pre>mysql> SHOW MASTER STATUS; </pre> </div>

هتلاقي ال`output` حاجه شبه كدا
```output
+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000001 |      899 | db           |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)
```
هنحتاج بيانات `file` و`postion`  فأحفظ الاتنين دول لحد مانروح لسيرفر العبد.

#### إنشاء نسخه من البيانات الحاليه: 
في الوضع الحالي اللي انت فيه المفروض انك قافل الجداول ومحدش قادر يكتب عليها او يقرا منها، وافتكر اننا عملنا كدا بغرض حماية النسخه اللي احنا ناوين نعملها، ولكن مع ألاسف لو انت طلعت من الجلسه الحاليه بتاعت `mysql` أو قفلت التيرمنال او قريت من الجداول، فدا هيؤدي أوتوماتيكلي لفتح الجداول مره تاني.

وعشان كدا حاول تخلي الخطوه الجايه في تيرمنال منفصله عن الحاليه اللي انت فاتحها.
 <div class="green_code_block"> <pre class="green_code_block">master:~$ mysqldump -u root -p <span class='highlight'>db</span> > <span class='highlight'>db</span>.sql</pre> </div>

في النقطة دي هيتم إنشاء ملف `db.sql` علي الجهاز ووقتها تقدر تنسخها لسيرفر العبد.

 
 <div class="green_code_block"> <pre class="green_code_block">master:~$ scp db.sql <span class='highlight'>slave_server_user</span>@<span class='highlight'>slave_server_ip</span>:/tmp</pre> </div>

بعدها تقدر ترجع لتيرمنال `sql` وترحع تفتح الجداول
   <div class="green_code_block"> <pre>mysql> UNLOCK TABLES; </pre></div>

ومن حيث هذه النقطه أحنا كدا أنتهينا من الخطوات علي سيرفر السيد، ودلوقتي نتتقل لسيرفر العبد

##### تسلم الإحداثيات للعبد:
أول حاجه لازم نعملها هي اننا ندخل علي سيرفر العبد ب`ssh` ثم بعدها نفتح `mysql`
<div class="Yallow_code_block"> <pre> slave:~$ mysql -u <span class='highlight'>sqluser</span> -p</pre> </div>

انشئ قاعدة البيانات اللي هتنسخ من أختها علي سيرفر السيد
<div class="Yallow_code_block"> <pre> mysql> CREATE DATABASE <span class='highlight'>db</span>;</pre> </div>

مش محتاج بعدها تعمل أي جداول جوا قاعدة اليانات بما انها هتنسخ من اختها ال `metadata` بتاعت ملف `db.sql` اللي اخدناه من سيرفر السيد.
<div class="Yallow_code_block"> <pre> slave:~$ mysql <span class='highlight'>db</span> &lt; /tmp/<span class='highlight'>db.sql</span></pre></div>

وبكدا عندنا نسخه كامله من البيانات اللي علي سيرفر السيد بافي بس خطوه أخيرة عشان نخلي البيانات تتنسخ اول بأول علي سيرفر العبد.

#### تعديل إعدادت mysql علي العبد:
نفس المره اللي فاتت هنعدل ملف `etc/mysql/mysql.conf.d/mysqld.cnf/` بس المره دي علي سيرفر العبد.
<div class="Yallow_code_block"> <pre> slave:~$ vim /etc/mysql/mysql.conf.d/mysqld.cnf</pre> </div>

عدل بقا `server-id` وأفتكر اني قلتلك مينفعش سيرفيرين ياخدو نفس الرقم في مجموعه النسخ.
 ``` text
 . . . 
 server-id               = 2
 . . .
 ```
 هنا حطينا رقم 2 برضو للتبيسط

دلوقتي دول علي `bin_log` و `binlog_do_db` وعدلها لنفس القيم اللي علي سيرفر السيد
```text
. . .
log_bin                 = /var/log/mysql/mysql-bin.log
. . .
binlog_do_db            = db
. . .
```
واخيرا ضيف السطر دا في اخر الملف واللي هيحدد الملف اللي هيعتمد عليه `mysql` في إستلام البيانات
```text
. . .
relay_log               = /var/log/mysql/mysql-relay-bin.log
```
واخير اعمل إعادة تشغيل ل `mysql`
<div class="Yallow_code_block"> <pre> slave:~$ service mysql restart</pre> </div>

وبكدا انت جاهز لبدء عمليه النسخ بين السيرفرين 

#### بدء واختبار النسخ:
علي سيرفر العبد شغل `mysql` وضيف البيانات دي
بمجرد متحط البيانات `sql` هبيداء يدخل علي السيرفر السيد اللي ال`ip` بتاعه في قيمه `SOURCE_HOST` وهيدخل علي `mysql` بالاسم والباس اللي تحت `SOURCE_USER` و `SOURCE_PASSWORD` وطبعا انت اكيد القيمتين اللي قلتلك احفظهم فوق لان دول هيتحطو في `SOURCE_LOG_POS` و `SOURCE_LOG_FILE` .
إتأكد من بياناتك كويس وابداء حطهم في `sql` .
<div class="Yallow_code_block"> <pre> 
mysql> CHANGE REPLICATION SOURCE TO
		SOURCE_HOST=<span class='highlight'>'source_server_ip'</span>,
		SOURCE_USER=<span class='highlight'>'replica_user'</span>,
		SOURCE_PASSWORD=<span class='highlight'>'password'</span>,
		SOURCE_LOG_FILE=<span class='highlight'>'mysql-bin.000001'</span>,
		SOURCE_LOG_POS=<span class='highlight'>899;</span>
		</pre></div>

تمم علي بياناتك وشغل أمر التكرار بقا.
<div class="Yallow_code_block"> <pre> mysql> START REPLICA;</pre> </div>
لو انت أشتغلت كل الخطوات صح فالمفروض انو `sql` دلوفتي بينسخ سيرفر السيد وأي بيانات هتغير هناك هبداء ينسخها هنا.
وعشان تتأكد شغل الأمر.
<div class="Yallow_code_block"> <pre> mysql> SHOW REPLICA STATUS;</pre> </div>

المفروض يطلع معاك الاوتبوت دا.
```
Output*************************** 1. row ***************************
             Replica_IO_State: Waiting for master to send event
                  Source_Host: 138.197.3.190
                  Source_User: replica_user
                  Source_Port: 3306
                Connect_Retry: 60
              Source_Log_File: mysql-bin.000001
          Read_Source_Log_Pos: 1273
               Relay_Log_File: mysql-relay-bin.000003
                Relay_Log_Pos: 729
        Relay_Source_Log_File: mysql-bin.000001
. . .
```

