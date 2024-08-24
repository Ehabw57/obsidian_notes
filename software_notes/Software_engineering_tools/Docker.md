# Docker_Basics
 برنامج مفتوح المصدر بيتيح ليك انك تغمل `deploye` للبرامج بتاعتك علي `layer` منفصله عن السيستم الأساسي بتاعك .
## Images
الصور هي مجرد نسخه من النظام اللي انت عايز تنزله عندك علي الجهاز وتشغله جوا `container` الجميل بقا انو النظام اللي انت هتنزله  ممكن يكون خفيف جدا ومفهوش اي بكجات نازله معاه أو نظام متكامل نازل بكل البكحات والتولز اللي انت عايزها.
عشان تنزل أي `image` علي دوكر بتعمل 
```bash
docker pull <image_name>
```
كمثال عشان انزل نسخه `ubuntu 12.04` هشغل الامر
 ```bash
zerobors@Manjaro ~ » docker pull ubuntu:12.04
12.04: Pulling from library/ubuntu
d8868e50ac4c: Pull complete
83251ac64627: Pull complete
589bba2f1b36: Pull complete
d62ecaceda39: Pull complete
6d93b41cfc6b: Pull complete
Digest: sha256:18305429afa14ea462f810146ba44d4363ae76e4c8dfc38288cf73aa07485005
Status: Downloaded newer image for ubuntu:12.04
docker.io/library/ubuntu:12.04
zerobors@Manjaro ~ »
```
وبكدا اكون نزلت النسخه عندي علي اللوكال واقدر أستعملها واعمل منها `containers`  زي منا عايز.

وعشان اشوف النسخ اللي عندي علي الجهاز بستعمل الامر `docker images`
```bash
zerobors@Manjaro ~ » docker images
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
ubuntu       20.04     2abc4dfd8318   3 months ago   72.8MB
ubuntu       latest    3db8720ecbf5   5 months ago   77.9MB
ubuntu       14.04     13b66b487594   3 years ago    196MB
ubuntu       12.04     5b117edd0b76   7 years ago    104MB
zerobors@Manjaro ~ »
```
ودا هيعرضلي كل الصور اللي نازله عندي علي الجهاز بالحجم بتاعها وال `id` بتاعها وهنعرف ايه لازمه الاي دي دا بعدين.

عشان امسح صوره من عندي علي الجهاز هستعمل الأمر `docker image rm <image_name>` 
```bash
zerobors@Manjaro ~ » docker image rm ubuntu:12.04
Untagged: ubuntu:12.04
Untagged: ubuntu@sha256:18305429afa14ea462f810146ba44d4363ae76e4c8dfc38288cf73aa07485005
zerobors@Manjaro ~ » docker images
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
ubuntu       20.04     2abc4dfd8318   3 months ago   72.8MB
ubuntu       latest    3db8720ecbf5   5 months ago   77.9MB
ubuntu       14.04     13b66b487594   3 years ago    196MB
zerobors@Manjaro ~ » 
```

حلو دلوقتي لازال عندنا مجرد صور علي الجهاز ملهاش اي لازمه انا عايز أعرف دلوقتي أعمل بأهلهم إيه
حلو عزيزي وهنا يجي دور ال`container`.

## containers
الحاويه ماهو إلا نسخه من الصوره بتقدر تشغلها وتتفاعل معاها وكأنها طبقة معزوله عن السيستم بتاعك وكل `container` بتتعامل معاه لوحده واي بكجات او تولز او فايلات متواجده عليه بتكون منعزله تماما عن السيستم الأصلي .
تعال بقا نعمل `container` من احد ال `images` اللي عندنا علي الجهاز
وهنا هنستعمل كوماند `docker run <based_image> 0` 
```bash
zerobors@Manjaro ~ » docker run ubuntu:14.04
zerobors@Manjaro ~ »
```
انا شغلت الكوماند ولكن مفيش حاجه حصلت ؟؟
لا بالعكس في حاجه حصلت تعال نشوفها سوا.
 **أول حاجه اتعمل `container` بالفعل من النسخه اللي انا ادتهالو `ubuntu 14.04`** 
وعشان اتأكد من الكلام دا هستعمل  `docker ps` المفروض الكوماند دا هيعرض كل الحاويات  اللي شغاله حاليا علي الجهاز .
```bash
zerobors@Manjaro ~ » docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
zerobors@Manjaro ~ » 
```
الظاهر انو مفيش عندي `container` شغال حاليا فخلينا نشوف كل الكونتينرات من خلال الفلاج `-a`
```bash
zerobors@Manjaro ~ » docker ps -a
CONTAINER ID   IMAGE          COMMAND       CREATED         STATUS                     PORTS     NAMES
68531eb660a9   ubuntu:14.04   "/bin/bash"   6 minutes ago   Exited (0) 6 minutes ago             charming_goldstine
zerobors@Manjaro ~ »
```
وهنا هنلاحظ انو ظهر عندنا اخيرا الحاويه تعال نتعرف علي المعلومات بتاعتها.

- **CONTAINTER_ID**: 
الأي دي الخاص بالحاويه وكل حاويه ليها الاي دي الخاص بيها اللي بيميزها عن بافي اخواتها بمعني عمرك ماهتلاقي أتنين حاوية بنفس ال`id` 
- **IMAGE**:  
ودا بيقولك الحاوية دي أتعملت من أنهو صوره ودا منطقي لاننا فوق فعلا عملهنا من `ubuntu 14.04` ولو كنا اخترانا صوره مختلفه كانت هتظهر هنا.
- **COMMAND**:
ودا بيقولك ايه الأمر اللي اشتغل علي الحاوية دي لما اتعملت وبما أنو احنا مشغلناش أي حاجه فاالحاوية شغلت ال `bash` وقفلت علي طول.
- **CREATED**:
ودا بيقولك الوقت اللي مر من ساعه ماتعملت  الحاوية 
- **STATUS**: 
ودا  ايه كان ال`exit code` بتاع الحاويه بعد ماقفلت
- **PORTS**:
هنبقي نشرحها قدام
- **NAME**: 
اسم الحاويه ايه، وبما أنو احنا مخترناش اسم للحاويه فهو سماها أي أسم عشوائي.

جميل ودلوقتي خلينا نعمل حاويه تاني ونشوف بس المره دي انا هشغل عليها أمر معين
```bash
zerobors@Manjaro ~ » docker run ubuntu:20.04 echo "hello_world"
hello_world
zerobors@Manjaro ~ »
```
اللي عملنا هنا هو حاويه جديده بس شغلنا عليها امر الطباعة `echo`  فهو مباشره دخل جوا الحاويه اللي علها وشغل الأمر دا، يعني الطباعة اللي ظهرت كانت من الحاوية مش من السيستم بتاعي.
```bash
zerobors@Manjaro ~ » docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED             STATUS                         PORTS     NAMES
04c248ad509d   ubuntu:20.04   "echo hello_world"   2 minutes ago       Exited (0) 2 minutes ago                 bold_chatelet
68531eb660a9   ubuntu:14.04   "/bin/bash"          About an hour ago   Exited (0) About an hour ago             charming_goldstine
zerobors@Manjaro ~ »
```
دي الحاويات اللي متاحه عندي عايزك تاخد وفتك  وتقارن وتشوف الفورق بينهم هتلاقي انو الأختلاف في ال`id` ودا بديهي وال `image` بما انو احنا اخترنا صوره مختلفه المره دي وكذالك `created` و `command`

هحاول أشغل أمر تاني بس المره دي علي حاويه اتعملت بالفعل من خلال `docker exec <container_name> <command> 0 
```bash
zerobors@Manjaro ~ » docker exec  charming_goldstine  echo "helo_world"
Error response from daemon: container 68531eb660a91aa79a6d55dcf7deb35bd02ac5eae3329ef6d58e148d9c3a8459 is not running
zerobors@Manjaro ~ » docker exec  6853  echo "helo_world"
Error response from daemon: container 68531eb660a91aa79a6d55dcf7deb35bd02ac5eae3329ef6d58e148d9c3a8459 is not running
zerobors@Manjaro ~ »
```
بيدني رساله بيقولي الحاويه مشغلاش  ودا منطقي لانها لو كانت شغاله كان ظهرت معايا في ال `dokcer ps` ومن غير الفلاج.
بس الجميل اللي عايزك تلاحظه انو docker عرف انا قصدي علي انهو حاويه مره بالاسم ومره بأول اربع حروف من ال id.

طيب حلو أيه الجميل في اني بمجرد ما اعمل حاويه مقدرش اشغل عليها غير امر واحد بس وبعدين بتقفل مني، ولحسن الحظ هنا يجي دور الفلاجين `-it` فتعالي نعمل حاويه جديده بافلاجين دول .
```bash
zerobors@Manjaro ~ » docker run -it ubuntu:14.04
root@a1e1b50f7bde:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@a1e1b50f7bde:/# echo "wellcome_to_docker"
wellcome_to_docker
root@a1e1b50f7bde:/# exit
exit
zerobors@Manjaro ~ »
```
انا هنا عملت حاويه جديده ودخلت جواها والاوامر اللي انت شايفها دي كلها من جوا الحاويه وبعد ما خلصت عملت `exit`  ودا رجعني للسيتم بتاعي، ولو جينا نشوف الحاويات اللي عندينا دلوقتي.
```bash
zerobors@Manjaro ~ » docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED              STATUS                          PORTS     NAMES
a1e1b50f7bde   ubuntu:14.04   "/bin/bash"          About a minute ago   Exited (0) About a minute ago             practical_shaw
04c248ad509d   ubuntu:20.04   "echo hello_world"   18 minutes ago       Exited (0) 18 minutes ago                 bold_chatelet
68531eb660a9   ubuntu:14.04   "/bin/bash"          About an hour ago    Exited (0) About an hour ago              charming_goldstine
zerobors@Manjaro ~ »
```
هنلاحظ انو ظهر عندا الحاويه اللي لسا كنا شغالين عليها .

طيب الدنيا هنا بقت مدعكه فخلاينا نخلص من شويه حاويات ملهاش لازمه دلوقتي من خلال `docker rm <container_name> 0`
```bash
zerobors@Manjaro ~ » docker rm practical_shaw 04c248
practical_shaw
04c248
zerobors@Manjaro ~ » docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
68531eb660a9   ubuntu:14.04   "/bin/bash"   About an hour ago   Exited (0) About an hour ago             charming_goldstine
zerobors@Manjaro ~ »
```
برضو لاحظ اني قدرت اوصل لحاويتين مختلفتين من خلال مره بالاسم ومره بالاي دي.
**في فلاجات كتير ل`docker run` ابقي بص عليها من خلال `docker run --help` لكن خليني اعرفك علي اهمهم**
- -d
خلي الحاويه شغاله في الخلفية `detashed mode`
- --name
حط إسم للحاويه
- -i
خلي ال STDIN ستريم بتاع الحاويه شغال  (مهم عشان تعرف تكتب اوامر جوا  الحاويه) 
- -ip
خصص `ip4` للحاويه 
- -t
احجز  للحاويه `tyy` مزيفه  (مهم عشان تعرف تتعامل مع الحاويه من خلال I/O streams)
- -v
أعمل mount لمكان معين في الحاويه علي مكان معين في السيستم
- --rm
امسح الحاويه بمجرد ماتتقفل
- -p
اربط port من الحاويه بport علي الجهاز 

تعال نجرب بعض الفلاجات دي
```bash
zerobors@Manjaro ~ » docker run -it -d --rm --name my_container ubuntu:20.04
bd2d04c010a7bec6173eecdbac60034503c05660b1c88ff9bce0a32cb23afd74
zerobors@Manjaro ~ » docker ps
CONTAINER ID   IMAGE          COMMAND       CREATED         STATUS         PORTS     NAMES
bd2d04c010a7   ubuntu:20.04   "/bin/bash"   9 seconds ago   Up 7 seconds             my_container
zerobors@Manjaro ~ » docker stop my_container
my_container
zerobors@Manjaro ~ » docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
zerobors@Manjaro ~ »
```
حلو دلوقتي بفضل ال `d-` اصبح عندنا حاويه شغاله في الخلفية يعني حتي لو قتلنا التيرمنال خالص الحاويه هتفضل شغاله 
نقدر نقفل حاويه شغاله من خلال `docker stop <contaner_name> 0` 
وزي مالاحظت بمجرد مالحاويه اتقفلت اتمسحت بسبب ال `rm--` ، حتي ال`docker ps -a` معرفش يلاقي حاجه.

## Dockerfiles

