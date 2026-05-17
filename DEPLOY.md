# Brew Lab v0.3 — دليل النشر والاختبار

## ما الذي في هذا المجلد

`brewlab-v2.html` — النسخة المدمجة الكاملة. هذا هو الملف الذي ستنشره.

النسخة الحالية هي **التطبيق الفعلي الشغّال** مع:
- ✓ واجهة iOS بنظام Apple HIG
- ✓ محرك حساب الطحن بالميكرون (يعمل فعلياً)
- ✓ تحليل الماء بالأرقام
- ✓ حفظ ملفات البن والماء في localStorage
- ✓ كاميرا تعرض الصورة (الاستخراج التلقائي دالة فارغة جاهزة لربط Claude API)
- ✓ Brew Mode بالمؤقت والاهتزاز
- ✓ Onboarding كامل
- ✓ Paywall سياقي
- ✓ تبديل عربي/إنجليزي
- ✓ Dark Mode تلقائي
- ✓ نموذج ملاحظات مدمج (يُنسخ للحافظة)

---

## خطوات النشر على GitHub Pages

### 1. أنشئ مستودعاً جديداً

اذهب إلى github.com وأنشئ مستودعاً اسمه `brewlab-app` (أو أي اسم تفضله). اجعله **Public**.

### 2. ارفع الملف

غيّر اسم الملف:
```bash
mv brewlab-v2.html index.html
```

ثم ارفعه عبر:
- **الأسهل:** اسحب الملف لصفحة المستودع على GitHub (drag & drop)
- أو عبر Git:
```bash
git clone https://github.com/yasseralqadhi/brewlab-app.git
cd brewlab-app
# انسخ index.html هنا
git add index.html
git commit -m "v0.3 prototype with full iOS-styled UI"
git push
```

### 3. فعّل GitHub Pages

في صفحة المستودع:
- Settings → Pages
- Source: `main` branch, `/ (root)`
- Save

انتظر دقيقة، ستظهر URL على نمط:
```
https://yasseralqadhi.github.io/brewlab-app/
```

---

## رسالة جاهزة للمختبرين (واتساب)

```
مرحبا! 👋

طوّرت تطبيق تجريبي لمحبي القهوة المختصة اسمه Brew Lab.
يحسب الوصفة المثالية بدقة الميكرون حسب بنّك وطريقتك ومائك.

أبغى رأيك الصادق قبل ما أكمل تطويره كتطبيق iPhone فعلي.

🔗 الرابط: [URL]

⚙️ كيف تستخدمه:
1. افتح الرابط على iPhone (Safari)
2. اضغط زر المشاركة → "أضف للشاشة الرئيسية"
   (يصير كتطبيق فعلي)
3. جرّبه لمدة 3-5 أيام

📝 أسئلة أبيك تجاوبني عليها:
- وش أكثر شي عجبك؟
- وش محبط أو غير دقيق؟
- لو ندفع 4.99$/شهر، وش الميزة اللي تقنعك؟
- هل النتائج (الطحن، الحرارة) دقيقة لقهوتك؟

في زر "ملاحظات" داخل التطبيق
أو ابعث لي مباشرة.

شكراً 🙏
```

---

## ما عمل وما لم يعمل (بصراحة)

### يعمل بالكامل
- إنشاء/تعديل/حذف ملفات بن وماء
- توليد وصفة كاملة بناءً على المعايير الفعلية
- حساب الطحن بالميكرون لكل المطاحن المعتمدة
- تحليل الماء وأثره على الوصفة
- المؤقت بمراحل الصبات والاهتزاز عند الانتقال
- تبديل اللغة عربي/إنجليزي
- Dark Mode تلقائي
- حفظ كل البيانات على الجهاز

### لا يعمل (مقصود)
- **الكاميرا الذكية:** الصورة تظهر لكن دالة `extractFromImage()` فارغة — جاهزة لك لتربط Claude API لاحقاً (تعليمات داخل الكود)
- **Live Activities** — تحتاج Swift Native
- **iCloud Sync** — تحتاج CloudKit (Swift)
- **Apple Watch app** — Swift

### اختياري لاحقاً
- PWA manifest لتثبيت التطبيق على Home Screen بدون شريط Safari
- Plausible analytics لتتبع السلوك الفعلي للمختبرين

---

## ربط الكاميرا الذكية لاحقاً

في الكود، ابحث عن:
```javascript
// TODO: extractFromImage(dataUrl, type)
```

عند فك الـ comment، الدالة `extractFromImage()` (فارغة الآن) ستستقبل:
- `dataUrl`: الصورة كـ base64
- `type`: 'bean' أو 'water'

عليك أن تربطها بـ Claude API. مثال:

```javascript
async function extractFromImage(dataUrl, type) {
  const response = await fetch('YOUR_BACKEND/extract', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ image: dataUrl, type })
  });
  return await response.json();
}
```

ثم تستدعيها في `pickImage()`:
```javascript
const data = await extractFromImage(dataUrl, 'bean');
state.beanDraft = { ...state.beanDraft, ...data };
renderBeanEdit();
```

تكلفة Claude للصورة الواحدة ~$0.005.

---

## بعد جمع الملاحظات (الخطوة القادمة)

لو ردود فعل المختبرين قوية (يستخدمونه بانتظام)، انتقل لـ:

1. **iOS Native (14 أسبوع)** — Swift/SwiftUI + Core Data + StoreKit
2. أو **PWA كامل** — للحصول على Native feel بدون 14 أسبوع تطوير

كلاهما يعتمد على أن المختبرين استخدموا التطبيق فعلياً لا أنهم قالوا "حلو".
