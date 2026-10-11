---
description: Vertical ad - hook, product, call to action, then merge
argument-hint: "[product / brand message]"
---

ابدأ إعلان فيديو عمودي (9:16) في «عيون المخرج». المنتج أو الرسالة:

$ARGUMENTS

اتبع سكل `director-workflow` وسكل `video-prompt-director` (الإعلان: النكتة مبنية على رسالة العلامة، المنتج يوصف بشكله ولونه) و`image-prompt-director` للفريمات. رد بالعربي.

1. **تجهيز:** `director_account` و`director_library` (هل صورة المنتج موجودة؟) و`director_library {source:"cast"}`. صورة المنتج مب موجودة: `director_upload_reference {purpose:"صورة المنتج", role:"object"}` وأعطِ المستخدم `upload_url` ثم `director_upload_result`. لا ترفع مرفقات المحادثة بنفسك.
2. **الخطة:** 3–6 لقطات: هوك ← مشكلة ← المنتج ← دعوة للتصرف (من داخل القصة). اعرضها بأسطر عربية قصيرة.
3. **الهوك (اختياري، يُنصح به):** اعرض بـ `director_choices` خيار «جرّب 3 افتتاحيات» (`director_hook` بافتتاحيات مختلفة فعلاً، kling لو بنمدد) مقابل «صوّر الإعلان مباشرة».
4. **المنتج:** لقطات المنتج من فريم ثابت: `director_product_scene {product:"ref:…", scene:"…", model:"gpt" لو فيه نص/شعار}` ← تحريكه بـ kling `references:[{ref:"gen:img_…#0", role:"start"}]`. نص الإعلان داخل الصورة بين «…» مع gpt.
5. **اللوحة:** `director_storyboard_save` (label لكل لقطة، المنتج `references:[{ref, role:"object"}]`، الشخص المتكرر `characters:[…]`) ← `director_storyboard_run {board}` ← تسعيرة وحدة. مع العارض أنهِ دورك؛ بدونه سعر صريح ثم `payment:{quote_id}` بعد الموافقة.
6. **التسليم:** `director_wait` ← `director_view` ← قص اللي يمط ← `director_board_finish {board}` (تعليق صوتي اختياري ≈ 1.8 كلمة/ثانية) ← أعطِ `final.url`.
