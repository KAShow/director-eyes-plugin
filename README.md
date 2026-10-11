# عيون المخرج — بلوغن Claude

بلوغن **director-eyes** يربط Claude بمنصة **عيون المخرج** (Director's Eyes) من [حوش خليفة](https://hawsh-khalifa.com): توليد صور وفيديو وصوت وموسيقى ومؤثرات سينمائية **على حسابك أنت ونقاطك**، مع سكلات كتابة البرومبت ومسارات جاهزة للحلقة والإعلان والشخصية الثابتة واللقطة.

> ما فيه أي مفتاح أو توكن داخل البلوغن. كل مستخدم يسجّل دخوله بحسابه عن طريق OAuth، ولا ينصرف شي من نقاطه إلا بعد ما يوافق على السعر.

## وش داخله

| النوع | الاسم | وش يسوي |
|---|---|---|
| خادم MCP | `director` | `https://hawsh-khalifa.com/mcp` — كل أدوات `director_*` (التوليد، التسعير، المكتبة، الطاقم، لوحة القصة، الهوك…) |
| سكل | `director-workflow` | متى تُستخدم كل أداة، تسلسل الإنتاج (ستوري بورد ← كاست ← توليد ← مراجعة ← تسليم)، قواعد المحتوى، والتسعير قبل الصرف |
| سكل | `video-prompt-director` | كتابة برومبتات الفيديو: شخصيات ثابتة، هوك، حوار خليجي مشكّل، وتشخيص الفيديو البارد |
| سكل | `image-prompt-director` | كتابة برومبتات الصور: مراجع الشخصيات، الإعلانات، الأغلفة، وإصلاح الصور المشوّهة |
| أمر | `/director-eyes:episode` | حلقة كاملة: قصة، كاست، لوحة قصة، تسعيرة وحدة، تصوير، دمج |
| أمر | `/director-eyes:ad` | إعلان عمودي: هوك، المنتج، دعوة للتصرف، ثم دمج |
| أمر | `/director-eyes:cast` | شخصية ثابتة: بورتريه مرجعي، حفظ بالطاقم، صوت |
| أمر | `/director-eyes:shot` | لقطة وحدة: صورة أو فيديو، تسعير، توليد، مراجعة |
| وكيل | `prompt-reviewer` | «مراجع البرومبت»: يفحص البرومبت أو اللوحة قبل الصرف (Claude Code وCowork بس) |

## التركيب

### ١) رفع الملف في تطبيق Claude (الأسهل)
1. نزّل ملف `director-eyes.plugin` من [آخر إصدار](https://github.com/KAShow/director-eyes-plugin/releases/latest).
2. في تطبيق Claude (الويب أو سطح المكتب): **Customize ← Plugins ← Add ← Upload plugin**، واختر الملف.
3. فعّل البلوغن.

### ٢) من GitHub كـ marketplace (Claude Code)
```bash
claude plugin marketplace add KAShow/director-eyes-plugin
claude plugin install director-eyes@hawsh-khalifa
```
أو داخل Claude Code: `/plugin marketplace add KAShow/director-eyes-plugin` ثم `/plugin install director-eyes@hawsh-khalifa`.

### ٣) من مجلد محلي (للتجربة)
```bash
claude plugin marketplace add ./director-eyes-plugin
claude plugin install director-eyes@hawsh-khalifa
```

## تسجيل الدخول
- **تطبيق Claude:** أول ما تستخدم أداة من عيون المخرج يطلب منك ربط الحساب — اضغط **Connect** وسجّل دخول بحساب حوش خليفة ووافق.
- **Claude Code:** شغّل `/mcp`، اختر خادم `director` التابع للبلوغن، واضغط **Authenticate** — يفتح المتصفح، تسجّل دخولك وترجع.
- الجلسة تتجدد تلقائياً. تقدر تفصل الحساب من نفس المكان بأي وقت.

## ملاحظات
- **لو عندك خادم director مضاف من قبل** (يدوياً أو كموصل بالتطبيق) بيصير عندك نسختين من نفس الأدوات. خلّ وحدة بس: إما البلوغن أو الإضافة القديمة.
- النقاط: أي توليد يرجع أول **تسعيرة مجانية**. في تطبيقات Claude تضغط «وافق وولّد» على الكرت؛ في Claude Code تقول "موافق" وClaude يكمّل.
- قواعد المحتوى من المنصة: الممنوع بس العري/الجنس، الإساءة للأديان ورموزها، والعنصرية.
- دليل الربط الرسمي: https://hawsh-khalifa.com/tools/director/mcp

## الرخصة
MIT — انظر [LICENSE](LICENSE).
