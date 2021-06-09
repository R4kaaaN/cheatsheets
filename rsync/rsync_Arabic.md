# rsync بالعربي

<div dir="rtl">
- أداة مخصصة لنسخ ومزامنة الملفات والمجلدات.

### يمكن إستخدامها لـ :

* من خلال الإنترنت عبر  بروتوكول ssh  أو rsync , إلخ ...

* قادرة على ضغط الملفات عند الإرسال وفك الضغط عند الإستقبال .  

* مزامنة بالتغييرات , نسخ الملفات المتغيره والجديدة وتخطي الملفات التي لم تتغير.

* تشفير الملفات المرسله.

* والكثير! 

### الإستخدام البسيط :

</div>

```bash
rsync [options] source destination
```

---

<div dir="rtl">

###  الخيارات المستخدمة في الشرح: 

- `-a` : وضع الأرشفه -rlptgoD (no -H,-A,-X).
- `-r` : التشعب نحو المجلدات الفرعية.
- `-l` : نسخ الإختصارات كـ إختصارات.
- `-p` : الحفاظ على الصلاحيات. *
- `-t` : الحفاظ على تاريخ التعديل. *
- `-g` : الحفاظ على المجموعات.
- `-o` : الحفاظ على المالك. *
- `-D` : الحفاظ على الملفات المميزة والأجهزة. *  
- `-v` : نتيجة مطوله. 
- `-e` : تحديد نوع الشل لإستخدامه في حالة الإتصال بسيرفر عبر الإنترنت.
- `-z` : ضغط الملفات عند الإرسال وفك الضغط عند الإستقبال (مفيد في حالة المزامنة عن بعد).
- `-c` : مقارنة عن طريق الهاش وليس عن طريق تاريخ التغيير والحجم.
- `-u` : نسخ الملفات المتغيره والجديدة فقط وإهمال الغير متغيره (فقط المتغير).
- `-P` : --partial && --progress. هذا الخيار دمج لخيارين الأول يختص في حالة انقطاع الإتصال يحفظ الحالة وعند إعادة الإرسال مرة أخرى يمكن من تلك النقطة والآخر يعرض التقدم.
- `-m` : يتجاهل المجلدات الفارغة.

\* يتطلب صلاحيات مدير نظام.

</div>

---

<div dir="rtl">

> مثال مزامنة/نسخ محتوى مجلد في نفس الجهاز

</div>

```bash
$ tree tests/
tests/
├── 01
│   └── 4.txt
├── 1.txt
├── 2.txt
└── 3.txt

1 directory, 4 files
```

<div dir="rtl">

* نلاحظ أنه يوجد مجلد فرعي اسمه 01 .

### سنقوم بمزامنه المحتوى الى مجلد backups .

</div>

```bash
$ rsync -v tests/* backups/ 
skipping directory 01
1.txt
2.txt
3.txt

sent 197 bytes  received 73 bytes  540.00 bytes/sec
total size is 0  speedup is 0.00
```

<div dir="rtl">

كما تلاحظ من المخرج تم تخطي المجلد 01 و بالنظر إلى المجلد`backups` نرى انه يحتوي فقط على

</div>

`1.txt`, `2.txt`, `3.txt`.



```bash
$ tree backups/
backups/
├── 1.txt
├── 2.txt
└── 3.txt

0 directories, 3 files
```

<div dir="rtl">

### لتفادي هذا السلوك الغير متوقع نستخدم خيار `-r` .

> مثال: 

</div>

```bash
$ rsync -rv tests backups/
sending incremental file list
tests/
tests/1.txt
tests/2.txt
tests/3.txt
tests/01/
tests/01/4.txt

sent 344 bytes  received 108 bytes  904.00 bytes/sec
total size is 0  speedup is 0.00
```

<div dir="rtl">

> `backups` محتوى :

</div>

```bash
$ tree backups/
backups/
└── tests
    ├── 01
    │   └── 4.txt
    ├── 1.txt
    ├── 2.txt
    └── 3.txt

2 directories, 4 files
```

<div dir="rtl">

#### ملاحظة هامة : 

اذا ابقيت علامة `/`  بعد إسم المجلد - `source/` -   ستقوم بمزامنه المحتوى فقط , ولكن اذا حذفت العلامة`source` ستقوم بمزامنه كامل المجلد كما شاهدنا في المثال اعلاه`tests` بالكامل تمت مزامنته في`backups`.   

#### قد ترغب بمزامنه المحتوى بدلا من مزامنه المجلد بالكامل في هذه الحالة ستضيف `/` إلى اسم المجلد.

</div>

```bash
$ rsync -rv tests/ backups/
sending incremental file list
1.txt
2.txt
3.txt
01/
01/4.txt

sent 326 bytes  received 104 bytes  860.00 bytes/sec
total size is 0  speedup is 0.00
```

```bash
$ tree backups/
backups/
├── 01
│   └── 4.txt
├── 1.txt
├── 2.txt
└── 3.txt

1 directory, 4 files
```

<div dir="rtl">

كما تشاهد تمت مزامنه محتوى مجلد `tests` بدلا من مزامنه كامل المجلد.

### نصيحة مجرب: 

تستطيع استخدام الخاصية  `--dry-run`  أو إختصارا `-n`  قبل تنفيذ عملية المزامنه , وستقوم بعرض المحتوى الذي سيتم مزامنته .
هذه الخاصية مفيدة جدا بحيث أنها لاتقوم بعملية المزامنه الفعلية ولكن ستقوم بعرض المحتوى الذي سيتم مزامنته.

#### ملاحظة: 

تحتاج إلى verbose لكي تعمل.

> مثال:

</div>

```bash
$ rsync -rvn tests/ backups/
sending incremental file list
1.txt
2.txt
3.txt
01/
01/4.txt

sent 174 bytes  received 32 bytes  412.00 bytes/sec
total size is 0  speedup is 0.00 (DRY RUN)
```

<div dir="rtl">

#### الملفات المعروضة هم الذين سيتم مزامنتهم. مجرد ماترى أن الأمور جيدة بالنسبة لك تستطيع حذف الخيار `-n` وتقوم بعملية المزامنه.

---

فرضاً لديك ملف اعدادات configuration file أو اي ملف تريد نقله والمحافظة على صلاحياته والملكية, إلخ....

في هذه الحاله ستضيف الخيار `-a`  حيث أنه سيتم نقل الملفات في وضع الأرشفه مع المحافظة على الإختصارات و الأجهزة والخصائص والصلاحيات والملكية.

</div>

```The files are transferred in archive mode, which ensures that symbolic links, devices, attributes, permissions, ownerships,  etc.  are preserved in the transfer.```

<div dir="rtl">

> مقارنة بسيطة: 
 
> بدون الخيار `-a`:

</div>

```bash
$ ls -la tests/
total 12
drwxr-xr-x 3 rakan rakan 4096 Jun  7 11:48 .
drwxr-xr-x 4 rakan rakan 4096 Jun  8 09:01 ..
drwxr-xr-x 2 rakan rakan 4096 Jun  7 11:48 01
-rwxrwxrwx 1 rakan rakan    0 Jun  7 11:48 1.txt
-rw-r--r-- 1 rakan rakan    0 Jun  7 11:48 2.txt
-rw-r--r-- 1 rakan rakan    0 Jun  7 11:48 3.txt
```

<div dir="rtl">

#### هنا قمت بتغير صلاحيات الملف `1.txt`  إلى `777`  فقط لتوضيح النقطة. وقمت بنسخ المحتوى بإستخدام الأمر التالي:

</div>

```bash
rsync -rv tests/ backups/
```

<div dir="rtl">
الأن لو قمنا بالتحقق من الصلاحيات:
</div>

```bash
$ ls -la backups/
total 12
drwxr-xr-x 3 rakan rakan 4096 Jun  8 09:03 .
drwxr-xr-x 4 rakan rakan 4096 Jun  8 09:04 ..
drwxr-xr-x 2 rakan rakan 4096 Jun  8 09:03 01
-rwxr-xr-x 1 rakan rakan    0 Jun  8 09:03 1.txt
-rw-r--r-- 1 rakan rakan    0 Jun  8 09:03 2.txt
-rw-r--r-- 1 rakan rakan    0 Jun  8 09:03 3.txt
```

<div dir="rtl">

كما نرى تم تغيير الصلاحيات من `777` إلى `755` , لكن ماذا لو أننا راضين مع `777` :) .

في هذه الحاله سنضيف الخيار `-a` .

> مع خيار `-a`  
#### لا نحتاج لإضافة الخيار `-r` بحيث أن الخيار `-a` سيضيفه لنا.

</div>

```bash
rsync -av tests/ backups/
```

```bash
$ ls -la backups/
total 12
drwxr-xr-x 3 rakan rakan 4096 Jun  7 11:48 .
drwxr-xr-x 4 rakan rakan 4096 Jun  8 09:08 ..
drwxr-xr-x 2 rakan rakan 4096 Jun  7 11:48 01
-rwxrwxrwx 1 rakan rakan    0 Jun  7 11:48 1.txt
-rw-r--r-- 1 rakan rakan    0 Jun  7 11:48 2.txt
-rw-r--r-- 1 rakan rakan    0 Jun  7 11:48 3.txt
```

<div dir="rtl">

الأن حصلنا على المخرج المطلوب ونحن راضين ;) 

---

فرضا قمنا بـ تعديل/تغيير ملف أو عدة ملفات,من الغير العملي بأنك تقوم بإعادة مزامنه كل شيء بالكامل.
بدلا من ذلك سنقوم بإستخدام الخيار  `-u`  لمزامنه/نسخ فقط التغييرات.

دعنا نأخذ مثالنا السابق:

</div>

```bash
$ tree tests/
tests/
├── 01
│   └── 4.txt
├── 1.txt
├── 2.txt
├── 3.txt
└── 4.txt

1 directory, 5 files
```

<div dir="rtl">

قمت بإضافة ملف 
`4.txt`.

الأن دعنا نستخدم `-u` ونقوم بالتأكد من المحتوى الذي سيتم مزامنته عن طريق `-n` وبالتأكيد سنقوم بالمقارنة بإستخدام الهاش `-c` لنكون دقيقين ;) 

</div>

```bash
$ rsync -avunc tests/ backups/
sending incremental file list
./
4.txt

sent 293 bytes  received 23 bytes  632.00 bytes/sec
total size is 0  speedup is 0.00 (DRY RUN)
```

<div dir="rtl">

كما ترى فقط الملف `4.txt` الذي ستتم مزامنته. الأن نستطيع حذف الخيار `-n` ونقوم بعملية المزامنه.

ماذا لو كنا نريد مزامنه عدة ملفات؟
تستطيع عمل ذلك بعدة طرق ولكن سنشرح أسهل طريقة
وهي تحدد الملفات المطلوب مزامنتها داخل `{}` مفصولين بـ فاصلة. 

> مثال:

</div>

```bash
rsync -av {tests/,/etc/passwd} backups/
```

> النتيجة : 

```bash
$ tree backups/
backups/
├── 01
│   └── 4.txt
├── 1.txt
├── 2.txt
├── 3.txt
├── 4.txt
└── passwd

1 directory, 6 files
```

---

<div dir="rtl">

مزامنه إمتدادات أو ملفات معينة وتجاهل أي شيء آخر.

في هذه الحالة سنقوم بإستخدام الخيارين `--include` و `--exclude` لإتمام العملية.

> فرضا لدينا  الهرميه التالية ونريد بمزامنه فقط الملفات التي تنتهي بـ `.pdf` ونتجاهل أي شيء آخر.

</div>

```bash
$ tree tests/
tests/
├── 01
│   ├── 4.txt
│   └── book2.pdf
├── 1.txt
├── 2.txt
├── 3.txt
├── 4.txt
├── blah.pdf
├── book1.pdf
└── news.pdf

1 directory, 9 files
```

```bash
rsync -avm --include="*/" --include='*.pdf' --exclude='*' tests/ backups/
```

```bash
$ tree backups/
backups/
├── 01
│   └── book2.pdf
├── blah.pdf
├── book1.pdf
└── news.pdf

1 directory, 4 files
```

<div dir="rtl">

#### حصلنا على النتيجة المطلوبة والأن سنقوم بالأمر الذي تم تنفيذه بالتفصيل:


* أولا, `-avm` عدة خيارات معروفة مسبقا لديك , الخيار `-m` يستخدم لتجنب التشعب داخل المجلدات الفارغة.

* ثانيا, `--include="*/"` تعني اذهب  إلى المجلدات الفرعية في المجلد المراد النسخ منه.

* ثالثا, `--include='*.pdf'` تعني قم بتضمين جميع الملفات التي تتضمن إمتداد `.pdf` 

* أخيرا, `--exclude='*'` قم بتجاهل أي ملف لا يحتوي على الإمتداد `.pdf` .

</div>

---

<div dir="rtl">

### Remotely عن بعد:

> مثال : نسخ ملف من جهازك إلى سيرفر : 

### يرجى الإنتباه: 

خدمة ssh/rsync لابد ان تكون تعمل على السيرفر أو على جهازك الشخصي في حالة النقل من السيرفر إلى جهازك.

</div>

```bash
rsync -v localFile user@10.10.10.10:~/remoteFile
```

<div dir="rtl">

`ملاحظة` :

اذا لم تقم بتحديد موقع على السيرفر سيتم وضع المحتوى داخل مجلد المنزل الخاص بالمستخدم.

> مثال آخر:

</div>

```bash
rsync -v localFile user@10.10.10.10: 
```

<div dir="rtl">

لم نقم تحديد موقع على السيرفر , اذا يتواجد الملف في  `/home/user/`

> مثال: نسخ ملف من السيرفر إلى جهازك الشخصي:

</div>

```bash
rsync -v user@10.10.10.10:~/remoteFile localFile 
```

<div dir="rtl">

> مثال : نسخ ملف من سيرفر مع رقم منفذ مخصص:

</div>

```bash
rsync -e 'ssh -p 1337' user@10.10.10.10:remoteFile localFile
```

<div dir="rtl">

> مثال : نسخ كامل محتوى المجلد مع تفعيل خيار `-P` وفقط الملفات الحديثة `-u` 

</div>

```bash
rsync -urvP dirLocal/ user@10.10.10.10:~/backups/
```

<div dir="rtl">

> نسخ عدة ملفات من السيرفر

</div>

```bash
rsync -zavP user@10.10.10.10:{/etc/passwd,/etc/hostname,/var/www/html/index.php} dirLocal/
```

<div dir="rtl">

### ملاحظة : 

إستخدمنا `-z` لضغط الملفات في عملية النقل للتسريع وتقليل إستخدام البيانات.

---

### خاتمة: 
rsync
اداة جدا قوية ويمكن إستخدامها لعدة استخدامات , كما انصح بالتعمق أكثر بالأداة , أستخدامها بعمليات الأتمتة , والعمليات المجدولة, إلخ...

</div>


[1.1]: http://i.imgur.com/wWzX9uB.png (twitter icon without padding)

[1]: http://www.twitter.com/R4kaaaN

<div dir="rtl">

لو كان عندك أي إستفسار خذ راحتك وتواصل معي على تويتر [![@R4kaaaN][1.1]][1] ;)  

</div>

---

EOF
