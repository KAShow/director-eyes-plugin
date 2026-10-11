# أدوات خادم director (عيون المخرج)

مأخوذة من `supabase/functions/director-mcp/tools/*.ts`. «تصرف» = بدون `payment` ترجع تسعيرة مجانية، ومع `payment:{quote_id}` أو `payment:{max_points}` تنفّذ وتخصم. الأسعار تتغير — المرجع `director_engines` و`director_quote`.

## الحساب والتسعير
| الأداة | النوع | وش تسوي |
|---|---|---|
| `director_account` | مجانية | الباقة، الرصيد، نافذة الوصول، سقف الوكلاء اليومي (used/left/reset)، `content_preferences` (عنف/دم/رعب/أسلوب الرد). |
| `director_engines {kind?, detail?}` | مجانية | المحركات: الدقات، المدد، النسب، حدود المراجع، البذرة، النقاط. `kind`: video / image / audio (الأصوات) / post (الرفع، الاستبدال، الموسيقى، المؤثرات) / scenes. |
| `director_quote {jobs:[…]}` | مجانية | يسعّر حتى 12 مهمة (`kind`: image / video / speech / upscale / music / sfx / recast / sfx_take بنفس حقول أدواتها). يرجع نقاط كل وحدة + المجموع + `quote_id` لكل وحدة (60 دقيقة، مرة وحدة) + `request {tool, arguments}` جاهز للإعادة مع payment. |

## التوليد (تصرف)
| الأداة | التكلفة التقريبية | مدخلات مهمة |
|---|---|---|
| `director_generate_image` | nano 0.2 / gpt 0.3 للصورة (+ ref surcharge لـ gpt)، 2k/4k أغلى | `prompt`, `model` (nano/gpt), `quality`, `aspect_ratio` (افتراضي 9:16), `count` 1–4, `references[]`, `characters[]`, `seed`, `negative_prompt`, `from_generation` |
| `director_generate_video` | حسب المحرك والدقة والمدة والصوت والمراجع | `prompt`, `model` (wan افتراضي / seedance / seedance25 / kling / minimax / gemini), `resolution`, `duration` (افتراضي 10)، `aspect_ratio`, `audio` (افتراضي true), `references[]` (≤15), `characters[]`, `character_voice`, `seed`, `from_generation` |
| `director_generate_speech` | سعر المحرك لكل 1000 حرف | `text` (≤2400), `dialect` (white/bh/sa/ae), `voice_id`, `engine`, `tone`, `emotion` (minimax), `character` (صوت شخصية محفوظة) |
| `director_generate_music` | 0.6 للمقطع | `prompt` (≤1000)، `duration_s` 5–190 (افتراضي 30) |
| `director_generate_sfx` | 0.2 | نص ← مؤثر: `prompt` (≤450)، `duration_s` 0.5–22، `loop`. أو فيديو صامت ← صوت مطابق: `video`, `prompt?`, `music_prompt?` |
| `director_upscale` | صورة 0.2 / فيديو 0.2 لكل ثانية (≤30 ث) | `source`, `target` (4k/2k للفيديو) |
| `director_product_scene` | مثل الصورة | `product` (ref)، `scene` (وصف بالكلام)، `characters?`, `model` (gpt لو فيه نص/شعار) |
| `director_recast` | 768p: 0.8 / 1080p: 1.2 لكل ثانية من المصدر | `source` (فيديو 5–30 ث)، `photos[]` 1–4 (من اليسار لليمين)، `resolution`, `prompt?` |
| `director_sfx_take` | 0.1 | `sfx_id` من `director_sfx_search` |
| `director_hook` | مجموع النسخ | `variants:[{prompt}]` 2–4 أو `prompt`+`count`، `model` |
| `director_hook_pick` | الاختيار مجاني، التمديد يصرف | `board`, `winner`, `extend_to_seconds?`, `prompt?`, `audio?` |
| `director_storyboard_run` | تسعيرة وحدة للوحة | `board`, `shots?:[index]` (إعادة لقطة)، `next_shot?` |

## اللوحات (مجانية)
| الأداة | وش تسوي |
|---|---|
| `director_storyboard_save` | مسودة ≤12 لقطة و≤120 ث، يرجع نقاط كل لقطة والمجموع. `board` لتعديل مسودة. |
| `director_board_trim {board, index, start?, end?}` | قص بداية/نهاية لقطة مكتملة (الأصل يبقى، null يلغي). |
| `director_board_finish {board, indices?, voiceover?}` | يدمج اللقطات المكتملة بفيديو واحد ← `final.url`. |
| `director_board_cancel {board, index?}` | يوقف اللي ما بدأ (يرجع النقاط) — بطلب المستخدم بس. |

## الطاقم (مجانية)
| الأداة | وش تسوي |
|---|---|
| `director_cast_save` | ينشئ/يعدّل شخصية: `name`, `images` (≤3)، `description` (≤400)، `voice {dialect}` أو `{engine, voice_id}`، `voice_sample`. **بدون consent** — العارض يعرض زر الإذن. |
| `director_cast_delete` | يحذف شخصية — بطلب صريح بس (صور المكتبة تبقى). |

## المكتبة والعرض والانتظار (مجانية)
| الأداة | وش تسوي |
|---|---|
| `director_wait {id, wait_seconds?}` | ينتظر `img_/vid_/aud_` أو لوحة `sb_…`. ما يرسم كرت. |
| `director_view {target, frames?}` | يخليك **تشوف** صورة أو فريمات فيديو (أول/وسط/آخر) أو لوحة كاملة. |
| `director_library` | التوليدات أو المراجع؛ `source:"cast"` للشخصيات، `source:"boards"` للوحات، `character:"<name>"`، `response_format:"detailed"` للوصفة الكاملة، `cursor` للصفحات. |
| `director_extract_frame {video, at?}` | يحفظ فريم (first/middle/last) كمرجع بالمكتبة. |
| `director_rename {target, title?, role?}` | يغير العنوان، أو الدور المحفوظ لمرجع. |
| `director_mark {id, favorite?, hidden?}` | مفضلة. `hidden:true` = **حذف نهائي** للمهمة كلها — بتأكيد المستخدم بس. |
| `director_report_problem {id, reason?}` | بلاغ عن توليدة **فاشلة** (تعويض فوري حسب قواعد الخادم: مرتين باليوم، مرة لكل مهمة). |
| `director_sfx_search {query}` | يبحث بمكتبة المؤثرات الجاهزة — دوّر هنا قبل ما تولّد مؤثر. |

## التفاعل والرفع (مجانية)
| الأداة | وش تسوي |
|---|---|
| `director_choices {question, options}` | 2–4 خيارات قابلة للضغط بأسعارها، بدل السؤال النصي. أدوات الخيارات: أدوات التوليد + upscale + product_scene + hook + storyboard_run + recast + sfx_take. |
| `director_pick {purpose, kind?, then}` | يخلي المستخدم يختار من مكتبته ثم ينفّذ `then`. |
| `director_pick_result {pick_id}` | نتيجة صفحة الاختيار (بدون العارض). |
| `director_upload_reference {purpose?, role?, kind?}` | يفتح نافذة رفع ويرجع `upload_url`. |
| `director_upload_result {upload_id}` | الملفات اللي رفعها المستخدم مع أدوارها. |
| `director_add_reference {url}` | يستورد رابط https عام (صورة ≤25MB؛ فيديو ≤25MB و2–15 ث؛ صوت ≤15MB و1.8–30 ث). |

## للتطبيق بس — لا تستدعيها
`director_viewer_upload`, `director_viewer_poll`, `director_viewer_action`, `director_viewer_settings`, `director_viewer_probe`.
