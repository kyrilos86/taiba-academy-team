# Taiba Academy — Smart Academic Assistant

مساعد أكاديمي ذكي لطلاب معهد طيبة العالي لتكنولوجيا الإدارة والمعلومات (فرع الجيزة).
واجهة محادثة عربية (PWA) مبنية على **قاعدة معرفة محلية**، مع طبقة **Gemini** آمنة تعمل عبر Serverless Function على Vercel. وإذا تعطل الذكاء الاصطناعي، يستمر الموقع في الإجابة من قاعدة المعرفة المحلية.

> مشروع طلابي لمادة نظم معلومات المؤسسات — إشراف: د. إسلام سميح محمد عاطف.

## هيكل المشروع

```
index.html        الواجهة + قاعدة المعرفة + منطق الشات (ملف واحد)
api/chat.js       Serverless Function: يستدعي Gemini من الخادم (المفتاح لا يصل للمتصفح)
sw.js             Service Worker (network-first + عمل بدون إنترنت)
manifest.json     إعدادات تثبيت التطبيق (PWA)
icon*.png/svg     أيقونات التطبيق | favicon.ico/favicon-*.png أيقونة التاب
vercel.json       مهلة الدالة + ترويسات الأمان
.env.example      أسماء متغيرات البيئة (بدون قيم حقيقية)
```

## التشغيل المحلي

1. ثبّت Node.js 20 أو أحدث.
2. ثبّت Vercel CLI: `npm i -g vercel`
3. انسخ `.env.example` إلى `.env.local` وضع مفتاح Gemini الحقيقي (من https://aistudio.google.com/apikey).
4. شغّل: `npm run dev` ثم افتح الرابط الذي يعرضه الأمر.

> بدون `vercel dev` (مثلًا فتح `index.html` مباشرة أو `python -m http.server`) سيعمل الموقع بقاعدة المعرفة المحلية فقط، لأن `/api/chat` غير متاح.

## الرفع على GitHub

```bash
git init -b main
git add .
git commit -m "Initial commit: Taiba Academy smart assistant"
git remote add origin https://github.com/<USERNAME>/<REPO>.git
git push -u origin main
```

ملف `.gitignore` يمنع رفع `.env` و`.env.local` و`node_modules` و`.vercel`. **لا ترفع مفتاح Gemini على GitHub أبدًا.**

## النشر

### الخيار الموصى به: Vercel (الموقع + الذكاء الاصطناعي)

1. من https://vercel.com اختر **Add New → Project** واستورد مستودع GitHub.
2. في **Settings → Environment Variables** أضف:
   - `GEMINI_API_KEY` = مفتاحك
   - `GEMINI_MODEL` = `gemini-3.8-flash` (اختياري، وهو الافتراضي)
3. اضغط **Deploy**. كل `git push` على `main` ينشر تلقائيًا.

### بديل: GitHub Pages (الموقع فقط)

يعمل الموقع كاملًا بقاعدة المعرفة المحلية، لكن **بدون Gemini** لأن GitHub Pages لا يشغّل Serverless Functions. من **Settings → Pages** اختر الفرع `main` والمجلد `/ (root)`.

## ملاحظات

- لو غيّرت أي ملف من ملفات الواجهة، ارفع رقم `CACHE` في `sw.js` (مثل `taiba-v7`) ليحصل المستخدمون على النسخة الجديدة فورًا.
- لعرض صورة المعاينة عند مشاركة الرابط، اجعل `og:image` في `index.html` رابطًا كاملًا بعد معرفة نطاق موقعك (مثل `https://your-site.vercel.app/icon-512.png`).
- بيانات المحادثات والتذاكر والتحليلات محفوظة في `localStorage` على جهاز كل مستخدم فقط، ولا تُرسل لأي خادم (باستثناء نص السؤال الذي يُرسل إلى `/api/chat`). ربط قاعدة بيانات سحابية هو المرحلة التالية.
- الرسوم والمواعيد في قاعدة المعرفة تقديرية لبعض الفرق؛ راجعها رسميًا مع المعهد قبل الاعتماد عليها.
