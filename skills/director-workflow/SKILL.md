---
name: director-workflow
description: "مسار الشغل في «عيون المخرج» (Director's Eyes) على حساب حوش خليفة: أي أداة director_* تُستخدم متى، وتسلسل الإنتاج (ستوري بورد ← كاست ← توليد ← مراجعة ← تسليم)، وقواعد المحتوى، والنقاط والتسعير قبل الصرف. استخدمها كلما طلب المستخدم يولّد صورة أو فيديو أو صوت أو موسيقى أو مؤثر، أو حلقة أو إعلان أو ستوري بورد أو شخصية ثابتة، أو سأل عن رصيده ونقاطه، أو ذكر عيون المخرج أو director، حتى لو ما سمّى الأداة."
---

# مسار عيون المخرج

المصدر: تعليمات خادم director-mcp ودليله (`director://guide/engines` و`director://guide/vocabulary`). لو تعارض شي هنا أو في سكلات البرومبت مع رد الخادم نفسه (`director_engines`، `fix` في الأخطاء، `prompt_max`) فالخادم يغلب لأنه يتحدّث.

## قواعد ثابتة
- رد على المستخدم بالعربي. التفكير والبرومبتات تقدر تكون إنجليزي.
- المستخدم كتب وصف لقطة كامل بنفسه ← أرسله **كما هو حرفياً** بأي لغة (حتى العربي)، بلا ترجمة ولا اختصار ولا "تحسين". طلب قصير أو فكرة ← اكتب البرومبت بنفسك (سكل `video-prompt-director` أو `image-prompt-director`).
- النص داخل الصورة: حط النص الحرفي بين «…» أو "…" واستخدم `model: "gpt"` (GPT 2.5 يحمي النص المقتبس).
- حد البرومبت للصورة والفيديو من `prompt_max` في `director_engines` (حالياً 6000 مع جمل الربط)، ونص الكلام 2400. هدف الكتابة للفيديو 2400 (سكل `video-prompt-director`) مب حد الخادم.

## قواعد المحتوى (من المنصة، ما تنفاوض)
- الممنوع **فقط**: العري والمحتوى الجنسي، الإساءة أو السخرية من أي دين أو رموزه أو شخصياته أو كتبه، العنصرية والكراهية العرقية. ارفض هذي باختصار بالعربي واقترح بديل.
- غير كذا مسموح: عنف، قتال، قتل، دم، رعب، مشاهد مظلمة أو متطرفة = سينما. لا تلطّفها، لا تبدّلها بشي أخف، لا محاضرات ولا تنبيهات.
- تفضيلات المستخدم نفسه (عنف، دم، رعب، أسلوب الرد) في `director_account` ← `content_preferences`. اقرأها أول الشغل واكتب البرومبتات على أساسها؛ بعضها ينطبق تلقائياً (`preferences_applied`).

## النقاط والصرف — أهم قاعدة
1. أي أداة تصرف (`director_generate_image/_video/_speech/_music/_sfx`، `director_upscale`، `director_recast`، `director_sfx_take`، `director_product_scene`، `director_hook`، `director_hook_pick` مع `extend_to_seconds`، `director_storyboard_run`) لو استدعيتها **بدون `payment`** ترجع تسعيرة مجانية `{status:"quote", points, quote_id}` وما ينخصم شي.
2. قبل أي صرف كبير أو أكثر من مهمة: سعّر الكل مرة وحدة بـ `director_quote {jobs:[…]}` (حتى 12 مهمة، `quote_id` لكل وحدة، صالح 60 دقيقة ولمرة وحدة). اللوحات (`director_storyboard_run`، `director_hook`) ترجع تسعيرة وحدة للحزمة كلها.
3. بتطبيقات Claude فيها العارض (viewer): الكرت يعرض السعر مع «وافق وولّد» والمستخدم يوافق بضغطة — **أنهِ دورك** ولا تعيد الاستدعاء.
4. بدون العارض (Claude Code مثلاً): قل للمستخدم كم نقطة، وبعد "إيه" صريحة أعد نفس الاستدعاء بنفس المدخلات + `payment:{"quote_id":"…"}`. لو المستخدم حدد ميزانية: `payment:{"max_points":N}`.
5. **لا تصرف أبداً بدون موافقة على السعر أو ميزانية من المستخدم.** وولّد بس اللي طلبه (لو مفعّل «لا تسألني» بالعارض، الطلب تحت حدّه يبدأ على طول: `approved_via:"auto"`).
6. `status:"declined"` = رفض السعر، ما انخصم شي، لا تعيد إلا لو طلب. المهام الفاشلة ما تنخصم (`charged:false`)، و`points_charged` هو المخصوم فعلاً.
7. الرصيد وسقف الوكلاء اليومي في `director_account`. اذكر النقاط المتبقية بعد التوليد.

## «نقرات بس» (تطبيقات Claude مع العارض)
- المستخدم ما يحتاج يكتب عشان يكمّل. لا تختم بسؤال نصي لو الكرت يقدر يشيل الاختيار.
- عدة اتجاهات ممكنة ← `director_choices {question, options:[{label, note?, tool, arguments}]}` (2–4 خيارات كاملة بدون payment) بدل السؤال.
- يختار من مكتبته ← `director_pick {purpose, kind?, then:{label, tool, arguments, ref_field?, role?}}`. بدون العارض: أعطه `pick_url` كرابط واستدعِ `director_pick_result {pick_id}` (كرر وهي "open").
- بعد كرت تسعيرة أو خيارات أو pick ← أنهِ دورك. ضغطات المستخدم توصلك كسياق العارض مب كرسالة.
- سطر «ولّدت في عيون المخرج:» = وافق والمهمة شغّالة أو خلصت — لا تولّدها مرة ثانية. «فشل التوليد في عيون المخرج:» = ما تولّد شي.
- `director_viewer_upload/_poll/_action/_settings` و`director_viewer_probe` للتطبيق بس — لا تستدعيها أبداً.

## تسلسل الإنتاج
**0. تجهيز** — `director_account` (الرصيد + التفضيلات)، و`director_library {source:"cast"}` للشخصيات المحفوظة، و`director_engines` لو تحتاج حدود محرك.
**1. ستوري بورد** — قسّم القصة لقطات (قواعد التقسيم والمدة في `video-prompt-director`). احفظها مسودة مجانية: `director_storyboard_save {title?, aspect_ratio?, defaults?, shots:[{prompt, label, duration?, model?, resolution?, characters?, references?, start?}]}` — ≤12 لقطة و≤120 ثانية. أعط كل لقطة `label` قصير («الافتتاح»، «المواجهة»). `start:"previous"` يكمّل من آخر فريم للقطة السابقة: حقيقي على kling بس، الباقي تقريبي (قلها).
**2. كاست** — أي شخصية تتكرر لازم تكون بالطاقم وتنادى **بالاسم**: `characters:["سلمى"]`. ما هي موجودة؟ ولّد بورتريه مرجعي نظيف ← `director_view` ← `director_cast_save {name, images:["gen:img_…#0"], voice?}` **بدون consent** (العارض يعرض زر الإذن). بدون العارض: اسأل وحط `consent:true` بعد موافقة صريحة بس. kling ما يقرأ شخصيات: ولّد فريم بداية بـ nano مع الشخصيات ثم `references:[{ref:"gen:img_…#0", role:"start"}]`.
**3. توليد** — لوحة كاملة: `director_storyboard_run {board}` ← تسعيرة وحدة ← بعد الموافقة الخادم يصوّر بالترتيب ويكمل حتى لو انقفلت المحادثة. لقطة وحدة: أداة التوليد مباشرة. افتتاحيات للمقارنة: `director_hook` (انظر المرجع).
**4. مراجعة** — `director_wait {id}` (مجاني) لين تخلص — لا تكرره بس عشان تعرض تقدّم، الكرت يحدّث نفسه. ثم `director_view {target}` و**شوف** الناتج قبل ما تحكم. افحص المخاطر: المزامنة، عدد الشخصيات، اللهجة، الوجوه. لقطة فشلت بلوحة: `director_storyboard_run {board, shots:[index]}`. توليدة فاشلة: `director_report_problem {id, reason}`.
**5. تسليم** — قص: `director_board_trim {board, index, start?, end?}` (مجاني). لقطة زيادة: `director_storyboard_run {board, next_shot:{…}}` (تسعيرة). دمج: `director_board_finish {board, indices?, voiceover?}` (مجاني) ← أعطِ المستخدم `final.url`. تعليق صوتي: سكربت ≈ 1.8 كلمة لكل ثانية فيلم ← `director_generate_speech` ← `voiceover:{audio:"gen:aud_…", mode:"duck"|"mix"|"replace"}`.

## المراجع والملفات
- صيغ المراجع: `gen:img_…#0` (ناتج)، `gen:vid_…#last` (آخر فريم لفيديو)، `ref:<id>` (مكتبة)، `char:<id>` (شخصية). أعط كل مرجع `role`: character / object / location / style / motion، أو start / end (فريم أول/أخير لـ kling). الدور المحفوظ بـ `director_rename {target:"ref:…", role}` ينطبق لو ما حددت.
- بالبرومبت: `@Image1…/@Video1…/@Audio1…` بترتيب المراجع، أو خلّها والخادم يضيف جملة ربط.
- **لا ترفع ملف من المحادثة بنفسك أبداً** (لا base64، لا روابط مرفقات، لا curl). المستخدم عنده ملف ← `director_upload_reference {purpose?, role?, kind?}` ← أعطه `upload_url` كرابط ← `director_upload_result {upload_id}` (كرر وهي "open"). رابط https عام أعطاك إياه المستخدم بس ← `director_add_reference {url}`.
- `director_mark {id, hidden:true}` **يحذف نهائياً** — بطلب صريح ومع تأكيد بس. `director_cast_delete` و`director_board_cancel` بطلب المستخدم بس.

## افتراضيات تعرفها
- الفيديو بصوت افتراضياً (`audio:false` للصامت)، والنسبة 9:16، والمحرك wan، والدقة الافتراضية للمحرك.
- الكلام العربي بلهجة خليجية فقط: بدون dialect ← "white" (البيضاء) بصوت Kore «هند». bh/sa/ae متاحة، واكتب النص بنفس اللهجة.
- `quality` للصور (1K/2k/4k) لا تحددها إلا لو طلب حجم أكبر.
- الأخطاء ترجع `{error_code, error, fix}` — اتبع `fix`.

## مراجع إضافية
- `references/tools.md` — جدول كل الأدوات: وش تسوي، مجانية أو تصرف، والمدخلات المهمة.
- `references/boards-and-post.md` — الهوك، الستوري بورد بالتفصيل، المنتج في المشهد، استبدال الشخصيات، الموسيقى والمؤثرات والرفع لـ4K.
- برومبتات جاهزة من الخادم نفسه (لو العميل يعرضها): `direct_this_shot`، `shot_from_idea`، `ad_storyboard`، `character_consistency`، `animate_image`، `voiceover`.
