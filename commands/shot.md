---
description: One shot - image or video, quote, generate, review
argument-hint: "[shot description or idea]"
---

ولّد لقطة وحدة في «عيون المخرج»:

$ARGUMENTS

اتبع سكل `director-workflow`. رد بالعربي.

1. حدد النوع: صورة أو فيديو (من الكلام؛ لو مب واضح استخدم `director_choices` بخيارين كاملين: صورة وفيديو).
2. **البرومبت:** لو المستخدم كتب وصف لقطة كامل ← أرسله **حرفياً** بلا ترجمة ولا تعديل. لو فكرة قصيرة ← اكتب برومبت سينمائي بسكل `video-prompt-director` أو `image-prompt-director` (أو مفردات `director://guide/vocabulary`)، واعرضه بسطر مع سطر عربي بوش اخترت وليش. نص داخل الصورة ← بين «…» و`model:"gpt"`.
3. شخصية محفوظة مذكورة ← `characters:["<الاسم>"]`. ملف من المستخدم ← `director_upload_reference` (لا ترفع بنفسك).
4. استدعِ أداة التوليد بدون payment ← تسعيرة. مع العارض أنهِ دورك. بدونه: قل النقاط، وبعد موافقة صريحة أعد بنفس المدخلات + `payment:{quote_id}`.
5. `running` ← `director_wait {id}`. ثم `director_view` وقيّم الناتج بصدق. اقترح الخطوة الجاية بـ `director_choices` (حرّك الصورة، كمّل من آخر فريم، 4K، صوت، نسخة ثانية) واذكر النقاط المتبقية.
