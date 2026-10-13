---
description: Consistent character - face and body reference sheet, save to cast, voice
argument-hint: "[character name + description]"
---

اصنع شخصية ثابتة في طاقم «عيون المخرج»:

$ARGUMENTS

اتبع سكل `director-workflow` (قسم الكاست) وسكل `image-prompt-director` (نوع الصورة: مرجع شخصية — ورقة «وجه وجسم»). رد بالعربي.

1. `director_library {source:"cast"}` — الشخصية موجودة؟ اعرض الموجود واسأل بـ `director_choices` أو كرت: تحديث أو شخصية جديدة.
2. المستخدم عنده صورة؟ `director_upload_reference {purpose:"صورة الشخصية", role:"character"}` ← `director_upload_result`. أو رابط https عام ← `director_add_reference`.
3. ما عنده صورة: اكتب برومبت ورقة «وجه وجسم» (16:9: وجه قريب بالثلث الأيسر + جسم كامل أمام وجانب وخلف، شخص واحد، خلفية رمادية فاتحة سادة، إضاءة ناعمة متساوية، الأسلوب يتبع صورته إن وُجدت) وسعّره بـ `director_generate_image` بدون payment. مع العارض أنهِ دورك؛ بدونه وافق ثم `payment:{quote_id}`. لو حاب بدائل: `count` 2–4 أو `director_choices`.
4. `director_view` على الناتج — صلّح أي خلل (يدين، وجه، ملابس) قبل الحفظ.
5. `director_cast_save {name, images:[{ref:"gen:img_…#0", angle:"sheet"}], description?, voice?:{dialect:"white"|"bh"|"sa"|"ae"}}` (زاوية `sheet` للورقة فقط؛ صورة المستخدم العادية بلا زاوية) **بدون consent**. حالة `consent_required` ← أنهِ دورك (زر الإذن بالعارض). بدون العارض: اسأل عن إذن صاحب الصورة وحط `consent:true` بعد "إيه" صريحة بس.
6. قل للمستخدم إن الشخصية تنادى بالاسم `characters:["<الاسم>"]` في كل صورة وفيديو ولوحة، وإن kling يحتاج فريم بداية فيه الشخصية.
