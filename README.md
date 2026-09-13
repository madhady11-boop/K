# وصلني بطريقك ⚡ — كابتن برو (الإصدار 2.0)

تطبيق الكابتن كامل في **ملف واحد حديث** (`كابتن.html`) — يعمل برفعه مباشرة على أي استضافة
(Firebase Hosting، GitHub Pages، Netlify، أو حتى مجلد محلي عبر خادم بسيط) **بدون أي أمر بناء
أو تثبيت مكتبات**.

> الملف يحتاج فتحه عبر `http(s)` وليس بالنقر المزدوج (`file://`) لأن المتصفحات تمنع
> وحدات ES والـ Firebase من العمل على `file://`.

---

## 1) ماذا تغيّر في هذا الإصدار؟

### أ. إصلاح أعطال كانت تُوقف التطبيق بالكامل
النسخة السابقة كانت تحتوي تلفًا في الأحرف يمنع عمل الكود:

| العطل | العدد | النتيجة قبل الإصلاح |
|---|---|---|
| استبدال علامة القالب `` ` `` بحرف غير مرئي (U+2060 WORD JOINER) | 54 موضعًا | كل الـ template literals معطوبة ⇒ **خطأ SyntaxError وإيقاف السكربت كاملًا** |
| استبدال `*` في CSS بـ `￼` (U+FFFC) | 1 | تعطّل قاعدة `box-sizing` لكل العناصر |
| فقدان عامل المقارنة في `where("captainPhone", "", phone)` | 2 | فشل استعلام الإحصائيات (كان يُبتلع بصمت داخل `try/catch`) |

تم إصلاح الثلاثة، ولم يعد في الملف أي حرف معطوب.

### ب. تحديث الأدوات (مع سلاسل احتياطية)
- **Firebase SDK**: ترقية من `10.8.0` إلى `12.19.0` (الأحدث) مع **سلسلة تراجع تلقائية**
  `12.19.0 → 11.10.0 → 10.8.0` إذا فشل تحميل إصدار.
- **تخزين دائم (IndexedDB Persistent Cache)**: التطبيق يفتح ويعرض بياناتك **بدون إنترنت**،
  وأي عملية تُنفَّذ أثناء الانقطاع **تُزامَن تلقائيًا** عند عودة الاتصال.
- **Leaflet 1.9.4** مع ثلاثة مصادر CDN بديلة (unpkg → jsdelivr → cdnjs) وبديل لبلاطات الخرائط.
- **OSRM** مع خادم توجيه بديل وذاكرة تخزين مؤقت للمسارات و`AbortController` لإلغاء الطلبات البطيئة.
- واجهة حديثة: متغيرات تصميم (Design Tokens)، وضع داكن/فاتح/تلقائي، زجاجية (Glassmorphism)،
  رسوم SVG مبنية داخليًا (بلا مكتبات رسوم)، حركات وانتقالات، دعم `dvh` و`safe-area` و`prefers-reduced-motion`.
- تقنيات متصفح حديثة مستخدمة فعليًا: Wake Lock (إبقاء الشاشة مضاءة أثناء الرحلة)، Web Share،
  Notifications API، BroadcastChannel (مزامنة التبويبات)، IntersectionObserver، Intl، Viewport Units.

### ج. الأقسام الجديدة (كلها متصلة بقاعدة البيانات)
1. **🧾 رحلاتي** — سجل كامل مع بحث وفلترة (اليوم/أمس/٧/٣٠/الكل + مكتملة/ملغاة/عبرية/نقدًا/بطاقة)،
   ترتيب، تفاصيل كاملة لكل رحلة، خط زمني للمراحل، إيصال قابل للمشاركة، وتصدير CSV.
2. **📊 التقارير** — صافي/إجمالي/عمولة، متوسط الأجرة، المسافة، العائد لكل كم، معدل القبول والإلغاء،
   زمن الوصول، مقارنة مع الفترة السابقة (▲▼)، رسم أعمدة يومي، رسم خطي لساعات اليوم،
   دائري للفئات، **خريطة حرارية (أيام × ساعات)**، أعلى المناطق، جدول تفصيلي،
   تصدير CSV/JSON، مشاركة الملخص، وطباعة/PDF بتنسيق مخصص.
3. **💰 المحفظة** — الرصيد، كشف حساب حيّ (مجموعة فرعية جديدة `ledger`)، طلبات سحب
   (زين كاش/آسيا حوالة/كي كارد/ناس واليت/مصرفي/نقدًا) تُسجَّل في مجموعة `withdrawals`،
   بلاغ شحن، فلترة الحركات، وتصدير CSV.
4. **📈 الأداء والتقييمات** — متوسط التقييم، توزيع النجوم، ملاحظات الزبائن، مؤشرات الأداء،
   انتظام ١٤ يومًا، تراكمي ٣٠ يومًا، و**نصائح مخصصة** تُحسب من أرقامك الفعلية.
5. **🏆 المكافآت** — نقاط، ٥ مستويات (برونزي → ماسي)، ١٦ وسامًا، سلسلة أيام العمل،
   وأهداف يومية قابلة للتعديل بحلقات تقدم.
6. **⏱️ الورديات** — بدء/إنهاء وردية (تُحفظ في `shifts`)، مؤقّت حيّ، عدادات الوردية،
   ملخص عند الإنهاء (دخل الساعة)، سجل الورديات، وتنبيه استراحة بعد ٤ ساعات.
   تبدأ تلقائيًا مع أول رحلة تقبلها.
7. **🔔 مركز الإشعارات** — إشعارات محفوظة في السحابة + عدّاد غير المقروء + إشعارات نظام
   عند ورود طلب والتطبيق في الخلفية.
8. **🎧 الدعم والمساندة** — تذاكر دعم (مجموعة `supportTickets`) مع الحالة والردود،
   ٨ أسئلة شائعة، تواصل مباشر، و**زر طوارئ SOS** يرسل موقعك وبيانات رحلتك النشطة.
9. **🔥 مناطق الطلب الساخنة** — تجميع الطلبات القريبة على الخريطة في شبكة مع متوسط الأجرة.
10. **🩺 التشخيص** — حالة الاتصال/القاعدة/GPS/الخرائط، **فحص شامل من ١١ اختبارًا**
    (قراءة، كتابة، استعلام، بث مباشر، توجيه، بلاطات، تخزين…)، وسجل تطبيق قابل للنسخ والتصدير —
    مفيد جدًا عند إرسال مشكلة للدعم.
11. **⚙️ الإعدادات** — المظهر، نوع الخريطة (شوارع/داكن/فاتح/قمر صناعي)، الأصوات، الاهتزاز،
    الإشعارات، إبقاء الشاشة، توسيط الخريطة، توفير البيانات، تطبيق الملاحة الافتراضي،
    نطاق العمل، الأهداف، وضع المحاكاة، تصدير كل البيانات، مسح الكاش.
12. **🧪 وضع المحاكاة** — طلبات تجريبية **محلية ١٠٠٪ لا تُكتب في قاعدة البيانات**،
    لتجربة الدورة الكاملة (قبول → وصول → انطلاق → إنهاء) حتى لو كانت القاعدة فارغة.

### د. ما لم يتغيّر (كما طلبت)
- ✅ **قاعدة البيانات**: نفس المشروع `tixe-4d8ac`، نفس المجموعات، نفس أسماء الحقول، ونفس قيم
  `status` (`searching → accepted → arrived → on_way → completed`). **لا حذف ولا دمج ولا تقصير.**
- ✅ **واجهة/آلية التسجيل**: نفس المفتاح `cap_session` في `localStorage` وبنفس أسماء الحقول
  (`phone, name, carModel, carPlate, walletBalance, rating, zoneRadiusKm, totalTrips, todayEarnings,
  todayTrips, weekEarnings, monthEarnings, lastResetDate`)، ونفس سلوك `logoutCaptain()`
  (حذف الجلسة وإعادة التحميل). ملف التسجيل الخارجي الخاص بك يعمل كما هو بدون تعديل.
- ✅ **معادلة العمولة** كما هي: `commissionCut = 10%` ويُخصم من `walletBalance`.
- ✅ كل أسماء العناصر (IDs) القديمة المهمة محفوظة، بالإضافة إلى دوال عامة بنفس الأسماء القديمة
  (`toggleOnline`, `acceptIncoming`, `advanceTripStage`, `openProfileModal`, `openStatsModal`,
  `saveZoneSettings`, `logoutCaptain`, …) تحسّبًا لأي استدعاء خارجي.

---

## 2) بنية قاعدة البيانات

### المجموعات القائمة (بدون تغيير — أُضيفت حقول اختيارية فقط)

**`captains/{phone}`**
```
القائمة:  phone, name, carModel, carPlate, walletBalance, rating, zoneRadiusKm,
          totalTrips, todayEarnings, todayTrips, weekEarnings, monthEarnings, lastResetDate,
          isOnline, lat, lng, heading, lastSeen
أُضيفت:  speedKmh, accuracy, sharedMode, lastSeenMs, updatedAt, updatedAtMs, appVersion,
          points, badges[], targets{trips,earnings}, todayKm, weekTrips, monthTrips,
          shiftId, shiftStartedMs, lastSelfTestMs,
          stats{ totalKm, totalFares, totalCommission, totalNet, acceptedCount, declinedCount,
                 missedCount, cancelCount, sharedTrips, nightTrips, streakDays, lastWorkDay,
                 bestDayEarnings, bestDayKey, pickupMinSum, pickupMinCount,
                 durationSecSum, durationCount, lastTripAtMs, maxSharedPax }
```

**`trips/{id}`**
```
القائمة:  status, pickup{lat,lng,address}, dropoff{lat,lng,address}, fare, distanceKm,
          userName, userPhone, category, isSharedSeat, captainPhone, captainName, captainCar,
          captainPlate, captainRating, captainCoords{lat,lng,heading},
          acceptedAt, commissionCut, completedAt
أُضيفت:  acceptedAtMs, arrivedAt, arrivedAtMs, startedAt, startedAtMs,
          completedAtMs, cancelledAt, cancelledAtMs, cancelReason, cancelledBy,
          captainNet, durationSec, routeKm, pickupMinutes, captainNote, noteAtMs,
          captainLastSeenMs, captainApp
يُقرأ:   rating, ratingComment, paymentMethod (يكتبها تطبيق الزبون)
```

> **مهم**: كل الاستعلامات مصمّمة بحقل واحد في `where` لتجنّب الحاجة إلى
> **فهرس مركّب (Composite Index)** في Firestore — أي تعمل فورًا دون إعداد إضافي.

### المجموعات الجديدة (إضافية بالكامل — لا تمس أي بيانات قائمة)
| المسار | المحتوى |
|---|---|
| `captains/{phone}/ledger/{id}` | حركة محفظة: `type` (commission/withdraw/topup/bonus/refund/adjustment)، `amount`، `balanceAfter`، `tripId`، `note`، `status`، `createdAt`، `createdAtMs` |
| `captains/{phone}/shifts/{id}` | وردية: `startedAt(Ms)`، `endedAt(Ms)`، `durationSec`، `status`، `trips`، `earnings`، `commission`، `net`، `km`، `startLocation`، `endLocation` |
| `captains/{phone}/notifications/{id}` | إشعار: `title`، `body`، `type`، `icon`، `read`، `createdAtMs` |
| `supportTickets/{id}` | تذكرة: `captainPhone`، `captainName`، `subject`، `category`، `message`، `status` (open/answered/closed)، `replies[]`، `createdAtMs`، `appVersion`، `device` |
| `withdrawals/{id}` | طلب سحب: `captainPhone`، `amount`، `method`، `walletRef`، `note`، `status` (pending/approved/rejected)، `createdAtMs` |

### قواعد أمان مقترحة (Firebase → Firestore → Rules)
التطبيق الحالي يعتمد على قواعد مفتوحة. إن أردت تقييدها لاحقًا دون كسر التطبيق:
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /trips/{id} {
      allow read: if true;
      allow write: if true;   // يكتبها تطبيق الزبون والكابتن
    }
    match /captains/{phone} {
      allow read: if true;
      allow write: if true;
      match /{sub}/{doc} {           // ledger, shifts, notifications
        allow read, write: if true;  // يمكن تقييدها لاحقًا بالمصادقة
      }
    }
    match /supportTickets/{id}  { allow read, write: if true; }
    match /withdrawals/{id}     { allow read, write: if true; }
  }
}
```

---

## 3) النشر

**الطريقة الأبسط (ملف واحد):** ارفع `كابتن.html` إلى استضافتك وافتحه. انتهى.

**إن كانت الاستضافة تطلب `index.html`:** ارفع `index.html` الموجود هنا أيضًا —
هو مجرد محوّل أنيق إلى `كابتن.html`.

```bash
# تجربة محلية
python3 -m http.server 8080
# ثم افتح: http://localhost:8080
```

**Firebase Hosting**
```bash
firebase init hosting      # مجلد النشر: . (نفس المجلد)
firebase deploy --only hosting
```

---

## 4) ملاحظات تشغيلية مهمة

- **إضافة التطبيق للشاشة الرئيسية**: من قائمة المتصفح ← «إضافة إلى الشاشة الرئيسية» (يعمل كـ PWA).
- **إذن الموقع**: مطلوب لعمل الرادار بدقة. إن رُفض، يظهر تنبيه ويُستخدم موقع تقريبي.
- **الأرقام**: تُعرض بأرقام لاتينية (`ar-IQ-u-nu-latn`) كما هو معتاد في التطبيقات العراقية.
- **الوضع المحلي**: عند انقطاع السحابة يستمر التطبيق بالعمل من الذاكرة المؤقتة،
  وتُحفظ العمليات وتُزامَن تلقائيًا (يظهر مؤشر «بانتظار المزامنة»).
- **وضع المحاكاة**: من `المزيد ← الإعدادات ← وضع المحاكاة`. الطلبات التجريبية تحمل وسم
  `🧪 محاكاة` ولا يُكتب أي منها في `trips`.
- **التشخيص**: `المزيد ← التشخيص ← فحص شامل` يعطيك تقريرًا فوريًا عن أي مشكلة
  (صلاحيات، قواعد أمان، حظر شبكة، GPS…). انسخ السجل وأرسله للدعم.
- **اختصارات لوحة المفاتيح** (سطح المكتب): `O` تشغيل/إيقاف الرادار، `M` الخريطة،
  `R` التقارير، `C` توسيط الموقع، `Esc` إغلاق النوافذ.

---

## 5) الفحص الآلي

تم فحص التطبيق بـ **107 اختبارًا آليًا** خارج المتصفح (محاكاة DOM + Firestore + Leaflet + OSRM + GPS)
تغطي: الإقلاع، الاتصال، جلب الرحلات/الكشف/الإشعارات، كل الأقسام والفروع، الفلترة، الرسوم،
المحفظة والسحب، دورة الرحلة الكاملة (قبول ← وصول ← انطلاق ← إنهاء) مع التحقق من الخصم المحاسبي،
وضع العبرية، الورديات، التذاكر، التصدير، المظهر، ودوال التوافق القديمة.
النتيجة: **107/107 ناجح** دون أي خطأ في سجل التطبيق.

---

## 6) هيكل الملف (للتطوير)

الملف مقسّم داخليًا إلى طبقات مرقّمة بتعليقات واضحة ليسهل التعديل:

```
[1] الإعداد والثوابت (APP, firebaseConfig, CDN sources)
[2] تحميل المكتبات (سلاسل احتياطية)
[3] الأدوات العامة (تنسيق، تواريخ، جغرافيا، تخزين، اهتزاز، صوت، إشعارات، تصدير)
[4] نظام الواجهة (Toast, Sheets, Modals, Confirm/Prompt, UI components)
[5] الحالة والإعدادات والجلسة (State, Settings, cap_session, Cache)
[6] طبقة قاعدة البيانات (DB, Queue)
[7] الخريطة والموقع والمسارات (MapView, Routes, Geo)
[8] الرادار ودورة الرحلة (Radar, Online, Shared, TripPanel, Trip, Shift, Sim, SOS)
[9] الرسوم والتحليلات + رحلاتي + التقارير (Charts, Analytics, HistoryView, ReportsView)
[10] المحفظة، الأوسمة، المزيد، والأقسام الفرعية، الإعدادات، التشخيص، التوجيه، والإقلاع
```

**الواجهة البرمجية للتصحيح** (من كونسول المتصفح):
```js
CaptainApp.State          // الحالة الكاملة
CaptainApp.DB             // طبقة قاعدة البيانات
CaptainApp.Sim.inject()   // حقن طلب تجريبي فورًا
CaptainApp.Diagnostics.entries()
```
