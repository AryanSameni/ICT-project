# ICT PRO — سند مرجع نهایی

## طراحی و توسعه چارچوب تحلیل ICT، دستیار معاملاتی و بک‌تستر در Pine Script v6

**نسخه:**  4.0
**نوع سند:** مشخصات جامع محصول و نقشه اجرای مرحله‌ای  
**محتوا:** معماری، منطق Engineها، تنظیمات، نمایش، آزمون و عیب‌یابی  
**نکته:** این سند هیچ کد اجرایی ندارد.

> این فایل منبع واحد پروژه است. هر موضوع در محل اصلی خود به‌طور کامل توضیح داده شده و ادامه همان موضوع در بخش دیگری پراکنده نشده است.

---

# 1. تعریف پروژه

## 1.1 هدف

ICT PRO یک سیگنال‌ساز ساده نیست. هدف، ساخت یک چارچوب تحلیلی است که مفاهیم ICT را شناسایی، ذخیره، طبقه‌بندی، به یکدیگر مرتبط و روی نمودار نمایش دهد. کاربر باید بتواند شواهد هر تحلیل را ببیند و تصمیم نهایی معامله را خودش بگیرد.

فرآیند اصلی محصول:

> **تشخیص → ذخیره → طبقه‌بندی → ایجاد ارتباط → به‌روزرسانی وضعیت → نمایش شواهد → کمک به تصمیم → تصمیم کاربر**

ترتیب اولویت:

> **Analysis First → Visualization Second → Assistance Third → Automation Last**

## 1.2 محصولات نهایی

| نام | نوع Pine | وظیفه |
|---|---|---|
| `ICT_PRO_CORE` | Library | Enumها، Typeها، قراردادهای داده، الگوریتم‌ها و توابع مشترک |
| `ICT_PRO_ANALYSIS_FRAMEWORK` | Indicator | تحلیل کامل بازار، MTF، نمایش Objectها و Dashboard |
| `ICT_PRO_TRADING_ASSISTANT` | Indicator | Setup، Confluence، پیشنهاد ناحیه ورود و هشدار |
| `ICT_PRO_BACKTEST` | Strategy | شبیه‌سازی معامله، ژورنال، آمار و Strategy Tester |

Core Library منبع مشترک منطق است. فایل‌های دیگر State زنده یکدیگر را مستقیم نمی‌خوانند؛ هرکدام Context لازم را با الگوریتم‌های مشترک بازسازی می‌کنند. این تفکیک از سنگین شدن یک Script واحد جلوگیری می‌کند و مانع اختلاف منطق Analysis، Assistant و Backtest می‌شود.

## 1.3 قابلیت‌های الزامی

- Swingهای Wick، Close و Combined؛
- درجات External، Internal و Micro در هر Timeframe؛
- HH، HL، LH، LL و ZigZag؛
- BSL، SSL، EQH، EQL، PDH، PDL، PWH، PWL و Session Liquidity؛
- Touch، Sweep Candidate، Validated Sweep و Accepted Beyond؛
- Protected High/Low، BOS، CHoCH و MSS؛
- Displacement، FVG، Consequent Encroachment، OB و Breaker؛
- Dealing Range، Premium، Discount، Equilibrium و OTE؛
- Session و Killzone؛
- تحلیل واقعی HTF و LTF؛
- DOL یا Draw On Liquidity؛
- Visual Manager، Dashboard و Object Manager؛
- Trading Assistant، Backtest، Alerts و QA.

## 1.4 اصول غیرقابل تغییر

1. وجود تحلیلی Object به نمایش آن وابسته نیست.
2. Logical ID با Drawing ID تفاوت دارد.
3. Timeframe با Structure Degree یکی نیست.
4. Authority، Quality و Confluence سه مقدار جدا هستند.
5. Sweep با Break و Acceptance یکی نیست.
6. BOS، CHoCH و MSS سه Event متفاوت‌اند.
7. Engine تحلیلی Drawing نمی‌سازد.
8. Dashboard تحلیل جدید انجام نمی‌دهد.
9. داده Developing در Backtest قطعی استفاده نمی‌شود.
10. تاریخچه تحلیلی، ظرفیت پردازش و ظرفیت نمایش سه بودجه اجرایی مستقل‌اند؛ بودجه توکن کامپایل‌شده نیز یک محدودیت مهندسی جداست.
11. هر Event مهم باید Source و Lineage قابل‌ردیابی داشته باشد.
12. هر تغییر State باید Trigger، Time و Reason داشته باشد.
13. هیچ Loop یا Collection بدون سقف مجاز نیست.
14. هر فاز فقط پس از قبولی آزمون‌های خودش بسته می‌شود.
15. کاهش توکن کامپایل‌شده فقط با Refactor هم‌ارز مجاز است و نباید هیچ Detection، State، Linkage، MTF، Drawing، Backtest یا Alert موردنیاز را حذف یا تضعیف کند.

## 1.5 خارج از دامنه

- مخفی کردن شواهد و نمایش صرف Buy/Sell؛
- اصطلاح BSS؛ اصطلاح صحیح موردنیاز پروژه OB است؛
- استفاده از آخرین BSL/SSL به‌عنوان Protected Level بدون منطق ساختاری؛
- تبدیل هر Wick-through به Sweep؛
- تبدیل هر Gap به FVG سخت‌گیرانه؛
- تبدیل هر کندل مخالف به OB؛
- اجبار همه اجزای Setup به رخ دادن روی یک کندل؛
- ساخت Backtest پیش از تثبیت Non-repaint و MTF.

---

# 2. معماری کل سیستم

## 2.1 لایه‌ها

| لایه | مسئولیت |
|---|---|
| Data | استانداردسازی Symbol، OHLC، Volume، Time، Timeframe، Tick Size و Bar State |
| Detection | کشف Object یا Event جدید بدون Drawing و بدون تصمیم معامله |
| State | Validation، Lifecycle، Linkage، Supersede، Expiry و Archive |
| Context | ترکیب خروجی Engineها برای TF، Degree، MTF و DOL |
| Presentation | Visual Manager و Dashboard |
| Assistance | Trading Assistant، Backtest و Alerts |

## 2.2 جریان داده

ترتیب اجرای منطقی:

1. استانداردسازی داده بازار؛
2. ساخت Swing Database؛
3. ساخت Liquidity Database و Clusterها؛
4. ثبت Interaction، Sweep و Acceptance؛
5. به‌روزرسانی Structure Context و Protected Levels؛
6. تولید BOS و CHoCH؛
7. ساخت MSS Candidate؛
8. تشخیص Displacement و تأیید MSS؛
9. ساخت FVG و OB؛
10. ساخت Dealing Range و تعیین PD Location؛
11. ساخت Session Context و Session Liquidity؛
12. تجمیع MTF Context؛
13. رتبه‌بندی DOL؛
14. مدیریت ظرفیت Objectها؛
15. رسم توسط Visual Manager؛
16. نمایش Dashboard؛
17. تولید Setup، Backtest و Alert در فایل‌های مربوط.

## 2.3 ارتباط Engineها

| Engine | ورودی اصلی | خروجی اصلی | مصرف‌کننده |
|---|---|---|---|
| Swing | Market Data | SwingObject | Liquidity، Structure، Dealing Range |
| Liquidity | Swing و Reference Levels | LiquidityObject/Cluster | Sweep، MTF، DOL |
| Sweep | Liquidity و Price | SweepEventObject | Structure، MSS، Assistant |
| Structure | Swing و Event Linkage | StructureContext/Event | Displacement، FVG، OB، MTF |
| Displacement | Price و Structure | DisplacementObject | MSS، FVG، OB |
| FVG | Price و Displacement | FVGObject | MTF، Visual، Assistant |
| OB | Price و Displacement | OBObject | MTF، Visual، Assistant |
| Dealing Range | Swing و Bias | DealingRangeObject | MTF، DOL، Assistant |
| Session | Time و Price | SessionObject/Liquidity | Sweep Quality، DOL، Assistant |
| MTF | Contextهای هر TF | MTFContextObject | DOL، Dashboard، Assistant |
| DOL | Liquidity، Structure، PD و MTF | DOLObject | Dashboard، Assistant، Backtest |
| Assistant | Confirmed Context | SetupObject | کاربر، Backtest، Alert |
| Backtest | Setup و Execution Rules | TradeObject/Metrics | Strategy Tester |
| Alert | Events و Setupها | AlertObject/Message | کاربر |

## 2.4 هویت و ذخیره‌سازی

هر Object تحلیلی یک Logical ID پایدار دارد که از داده‌های ثابت مانند Symbol، Origin TF، Object Type، Origin Time و Price-in-ticks ساخته می‌شود. با تغییر Timeframe نمودار، Script دوباره اجرا می‌شود؛ Drawing ID قبلی باقی نمی‌ماند، اما همان Object باید با Logical ID یکسان بازسازی شود.

Logical ID مالک هویت، تاریخچه، Lineage و Lifecycle است؛ Drawing ID فقط Handle موقت Line، Box، Label یا Polyline است. بنابراین Hide، Fade، حذف، Recycle یا ساخته‌نشدن Drawing هرگز نباید Object منطقی را حذف، دوباره تولید یا State آن را تغییر دهد. تغییر State فقط در Engine مالک انجام می‌شود و Visual Manager صرفاً نتیجه آن را بازتاب می‌دهد.

برای جلوگیری از رسم تکراری، هر Drawing با یک Drawing Key مشتق‌شده از `Logical ID + Chart TF + Drawing Type` مدیریت می‌شود. یک Logical Object می‌تواند چند جزء بصری مانند Box، CE Line و Label داشته باشد، اما برای هر ترکیب Drawing Type و Chart Context فقط یک VisualRef فعال مجاز است. هر تغییر داده، State، Style یا Projection باید Dirty Flag ایجاد کند تا فقط Drawingهای متأثر Update شوند.

برای هر گروه Object:

- Array تاریخچه مرتب را نگه می‌دارد؛
- Map دسترسی سریع با Logical ID را فراهم می‌کند؛
- Event Queue فقط موارد جدید را به Engine بعدی می‌رساند؛
- Cursor مشخص می‌کند پردازش Incremental تا کجا انجام شده است.

Creation، Update و Cleanup باید Array، Map، Queue و Cursor را هم‌زمان هماهنگ نگه دارند.

## 2.5 متادیتای مشترک

در صورت کاربرد، Objectها باید این اطلاعات را داشته باشند:

- Logical ID؛
- Symbol؛
- Origin TF؛
- Origin Time و Confirmation Time؛
- Direction و Degree؛
- Confirmation State؛
- Authority، Quality و Confluence؛
- Parent/Source/Related IDs؛
- Archived یا Invalid status؛
- Last State-change Time و Terminal Time؛
- Reason تغییر وضعیت؛
- Dirty flags موردنیاز Presentation، Dashboard و downstream consumers.

## 2.6 Timeframe، Degree و قدرت

Origin Timeframe محل تشکیل Object است. Structure Degree جایگاه External، Internal یا Micro همان Object در ساختار همان TF است. روی 4H هر سه Degree می‌توانند وجود داشته باشند.

HTF، Current و LTF نقش‌های نسبی‌اند و نسبت به نمودار فعلی محاسبه می‌شوند.

- **Authority:** اهمیت سلسله‌مراتبی TF و Degree؛
- **Quality:** کیفیت ذاتی Level یا Event؛
- **Confluence:** هماهنگی با سایر Contextها.

این مقادیر جدا ذخیره می‌شوند. Score ترکیبی فقط برای رتبه‌بندی ساخته می‌شود و نباید اجزای اصلی را پنهان کند.

---

# 3. نقشه فازها

| فاز | عنوان | خروجی اصلی |
|---|---|---|
| 0 | Architecture Freeze | قرارداد نهایی پروژه |
| 1 | Core Architecture | Enum، Type، ID، Storage و Event Foundation |
| 2 | Swing Engine | Swing Database سه‌درجه‌ای |
| 3 | Liquidity Engine | BSL/SSL، EQH/EQL، Reference Liquidity و Sweep |
| 4 | Market Structure | Protected Levels، BOS، CHoCH و MSS Candidate |
| 5 | Displacement & MSS | Displacement و MSS Confirmed |
| 6 | FVG Engine | FVG Database و Lifecycle |
| 7 | Order Block Engine | OB، Mitigation و Breaker |
| 8 | Premium/Discount | Dealing Range و OTE |
| 8B | Session/Killzone | Session Context و Session Liquidity |
| 9 | Real MTF | Context واقعی HTF/LTF |
| 9B | DOL | اهداف رتبه‌بندی‌شده نقدینگی |
| 10 | Visual Manager | زیرساخت مرکزی Drawing |
| 11 | Dashboard | خلاصه وضعیت بازار |
| 12 | Object Manager | Capacity، Cleanup و Performance |
| 13 | Trading Assistant | Setup و Confluence |
| 14 | Backtest Strategy | Strategy Tester، Journal و Analytics |
| 15 | Alert Engine | هشدارهای Event-based |
| 16 | QA & Release | اعتبارسنجی و انتشار |

---

# 4. فازهای توسعه

## Phase 0 — Architecture Freeze

### هدف

تبدیل نیازهای پروژه به قرارداد غیرمبهم پیش از نوشتن Engineها.

### فعالیت‌ها

1. تثبیت مرز چهار فایل؛
2. تثبیت واژگان و تعریف Eventها؛
3. تعیین Enumها، Typeها و Lifecycleها؛
4. تعیین Logical ID و Drawing ID؛
5. تعیین Array، Map، Queue و Cursor؛
6. تعیین Confirmation و Non-repaint policy؛
7. تعیین Analysis، Processing و Visual budgets؛
8. تعیین Compiled-token Budget با سقف سخت `100,256` و حاشیه امن Release؛
9. تعیین ورودی و خروجی هر Engine؛
10. تعریف Bug Ledger، Decision Log و Regression Suite؛
11. تعیین آزمون هم‌ارزی پیش و پس از Refactor؛
12. تعیین آزمون و Exit Gate هر فاز.

### خروجی

Architecture Contract نسخه‌بندی‌شده. هر تغییر بعدی باید دلیل، دامنه اثر، Migration و Regression Test داشته باشد.

### معیار پایان

- هیچ اصطلاح دوپهلو باقی نماند؛
- تمام Engineها مرز مسئولیت مشخص داشته باشند؛
- State transitionها معلوم باشند؛
- ترتیب فازها قطعی باشد؛
- قابلیت‌های غیرقابل‌حذف و معیار هم‌ارزی Refactor ثبت شده باشند؛
- سقف سخت و آستانه هشدار توکن کامپایل‌شده مشخص باشند.

---

## Phase 1 — Core Architecture

### هدف

ساخت زیرساخت داده، State و ارتباط Engineها بدون منطق کامل ICT و بدون Drawing نهایی.

### Enumهای پایه

- Direction: Bullish، Bearish، Neutral؛
- SwingSide: High، Low؛
- SwingBasis: Wick، Close، Combined؛
- SwingDegree: External، Internal، Micro؛
- SwingRelation: HH، HL، LH، LL، Unknown؛
- RelativeTfRole: HTF، Current، LTF، Unknown؛
- ConfirmationState: Developing، Confirmed؛
- VisibilityState: Visible، Faded، Hidden؛
- DisplayScope: `ACTIVE_ONLY`، `LATEST_ONLY`، `LATEST_N`، `RECENT_BARS`، `RECENT_TIME`، `UNTOUCHED_ONLY`، `UNMITIGATED_ONLY`، `UNSWEPT_ONLY`، `DEVELOPING_ONLY`، `CONFIRMED_ONLY`، `LIVE_AND_LATEST_N`؛
- ExtensionMode: `TO_LIVE_BAR`، `TO_LIFECYCLE_EVENT`، `N_BARS_FROM_CREATION`، `N_BARS_FROM_CURRENT`، `FULL_HISTORY`، `TO_FIRST_TOUCH`، `TO_INVALIDATION`، `TO_REPLACEMENT`، `TO_SESSION_END`، `TO_STRUCTURE_EVENT`؛
- StateVisualAction: `KEEP`، `FADE`، `HIDE`، `MARKER_ONLY`، `ARCHIVE`؛
- StateEndpointPolicy: `FREEZE_AT_TRANSITION`، `CONTINUE_TO_LIVE`، `EXTEND_N_AFTER_TRANSITION`، `KEEP_EXTENDING`، `CONVERT_AT_TRANSITION`؛
- LabelHorizontalAnchor: `START`، `MIDDLE`، `END`، `AUTO`؛
- LabelVerticalAnchor: `ABOVE`، `ON_OR_INSIDE`، `BELOW`، `AUTO`؛
- Lifecycle Enumهای اختصاصی فازهای بعد.

### Typeهای پایه

- ObjectMeta؛
- VisualRef؛
- ValidationResult؛
- EventEnvelope؛
- Context Key و Bucket Key؛
- Typeهای تحلیلی که فیلدهای اختصاصی آن‌ها در فاز مربوط تکمیل می‌شود.

### Storage

1. Array مرتب برای هر Object؛
2. Map بر اساس Logical ID؛
3. Queue برای Eventهای جدید؛
4. Cursor مستقل برای هر مصرف‌کننده؛
5. Counterهای Integrity؛
6. سقف اولیه Collectionها.

### Utilityها

- Timeframe normalization و comparison؛
- Price-to-ticks و tick-safe comparison؛
- Logical ID generation؛
- Safe array/map access؛
- Range، percentage و normalization؛
- Validation reason handling؛
- bounded push و synchronized cleanup.

### قرارداد قابلیت و بودجه کامپایل

- Core باید قرارداد مشترک Type، Enum، State transition، ID، Storage، Filter، Extension و Rendering را یک‌بار تعریف کند تا Engineها نسخه‌های تکراری نسازند؛
- کد مشترک باید به Utility یا Manager واحد منتقل شود، اما مرز مسئولیت و خروجی هر Engine مستقل بماند؛
- حذف Alias، Debug، Dev Renderer، مسیر قدیمی و کد مرده فقط پس از اثبات بی‌مصرف‌بودن و عبور Regression مجاز است؛
- کوچک‌سازی Type یا MTF Payload فقط زمانی مجاز است که هیچ داده لازم برای State، Lineage، Projection، Backtest یا Alert از بین نرود؛
- عبور از سقف توکن باعث Compile نشدن کل Script می‌شود و نباید با خاموش‌کردن پنهانی یک Engine یا Feature مدیریت شود.

### آزمون‌ها

- Create، Get، Update و Delete؛
- Duplicate ID؛
- Missing ID/Index؛
- Array/Map synchronization؛
- Queue once-only consumption؛
- Cursor correction بعد از Shift؛
- Overflow و bounded storage؛
- تولید ID یکسان از ورودی یکسان؛
- هم‌ارزی Counts، IDs، Stateها و Linkageها پیش و پس از Refactor Core.

### معیار پایان

- Core بدون خطا اجرا شود؛
- Integrity mismatch صفر باشد؛
- Lookup مستقیم و تاریخچه مرتب هر دو صحیح باشند؛
- هیچ Collection بدون سقف وجود نداشته باشد؛
- زیرساخت مشترک بدون Duplicate implementation و بدون کاهش قابلیت آماده باشد.

---

## Phase 2 — Swing Engine

### هدف

ساخت منبع اصلی Liquidity، Structure و Dealing Range.

### زیر‌فازها

- **2A — Raw Detection:** تشخیص Swing High/Low؛
- **2B — Relation:** تعیین HH/HL/LH/LL؛
- **2C — Hierarchy:** تشکیل External/Internal/Micro؛
- **2D — Finalization:** Confirmation، Parent و Liquidity Eligibility؛
- **2E — Hardening:** Storage safety و جداسازی Analysis از Visibility.

### SwingObject

فیلدهای اختصاصی:

- Side؛
- Basis؛
- Degree؛
- Relation؛
- Pivot Price و Pivot Time؛
- Pivot Bar reference؛
- Wick Price و Close Price؛
- Strength؛
- Parent Swing ID؛
- Liquidity Eligible؛
- Confirmation State.

### Detection

- Wick Mode از High/Low استفاده می‌کند؛
- Close Mode Pivot را بر اساس Close می‌سازد؛
- Combined هر دو Stream را حفظ می‌کند و توافق آن‌ها Quality را افزایش می‌دهد؛
- External، Internal و Micro Strength مستقل دارند؛
- Pivot تا کامل شدن پنجره تأیید Developing است؛
- Relation فقط با Swing هم‌Side، هم‌TF و هم‌Degree مقایسه می‌شود؛
- توالی معتبر باید High و Low را به‌شکل منطقی دنبال کند؛
- Degree از TF جداست و هر TF می‌تواند هر سه Degree را داشته باشد؛
- همه Degreeها همیشه از نظر تحلیلی فعال‌اند.

### تنظیمات تحلیل

- Detection Basis؛
- Strength هر Degree؛
- Minimum separation یا noise filter؛
- Confirmation policy؛
- Combined agreement tolerance.

گزینه‌های Detect External/Internal/Micro در UI قرار نمی‌گیرند، زیرا خاموش شدن آن‌ها زنجیره تحلیل را می‌شکند.

### نمایش Swing و ZigZag

- Show External/Internal/Micro؛
- Show High/Low؛
- Show HH/HL/LH/LL؛
- Marker و Label با Size، Color و TF/Degree Tag؛
- ZigZag مستقل برای هر Degree؛
- Current/HTF/LTF filter؛
- تمام Display Scopeهای مشترک، با پیش‌فرض `LIVE_AND_LATEST_N`؛
- انتخاب N مستقل برای Swingها و Segmentها؛
- Confirmed-only display؛
- Compact label مانند HH و Detailed label مانند `HH | 4H | EXT`.

برای Swing، Live شامل Swingهای Developing و آخرین Swingهای Confirmed و ساختاریِ هنوز مرتبط است. `LATEST_N` باید جداگانه بر اساس TF، Degree و Side اعمال شود تا یک گروه پرحجم، گروه دیگر را از نمایش خارج نکند. Swingهای Superseded، Swept، Broken یا Invalid بر اساس State Visual Action عمومی Keep/Fade/Hide/Marker Only/Archive مدیریت می‌شوند. Marker ذاتاً در Pivot Time ثابت است؛ Segment ZigZag نیز بین دو Pivot ثابت می‌ماند و از Extension افقی سراسری استفاده نمی‌کند.

ZigZag فقط View پایگاه داده Swing است و Swing جدید تولید نمی‌کند. هر Segment با From Swing ID و To Swing ID ثبت می‌شود.

### آزمون‌ها

- تفاوت Wick و Close؛
- توافق و اختلاف Combined؛
- هر چهار Relation؛
- توالی High/Low؛
- Parent linkage؛
- سه Degree در یک TF؛
- خاموش شدن marker بدون تغییر Swing count؛
- باقی ماندن Structure downstream هنگام مخفی شدن Swing؛
- هماهنگی Array، Map و Cursor در Overflow؛
- عدم ایجاد دو Renderer برای Raw Swing.

### معیار پایان

- Swing Database سه‌درجه‌ای پایدار باشد؛
- Pivot و Confirmation Time صحیح باشند؛
- هر Swing با ID قابل بازیابی باشد؛
- Visibility هیچ اثری بر Analysis نداشته باشد.

---

## Phase 3 — Liquidity Engine

### هدف

ساخت پایگاه داده کامل نقدینگی و تشخیص نوع تعامل قیمت با هر سطح.

### زیر‌فازها

- **3A — Swing Liquidity:** BSL و SSL؛
- **3B — Equal Liquidity:** EQH و EQL؛
- **3C — Interaction:** Touch، Sweep و Acceptance؛
- **3D — Reference Liquidity:** PDH/PDL، PWH/PWL و Session Levels.

### LiquidityObject

فیلدهای اختصاصی:

- Kind؛
- Origin؛
- Basis و Degree؛
- Price یا Zone Bounds؛
- Source Swing ID یا Cluster ID؛
- State؛
- Touch Count؛
- First Interaction Time؛
- Sweep Event ID؛
- Consumption Time.

### BSL و SSL

Swing High واجد شرایط منبع BSL و Swing Low منبع SSL است. چند سطح هم‌زمان مجاز است. هر سطح Origin TF، Degree، Basis و Source ID خود را حفظ می‌کند.

Internal/External بودن نقدینگی از Structure Degree و جایگاه واقعی Swing می‌آید، نه از یک previousHigh/Low ناپایدار.

### EQH و EQL

Equal Liquidity به‌صورت Cluster ساخته می‌شود، نه فقط یک جفت Swing.

فیلدهای Cluster:

- Anchor Price؛
- Mean Price؛
- Upper/Lower Bounds؛
- Member Swing IDs؛
- Member Count؛
- Maximum Spread؛
- Basis؛
- State.

Tolerance modeها:

- Ticks؛
- ATR؛
- Percentage؛
- Hybrid.

عضو جدید باید هم در محدوده مرکز Cluster باشد و هم باعث عبور عرض کل Cluster از Maximum Width نشود. این Anti Chain-link Guard مانع اتصال A و C صرفاً به‌واسطه B می‌شود.

Wick-based و Close-based Equal Liquidity در Data از هم متمایز می‌مانند. Combined agreement می‌تواند Quality را بالا ببرد.

### Sweep و Acceptance

Lifecycle Liquidity:

> Fresh → Touched → SweepCandidate → ValidatedSweep یا AcceptedBeyond → Consumed/Invalid

برای BSL:

- عبور High از Level = SweepCandidate؛
- Close/Reclaim معتبر زیر Level = ValidatedSweep؛
- تثبیت معتبر بالای Level = AcceptedBeyond.

برای SSL همین منطق به‌صورت معکوس اجرا می‌شود.

عوامل Quality Sweep:

- Authority سطح؛
- نوع Level و قدرت Cluster؛
- Penetration Depth؛
- Reclaim Strength؛
- Wick/Body Rejection؛
- First Interaction؛
- Freshness و Touch Count؛
- Post-Sweep Displacement؛
- Structural Response؛
- Session Context.

Post-Sweep Displacement برای ارتقای کیفیت است و شرط اجباری وجود Sweep نیست، مگر در Validation Mode بسیار سخت‌گیرانه.

### تنظیمات تحلیل

- نوع Liquidityهای فعال؛
- Tolerance Mode/Value؛
- Maximum Cluster Width؛
- Reclaim rule؛
- Acceptance bars/threshold؛
- Maximum touches؛
- Sweep quality weights؛
- Reference periods.

### نمایش

- BSL و SSL جدا؛
- EQH و EQL جدا؛
- External/Internal/Micro جدا؛
- Wick/Close/Combined filter؛
- Fresh/Touched/Candidate/Validated/Accepted/Consumed filter؛
- Horizontal line یا Thin Zone؛
- TF، Degree، State و Member Count labels؛
- Extensionهای `TO_LIVE_BAR`، `TO_LIFECYCLE_EVENT`، `N_BARS_FROM_CREATION`، `N_BARS_FROM_CURRENT`، `FULL_HISTORY`، `TO_FIRST_TOUCH`، `TO_INVALIDATION`، `TO_REPLACEMENT` و برای Session Liquidity حالت `TO_SESSION_END`؛
- State Endpoint و State Visual Action مستقل برای Fresh، Touched، SweepCandidate، ValidatedSweep، AcceptedBeyond، Consumed و Invalid؛
- تمام Display Scopeهای مشترک، با پیش‌فرض `LIVE_AND_LATEST_N` و N مستقل برای هر Kind/TF/Degree/Side؛
- فیلتر Untouched، Unswept، Unconsumed، فاصله از قیمت و Minimum Authority/Quality.

پیش‌فرض پیشنهادی این Engine آن است که Fresh، Touched و SweepCandidate زنده محسوب شوند؛ ValidatedSweep و AcceptedBeyond در آخرین N باقی بمانند؛ Consumed و Invalid ابتدا در زمان Transition متوقف و سپس Fade یا Archive شوند. `TO_LIFECYCLE_EVENT` برای هر Kind به رویداد نهایی مرتبط ترجمه می‌شود و نباید همه Liquidityها به‌اشتباه Mitigation نامیده شوند.

Liquidity Visibility کاملاً از Swing Visibility مستقل است.

### آزمون‌ها

- BSL/SSL با Source درست؛
- چند Level هم‌زمان؛
- چهار Tolerance Mode؛
- Anti Chain-link؛
- Wick و Close Cluster؛
- Touch بدون Sweep؛
- Wick-through همراه Reclaim؛
- Break همراه Acceptance؛
- جلوگیری از Sweep تکراری؛
- Degree filters؛
- نمایش مستقل از Swing؛
- Cluster performance روی History بلند.

### معیار پایان

- هر Liquidity Source قابل‌ردیابی داشته باشد؛
- Sweep و Acceptance اشتباه نشوند؛
- Clusterها پایدار و bounded باشند؛
- همه Stateها قابل نمایش و فیلتر باشند.

---

## Phase 4 — Market Structure Engine

### هدف

ساخت Context ساختاری مستقل برای هر TF و Degree و تولید Protected Levels، BOS، CHoCH و MSS Candidate.

### زیر‌فازها

- **4A — Structure Context**؛
- **4B — BOS/CHoCH Events**؛
- **4C — Structural Swing Lifecycle**؛
- **4D — Pre-MSS Contract**؛
- **4E — Dependency and Integrity Hardening**.

4E-A به‌عنوان Engine مستقل وجود ندارد. 4D قرارداد MSS Candidate را نگه می‌دارد و 4E جداسازی Analysis/Visibility و ایمن‌سازی اتصال‌ها را کامل می‌کند.

### StructureContextObject

- Origin TF و Degree؛
- Bias؛
- Latest High/Low IDs و Prices؛
- Protected High/Low IDs و Prices؛
- Last BOS/CHoCH/MSS IDs؛
- Pending Candidate ID؛
- Context State.

### Protected Levels

در ساختار صعودی، Protected Low همان Swing Low ساختاری مبدأ Leg منتهی به BOS معتبر است. در ساختار نزولی، Protected High به‌صورت متقارن تعیین می‌شود.

Structural Swing states:

- Active؛
- Protected؛
- Broken؛
- Consumed.

Consumed یعنی Swing حفاظت‌شده با Swing جدید جایگزین شده، بدون آنکه خودش شکسته شود.

### BOS و CHoCH

StructureEventObject شامل:

- Event Type؛
- Direction؛
- Origin TF و Degree؛
- Broken Swing ID؛
- Break Level و Break Basis؛
- Pivot Time و Break Time؛
- Linked Liquidity/Sweep ID؛
- Lifecycle و Quality.

قواعد:

- شکست در جهت Bias = BOS؛
- نخستین شکست خلاف Bias = CHoCH؛
- Break فقط از Swing ساختاری معتبر می‌آید؛
- Liquidity می‌تواند linked evidence باشد، نه جایگزین Swing؛
- Wick، Close و Combined break basis قابل انتخاب‌اند؛
- Double Break policy باید Conflict را قطعی حل کند؛
- External و Internal Event در یک TF هر دو ممکن‌اند.

### MSS Candidate

CHoCH معتبر یک MSSCandidateObject می‌سازد. این Object شامل Source CHoCH ID، Context ID، Direction، Break Level، Linked Sweep، State و Reason است.

Lifecycle:

> Pending → Confirmed یا Superseded/Invalid

- پیش از Phase 5، Confirmed باید صفر باشد؛
- نبود Displacement = Pending؛
- جایگزینی با Candidate جدید مخالف = Superseded؛
- تغییر Context جاری، Event تاریخی را Invalid نمی‌کند؛
- Invalid فقط برای شکست واقعی Contract و با Reason است.

### تنظیمات تحلیل

- فعال بودن Structure Degreeهای موردنظر برای Context؛
- Break Basis؛
- BOS/CHoCH strictness؛
- Double Break policy؛
- Candidate expiry؛
- Sweep requirement mode؛
- Protection update policy.

### نمایش

- Protected High و Protected Low؛
- BOS، CHoCH و MSS جدا؛
- Bullish/Bearish؛
- External/Internal/Micro؛
- HTF/Current/LTF؛
- Confirmed/Developing؛
- line color/style/width؛
- label position/size؛
- TF و Degree tag؛
- تمام Display Scopeهای مشترک، با پیش‌فرض `LIVE_AND_LATEST_N`؛
- State Visual Action مستقل برای Pending، Confirmed، Broken، Consumed، Superseded و Invalid؛
- Fade by age و N مستقل برای هر Event/TF/Degree/Direction؛
- MSS به‌صورت Line، Box، Label یا All.

پیش‌فرض: BOS خط Solid، CHoCH خط Dashed و MSS خط ضخیم‌تر یا Box. خط BOS/CHoCH از Pivot Time سطح شکسته‌شده تا Break Time کشیده می‌شود.

BOS، CHoCH و MSS رویدادهای زمان‌دار هستند و خط اصلی آن‌ها به‌طور پیش‌فرض در Break/Confirmation Time پایان می‌یابد؛ `TO_LIVE_BAR` یا سایر Extensionها فقط برای Levelهای Protected یا Projectionهای صریح مجاز است. Protected High/Low می‌تواند تا Break، Invalidation، Replacement، زمان جاری یا N کندل ادامه یابد. Live در این Engine شامل Protected Levelهای فعال، Eventهای Pending و آخرین Event Confirmed مرتبط با Context جاری است.

### آزمون‌ها

- BOS ادامه‌ای؛
- CHoCH خلاف جهت؛
- External/Internal/Micro در یک TF؛
- Protected origin leg؛
- Broken و Consumed؛
- Double Break؛
- Event lineage؛
- Pending/Superseded/Invalid distribution؛
- حفظ Eventها با خاموش شدن Swing؛
- Queue once-only consumption؛
- Missing Protection counter؛
- Pivot-to-break drawing alignment.

### معیار پایان

- هر Event به Swing شکسته‌شده متصل باشد؛
- Protected Levels ساختاری صحیح باشند؛
- Invalid بدون Reason وجود نداشته باشد؛
- MSS Candidate سالم برای Phase 5 آماده باشد.

---

## Phase 5 — Displacement و MSS Confirmation

### هدف

تشخیص حرکت قدرتمند و استفاده از آن برای تبدیل MSS Candidate به MSS Confirmed.

### DisplacementObject

- Direction؛
- Candle Range؛
- Body Percentage؛
- ATR و ATR Ratio؛
- Body/Range Expansion؛
- Close Location؛
- Momentum Score؛
- Origin/Confirmation Time؛
- Linked Structure Event/Candidate؛
- Quality Components؛
- State.

Lifecycle:

> Candidate → Confirmed/Invalid

### Detection

Displacement فقط «کندل بزرگ» نیست. باید ترکیبی از موارد زیر بررسی شود:

- نسبت بدنه به Range؛
- Expansion نسبت به ATR؛
- Expansion نسبت به کندل‌های قبل؛
- Close نزدیک انتهای Range؛
- جهت روشن؛
- ارتباط زمانی و ساختاری با Event.

Thresholdها Input یا Constant نسخه‌بندی‌شده‌اند و Magic Number پنهان مجاز نیست.

### MSS Confirmation

1. Candidate باید Pending باشد؛
2. Directionها یکسان باشند؛
3. Displacement در Confirmation Window رخ دهد؛
4. Break Contract تکمیل شود؛
5. Candidate Superseded، Invalid یا Expired نباشد؛
6. MSS و Displacement لینک دوسویه بگیرند.

Validation modes:

- Standard: CHoCH + Displacement؛
- Strict: Validated Sweep + CHoCH + Displacement.

رویدادها می‌توانند روی چند کندل رخ دهند:

> Sweep → CHoCH → Displacement → MSS → PD Array → Retracement

### تنظیمات تحلیل

- Minimum Body Percent؛
- ATR Multiplier؛
- Relative expansion length؛
- Close-location threshold؛
- Confirmation Window؛
- Standard/Strict MSS mode؛
- Quality weights.

### نمایش

- Candle Highlight؛
- Marker یا Label `DISP`؛
- Direction و Quality؛
- Developing/Confirmed style؛
- MSS upgrade policy: Replace CHoCH، Keep Both یا MSS Only؛
- تمام Display Scopeهای مشترک با پیش‌فرض `LIVE_AND_LATEST_N`؛
- State Visual Action مستقل برای Candidate، Confirmed و Invalid؛
- فیلتر Minimum Quality، Direction، TF و پیوند ساختاری.

Displacement Candle Highlight و Marker در Origin/Confirmation Time ثابت‌اند و Extension افقی ندارند. اگر Range یا Projection کمکی برای Displacement رسم شود، Extension آن تابع قرارداد عمومی Visual Manager است. Live شامل Candidateهای Developing و Displacementهای Confirmed مرتبط با MSS، FVG یا OB فعال است.

### آزمون‌ها

- کندل بزرگ بدون Structure؛
- Structure بدون Displacement؛
- جهت مخالف؛
- Candidate Superseded؛
- Displacement خارج Window؛
- Sequence چندکندلی؛
- عدم Confirmation تکراری؛
- Quality components؛
- ثبات MSS پس از Reload.

### معیار پایان

- هر MSS Confirmed به CHoCH و Displacement معتبر متصل باشد؛
- Candidate نامعتبر تأیید نشود؛
- State پس از تأیید بازنویسی نشود.

---

## Phase 6 — FVG Engine

### هدف

ساخت Fair Value Gap Database با Validation، Fill Tracking و Lifecycle کامل.

### FVGObject

- Direction؛
- Top و Bottom؛
- Midpoint/Consequent Encroachment؛
- Size و Size-to-ATR؛
- Origin TF/Time؛
- Fill Percentage؛
- First Touch و Mitigation Time؛
- Linked Displacement و Structure IDs؛
- PD Location؛
- State.

Lifecycle:

> Fresh → Partial → Mitigated/Invalid

### Detection و Validation

- Bullish: Low کندل سوم بالاتر از High کندل اول؛
- Bearish: High کندل سوم پایین‌تر از Low کندل اول.

Validation modes:

- Raw: هر Gap سه‌کندلی؛
- Standard: حداقل Size/ATR؛
- Strict ICT: Gap همراه Displacement و Structure Context معتبر.

Mitigation rule می‌تواند CE touch یا Full Fill باشد. Fill Percentage باید در بازه معتبر باقی بماند و جهت Bullish/Bearish را درست لحاظ کند.

### تنظیمات تحلیل

- Validation Mode؛
- Minimum Size و ATR Ratio؛
- CE/Full mitigation؛
- Maximum Age؛
- Structural invalidation؛
- Overlap/merge policy.

### نمایش

- Bullish/Bearish؛
- Fresh/Partial/Mitigated/Invalid؛
- Box، Border، Label و CE Line؛
- Fill Percentage label؛
- تمام Extension Modeهای قابل‌کاربرد، شامل زمان جاری، Mitigation، First Touch، Invalidation، Replacement، N کندل از تشکیل، N کندل از زمان جاری و Full History؛
- HTF/Current/LTF filter؛
- State Endpoint و State Visual Action مستقل برای Fresh، Partial، Mitigated و Invalid؛
- تمام Display Scopeهای مشترک، با پیش‌فرض `LIVE_AND_LATEST_N`؛
- فیلتر Unmitigated/Untouched، Fill Percentage، فاصله، Size/ATR، Authority و Quality.

Fresh و Partial زنده‌اند؛ Mitigated و Invalid فقط در بخش Latest N یا Historical نمایش داده می‌شوند، مگر کاربر خلاف آن را انتخاب کند. در `TO_LIFECYCLE_EVENT` انتهای Box دقیقاً روی Mitigation Time یا Invalidation Time ثابت می‌شود. CE Line و Box باید State، Endpoint و Visibility هماهنگ داشته باشند، ولی Label می‌تواند مستقل خاموش شود.

### آزمون‌ها

- Bullish/Bearish؛
- Gap صفر و کوچک؛
- Duplicate origin؛
- Partial Fill؛
- CE و Full Mitigation؛
- Direction-specific calculation؛
- Overlap policy؛
- HTF identity و alignment؛
- History overflow؛
- تغییر Visibility بدون تغییر Database.

### معیار پایان

- Lifecycle یک‌طرفه و صحیح باشد؛
- Fill Percentage معتبر باشد؛
- Lineage کامل و Storage bounded باشد.

---

## Phase 7 — Order Block و Breaker

### هدف

شناسایی OB متصل به حرکت impulsive و Structure Break، نه صرفاً تغییر رنگ کندل.

### OBObject

- Direction؛
- High/Low Bounds؛
- Origin Candle/Cluster Time؛
- Range Basis: Full، Body یا Wick؛
- Linked Displacement/Structure IDs؛
- First Test و Mitigation Time؛
- Lineage Root ID؛
- Breaker From ID؛
- State.

Lifecycle:

> Candidate → Fresh → Tested → Mitigated یا Breaker/Invalid

### Detection و Validation

- Bullish OB: آخرین کندل نزولی یا Cluster معتبر قبل از حرکت صعودی مؤثر؛
- Bearish OB: آخرین کندل صعودی یا Cluster معتبر قبل از حرکت نزولی مؤثر؛
- Displacement و Structure Event باید قابل‌ردیابی باشند؛
- Freshness، Overlap و Age بررسی شوند؛
- Breaker از همان OB و با حفظ Root ID ایجاد شود.

### تنظیمات تحلیل

- Candle/Cluster search window؛
- Range Basis؛
- Required Structure Event؛
- Required Displacement Quality؛
- Test و Mitigation rule؛
- Breaker rule؛
- Overlap/merge و Maximum Age.

### نمایش

- Bullish/Bearish/Breaker؛
- Candidate/Fresh/Tested/Mitigated؛
- Box، Border و Label؛
- Root/Linked Event label اختیاری؛
- Extension تا Live Bar، First Touch، Mitigation، Invalidation، Breaker conversion، Replacement، N کندل از تشکیل/جاری یا Full History؛
- HTF/Current/LTF؛
- State Endpoint و State Visual Action مستقل برای Candidate، Fresh، Tested، Mitigated، Breaker و Invalid؛
- تمام Display Scopeهای مشترک، با پیش‌فرض `LIVE_AND_LATEST_N`؛
- فیلتر Untouched/Unmitigated، Range Basis، فاصله، Authority، Quality و Linked Event.

Candidate، Fresh، Tested و Breaker فعال زنده محسوب می‌شوند. هنگام تبدیل OB به Breaker، Root Logical ID حفظ می‌شود اما Drawing Breaker VisualRef و Style Key مستقل می‌گیرد؛ Drawing قبلی بسته به State Visual Action می‌تواند متوقف، Fade، Hide یا به Marker تبدیل شود.

### آزمون‌ها

- Opposing candle صحیح؛
- کندل مخالف بدون Displacement؛
- First Test؛
- Mitigation؛
- Invalidation؛
- Role Reversal؛
- Breaker lineage؛
- Duplicate و Overlap؛
- Age pruning و Scan cap.

### معیار پایان

- هر OB منشأ ساختاری معتبر داشته باشد؛
- Breaker به Root OB متصل باشد؛
- Interaction Time و State صحیح باشند.

---

## Phase 8 — Dealing Range، Premium/Discount و OTE

### هدف

تعیین موقعیت جهت‌دار قیمت در محدوده یک Leg ساختاری معتبر.

### DealingRangeObject

- Direction؛
- High/Low Swing IDs؛
- High/Low Prices و Times؛
- Equilibrium؛
- OTE 62، 70.5 و 79؛
- Current PD Location؛
- Origin TF و Degree؛
- State.

### ساخت Range

Source modes:

- Auto Structural؛
- Current TF؛
- Selected HTF؛
- Manual.

- Bullish Range از Swing Low به Swing High؛
- Bearish Range از Swing High به Swing Low.

سطوح:

- 0%؛
- 50% Equilibrium؛
- 100%؛
- OTE 62%؛
- Mid OTE 70.5%؛
- OTE 79%.

در Context صعودی، Discount و OTE سمت مطلوب Retracement برای Long هستند. در Context نزولی، Premium و OTE سمت مطلوب Retracement برای Short محسوب می‌شوند. این موقعیت فقط Context است و Entry قطعی نیست.

### تنظیمات تحلیل

- Range Source؛
- Degree و TF؛
- Auto-update rule؛
- Stale/Invalid policy؛
- OTE levels؛
- Equilibrium tolerance؛
- Range priority.

### نمایش

- Premium و Discount zones؛
- Equilibrium line؛
- OTE zone و خطوط 62/70.5/79؛
- Source TF و Direction label؛
- Current/HTF ranges؛
- تمام Display Scopeهای مشترک، با پیش‌فرض `LIVE_AND_LATEST_N`؛
- N مستقل بر اساس TF، Degree و Direction؛
- Extension تا Live Bar، Replacement، Invalidation، Structure Event، N کندل از تشکیل/جاری یا Full History؛
- State Visual Action مستقل برای Active، Stale، Replaced و Invalid؛
- Color و Transparency مستقل.

Live در این Engine شامل Dealing Range فعال Context جاری است. Rangeهای Stale یا Replaced در Latest N باقی می‌مانند و به‌طور پیش‌فرض در زمان Replacement متوقف و Fade می‌شوند. اجزای Premium، Discount، Equilibrium و OTE یک Composite Drawing هستند و باید Endpoint یکسان داشته باشند؛ Visibility جزءها می‌تواند مستقل باشد.

### آزمون‌ها

- Bullish/Bearish calculation؛
- EQ دقیق؛
- OTE در سمت صحیح؛
- Source switching؛
- Stale range؛
- Overlap و Priority؛
- Location mismatch؛
- HTF range projection.

### معیار پایان

- محاسبات جهت‌دار صحیح باشند؛
- Range به Swingهای معتبر متصل باشد؛
- State تغییر Range قابل‌ردیابی باشد.

---

## Phase 8B — Session و Killzone

### هدف

ساخت Context زمانی و تبدیل High/Low هر Session به Liquidity استاندارد.

### SessionObject

- Session Kind؛
- Timezone؛
- Start و End؛
- Open، High، Low و Close؛
- Range؛
- High/Low Liquidity IDs؛
- High/Low Swept status؛
- State.

Lifecycle:

> Inactive → Active → Closed

### Sessionها

- Asia؛
- London؛
- New York؛
- London Close در صورت نیاز؛
- Custom.

Timezone باید DST-aware باشد. Session عبوری از نیمه‌شب نیز باید درست محاسبه شود.

### نقش تحلیلی

- Session High/Low به Liquidity Database وارد می‌شود؛
- Session Context یکی از عوامل Sweep Quality و Setup Confluence است؛
- رویداد خارج Killzone حذف یا Invalid نمی‌شود؛
- Session بسته‌شده immutable است.

### تنظیمات تحلیل

- Session enable؛
- Start/End؛
- Timezone؛
- Reference days؛
- Session liquidity expiry؛
- Quality weight.

### نمایش

- Background؛
- Session Box؛
- Open، High و Low lines؛
- Extend High/Low؛
- Active/Closed style؛
- Color/Transparency؛
- تمام Display Scopeهای مشترک، با پیش‌فرض `LIVE_AND_LATEST_N`؛
- N مستقل برای هر Session Kind؛
- Extension Session Box تا Session End و Extension خطوط High/Low تا Session End، Interaction، Invalidation، N کندل، Live Bar یا Full History؛
- State Visual Action مستقل برای Upcoming، Active، Closed و Invalid.

Live شامل Session فعال و در صورت نیاز Session بعدی در حالت Upcoming است. Session Box در Close Time متوقف می‌شود و بعد از بسته‌شدن immutable است؛ ادامه خطوط High/Low پس از پایان Session مربوط به LiquidityObjectهای مشتق‌شده است و از Lifecycle فاز Liquidity پیروی می‌کند، نه از SessionObject.

### آزمون‌ها

- Start/End؛
- عبور از نیمه‌شب؛
- DST؛
- OHLC؛
- Liquidity creation؛
- Duplicate prevention؛
- Closed immutability؛
- Visibility independence.

### معیار پایان

- Session State و OHLC صحیح باشند؛
- Session Liquidity استاندارد و قابل‌ردیابی باشد؛
- Timezone/DST درست عمل کند.

---

## Phase 9 — Real MTF Context Engine

### هدف

ساخت Context واقعی چندتایم‌فریمی با همان منطق Core، نه جایگزین‌هایی مانند SMA یا Rolling High/Low.

### MTFContextObject

- Origin TF؛
- Confirmation State؛
- External/Internal/Micro Bias؛
- Last BOS/CHoCH/MSS IDs؛
- Protected Levels؛
- Nearest/Active Liquidity؛
- Last Sweep؛
- Active FVG/OB؛
- PD Location؛
- Session Context؛
- Primary DOL ID پس از Phase 9B.

### پروفایل‌ها

- Macro؛
- Intraday؛
- Scalping؛
- Fast Scalping؛
- Custom.

هر TF Slot سه حالت دارد:

- Disabled؛
- Context Only؛
- Context + Visual Objects.

### HTF

- Confirmed Mode برای Assistant، Backtest و Alert؛
- Developing Mode فقط برای Preview؛
- Origin Time و Confirmation Time جدا؛
- Logical ID مستقل از Chart TF؛
- Payload تحلیلی بدون Drawing handle؛
- رسم در Chart Context با محور Time.

### LTF

برای Summary می‌توان Payload خلاصه گرفت. اگر تمام Eventهای داخل یک HTF Bar لازم باشند، پردازش Intrabar آرایه‌ای و محدود استفاده می‌شود. یک مقدار LTF ساده نماینده تمام Intrabarها فرض نمی‌شود.

### مدیریت Request

- Registry مرکزی؛
- Deduplication؛
- Payload کوچک و typed؛
- Context Only به‌عنوان حالت سبک؛
- محدودیت تعداد TF و جزئیات؛
- ثبت Request Budget؛
- بازبینی محدودیت‌های جاری پلتفرم پیش از Release.

کاهش Compiled Tokens در MTF باید با اشتراک‌گذاری Pipeline، Registry، Payload builder و Projection logic انجام شود، نه با حذف TF Slot، Degree، Engine output یا حالت Confirmed/Developing. Payload کوچک‌تر فقط وقتی پذیرفته است که تمام فیلدهای موردنیاز مصرف‌کنندگان واقعی حفظ شوند. اگر Context Only یا Detail Limit عمداً خروجی اختیاری را کم می‌کند، این رفتار باید Input شفاف کاربر باشد و نباید به‌صورت راه‌حل پنهانی برای عبور از سقف کامپایل فعال شود.

### تنظیمات

- Profile؛
- Custom TF slots؛
- Slot mode؛
- Confirmed/Developing preview؛
- Object projection filters؛
- HTF/LTF detail limits؛
- Request/Intrabar budget.

### نمایش

Objectهای Projected از تنظیمات بصری همان Engine پیروی می‌کنند و Tag تایم‌فریم دارند. Developing Objectها با Border/Transparency متفاوت مشخص می‌شوند.

State، Lifecycle و قیمت‌های هر Object فقط در Origin TF یا Resolution Canonical تعیین می‌شوند و Chart TF صرفاً محل Projection است. تغییر Chart TF نباید State، Logical ID، Creation Time، Confirmation Time، Mitigation Time یا Terminal Time را عوض کند. برای هم‌ترازی، مختصات افقی بر پایه Time ذخیره می‌شوند و نه Chart `bar_index`.

قواعد Projection:

- HTF روی Current/LTF به‌صورت پیش‌فرض مجاز و دارای TF Tag است؛
- LTF روی HTF فقط با انتخاب صریح، سقف جزئیات و Aggregation/Selection Policy نمایش داده می‌شود؛
- `CONFIRMED_ONLY` حالت پیش‌فرض مصرف تحلیلی و Projection است و Developing فقط Preview با Style متفاوت است؛
- یک Drawing Key از Logical ID، Chart TF و Drawing Type مانع Drawing تکراری می‌شود؛
- تشخیص Mitigation/Sweep/Fill می‌تواند بر مبنای Wick یا Close تایم‌فریم منبع یا داده LTF دقیق‌تر باشد، اما Resolution انتخاب‌شده Canonical است و با تغییر Chart TF تغییر نمی‌کند؛
- در برخوردهای بصری، Higher TF، Degree بالاتر، Authority/Quality بیشتر، نزدیکی به قیمت و Freshness اولویت دارند؛
- Overlapهای هم‌نوع می‌توانند Cluster یا Deduplicate شوند، اما Logical Objectهای منبع حذف یا ادغام هویتی نمی‌شوند؛
- تمام Display Scope، Extension، State Action و Label Anchorها از تنظیمات Engine مبدأ پیروی می‌کنند.

### آزمون‌ها

- سطح 1H روی Chart 15M؛
- Logical ID ثابت با تغییر Chart TF؛
- Confirmed در برابر Developing؛
- Reload determinism؛
- چند Event LTF در یک HTF Bar؛
- Profile switching؛
- Request deduplication؛
- Payload limits و Runtime؛
- خروجی یکسان MTF پیش و پس از Refactor کاهش توکن؛
- ثابت ماندن Logical ID، State، Confirmation Time و Projection در همه TF Slotها.

### معیار پایان

- Context از منطق واقعی Core ساخته شود؛
- HTF/LTF role پویا باشد؛
- Confirmed Context برای downstream پایدار باشد؛
- بهینه‌سازی حجم کد هیچ Slot یا قابلیت تحلیلی/نمایشی MTF را حذف نکرده باشد.

---

## Phase 9B — DOL Engine

### هدف

انتخاب و رتبه‌بندی هدف‌های محتمل قیمت از میان Liquidity Objectهای معتبر.

### DOLObject

- Direction؛
- Target Liquidity ID؛
- Target Price؛
- Score Components؛
- Priority؛
- Origin Context IDs؛
- Activation/Reached Time؛
- State و Reason.

Lifecycle:

> Candidate → Active → Reached/Invalid

### Candidateها

- External BSL/SSL؛
- EQH/EQL؛
- PDH/PDL؛
- PWH/PWL؛
- Session High/Low؛
- Swing Liquidity معتبر.

### رتبه‌بندی

- Structure Direction Alignment؛
- Authority؛
- Quality و Freshness؛
- Distance و Reachability؛
- PD Location؛
- MTF Alignment؛
- Session Context؛
- Penalty برای Consumed یا Accepted Beyond.

Candidateها Primary، Secondary یا Tertiary می‌شوند. برای جلوگیری از تغییر هدف در هر Bar، Hysteresis یا Minimum Score Difference لازم است.

### تنظیمات

- Candidate kinds؛
- Minimum Score؛
- Maximum Distance؛
- Component weights؛
- Hysteresis؛
- Maximum active targets؛
- Reached tolerance.

### نمایش

- Target line و Label؛
- Liquidity kind، TF، Price و Priority؛
- Primary/Secondary/Tertiary filters؛
- Direction و Score؛
- تمام Display Scopeهای مشترک، با پیش‌فرض `LIVE_AND_LATEST_N`؛
- Extension تا Reached، Invalidation، Replacement، Live Bar، N کندل از تشکیل/جاری یا Full History؛
- State Endpoint و State Visual Action مستقل برای Candidate، Active، Reached و Invalid؛
- Current و Historical targets.

Candidate و Active زنده‌اند. Reached و Invalid در زمان Transition متوقف و در Latest N به‌طور پیش‌فرض Fade می‌شوند. هنگام تعویض Primary Target به علت Hysteresis/Score، Target قبلی باید Reason و Terminal Time بگیرد تا Hide شدن Drawing باعث گم‌شدن تاریخچه تصمیم نشود.

### آزمون‌ها

- Context صعودی و نزولی؛
- HTF Authority در برابر LTF Quality؛
- Fresh در برابر Consumed؛
- Distance tie؛
- Context change؛
- Reached؛
- Hysteresis و ثبات هدف.

### معیار پایان

- انتخاب هدف قابل توضیح باشد؛
- DOL فقط Liquidity معتبر مصرف کند؛
- تغییرات بدون دلیل رخ ندهند.

---

## Phase 10 — Visual Manager

### هدف

ساخت زیرساخت مرکزی و یگانه Drawing. این فاز قرارداد عمومی هویت بصری، دامنه نمایش، Extension، واکنش به State، Label، فیلتر، اولویت و Render را تعریف می‌کند. نوع Drawing و رفتار اختصاصی هر Engine در فاز همان Engine تعیین شده است تا منطق عمومی تکرار نشود و جزئیات هر مفهوم پراکنده نماند.

### مرز مالکیت و هویت

- Engine مالک Logical Object، State، Lifecycle، Lineage و Reason است؛
- Visual Manager مالک Line، Box، Label، Polyline، Table reference و Pool است؛
- Hide، Fade، Delete یا Evict فقط Drawing را تغییر می‌دهد و Logical Object را دست‌کاری نمی‌کند؛
- Visual Manager هیچ Swing، Sweep، BOS، FVG، OB، DOL یا Setup جدیدی تشخیص نمی‌دهد؛
- یک Logical Object می‌تواند چند Drawing component داشته باشد، اما برای هر `Logical ID + Chart TF + Drawing Type` فقط یک VisualRef فعال مجاز است؛
- تغییر State یا داده تحلیلی Dirty Flag ایجاد می‌کند و فقط VisualRefهای متأثر Update می‌شوند؛
- بازسازی پس از Reload یا تغییر Chart TF باید با Logical ID انجام شود و به Drawing ID قبلی وابسته نباشد.

### VisualRef

- Logical Object ID؛
- Drawing Key؛
- Chart TF و Drawing Type؛
- Line، Box، Label و Polyline IDs؛
- Visibility State؛
- Display Scope result؛
- Extension Mode و Effective End Time؛
- State Endpoint Policy و State Visual Action؛
- Label anchors و offsets؛
- Last Render Time؛
- Style Key؛
- Priority؛
- Pool Slot؛
- Dirty flags.

### دامنه نمایش یا Display Scope

تمام Engineهای دارای Drawing باید این حالت‌ها را ارائه کنند. حالت پیش‌فرض سراسری `LIVE_AND_LATEST_N` است و هر Engine می‌تواند N پیش‌فرض مناسب خودش را داشته باشد.

| Mode | تعریف دقیق |
|---|---|
| `ACTIVE_ONLY` | فقط Objectهایی که بر اساس Lifecycle همان Engine هنوز زنده‌اند و به State نهایی نرسیده‌اند. |
| `LATEST_ONLY` | فقط آخرین Object معتبر در هر گروه مستقل Engine/TF/Degree/Direction/Kind. |
| `LATEST_N` | آخرین N Object معتبر در هر گروه مستقل، اعم از Active یا Terminal. |
| `RECENT_BARS` | Objectهایی که Origin یا Confirmation آن‌ها در N کندل اخیر Resolution مرجع قرار دارد. |
| `RECENT_TIME` | Objectهای تشکیل یا تأییدشده در بازه زمانی انتخابی. |
| `UNTOUCHED_ONLY` | فقط Objectهایی که هنوز First Touch معتبر ندارند. |
| `UNMITIGATED_ONLY` | فقط FVG، OB و Objectهای دارای مفهوم Mitigation که هنوز Mitigated نشده‌اند. |
| `UNSWEPT_ONLY` | فقط Liquidityهایی که هنوز Validated Sweep یا Acceptance نهایی ندارند. |
| `DEVELOPING_ONLY` | فقط Objectهای Preview/Candidate تأییدنشده. |
| `CONFIRMED_ONLY` | فقط Objectهای Confirmed؛ این فیلتر لزوماً Terminalها را حذف نمی‌کند. |
| `LIVE_AND_LATEST_N` | تمام Objectهای زنده به‌اضافه آخرین N Object نهایی یا غیرفعال هر گروه؛ حالت پیش‌فرض. |

تعریف Live باید در فاز هر Engine صریح باشد. Live مترادف «فقط Object ایجادشده روی آخرین کندل» نیست؛ یک OB یا FVG قدیمی اما فعال همچنان Live است. اگر کاربر فقط تازه‌ترین موارد تشکیل‌شده را بخواهد باید `LATEST_ONLY`، `LATEST_N`، `RECENT_BARS` یا `RECENT_TIME` را انتخاب کند.

N و Recent Window باید بتوانند به‌صورت Global تعریف و در سطح Engine، TF، Degree، Direction یا Kind Override شوند. انتخاب Latest قبل از Visual Budget انجام می‌شود تا خروجی به ترتیب Array یا ازدحام یک گروه وابسته نباشد.

### قرارداد Extension

Extension فقط مختصات افقی Drawing را تعیین می‌کند و قیمت Line یا Bounds ناحیه را تغییر نمی‌دهد. عبارت UI «تا قیمت/کندل زنده» به معنی ادامه افقی تا زمان آخرین کندل است، نه دنبال‌کردن عمودی قیمت بازار.

| Mode | رفتار |
|---|---|
| `TO_LIVE_BAR` | انتهای Drawing همراه زمان آخرین کندل حرکت می‌کند. |
| `TO_LIFECYCLE_EVENT` | تا رویداد نهایی مناسب همان Engine مانند Mitigation، Sweep، Fill، Reach، Break یا Invalidation ادامه می‌یابد و سپس در Event Time ثابت می‌شود. |
| `N_BARS_FROM_CREATION` | از Origin یا Confirmation، مطابق Anchor انتخابی، دقیقاً N کندل Resolution مرجع ادامه می‌یابد. |
| `N_BARS_FROM_CURRENT` | انتها همیشه N کندل جلوتر از کندل جاری است و تا زنده بودن حالت حرکت می‌کند. |
| `FULL_HISTORY` | از Start Time تا تمام End Time تاریخی معتبر Object نمایش داده می‌شود؛ این Mode از Visual Budget معاف نیست. |
| `TO_FIRST_TOUCH` | در First Touch معتبر متوقف می‌شود، حتی اگر State نهایی هنوز رخ نداده باشد. |
| `TO_INVALIDATION` | تا Invalidation معتبر ادامه می‌یابد. |
| `TO_REPLACEMENT` | با Supersede یا جایگزینی Object هم‌نوع متوقف می‌شود. |
| `TO_SESSION_END` | در End Time همان Session متوقف می‌شود. |
| `TO_STRUCTURE_EVENT` | در BOS، CHoCH، MSS یا Structure Event مرتبط متوقف می‌شود. |

Start Anchor برای هر Drawing باید Origin Time، Confirmation Time یا Event Time باشد. Resolution مبنای شمارش N نیز باید Origin TF، Chart TF یا Canonical Evaluation TF باشد و حالت پیش‌فرض Origin TF است. برای Eventهایی که ماهیت نقطه‌ای دارند، مانند Marker خود BOS یا Displacement، Extension اعمال نمی‌شود مگر یک Level/Projection همراه آن‌ها رسم شود.

### واکنش به تغییر State

واکنش پس از Transition از دو محور مستقل ساخته می‌شود تا «کجا پایان یابد» با «چگونه دیده شود» مخلوط نشود.

**State Endpoint Policy:**

- `FREEZE_AT_TRANSITION`: انتها روی State-change Time ثابت شود؛
- `CONTINUE_TO_LIVE`: پس از تغییر State نیز تا آخرین کندل ادامه یابد؛
- `EXTEND_N_AFTER_TRANSITION`: از زمان Transition به‌اندازه N کندل ادامه یابد؛
- `KEEP_EXTENDING`: Extension قبلی بدون تغییر حفظ شود؛
- `CONVERT_AT_TRANSITION`: Drawing در Transition بسته و به Drawing Type دیگری تبدیل شود.

**State Visual Action:**

- `KEEP`: با Style اصلی قابل مشاهده بماند؛
- `FADE`: با Transparency بیشتر، رنگ خنثی‌تر، ضخامت کمتر یا خط‌چین باقی بماند؛
- `HIDE`: Drawing حذف شود ولی Logical Object و تاریخچه آن حفظ شوند؛
- `MARKER_ONLY`: Line/Box حذف و فقط Marker یا Label رویداد باقی بماند؛
- `ARCHIVE`: فقط در Full History یا Historical/Preset مربوط قابل نمایش باشد.

هر State از هر Engine باید Endpoint Policy، Visual Action، Style و Visibility مستقل داشته باشد. Stateهای نامرتبط نباید به همه Engineها تحمیل شوند. ماتریس حداقلی مورد انتظار چنین است:

| Engine | Stateهای قابل پیکربندی |
|---|---|
| Swing | Developing/Candidate، Confirmed، Protected، Swept، Broken، Superseded، Invalid |
| Liquidity و EQH/EQL | Fresh، Touched، SweepCandidate، ValidatedSweep، AcceptedBeyond، Consumed، Invalid/Merged |
| Structure | Pending، Confirmed، Broken، Consumed، Superseded، Invalid |
| Displacement | Candidate، Confirmed/Qualified، Invalid/Failed، Superseded |
| FVG | Fresh، Partial، Mitigated/FullyFilled، Invalid |
| OB/Breaker | Candidate، Fresh، Tested، Mitigated، Breaker، Invalid |
| Dealing Range | Active، Stale، Replaced، Invalid |
| Session | Upcoming، Active، Closed، Invalid |
| DOL | Candidate، Active، Reached، Invalid |
| Setup | Candidate، Qualified، Armed، Triggered، Expired، Invalid |

### سیستم Label

Label یک Drawing component مستقل است. خاموش‌کردن Label نباید Line، Zone یا Logical Object را مخفی کند. متن، Size، Color، Transparency، Detail Mode و Position می‌توانند Global باشند و در سطح Engine، TF، Degree، Kind و State Override شوند.

موقعیت از دو محور مستقل تشکیل می‌شود:

| محور افقی | رفتار |
|---|---|
| `START` | روی Start Anchor یا ابتدای تشکیل Drawing. |
| `MIDDLE` | در میانه Start Time و Effective End Time. |
| `END` | روی انتهای مؤثر Extension. |
| `AUTO` | انتخاب Start/Middle/End بر اساس فضای قابل مشاهده و برخوردها. |

| محور عمودی | رفتار |
|---|---|
| `ABOVE` | بالای Line یا Upper Bound ناحیه. |
| `ON_OR_INSIDE` | روی Line یا داخل Zone. |
| `BELOW` | زیر Line یا Lower Bound ناحیه. |
| `AUTO` | انتخاب محل خواناتر با توجه به Direction، Price Scale و تراکم. |

رفتار `END` باید با Extension هماهنگ باشد:

- در `TO_LIVE_BAR` همراه انتهای زنده حرکت کند؛
- در `TO_LIFECYCLE_EVENT` پس از رویداد روی Event Time قفل شود؛
- در `N_BARS_FROM_CREATION` روی انتهای ثابت N کندل قرار گیرد؛
- در `N_BARS_FROM_CURRENT` همراه انتهای متحرک جابه‌جا شود؛
- در `FULL_HISTORY` روی آخرین End Time قابل‌رسم قرار گیرد؛
- در State Endpointهای Freeze/Convert از Effective End Time جدید پیروی کند.

تنظیمات تکمیلی Label:

- X Offset بر حسب Bar/Time و Y Offset بر حسب Tick، درصد یا ATR؛
- Compact، Standard و Detailed Text؛
- نمایش اختیاری Type، TF، Degree، State، Price، Quality، Score، Member Count و Source؛
- نمایش فقط برای Active، Primary، Latest N یا Objectهای عبورکرده از حداقل Priority؛
- Collision Avoidance تقریبی و Staggering بر اساس Bucketهای Time/Price برای جلوگیری از هم‌پوشانی؛
- حداکثر Label مستقل از حداکثر Line/Box؛
- `AUTO` برای انتخاب Anchor خواناتر در فضای قابل مشاهده؛
- Fallback از END به MIDDLE یا START اگر انتهای Drawing خارج Viewport یا خارج محدودیت زمانی مجاز باشد.

پیش‌فرض عمومی Label، `END + ON_OR_INSIDE` است. برای خطوط Liquidity، Protected Level و DOL، پیش‌فرض اختصاصی می‌تواند `END + ABOVE/BELOW` بر اساس Side/Direction باشد. برای Eventهای نقطه‌ای، START و END یک زمان دارند و Label روی Event Time قرار می‌گیرد.

### فیلتر و کاهش ازدحام

علاوه بر Display Scope، این فیلترها باید در صورت کاربرد قابل تنظیم باشند:

- TF Role و فهرست TFهای مجاز؛
- External/Internal/Micro؛
- Bullish/Bearish؛
- Kind و State؛
- Minimum Authority، Quality و Confluence؛
- هماهنگی با Bias جاری؛
- حداکثر فاصله از قیمت جاری بر حسب Tick، درصد یا ATR؛
- Visible Range only؛
- First/Fresh/Untouched/Unmitigated/Unswept؛
- قوی‌ترین Object هر Cluster یا محدوده هم‌پوشان؛
- Priority-based selection پس از تمام فیلترهای منطقی.

Clustering و Deduplication بصری فقط انتخاب Drawing را تغییر می‌دهد و Logical IDها را ادغام یا حذف نمی‌کند.

### Render Pipeline

1. دریافت Objectهای تحلیلی؛
2. تعیین Live/Terminal بر اساس تعریف همان Engine؛
3. اعمال Display Scope و فیلترهای همان Engine؛
4. محاسبه Extension، Effective End Time و Label Anchor؛
5. اعمال State Endpoint Policy و State Visual Action؛
6. محاسبه Priority و انتخاب در Visual Budget؛
7. ساخت Drawing Key و یافتن VisualRef موجود؛
8. Create، Update، Convert، Hide یا Recycle؛
9. اعمال Style و Collision policy؛
10. Audit Duplicate، Orphan و Budget.

### اولویت کلی

1. HTF External Liquidity؛
2. HTF Structure Events؛
3. HTF OB؛
4. HTF FVG؛
5. Current TF Structure؛
6. Current TF Liquidity؛
7. LTF Objects؛
8. Micro Details.

داخل هر گروه، Authority، Quality، Confluence، Freshness و Age اولویت را تعیین می‌کنند.

Objectهای Active پیش از Terminalها، Objectهای نزدیک قیمت پیش از دورها و Drawingهای اصلی پیش از Labelهای کم‌اولویت حفظ می‌شوند. `FULL_HISTORY` ترتیب اولویت را لغو نمی‌کند.

### قواعد

- هیچ Engine دیگری Drawing نمی‌سازد؛
- هر نوع Object یک Renderer اصلی دارد؛
- Create once و Update/Reuse؛
- Delete واقعی برای آزادسازی؛
- VisualRef و Drawing هم‌زمان حذف شوند؛
- Rendering عمدتاً روی آخرین Bar؛
- Time coordinate برای MTF؛
- Drawing Reserve برای Eventهای جدید؛
- Dev Renderer پس از تثبیت حذف شود؛
- تغییر Display Scope، Extension، Label یا Preset نباید Counts و Stateهای تحلیلی را تغییر دهد؛
- Hide و Budget Eviction باید در Render بعدی قابل بازسازی باشند؛
- Style و Position Label نباید Trigger تحلیلی یا State transition ایجاد کند.

### قرارداد تنظیمات هر Engine

بخش نمایش هر Engine باید دقیقاً این ترتیب را تکمیل کند:

1. Enable/Disable؛
2. Source TF و TF Role؛
3. Degree، Direction و Kind؛
4. تعریف Live برای همان Lifecycle؛
5. Display Scope با پیش‌فرض `LIVE_AND_LATEST_N`؛
6. N و Recent Window؛
7. Extension Mode و Start Anchor؛
8. Endpoint Policy برای هر State؛
9. Visual Action برای هر State؛
10. Distance، Authority، Quality و Confluence filters؛
11. Maximum Line/Box/Label؛
12. Priority، Overlap و Deduplication؛
13. Drawing Type و Style؛
14. Label Text، Horizontal Anchor، Vertical Anchor و Offset؛
15. رفتار MTF/Developing؛
16. آزمون و معیار پذیرش.

### تنظیمات عمومی

- Global transparency؛
- Label size؛
- Label horizontal/vertical anchor و offsets؛
- Label detail و collision policy؛
- Line width؛
- Box transparency؛
- Theme؛
- Display Scope؛
- Default Latest N و Recent Window؛
- Extension Mode و Start Anchor؛
- State Endpoint/Visual defaults؛
- Priority override؛
- Global visible budget؛
- Preset؛
- Debug visual refs.

### آزمون‌ها

- یک VisualRef برای هر Logical ID؛
- Create/Update/Delete؛
- Pool reuse؛
- Priority eviction؛
- Reserve؛
- Orphan detection؛
- تغییر TF؛
- تغییر Preset؛
- Full redraw در برابر incremental output؛
- تمام Display Scopeها و پیش‌فرض `LIVE_AND_LATEST_N`؛
- تمام Extension Modeها و End Time صحیح؛
- ترکیب هر Endpoint Policy با Keep/Fade/Hide/Marker Only/Archive؛
- START/MIDDLE/END/AUTO با ABOVE/ON_OR_INSIDE/BELOW/AUTO؛
- حرکت Label انتهایی در Extensionهای پویا و قفل‌شدن آن در Transition؛
- خاموش‌شدن Label بدون تغییر Line/Box و Analysis؛
- Collision fallback و Label budget؛
- Hide و Eviction سپس بازسازی صحیح؛
- عدم تغییر Counts با تغییر تنظیمات بصری.

### معیار پایان

- Drawing فقط در این لایه انجام شود؛
- Duplicate و Orphan صفر باشند؛
- Budget رعایت و Alignment پایدار باشد؛
- همه Stateهای هر Engine Endpoint و Visual Action مشخص داشته باشند؛
- حالت پیش‌فرض خروجی خوانا و منطبق با `LIVE_AND_LATEST_N` باشد؛
- Labelها در همه Extensionها و State transitionها موقعیت قطعی و قابل‌آزمون داشته باشند.

---

## Phase 11 — Dashboard

### هدف

نمایش خلاصه Stateهای آماده بدون محاسبه دوباره Analysis.

### حالت Minimal

- Bias؛
- Last Structure Event؛
- PD Location؛
- DOL؛
- MTF Alignment.

### حالت Advanced

- موارد Minimal؛
- Nearest Liquidity؛
- Last Sweep و Quality؛
- MSS؛
- Active FVG/OB؛
- Session Context؛
- Setup readiness.

### حالت Diagnostic

- Object counts؛
- Array/Map integrity؛
- Drawing budgets؛
- Request usage؛
- Queue depth؛
- Cursor positions؛
- Last cleanup؛
- Engine health.

### تنظیمات

- Position؛
- Size و Font؛
- Background، Transparency و Border؛
- Section Visibility؛
- Compact/Detailed؛
- TF Rows؛
- Update cadence.

Dashboard فقط با Table ساخته می‌شود. Unknown، Developing و Confirmed باید صریح نمایش داده شوند.

### آزمون‌ها

- هر سه Mode؛
- Position و Size؛
- Section toggles؛
- MTF Profiles؛
- Unknown State؛
- تطبیق مقادیر با Database؛
- ثابت ماندن Plot Budget.

### معیار پایان

- Dashboard تحلیل مستقل نداشته باشد؛
- همه تنظیمات واقعاً اعمال شوند؛
- داده نمایشی با State Engineها برابر باشد.

---

## Phase 12 — Object Manager و Performance

### هدف

مدیریت ظرفیت تحلیلی، بار پردازش و ظرفیت نمایش بدون تغییر نتیجه منطق.

### سه بودجه اجرایی و بودجه کامپایل

- Analysis History Budget؛
- Processing Budget؛
- Visual Budget.

هرکدام تنظیم و Counter مستقل دارد. علاوه بر آن‌ها، **Compiled-token Budget** یک محدودیت مهندسی مستقل است: سقف سخت این پروژه `100,256` توکن کامپایل‌شده در نظر گرفته می‌شود و پیش از هر Release باید محدودیت جاری TradingView نیز بررسی شود. عبور از این سقف باعث Compile نشدن کل Script می‌شود؛ پلتفرم بخشی از Engineها را انتخابی حذف یا غیرفعال نمی‌کند.

توکن کامپایل‌شده با تعداد کاراکتر، خطوط فایل، Runtime، Memory، تعداد `request.*` یا Drawing یکی نیست. بنابراین حل یک محدودیت نباید به‌اشتباه برای محدودیت دیگر استفاده شود.

### سیاست حاشیه امن توکن

- سقف سخت: `100,256` Compiled Tokens؛
- آستانه هشدار پروژه: 90 درصد سقف، حدود `90,230` توکن؛
- بالاتر از آستانه هشدار، افزودن Feature جدید فقط همراه Token Impact Review و Refactor هم‌ارز مجاز است؛
- Release نباید به مرز سخت متکی باشد، زیرا تغییر نسخه Compiler یا گسترش فازهای بعد می‌تواند Reserve را مصرف کند؛
- مقدار سنجیده‌شده، تاریخ اندازه‌گیری، نسخه Pine/Compiler و تغییر نسبت به نسخه قبل در Release Log ثبت شود.

### Refactor بدون کاهش قابلیت

ترتیب مجاز کاهش توکن:

1. حذف کد مرده، Debug موقت، Dev Renderer، مسیر اجرایی متروک و Aliasهای پایان‌یافته؛
2. ادغام توابع و شرط‌های تکراری بدون تغییر ترتیب Eventها؛
3. استفاده از Utility مشترک برای ID، Storage، Lifecycle، Filter، Extension، Label و Cleanup؛
4. استفاده از Rendererهای عمومی Line/Box/Label با Style و Policy داده‌محور؛
5. یکپارچه‌سازی Registry و Payload builderهای MTF و حذف Requestهای تکراری؛
6. کوچک‌سازی Payload و UDT فقط با حفظ تمام فیلدهای مصرف‌شده؛
7. ساده‌سازی مسیرهای نمایشی اختیاری، بدون حذف Display Mode یا State قابل‌درخواست.

اقدامات ممنوع برای کاهش توکن:

- حذف یا خاموش‌کردن Detection یک Engine؛
- حذف State، Reason، Time، Logical ID یا Lineage؛
- حذف External/Internal/Micro یا TF Slotهای موردنیاز؛
- جایگزینی MTF واقعی با Proxy ساده؛
- حذف Confirmed/Developing یا Non-repaint policy؛
- وابسته‌کردن Analysis به Show flag؛
- حذف Extension، State Visual Action، Label mode یا Drawing موردنیاز؛
- تغییر منطق Setup، Backtest، Execution یا Alert؛
- کاهش History، Request، Drawing یا Processing فقط برای حل خطای Compiled-token، مگر همان محدودیت جداگانه نیز واقعاً وجود داشته و تغییر به‌عنوان رفتار قابل‌تنظیم مستند شده باشد.

### قرارداد هم‌ارزی

Refactor کاهش توکن فقط زمانی پذیرفته است که روی Dataset و تنظیمات ثابت، پیش و پس از تغییر این موارد برابر بمانند:

- تعداد و Logical ID تمام Objectها و Eventها؛
- Origin/Confirmation/State-change/Terminal Time؛
- State، Reason، Source و Lineage؛
- Authority، Quality، Confluence و Score؛
- خروجی تمام TF Slotها و Projectionهای MTF؛
- Drawing selection، Extension endpoint، Label placement و State Visual Action؛
- Setupها، Entry/Exitها، Trades، Metrics و Alerts؛
- رفتار Historical، Replay، Realtime، Reload و تغییر Chart TF.

اگر اختلافی وجود دارد، Refactor هم‌ارز نیست و حتی در صورت کاهش توکن نباید Merge یا Release شود.

برای جلوگیری از برداشت نادرست از `FULL_HISTORY`، سه حد باید جدا تعریف شوند:

- **Logical History Limit:** تعداد Objectهای منطقی قابل نگهداری در Array/Map؛
- **Calculation Lookback/Processing Limit:** دامنه و تعداد مواردی که Engine در هر Bar بررسی می‌کند؛
- **Drawing History Limit:** تعداد Line/Box/Label/Polyline که Visual Manager اجازه رسم دارد.

`FULL_HISTORY` فقط از میان Logical Objectهای موجود، کل تاریخچه قابل‌رسم را درخواست می‌کند و مجوز عبور از محدودیت‌های Drawing پلتفرم نیست. کم‌شدن Drawing History Limit یا Hide شدن Object نباید Logical History را پاک کند. همچنین افزایش Visual Limit نباید Engine را مجبور به محاسبه دوباره تاریخچه کند.

### Cleanup تحلیلی

ترتیب عمومی حذف:

1. Invalid؛
2. Archived بسیار قدیمی؛
3. Mitigated/Consumed قدیمی؛
4. Low-quality و Low-authority؛
5. LTF/Micro قدیمی.

Objectهای دارای Linkage فعال و HTF Fresh در اولویت بقا هستند.

### بهینه‌سازی پردازش

- Map lookup به‌جای Array scan؛
- Event Queue به‌جای Full-history scan؛
- Incremental Cursor؛
- Scan Cap با ادامه در Bar بعد؛
- Bucket Index برای Clusterها با کلید TF+Degree+Side+Basis؛
- Cleanup دوره‌ای؛
- Request deduplication؛
- Object Pool؛
- Profiler per Engine.

### تنظیمات

- Maximum Analysis History برای هر Object؛
- Maximum Visible برای هر Engine/TF/Degree/Direction/Kind؛
- Maximum Line، Box، Label و Polyline مستقل؛
- Processing Cap؛
- Cleanup Interval؛
- Drawing Reserve؛
- Terminal History سهمیه‌ای برای `LATEST_N` و `FULL_HISTORY`؛
- Compiled-token Hard Limit، Warning Threshold و Release Reserve؛
- Debug Mode.

در فشار ظرفیت بصری، ترتیب حفظ پیشنهادی چنین است:

1. Objectهای Active؛
2. Event یا Objectهای تازه ایجادشده با استفاده از Drawing Reserve؛
3. Objectهای نزدیک قیمت جاری؛
4. Higher TF و Degree بالاتر؛
5. Authority، Quality و Confluence بیشتر؛
6. آخرین N Object نهایی موردنیاز `LIVE_AND_LATEST_N`؛
7. Terminalهای قدیمی، Mitigated، Consumed، Reached و Invalid.

Eviction بصری باید فقط VisualRef و Drawing را آزاد کند و Object منطقی قابل بازسازی بماند. Labelهای کم‌اولویت باید پیش از Line/Box اصلی Evict شوند.

### آزمون‌ها

- History بلند؛
- همه Engineها فعال؛
- Array/Map/Cursor integrity؛
- Queue overflow؛
- Starvation؛
- Drawing reserve؛
- نتیجه یکسان پیش و پس از Optimization؛
- Graceful degradation؛
- عبور عمدی از آستانه هشدار و فعال‌شدن Token Impact Review؛
- اندازه‌گیری قبل/بعد Refactor و حفظ قرارداد هم‌ارزی؛
- Compile شدن با تمام Engineها، TF Slotها و قابلیت‌های موردنیاز فعال؛
- تفکیک خطای Compiled-token از Request، Drawing، Runtime و Memory.

### معیار پایان

- Runtime و Memory پایدار باشند؛
- Collection بی‌نهایت وجود نداشته باشد؛
- Optimization خروجی تحلیلی را تغییر ندهد؛
- همیشه ظرفیت لازم برای Event جدید وجود داشته باشد؛
- مصرف Compiled Tokens زیر آستانه Release و دارای Reserve باشد؛
- هیچ Feature برای عبور از سقف کامپایل حذف یا پنهانی غیرفعال نشده باشد.

---

## Phase 13 — Trading Assistant

### هدف

تبدیل Context تأییدشده به Setup قابل توضیح، بدون گرفتن اختیار تصمیم از کاربر.

### SetupObject

- Model؛
- Direction؛
- TF و Context IDs؛
- Hard Gates؛
- Score Components؛
- Suggested POI؛
- Entry Method؛
- Invalidation؛
- Stop Logic؛
- Target/DOL؛
- Time Window؛
- State و Reason.

Lifecycle:

> Candidate → Qualified → Armed → Triggered یا Expired/Invalid

### مدل‌ها

- **A:** Structure جهت‌دار، Liquidity Interaction، MSS و PD Array مناسب؛
- **A+:** شرایط A به‌اضافه MTF Alignment، PD Location و Killzone؛
- **Sniper:** شرایط A+ به‌اضافه DOL Alignment و Displacement/FVG/OB با Quality بالا.

### Hard Gates و Soft Score

Hard Gates پیش از Score بررسی می‌شوند. Long Setup بدون Bullish Structure معتبر Candidate نمی‌شود، حتی اگر عوامل دیگر امتیاز زیادی داشته باشند.

LongScore و ShortScore جدا هستند. عوامل:

- Structure/MSS؛
- Sweep Quality؛
- Displacement؛
- FVG؛
- OB؛
- PD/OTE؛
- MTF؛
- DOL؛
- Session؛
- Freshness و Distance.

وزن‌ها Normalize می‌شوند و هیچ عامل دو بار محاسبه نمی‌شود.

### تنظیمات

- Model enable؛
- Gate strictness؛
- Component weights؛
- Minimum score؛
- POI preference؛
- Entry/Invalidation mode؛
- Expiry window؛
- Confirmed-only policy.

### نمایش

- Model و Direction؛
- Gate status؛
- Score breakdown؛
- Suggested POI؛
- Invalidation؛
- Target/DOL؛
- Warning برای Developing HTF یا Location Mismatch؛
- تمام Display Scopeهای مشترک با پیش‌فرض `LIVE_AND_LATEST_N`؛
- State Visual Action مستقل برای Candidate، Qualified، Armed، Triggered، Expired و Invalid؛
- Label/Marker مستقل برای Entry، Invalidation، Target و Reason.

Live شامل Setupهای Candidate، Qualified و Armed و در صورت نیاز آخرین Triggered است. خطوط POI، Invalidation و Target می‌توانند تا Trigger، Expiry، Invalidation، Target Reach، Live Bar یا N کندل ادامه یابند؛ هرکدام Endpoint خود را دارند اما همگی به Logical Setup ID مشترک متصل‌اند.

### آزمون‌ها

- Long/Short conflict؛
- Score بالا با Gate شکست‌خورده؛
- Duplicate Setup؛
- Expiry و Invalidation؛
- Developing Context؛
- Score normalization؛
- Evidence و Reason completeness.

### معیار پایان

- هر Setup قابل توضیح باشد؛
- شواهد آن در Analysis Framework قابل مشاهده باشند؛
- تصمیم نهایی با کاربر بماند.

---

## Phase 14 — Backtest Strategy

### هدف

ارزیابی عملکرد مدل‌ها در Strategy Tester و ثبت دلیل موفقیت یا شکست.

### TradeObject

- Setup ID و Version؛
- Direction و TF؛
- Entry، Stop و Target؛
- Signal، Entry و Exit Times؛
- Planned/Actual R؛
- Result؛
- Commission و Slippage؛
- Context Snapshot؛
- Reason، Mistake و Lesson.

Lifecycle:

> Planned → Pending → Open → Closed/Cancelled

### حالت‌ها

- Manual Journal؛
- ICT Model Backtest.

### Entry

- Market؛
- Limit؛
- Zone Entry.

### Stop

- Fixed؛
- ATR؛
- Structure؛
- OB؛
- Swing.

### Target

- Fixed RR؛
- Liquidity/DOL؛
- Custom.

### Context Snapshot

هنگام Signal یا Entry، وضعیت Bias، Liquidity، Sweep، MSS، Displacement، FVG، OB، PD، Session، MTF، DOL و Confluence ذخیره می‌شود. Snapshot بعداً با تغییر بازار بازنویسی نمی‌شود.

### آمار

- Total Trades؛
- Wins/Losses و Win Rate؛
- Average/Median R؛
- Largest Win/Loss؛
- Profit Factor؛
- Expectancy؛
- Maximum Drawdown؛
- Win/Loss Streak؛
- Recovery Factor؛
- تفکیک بر اساس Model، Direction، TF، Session، Bias و Score Band.

### اصول صحت

- Confirmed data only؛
- No future leak؛
- Signal time جدا از executable entry time؛
- Same-bar stop/target policy؛
- Commission و Slippage واقعی؛
- Versioned rules؛
- Sample size همراه نتیجه؛
- Out-of-sample یا Walk-forward برای Optimization.

### آزمون‌ها

- معاملات مصنوعی با نتیجه معلوم؛
- تقارن Long/Short؛
- Gap و Slippage؛
- Stop و Target در یک Bar؛
- No-trade periods؛
- Reload determinism؛
- Trade count برابر Journal؛
- صحت Metrics و Filters.

### معیار پایان

- نتیجه قابل بازتولید باشد؛
- Future leak وجود نداشته باشد؛
- فرض‌های Execution مستند باشند؛
- Metrics با Context و Sample Size گزارش شوند.

---

## Phase 15 — Alert Engine

### هدف

تولید هشدار از Event Transition بدون تحلیل موازی و بدون Spam.

### AlertObject

- Alert Type؛
- Related Event/Object ID؛
- Symbol؛
- Origin TF و Degree؛
- Direction و Price؛
- Confirmation State؛
- Message؛
- Dedup Key؛
- Trigger/Expiry Time؛
- State.

Lifecycle:

> New → Triggered → Expired

### رویدادها

- Liquidity Created/Touched/Swept/Accepted/Consumed؛
- EQH/EQL؛
- BOS/CHoCH/MSS؛
- Displacement؛
- FVG Created/Partial/Mitigated؛
- OB Created/Tested/Mitigated/Breaker؛
- Premium/Discount/OTE Entry؛
- MTF Alignment؛
- DOL Changed/Reached؛
- A/A+/Sniper Setup.

### جریان

> Event → Validation → User Filter → Deduplication → Cooldown → Message → Trigger

### Anti-spam

- Dedup Key بر اساس Event ID و Alert Type؛
- Once per Transition؛
- Cooldown؛
- Maximum Alerts per Session؛
- Priority؛
- Invalid/Expired filter.

### تنظیمات

- Event groups؛
- TF/Degree/Direction filters؛
- Confirmed-only default؛
- Priority threshold؛
- Cooldown؛
- Session cap؛
- Message detail.

### Message Contract

هر پیام حداقل Event، Symbol، TF، Degree، Direction، Price، State و Confirmation را دارد. Context summary اختیاری است.

### آزمون‌ها

- یک Alert برای هر Transition؛
- Cooldown و Session cap؛
- Confirmed-only؛
- Filters؛
- Message fields؛
- Reload؛
- استقلال از Visual toggles.

### معیار پایان

- Alert تکراری بی‌دلیل وجود نداشته باشد؛
- پیام برای تصمیم کاربر کافی باشد؛
- همه Alertها از Event استاندارد بیایند.

---

## Phase 16 — QA و Release Validation

### هدف

اثبات صحت منطق، پایداری MTF، کنترل Repaint و رعایت ظرفیت‌ها.

### Static و Compile

- Syntax؛
- Declaration order؛
- Type/Enum؛
- Scope؛
- Duplicate names؛
- Collection declarations؛
- Plot/Request budgets؛
- مقدار Compiled Tokens و تغییر آن نسبت به Baseline؛
- سقف سخت `100,256`، آستانه هشدار `90,230` و Reserve نسخه؛
- Compile با مجموعه کامل Engineها و قابلیت‌های الزامی.

### Unit Tests

- Detection؛
- Validation؛
- State transitions؛
- ID و Lookup؛
- محاسبات Range، Fill، Score و Metrics.

### Integration Tests

- Swing → Liquidity؛
- Liquidity → Sweep؛
- Swing → Structure؛
- CHoCH → Displacement → MSS؛
- Displacement → FVG/OB؛
- Structure → Dealing Range؛
- Context → MTF → DOL؛
- Setup → Backtest/Alert.

### Historical و Replay

- Trend؛
- Range؛
- Volatility Spike؛
- Gap؛
- تاریخچه کوتاه و بلند؛
- Bar-by-bar Replay.

### Realtime و MTF

- HTF روی LTF؛
- LTF Intrabar؛
- Developing/Confirmed؛
- Reload؛
- تغییر Chart TF؛
- Profile switching؛
- Timezone/DST.

### Visual و Performance

- Alignment؛
- Presetها؛
- تمام Display Scopeها و پیش‌فرض `LIVE_AND_LATEST_N`؛
- تمام Extension Modeها و State Endpointها؛
- Keep/Fade/Hide/Marker Only/Archive برای Stateهای هر Engine؛
- Label START/MIDDLE/END/AUTO و ABOVE/ON_OR_INSIDE/BELOW/AUTO؛
- حرکت، Freeze، Offset، Collision و Budget مستقل Label؛
- Drawing budget؛
- Cleanup و Pool؛
- Requests؛
- Runtime و Memory؛
- Profiler.

### Backtest و Alert

- Determinism؛
- No future leak؛
- Same-bar policy؛
- Deduplication؛
- Message correctness.

### ماتریس بازار

- نمادهای با Tick Size و قیمت متفاوت؛
- بازار 24/7 و Session-based؛
- Timeframe از 1M تا Weekly؛
- نوسان کم، زیاد و جهشی؛
- Timezone و DST.

### Release Gates

- صفر Compile Error؛
- Compiled Tokens پایین‌تر از آستانه Release و دارای Reserve؛
- ثبت Token count، Compiler/Pine version و اختلاف با نسخه قبل؛
- صفر Runtime Error در ماتریس اصلی؛
- صفر Integrity Mismatch؛
- صفر Duplicate Logical ID ناخواسته؛
- صفر Drawing Orphan؛
- صفر Alert Duplicate؛
- بدون Future Leak؛
- ثبات Eventهای Confirmed پس از Reload؛
- عبور کامل آزمون هم‌ارزی پس از هر Refactor کاهش توکن؛
- فعال و سالم ماندن تمام Engineها، TF Slotها، Stateها، Linkageها، Drawingها، Backtest و Alertها؛
- رعایت Budget همراه Reserve؛
- Default Preset خوانا؛
- Inputs و Known Limitations مستند.

### معیار پایان

نسخه فقط پس از عبور از همه Gateها منتشر می‌شود. هر Bug رفع‌شده باید یک Regression Test دائمی داشته باشد.

---

# 5. استانداردهای مشترک پیاده‌سازی

## 5.1 ترتیب فایل Pine

1. Script Declaration؛
2. Constants؛
3. Enums؛
4. User-Defined Types؛
5. Inputs؛
6. Global Arrays/Maps/Queues؛
7. Utility Functions؛
8. Detection Engines؛
9. State Managers؛
10. MTF Aggregator؛
11. Object Manager؛
12. Visual Manager؛
13. Dashboard؛
14. Assistant/Alert یا Strategy Logic در فایل مربوط.

## 5.2 قواعد Function

- هر Function یک مسئولیت؛
- ورودی و خروجی typed؛
- Detector بدون Drawing؛
- عدم تغییر پنهان Global State؛
- Loop bounded؛
- Missing ID و NA handling؛
- Failure reason؛
- Helper تکراری ممنوع.

## 5.3 قواعد Collection

- Initialization یک‌باره؛
- Duplicate guard؛
- Array و Map هم‌زمان update شوند؛
- پس از Push سقف اعمال شود؛
- Cleanup، Cursorها را اصلاح کند؛
- Scan بزرگ با Map، Queue یا Bucket جایگزین شود؛
- Processing cap باید continuation داشته باشد؛
- `FULL_HISTORY` بدون عبور از Drawing Budget؛
- کاهش Visual Budget بدون تغییر Logical counts؛
- اولویت Active و Reserve در زمان ازدحام؛
- Evict و بازسازی VisualRef بدون Duplicate یا Orphan.

## 5.4 قواعد Non-repaint

- Creation قطعی روی Bar تأییدشده؛
- Developing state صریح؛
- Origin Time جدا از Confirmation Time؛
- HTF Confirmed برای Backtest/Alert؛
- عدم استفاده از آینده؛
- ثبات Eventهای Confirmed پس از Reload.

## 5.5 سه بودجه اجرایی و بودجه کامپایل

- **Analysis History:** تعداد Objectهای ذخیره‌شده؛
- **Processing:** تعداد موارد بررسی‌شده در هر Bar؛
- **Visual:** تعداد Drawingهای قابل نمایش.
- **Compilation:** تعداد Compiled Tokens؛ سقف سخت پروژه `100,256` و آستانه هشدار پیش‌فرض `90,230` است.

چهار بودجه مستقل‌اند. کاهش Visual Budget نباید Analysis History را کاهش دهد. کاهش Processing Cap نباید باعث Starvation شود. `FULL_HISTORY` یک درخواست نمایشی درون Visual Budget است، نه دستور ذخیره یا رسم نامحدود. Eviction بصری نیز نباید Object منطقی را Archive یا Invalid کند. خطای Compiled-token نیز باید با Refactor هم‌ارز حل شود، نه با کاهش پنهانی History، Request، Drawing یا قابلیت Engineها.

## 5.6 تنظیمات پیش‌فرض

Display Scope پیش‌فرض تمام Engineهای قابل‌رسم `LIVE_AND_LATEST_N` است. مقدار N باید برای هر Engine معقول، محدود و قابل Override باشد. Preset می‌تواند Visibility، N، Style، Label و فیلترها را تغییر دهد، اما نباید داده تحلیلی یا Lifecycle را تغییر دهد.

روشن:

- External Structure؛
- Main Swings و External ZigZag؛
- External BSL/SSL؛
- Basic Bias؛
- Premium/Discount Base؛
- Minimal Dashboard.

خاموش:

- Internal/Micro details؛
- Detailed Equal Liquidity و Sweep diagnostics؛
- FVG/OB history گسترده؛
- MTF full projection؛
- Diagnostic Dashboard؛
- Assistant، Backtest و Alerts.

Presetها:

- Clean؛
- Structure Map؛
- Liquidity Map؛
- PD Arrays؛
- HTF Context؛
- Entry Refinement؛
- Full ICT Map؛
- Diagnostic.

Preset فقط نمایش را تغییر می‌دهد.

---

# 6. راهنمای عیب‌یابی

## 6.1 فرآیند استاندارد

1. خطا را با کمترین تنظیمات بازتولید کن؛
2. نوع خطا را مشخص کن؛
3. آخرین نسخه سالم را تعیین کن؛
4. Counts و Lineage را قبل از ظاهر نمودار بررسی کن؛
5. کوچک‌ترین اصلاح ممکن را اعمال کن؛
6. Save و Reload کن؛
7. آزمون فاز مربوط را اجرا کن؛
8. Regression فازهای قبلی را انجام بده؛
9. علت و Test Case را ثبت کن.

## 6.2 خطاهای Compile

| نشانه | علت محتمل | اقدام صحیح |
|---|---|---|
| Already defined / CE10095 | Patch، Alias یا Renderer تکراری | همه occurrenceها را پیدا و فقط یک تعریف اصلی نگه دار. |
| Undeclared identifier / CE10272 | استفاده پیش از declaration یا نام قدیمی | ترتیب فایل را اصلاح و reference قدیمی را حذف کن. |
| Multi-line / CE10156 | پرانتز، indentation یا tuple نامعتبر | Expression را ساده و ساختار چندخطی را کامل کن. |
| Type mismatch | Enum/String یا UDT/NA ناسازگار | قرارداد type ورودی، خروجی و field را بررسی کن. |
| Global mutation | تغییر Global در Function | Function نتیجه برگرداند و Caller تغییر را اعمال کند. |
| Plot scope | Plot در Local Scope یا Function | Plot را به Global Scope منتقل یا Drawing Manager استفاده کن. |
| Member access | Field یا escape اشتباه | نام Field واقعی و کاراکترهای منتقل‌شده را بررسی کن. |
| Compiled-token limit / Script too large | پیاده‌سازی تکراری، مسیرهای قدیمی، Debug/Dev Renderer یا رشد کنترل‌نشده Featureها | Token count را ثبت کن، طبق ترتیب Refactor فاز 12 کد مشترک را ادغام کن و سپس آزمون هم‌ارزی کامل اجرا کن؛ هیچ Engine یا قابلیت را حذف نکن. |

اگر سقف Compiled Tokens رد شود، کل Script Compile نمی‌شود. این خطا با Request limit، Too Many Drawings، Too Many Plots، Runtime یا Memory یکی نیست. خاموش‌کردن Show flag یا حذف MTF/Engine برای عبور از سقف، رفع معتبر محسوب نمی‌شود.

## 6.3 Too Many Plots / RE10140

علت‌ها:

- Debug plotهای موقت؛
- plotshape برای هر حالت؛
- alertconditionهای زیاد؛
- تصور آزاد شدن Plot با `display.none`.

رفع:

- Debug عددی را به Table منتقل کن؛
- Markerها را با Label/Line Pool بساز؛
- Alerts را در Assistant نگه دار؛
- Plotهای آزمایشی را پس از پذیرش فاز حذف کن؛
- Plot Budget را ثبت کن.

## 6.4 Runtime و Memory

علت‌ها:

- Full-history scan در هر Bar؛
- Array lookup خطی برای ID؛
- Cluster search بدون Bucket؛
- Loop بدون Cap؛
- Redraw کامل؛
- Request تکراری؛
- Payload بزرگ؛
- Collection بدون سقف.

ترتیب رفع:

1. Map lookup؛
2. Event Queue؛
3. Incremental Cursor؛
4. Scan Cap؛
5. Bucket Index؛
6. Object Pool و render-on-last؛
7. Request deduplication؛
8. Payload reduction؛
9. کاهش History فقط در آخرین مرحله.

برای حل Performance نباید Analysis با Show flag خاموش شود.

## 6.5 Drawing تکراری یا Orphan

- یک Renderer اصلی نگه دار؛
- Create once و Update انجام بده؛
- VisualRef و Drawing را هم‌زمان حذف کن؛
- Logical ID را بررسی کن؛
- Pool، Reserve و Audit Counter داشته باش.

### 6.5.1 Extension یا State Visual اشتباه

- اگر Drawing پس از Mitigation/Sweep/Reach هنوز حرکت می‌کند، Effective End Time و State Endpoint Policy را بررسی کن؛
- اگر Hide باعث حذف Object از Analysis شده، State Action در Engine یا Cleanup مصرف شده و باید فقط به Visual Manager محدود شود؛
- اگر `N_BARS_FROM_CREATION` با تغییر Chart TF جابه‌جا می‌شود، شمارش به Chart TF وابسته شده است؛ مبنا را Origin یا Canonical TF قرار بده؛
- اگر `FULL_HISTORY` خطای ظرفیت می‌دهد، Drawing History Limit و اولویت Eviction را اعمال کن؛ Logical History را کاهش نده؛
- اگر `LIVE_AND_LATEST_N` Terminalهای یک گروه را اشتباه حذف می‌کند، Group Key و ترتیب Scope-before-Budget را بررسی کن.

### 6.5.2 Label ناهماهنگ یا هم‌پوشان

- Label انتهایی باید از Effective End Time استفاده کند، نه همیشه Time جاری؛
- برای MIDDLE، میانه Start و Effective End پس از هر Update دوباره محاسبه شود؛
- Label خاموش نباید VisualRef اصلی Line/Box یا Logical Object را حذف کند؛
- Collision باید ابتدا Offset/Stagger، سپس Anchor fallback و در پایان Label-only eviction را اجرا کند؛
- Label MTF باید Time coordinate و Source TF Tag داشته باشد و از Chart `bar_index` مشتق نشود؛
- هنگام Freeze یا Convert، Label و Drawing اصلی باید در یک Render transaction به Endpoint جدید منتقل شوند.

## 6.6 Array/Map/Cursor mismatch

نشانه‌ها:

- Object در Array هست ولی با ID پیدا نمی‌شود؛
- Map به Object حذف‌شده اشاره می‌کند؛
- Event دوباره پردازش می‌شود؛
- Cursor از اندازه Collection عبور می‌کند.

رفع: Creation و Cleanup را عملیات هماهنگ در نظر بگیر و پس از هر Shift، Map، Queue و تمام Cursorهای وابسته را اصلاح کن.

## 6.7 بیشتر MSS Candidateها Invalid هستند

احتمالاً Event تاریخی با Context جاری اعتبارسنجی شده است.

طبقه‌بندی صحیح:

- بدون Displacement = Pending؛
- جایگزین‌شده = Superseded؛
- Contract شکسته = Invalid با Reason؛
- Displacement معتبر = Confirmed.

پس از اصلاح Lifecycle، History از ابتدا محاسبه شود.

## 6.8 خاموش شدن Swing باعث حذف Structure می‌شود

Show flag در Detection، Storage یا Queue Publishing استفاده شده است. تمام مصرف‌های Show flag را Audit کن؛ محل مجاز آن فقط Renderer و Dashboard است.

## 6.9 Liquidity Degreeها یکجا نمایش داده می‌شوند

Renderer فیلتر Degree مستقل ندارد. BSL/SSL و EQH/EQL باید External/Internal/Micro filters خود را داشته باشند و از Swing Visibility استفاده نکنند.

## 6.10 Sweep اشتباه

اگر هر Wick-through Sweep شناخته می‌شود، Reclaim و Acceptance بررسی نشده‌اند. ابتدا SweepCandidate بساز و سپس ValidatedSweep یا AcceptedBeyond را تعیین کن.

## 6.11 Protected Level اشتباه

اگر Level از آخرین Pivot یا Liquidity گرفته شده، Origin Leg رویداد BOS را بررسی کن و Protected Level را از Swing ساختاری مبدأ آن Leg بساز.

## 6.12 FVG یا OB بیش‌ازحد

- برای FVG، Raw و Strict Validation را جدا کن؛
- برای OB، Displacement و Structure Linkage را شرط کن؛
- Duplicate، Overlap، Minimum Size و Age policy را بررسی کن.

## 6.13 MTF بعد از Reload تغییر می‌کند

- Developing و Confirmed را جدا کن؛
- Origin و Confirmation Time را بررسی کن؛
- HTF Confirmed policy را اعمال کن؛
- Logical ID را از Chart bar index نساز؛
- برای LTF، Summary را با تمام Intrabarها اشتباه نگیر.

## 6.14 Request یا Payload limit

- Registry مرکزی؛
- Deduplication؛
- Context Only؛
- TF slots کمتر؛
- Payload کوچک‌تر؛
- Intrabar فقط برای Feature ضروری.

## 6.15 Backtest غیرواقعی

بررسی کن:

- Future leak؛
- Developing HTF؛
- Same-bar stop/target؛
- Commission/Slippage؛
- Signal time و execution time؛
- Rule version؛
- Sample size و Overfitting.

## 6.16 Alert تکراری

Alert به Boolean چندBarه متصل شده است. منبع Alert باید Event Transition باشد و Dedup Key، Cooldown و once-per-transition guard داشته باشد.

---

# 7. مدیریت اجرای پروژه

## 7.1 قالب شروع هر فاز

پیش از پیاده‌سازی موارد زیر ثبت شوند:

- هدف؛
- خارج از دامنه؛
- پیش‌نیاز؛
- Type/Enum changes؛
- Inputs؛
- Detection rules؛
- State transitions؛
- Linkage و Event flow؛
- Performance budget؛
- Test cases؛
- Exit criteria؛
- Rollback point.

## 7.2 قالب پایان هر فاز

1. Compile؛
2. Save/Reload؛
3. Logic counters؛
4. Integrity check؛
5. Visual verification؛
6. Performance profiling؛
7. Regression؛
8. Documentation update.

## 7.3 Definition of Done نهایی

پروژه زمانی کامل است که:

- تمام مفاهیم دامنه Object یا Event استاندارد داشته باشند؛
- Lineage همه رویدادهای اصلی قابل‌ردیابی باشد؛
- هر Object معنادار در صورت درخواست قابل نمایش باشد؛
- Analysis از Visibility مستقل باشد؛
- TF و Degree اشتباه نشوند؛
- Sweep و Acceptance تفکیک شوند؛
- BOS، CHoCH و MSS تعریف و Lifecycle صحیح داشته باشند؛
- FVG و OB به Displacement و Structure متصل باشند؛
- Dealing Range جهت‌دار باشد؛
- HTF Object روی LTF با هویت ثابت بازسازی شود؛
- DOL قابل توضیح باشد؛
- Visual Manager تنها Drawing Layer باشد؛
- Dashboard فقط State را نمایش دهد؛
- Assistant Evidence و Reason ارائه کند؛
- Backtest بدون Future Leak و قابل بازتولید باشد؛
- Alertها تکراری نباشند؛
- Storage، Processing و Visual budgets کنترل شوند؛
- Compiled Tokens زیر آستانه Release و دارای Reserve باشند؛
- Refactorهای کاهش توکن بدون تغییر Counts، IDs، Stateها، Linkage، MTF، Drawing، Backtest و Alertها باشند؛
- تمام Release Gates پاس شوند.

---

**پایان سند مرجع ICT PRO**
