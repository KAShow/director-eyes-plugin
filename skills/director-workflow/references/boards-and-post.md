# اللوحات وما بعد التوليد — تفاصيل

## ضاعف الهوك (`director_hook`)
- افتتاحيات قصيرة للمقارنة: wan / kling / gemini = 3 ث، seedance / seedance25 = 4 ث، minimax مستثنى.
- `variants:[{prompt}…]` 2–4 (افتراضي 3) — اكتب افتتاحيات **مختلفة فعلاً** (قواعد الهوك في `video-prompt-director` §٥)، أو `prompt` + `count` (العشوائية تفرّق بينها).
- `quote_id` واحد يغطي الحزمة (السعر = مجموع النسخ؛ كل نسخة تنخصم لما تنرسل، والفاشلة ما تنخصم). النسخة الرابعة تنتظر حد 3 فيديوهات بنفس الوقت.
- الناتج لوحة `{status:"board", id:"sb_…", items[]}` ← `director_wait {id:"sb_…"}` ← العارض يشغّلها جنب بعض والمستخدم يضغط «اختر هذا».
- ثم `director_hook_pick {board, winner:<index>}` (مجاني). للتمديد: `extend_to_seconds` (مثلاً 10) + `prompt` (وش يصير بعدين) / `audio` — بتسعيرة أولاً.
- لو فيه تمديد مخطط: **kling** — الوحيد اللي يكمّل من آخر فريم حقيقي. بعد التمديد: `director_board_finish {board}` يدمج الفائز + التمديد (مجاني).

## لوحة القصة (`director_storyboard_save` ← `director_storyboard_run`)
- اللقطة: `{prompt, label, duration?, model?, resolution?, characters?, references?, start?:"none"|"previous"|<image ref>}`.
- `defaults:{model, duration, resolution, audio}` للقطات اللي ما تحدد. `aspect_ratio` افتراضي 9:16 ولازم كل محرك يدعمه.
- الدقة المحذوفة = افتراضي المحرك (مثل الموقع) مب الأرخص.
- `board:"sb_…"` في save يستبدل لقطات مسودة موجودة.
- run يرجع **تسعيرة وحدة** للوحة. بعد الموافقة الخادم يصوّر بالترتيب ويكمل حتى لو انقفلت المحادثة (يوقف لو تغيّر السعر أو خلصت النقاط).
- `director_wait {id:"sb_…"}` للتقدم، `director_view {target:"sb_…"}` للشريط، `director_library {source:"boards"}` للقائمة.
- بعد التصوير (العارض فيه زر لكل وحدة — أنهِ دورك لما يظهر):
  - لقطة جاية: `director_storyboard_run {board, next_shot:{prompt, label?, model?, duration?, resolution?, audio?, characters?, references?, start?}}` — تكمل من آخر فريم، والشخصيات المحذوفة = شخصيات اللقطة السابقة.
  - قص: `director_board_trim`. دمج: `director_board_finish` ← `final.url`. إيقاف: `director_board_cancel` بطلب المستخدم.
- فريم أول دقيق: `director_generate_image` بالحجم المطلوب ← `start:"gen:img_…#0"` على اللقطة (أو `references:[{ref, role:"start"}]`).

## التعليق الصوتي على الدمج
- السكربت ≈ 1.8 كلمة × ثواني الفيلم (فيلم 20 ث ≈ 36 كلمة). الأطول ينقص عند نهاية الفيلم.
- اكتب النص باللهجة أولاً (الصوت يقرأ الكلمات كما هي) ← `director_generate_speech {text, dialect}` ← `director_board_finish {board, voiceover:{audio:"gen:aud_…", mode}}`.
- `duck` (افتراضي، الصوت فوق صوت اللقطات) / `mix` (متساوي) / `replace` (الصوت بس). مع تعليق صوتي يصير تصدير لقطة وحدة ممكن.

## المنتج في المشهد (`director_product_scene`)
- `product` = صورة المنتج (`ref:` أو `gen:img_…#i`)، `scene` = وصف المشهد بالكلام (أي لغة)، `characters?` يقدّمونه.
- يحافظ على الشكل والملصق والألوان والشعار. `model:"gpt"` لو العبوة فيها نص/شعار لازم ينقرا، غير كذا nano.
- قوالب المشاهد مب متاحة بعد — وصف بالكلام.
- لتحريك فريم: `director_generate_video {model:"kling", references:[{ref:"gen:img_…#0", role:"start"}]}`.

## استبدال الشخصيات (`director_recast`)
- يبدّل الناس بفيديو 5–30 ث بالناس في 1–4 صور (صورة لكل شخص، من اليسار لليمين كما يظهرون). الحركة والكاميرا والصوت تبقى.
- السعر لكل ثانية من المصدر: 768p 0.8 / 1080p 1.2. يُحسب من حد 3 فيديوهات بنفس الوقت.

## الصوت والموسيقى والجودة
- مؤثر: دوّر أول بـ `director_sfx_search {query}` (مجاني) ← `director_sfx_take {sfx_id}` بـ 0.1 أرخص من توليد مؤثر (0.2).
- صوت لفيديو صامت: `director_generate_sfx {video:"gen:vid_…"}` (0.2).
- موسيقى: `director_generate_music {prompt, duration_s}` (0.6) ← استخدمها كمرجع صوتي `references:["gen:aud_…"]` على wan / seedance25.
- كلام كشريط للفيديو: ولّد الكلام أول ثم أرفقه مرجع صوتي (wan / seedance25) عشان الشفايف والإيقاع تتبعه.
- 4K: `director_upscale {source}` — صورة 0.2، فيديو 0.2 لكل ثانية (≤30 ث). الصورة اللي أصلاً 4K تنرفض.
- استمرارية: `references:["gen:vid_…#last"]` تخلي أول فريم = آخر فريم للسابق. أو `director_extract_frame` تحفظه مرجع.
