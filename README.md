# SubGame Lab 🎮🎬

**AI workspace for cinema subtitles and video game localization**  
**استودیوی هوش مصنوعی برای زیرنویس فیلم و بومی‌سازی بازی**  
**استوديو ذكاء اصطناعي لترجمة الأفلام وتوطين الألعاب**

[🇬🇧 English](#english) · [🇮🇷 فارسی](#persian) · [🇸🇦 العربية](#arabic)

[Live demo](https://subgame-lab-production-5e1a.up.railway.app/) · [GitHub](https://github.com/gguhfhu7-sketch/SubGame-Lab) · [Telegram](https://t.me/MySaeedLab)

---

<a id="english"></a>
## 🇬🇧 English

### What's new in this update?

**More provider choice, separate Cinema/Game workspaces, and improvements to translation reliability.**

- **Custom Provider / BYOK:** Gemini is no longer the only option for text translation. Connect an compatible text-generation API using your own Base URL, API Key, and Model ID. If Gemini is unavailable to you, select Custom Provider and continue with a compatible service you can access.
- **Expanded Gemini model catalog:** The application now lists additional Flash options alongside Pro and progressive streaming, with an in-app model guide. The current configured translation default is `gemini-3.8-flash`; see the model list below.
- **Separate Cinema and Game sessions:** Each mode has its own document, file format, translations, and settings. Switching modes saves the current workspace and restores the other, instead of showing a cinema file as a game document.
- **Browser autosave:** Mode sessions are saved locally using IndexedDB/Dexie and loaded again on startup. Autosave is not cloud synchronization or a substitute for exporting backups.
- **UI/UX improvements:** Adjusted header spacing, wrapping, and file-badge visibility to reduce overlap. Translation progress now displays the selected custom provider/model rather than substituting a Gemini label.
- **More robust Custom Provider requests:** Added a translation deadline, response-size limits, bounded concurrency, response validation, request/job identifiers, and timing diagnostics. Permanent errors such as invalid configuration or unauthorized keys no longer follow the same retry path as temporary failures.
- **Cancellation and stale-response checks:** Batch translation uses AbortController and job identity checks; the custom translation backend also responds to client disconnection.
- **File-processing fixes:** Improved SRT/VTT millisecond rounding, timestamp handling, WebVTT metadata/cue settings, ASS event parsing, MicroDVD FPS-header detection on import, nested JSON paths, and JSON target-field selection.
- **Stronger game-variable checks:** The editor checks missing and extra occurrences of recognized placeholders, including repeated variables, instead of checking presence alone.
- **Provider-aware quality review:** Quality auditing supports Custom Provider as well as Gemini and processes the input in smaller groups.

These are changes reflected in the supplied source, not a claim that every issue is fixed or every service/device has been tested. Model availability and performance depend on the provider, account, network, and workload.

### What can you do with SubGame Lab?

**Cinema Mode**

Import `.srt`, `.vtt`, `.ass`, `.ssa`, or `.sub` subtitles; edit source text, translations, and timecodes; translate in batches; create bilingual subtitle output; and preview subtitles over a local video. Gemini-based audio transcription can generate SRT text from media that your browser can decode. File-container and codec compatibility varies by browser.

**Game Mode**

Import `.csv`, `.json`, `.xlsx`, or `.txt` localization files; map source, target, key, and context columns where supported; translate dialogue and UI strings; inspect placeholder warnings; and export supported formats. Review the output against your game's expected schema before shipping, especially for complex workbooks and nested data.

**Shared tools**

Multiple target languages, localization tones, custom instructions, adjustable batch sizes and pacing, multi-key Gemini configuration, search/filtering, find-and-replace, single-line retranslation, virtualized lists, and Persian/Arabic/English interface text. Add terminology rules to the custom prompt when you need consistent character names or vocabulary; model compliance still needs review.

### Using a Custom Provider instead of Gemini

1. Open **API Key** settings and select **Custom Provider**.
2. Enter an optional provider name.
3. Enter the **Base API URL**, including the API version/path supplied by your provider. For example: `https://api.example.com/v1`.
4. Paste your **API Key** and the exact **Model ID** supported by that service.
5. Click **Test Connection**. Resolve any URL, authentication, model, or quota error before translating.
6. Choose a file, target language, and tone, then start translation.

The application uses `POST /chat/completions`, a `messages` array, and `Authorization: Bearer ...`. The model must be able to return the requested JSON translation structure. The endpoint suffix is appended automatically when the supplied URL does not already end in `/chat/completions`.

**Compatibility, not a provider lock-in:** compatible text-generation API from services such as OpenRouter, Groq, DeepSeek, Together, or another API supplier may be usable when they meet this contract. These names are examples, not a tested compatibility matrix. An arbitrary API key alone is not sufficient: the URL, authentication scheme, endpoint, and model must match.

**Important limits:**

- No Gemini key is required for text translation or quality review through a configured Custom Provider. Gemini audio transcription and the Gemini streaming path are separate features; Custom Provider does not automatically replace them.
- Standard Custom Provider translation is batch-based. Selecting the app's Gemini Live Stream option with a custom provider falls back to standard custom translation.
- This version blocks local/private/metadata destinations in Custom Provider validation. A localhost Ollama or LM Studio URL will not work unchanged, even when its API is compatible text-generation API. Do not disable network protections on a public deployment just to connect it.
- Use the direct API endpoint, preferably HTTPS. Translation requests reject redirects. Avoid query-string-based URLs in this version and follow the provider's documented base-path format.
- Connection testing checks basic reachability/authentication/model response; it does not prove that the model will produce valid JSON for every translation batch.
- A different provider is an alternative access route, not a guarantee of free usage, unlimited quota, or exemption from that provider's policies.

### Gemini options configured in the project

| Entry in the app | Configured identifier |
| --- | --- |
| Gemini 3.8 Flash, translation default | `gemini-3.8-flash` |
| Gemini 3.7 Flash | `gemini-3.7-flash` |
| Gemini 3.6 Flash | `gemini-3.6-flash` |
| Gemini 3.5 Flash | `gemini-3.5-flash` |
| Gemini 3.1 Flash Lite | `gemini-3.1-flash-lite` |
| Gemini 3.1 Pro Preview | `gemini-3.1-pro-preview` |
| Gemini Live Stream, application streaming mode | `gemini-live-stream` |
| Audio transcription configuration | `gemini-3.5-transcribe` |

Source of truth: [`src/modelRegistry.ts`](src/modelRegistry.ts). These are identifiers configured in this repository, **not confirmation that Google currently exposes every identifier to every account**. `gemini-live-stream` is an application mode, not a standalone Google API model ID. Verify actual availability with your provider before deployment; update the registry if needed. Fallback attempts do not guarantee uninterrupted service or bypass shared quotas.

### Local setup

Use a current Node.js 22 LTS release and npm.

```bash
git clone https://github.com/gguhfhu7-sketch/SubGame-Lab.git
cd SubGame-Lab
npm install
npm run dev
```

Open `http://localhost:3000`. Configure Gemini or Custom Provider in the app. For an optional server-side Gemini key, create `.env`:

```dotenv
GEMINI_API_KEY=your_gemini_api_key
PORT=3000
```

The current server calls `dotenv.config()`, so `.env` is the default file; `.env.local` is not explicitly loaded by that call. Never commit real credentials.

```bash
npm run lint    # TypeScript checking
npm run build   # Frontend + server bundle
npm start       # Production server
```

### Deploy the online version (Railway)

This repository ships **Railway-ready configuration**: a `railway.json` file at the project root tells Railway exactly how to build, start, and verify the app — no manual dashboard tweaks are required. The server already listens on the platform-injected `PORT` variable and binds `0.0.0.0`, serves the built frontend with an SPA fallback, exposes `/api/health` for the deployment healthcheck, and performs a graceful shutdown on `SIGTERM`/`SIGINT` so redeploys do not appear as crashes.

**Deploy on Railway in five steps:**

1. Fork the SubGame Lab repository to your GitHub account.
2. Go to [railway.com/new](https://railway.com/new), choose **Deploy from GitHub repo**, and pick your fork. Railway builds with Railpack: it detects Node, installs dependencies, and runs `npm run build` (from `railway.json`).
3. Open the service's **Variables** tab and add the optional variables listed below. `PORT` is injected by Railway automatically — do not set it manually, and there is no fixed port such as 8080 to configure.
4. Deploy. Railway starts the app with `node dist/server.cjs` (a direct Node start, so the process receives `SIGTERM` and shuts down gracefully), then waits for `/api/health` to return a 2xx response before switching traffic.
5. In **Settings → Networking → Public Networking**, click **Generate Domain** to get your `*.railway.app` URL, then open it and configure Gemini or Custom Provider inside the app.

**Optional service variables:**

| Variable | Purpose |
| -------- | ------- |
| `GEMINI_API_KEY` | Server-side Gemini key used when users do not provide their own. |
| `ALLOW_SERVER_KEY` | Set to `true` to allow requests that rely on the server key. |
| `ALLOWED_ORIGIN` | Comma-separated list of origins allowed to call the API (e.g. your `*.railway.app` domain). Leave unset to keep the open default. |
| `AI_RATE_LIMIT` | Per-IP requests-per-minute cap on AI endpoints (default `30`, `0` disables). |
| `GEMINI_BASE_URL` | Optional proxy/base URL for Gemini traffic. |

Healthcheck path (`/api/health`) and the start command are defined in `railway.json`, so Railway can verify every new deployment before going live. To redeploy, just push to your fork; to change build/start behavior, edit `railway.json` or the service settings.

### Deploy on Render (alternative)

The repository is equally **Render-ready**: a `render.yaml` Blueprint at the project root defines the web service, so you can deploy without filling any dashboard fields manually. The server binds `0.0.0.0:$PORT` (Render injects `PORT`, default `10000`), serves the built frontend with an SPA fallback, exposes `/api/health` (2xx within the 5-second health-check window), and performs a graceful shutdown on `SIGTERM` — far inside Render's 30-second shutdown delay — so redeploys are zero-downtime instead of crashes.

**Deploy on Render in five steps:**

1. Fork the SubGame Lab repository to your GitHub account.
2. In the Render Dashboard click **New → Blueprint**, connect your fork, and Render reads `render.yaml` automatically: `npm ci && npm run build` as the build command and `node dist/server.cjs` as the start command (a direct Node start, so `SIGTERM` reaches the app).
3. When applying the Blueprint, Render prompts you for the secret variables marked `sync: false` (`GEMINI_API_KEY`, `ALLOW_SERVER_KEY`, `ALLOWED_ORIGIN`, `GEMINI_BASE_URL`). `PORT` is injected by Render — never set it manually.
4. Apply. Render waits for `/api/health` to return 2xx before routing traffic, then keeps checking every few seconds and auto-restarts unresponsive instances.
5. Open the generated `*.onrender.com` URL and configure Gemini or Custom Provider inside the app.

The Node.js version is pinned to `22.14.0` via the `NODE_VERSION` variable and the `.node-version` file. Note that on the Render **free plan** the service sleeps after ~15 minutes of inactivity and the first request afterwards takes a little longer while it wakes up; upgrade the plan in the dashboard to avoid cold starts.

**Troubleshooting — blank (white) page after deploy:** a white page means the browser got an `index.html` that is not the production build. The server now protects you from the two classic causes: (1) the **Build Command** never ran `npm run build`, so `dist/` is missing — the server then returns a clear **503 guide page** (in Persian and English) on web routes while `/api/health` stays green, instead of silently serving the repository; and (2) misconfigured commands that run the app from TypeScript source. Verify these exact dashboard values and redeploy:

| Field | Required value |
|---|---|
| Build Command | `npm ci && npm run build` |
| Start Command | `node dist/server.cjs` |
| Health Check Path | `/api/health` |

Never set `NODE_ENV` manually, and never use `npm run dev` / `tsx server.ts` as a Start Command — those are local-development only. You can also check the `/api/health` response: `"static":"build"` means the built frontend is being served, `"static":"missing"` means the build output is absent. As an extra hardening, the server no longer serves the repository root, so source files such as `server.ts` or `package-lock.json` can never leak through the web server.

### Network access and privacy

API calls are sent by the application server. For a hosted deployment, the server must reach the selected provider; for local development, your local Node.js process must reach it. Proxy/VPN requirements depend on routing and provider restrictions: the hosted demo does not guarantee access in every region. Use services available to you under their terms; multiple keys do not remove provider quotas.

BYOK credentials are stored in browser localStorage and sent to the application backend for provider requests. Workspace sessions are stored in IndexedDB. Use a trusted deployment, avoid saving keys on shared devices, and export important work regularly. Clearing browser site data can remove saved sessions and keys. Before public deployment, review authentication, rate/cost limits, network-egress controls, and privacy requirements; the included safeguards are not a complete security assessment.

**Current stack:** React 19, TypeScript, Vite 6, Tailwind CSS 4, Express, `@google/genai`, TanStack Virtual, Dexie/IndexedDB, ExcelJS, PapaParse, and jschardet. Rendering performance depends on the document and device; no fixed 50,000-row/60-FPS guarantee is claimed.

---

<a id="persian"></a>
## 🇮🇷 فارسی

### در این به‌روزرسانی چه تغییر کرده؟

**انتخاب آزادتر سرویس هوش مصنوعی، محیط مستقل سینما و بازی، و بهبود پایداری ترجمه.**

- **اضافه‌شدن Custom Provider و کلید شخصی (BYOK):** ترجمهٔ متن دیگر فقط به Gemini محدود نیست. با واردکردن آدرس پایه، کلید API و شناسهٔ مدل می‌توانید از سرویس‌های سازگار با compatible text-generation API استفاده کنید. اگر به Gemini دسترسی ندارید، Custom Provider را انتخاب کنید و ترجمه را با سرویس سازگارِ در دسترس خود ادامه دهید.
- **گسترش فهرست مدل‌های Gemini:** گزینه‌های بیشتر Flash در کنار Pro و ترجمهٔ جریانی، همراه با راهنمای مدل‌ها در برنامه قرار گرفته‌اند. پیش‌فرض ترجمه در کد فعلی `gemini-3.8-flash` است؛ فهرست کامل پایین آمده است.
- **استقلال Cinema Mode و Game Mode:** هر مود فایل، فرمت، ترجمه‌ها و تنظیمات خودش را دارد. هنگام جابه‌جایی، وضعیت مود فعلی ذخیره و وضعیت مود مقصد بازیابی می‌شود؛ فایل سینما دیگر صرفاً با تعویض مود به فایل بازی تغییر هویت نمی‌دهد.
- **ذخیرهٔ خودکار محلی:** وضعیت هر مود با IndexedDB و Dexie در مرورگر ذخیره و هنگام بازشدن برنامه بازیابی می‌شود. این قابلیت همگام‌سازی ابری نیست و جای نسخهٔ پشتیبان خروجی را نمی‌گیرد.
- **بهبود رابط و تجربهٔ کاربری:** فاصله‌گذاری هدر، شکستن ردیف کنترل‌ها و نمایش نشان فایل برای کاهش تداخل عناصر تنظیم شده‌اند. نوار پیشرفت نیز نام واقعی سرویس‌دهنده و مدل سفارشی را به‌جای برچسب اشتباه Gemini نمایش می‌دهد.
- **بهبود مدیریت درخواست‌های Custom Provider:** محدودیت زمانی ترجمه، سقف اندازهٔ پاسخ، کنترل درخواست‌های هم‌زمان، اعتبارسنجی پاسخ، شناسهٔ درخواست/عملیات و ثبت زمان مراحل اضافه شده‌اند. خطاهای دائمی مانند تنظیمات نامعتبر یا کلید غیرمجاز، دیگر مثل خطای موقت وارد همان مسیر تکرار نمی‌شوند.
- **کنترل لغو و پاسخ‌های قدیمی:** ترجمهٔ دسته‌ای از AbortController و شناسهٔ عملیات برای کنترل پاسخ‌ها استفاده می‌کند؛ بک‌اند ترجمهٔ سفارشی هم به قطع اتصال کاربر واکنش نشان می‌دهد.
- **اصلاح پردازش فایل‌ها:** گردکردن میلی‌ثانیه در SRT/VTT، مدیریت تایم‌کد، اطلاعات و تنظیمات cue در WebVTT، خواندن رویدادهای ASS، تشخیص هدر FPS در ورودی MicroDVD، مسیرهای تو‌در‌توی JSON و انتخاب فیلد مقصد JSON بهبود یافته‌اند.
- **کنترل دقیق‌تر متغیرهای بازی:** ادیتور تعداد تکرار متغیرهای شناخته‌شده را هم بررسی می‌کند و برای موارد حذف‌شده یا اضافه‌شده هشدار می‌دهد؛ صرفاً وجود یک نمونه از متغیر کافی نیست.
- **بررسی کیفیت با ارائه‌دهندهٔ انتخابی:** ارزیابی کیفیت علاوه بر Gemini از Custom Provider پشتیبانی می‌کند و ورودی را به گروه‌های کوچک‌تر تقسیم می‌کند.

این موارد تغییرات موجود در سورس ارسالی‌اند، نه ادعای رفع تمام باگ‌ها یا تست همهٔ دستگاه‌ها و سرویس‌ها. دسترسی به مدل و سرعت واقعی به ارائه‌دهنده، حساب، شبکه و حجم کار وابسته است.

### امکانات اصلی

**مود سینما**

ورود زیرنویس‌های `.srt`، `.vtt`، `.ass`، `.ssa` و `.sub`؛ ویرایش متن اصلی، ترجمه و زمان‌ها؛ ترجمهٔ دسته‌ای؛ ساخت خروجی دوزبانه و پیش‌نمایش زیرنویس روی ویدیوی محلی. استخراج صوت با Gemini می‌تواند از فایل‌هایی که مرورگر قادر به رمزگشایی آنهاست زیرنویس SRT تولید کند؛ پشتیبانی از ظرف فایل و کدک در مرورگرها یکسان نیست.

**مود بازی**

ورود فایل‌های `.csv`، `.json`، `.xlsx` و `.txt`؛ نگاشت ستون‌های مبدأ، مقصد، شناسه و زمینه در موارد پشتیبانی‌شده؛ ترجمهٔ دیالوگ و متن رابط بازی؛ نمایش هشدار متغیرها و خروجی در فرمت‌های پشتیبانی‌شده. قبل از استفاده در بازی، خروجی را با ساختار موردانتظار موتور بازی تطبیق دهید، مخصوصاً برای اکسل پیچیده و داده‌های تو‌در‌تو.

**ابزارهای مشترک**

زبان‌های مقصد متعدد، لحن ترجمه، دستورالعمل سفارشی، اندازهٔ دسته و فاصلهٔ درخواست قابل‌تنظیم، چند کلید Gemini، جستجو و فیلتر، یافتن و جایگزینی، ترجمهٔ دوبارهٔ تک‌سطر، فهرست مجازی‌سازی‌شده و رابط فارسی، عربی و انگلیسی. برای نام شخصیت‌ها و واژگان ثابت، قواعد اصطلاحات را در پرامپت سفارشی وارد کنید؛ رعایت آنها توسط مدل همچنان نیازمند بازبینی است.

### اگر Gemini در دسترس نبود، چطور از Custom Provider استفاده کنیم؟

1. بخش **کلید API** را باز کنید و **Custom Provider / سرویس‌دهنده سفارشی** را انتخاب کنید.
2. در صورت تمایل نامی برای سرویس‌دهنده بنویسید.
3. **Base API URL** را دقیقاً مطابق مستندات سرویس وارد کنید؛ مسیر نسخهٔ API هم باید درست باشد. نمونه: `https://api.example.com/v1`.
4. **API Key** و **Model ID** واقعی همان سرویس را وارد کنید.
5. **Test Connection / تست اتصال** را بزنید و خطاهای آدرس، احراز هویت، مدل یا سهمیه را برطرف کنید.
6. فایل، زبان مقصد و لحن را انتخاب کنید و ترجمه را شروع کنید.

برنامه از `POST /chat/completions`، آرایهٔ `messages` و احراز هویت `Authorization: Bearer ...` استفاده می‌کند. مدل باید بتواند ساختار JSON درخواست‌شده برای ترجمه را برگرداند. اگر آدرس به `/chat/completions` ختم نشود، این پسوند خودکار اضافه می‌شود.

**محدود به یک فروشنده نیستید:** APIهای سازگار از سرویس‌هایی مثل OpenRouter، Groq، DeepSeek، Together یا فروشندهٔ دیگر، در صورت رعایت این قرارداد قابل استفاده‌اند. این نام‌ها نمونه‌اند، نه فهرست سرویس‌های تست‌شده. «کلید از هرجا» به‌تنهایی کافی نیست؛ آدرس، روش احراز هویت، endpoint و مدل باید با برنامه سازگار باشند.

**نکات مهم:**

- برای ترجمهٔ متن و بررسی کیفیت با Custom Provider تنظیم‌شده، به کلید Gemini نیاز ندارید. استخراج صوت و مسیر پخش جریانی Gemini قابلیت‌های جداگانه‌اند و خودکار به ارائه‌دهندهٔ سفارشی منتقل نمی‌شوند.
- ترجمهٔ استاندارد Custom Provider دسته‌ای است؛ انتخاب Gemini Live Stream همراه با ارائه‌دهندهٔ سفارشی به ترجمهٔ استاندارد سفارشی برمی‌گردد.
- نسخهٔ فعلی آدرس‌های localhost، شبکهٔ خصوصی و سرویس‌های metadata را در اعتبارسنجی مسدود می‌کند. بنابراین Ollama یا LM Studio محلی با همان آدرس localhost کار نمی‌کند، حتی اگر API سازگار داشته باشد. برای اتصال آن، محافظت شبکهٔ نسخهٔ عمومی را بی‌حساب غیرفعال نکنید.
- از آدرس مستقیم API، ترجیحاً HTTPS، استفاده کنید. مسیر ترجمه redirect را قبول نمی‌کند. در این نسخه از آدرس‌های وابسته به query string پرهیز و قالب مسیر پایهٔ اعلام‌شده توسط سرویس را رعایت کنید.
- موفقیت تست اتصال، پاسخ‌گویی اولیهٔ سرویس و مدل را می‌سنجد؛ تضمین نمی‌کند مدل برای هر دسته ترجمه JSON صحیح تولید کند.
- ارائه‌دهندهٔ جایگزین به معنی استفادهٔ رایگان، سهمیهٔ نامحدود یا حذف قوانین آن سرویس نیست.

### مدل‌ها و گزینه‌های Gemini ثبت‌شده در پروژه

| گزینهٔ برنامه | شناسهٔ تنظیم‌شده |
| --- | --- |
| Gemini 3.8 Flash، پیش‌فرض ترجمه | `gemini-3.8-flash` |
| Gemini 3.7 Flash | `gemini-3.7-flash` |
| Gemini 3.6 Flash | `gemini-3.6-flash` |
| Gemini 3.5 Flash | `gemini-3.5-flash` |
| Gemini 3.1 Flash Lite | `gemini-3.1-flash-lite` |
| Gemini 3.1 Pro Preview | `gemini-3.1-pro-preview` |
| Gemini Live Stream، حالت جریانی برنامه | `gemini-live-stream` |
| تنظیم مدل استخراج صوت | `gemini-3.5-transcribe` |

مرجع فهرست: [`src/modelRegistry.ts`](src/modelRegistry.ts). اینها **شناسه‌های ثبت‌شده در سورس‌اند، نه تأیید عرضهٔ فعلی تمام مدل‌ها از طرف گوگل یا دسترسی همهٔ حساب‌ها**. `gemini-live-stream` نام یک حالت داخلی برنامه است، نه شناسهٔ مستقل مدل API گوگل. پیش از استقرار، دسترس‌پذیری واقعی را بررسی و در صورت نیاز registry را اصلاح کنید. Fallback تلاش برای جایگزینی مدل است و ادامهٔ بی‌وقفه یا عبور از سهمیهٔ مشترک را تضمین نمی‌کند.

### راه‌اندازی محلی

از نسخهٔ به‌روز Node.js 22 LTS و npm استفاده کنید:

```bash
git clone https://github.com/gguhfhu7-sketch/SubGame-Lab.git
cd SubGame-Lab
npm install
npm run dev
```

برنامه را در `http://localhost:3000` باز کنید و Gemini یا Custom Provider را از رابط تنظیم کنید. برای کلید اختیاری Gemini در سمت سرور، فایل `.env` بسازید:

```dotenv
GEMINI_API_KEY=your_gemini_api_key
PORT=3000
```

سرور فعلی از `dotenv.config()` استفاده می‌کند؛ فایل پیش‌فرض `.env` است و این فراخوانی `.env.local` را صریحاً بارگذاری نمی‌کند. کلید واقعی را داخل مخزن قرار ندهید.

```bash
npm run lint
npm run build
npm start
```

دستور اول بررسی TypeScript، دستور دوم ساخت فرانت‌اند و سرور، و دستور سوم اجرای نسخهٔ ساخته‌شده است.

### استقرار نسخهٔ آنلاین (Railway)

این مخزن از قبل برای Railway آماده شده است: فایل `railway.json` در ریشهٔ پروژه به Railway می‌گوید پروژه را چطور build کند، چطور اجرا کند و سلامت آن را چطور بررسی کند — بدون هیچ تنظیم دستی در داشبورد. سرور از `PORT` تزریق‌شدهٔ پلتفرم استفاده می‌کند و روی `0.0.0.0` گوش می‌دهد، فرانت‌اند build‌شده را با SPA fallback ارائه می‌دهد، مسیر `/api/health` را برای healthcheck در اختیار می‌گذارد و هنگام `SIGTERM`/`SIGINT` خاموشی تمیز انجام می‌دهد تا ری‌دپلوی‌ها به‌صورت «Crashed» دیده نشوند.

**استقرار روی Railway در پنج گام:**

1. مخزن SubGame Lab را در GitHub خود fork کنید.
2. به [railway.com/new](https://railway.com/new) بروید، گزینهٔ **Deploy from GitHub repo** را انتخاب و fork را انتخاب کنید. Railway با Railpack می‌سازد: Node را تشخیص می‌دهد، وابستگی‌ها را نصب و `npm run build` را اجرا می‌کند (مطابق `railway.json`).
3. در تب **Variables** سرویس، متغیرهای اختیاری جدول زیر را اضافه کنید. `PORT` به‌صورت خودکار توسط Railway تزریق می‌شود — آن را دستی ست نکنید و پورت ثابتی مثل 8080 هم برای تنظیم وجود ندارد.
4. Deploy کنید. Railway برنامه را با `node dist/server.cjs` اجرا می‌کند (اجرای مستقیم Node تا سیگنال `SIGTERM` به خود برنامه برسد و خاموشی تمیز انجام شود)، سپس منتظر می‌ماند `/api/health` پاسخ 2xx بدهد و بعد ترافیک را سوئیچ می‌کند.
5. در **Settings → Networking → Public Networking** روی **Generate Domain** بزنید تا آدرس `*.railway.app` ساخته شود، آن را باز کنید و Gemini یا Custom Provider را از داخل برنامه تنظیم کنید.

**متغیرهای اختیاری سرویس:**

| متغیر | کاربرد |
| ----- | ------ |
| `GEMINI_API_KEY` | کلید سمت سرور برای وقتی که کاربر کلید خودش ندارد. |
| `ALLOW_SERVER_KEY` | برای اجازهٔ استفاده از کلید سرور مقدار `true` بگذارید. |
| `ALLOWED_ORIGIN` | لیست اورجین‌های مجاز برای فراخوانی API (مثلاً دامنهٔ `*.railway.app` شما)؛ جداکننده ویرگول. اگر خالی بماند رفتار باز قبلی حفظ می‌شود. |
| `AI_RATE_LIMIT` | سقف درخواست در دقیقه به‌ازای هر IP روی endpointهای AI (پیش‌فرض `30`، با `0` غیرفعال می‌شود). |
| `GEMINI_BASE_URL` | پراکسی/URL پایهٔ اختیاری برای ترافیک Gemini. |

مسیر healthcheck (`/api/health`) و دستور اجرا در `railway.json` تعریف شده‌اند تا Railway هر استقرار جدید را قبل از رفتن روی ترافیک تأیید کند. برای ری‌دپلوی کافی است به fork خود push کنید؛ برای تغییر رفتار build/start فایل `railway.json` یا تنظیمات سرویس را ویرایش کنید.

### استقرار روی Render (جایگزین)

این مخزن برای Render هم آماده است: فایل Blueprint با نام `render.yaml` در ریشهٔ پروژه، سرویس وب را کامل تعریف می‌کند تا بدون پرکردن دستی فیلدهای داشبورد دیپلوی شود. سرور روی `0.0.0.0:$PORT` گوش می‌دهد (Render مقدار `PORT` را تزریق می‌کند؛ پیش‌فرض `10000`)، فرانت‌اند build‌شده را با SPA fallback ارائه می‌دهد، مسیر `/api/health` را در اختیار می‌گذارد (پاسخ 2xx در پنجرهٔ ۵ثانیه‌ای healthcheck) و هنگام `SIGTERM` خاموشی تمیز انجام می‌دهد — خیلی داخل مهلت ۳۰ثانیه‌ای Render — بنابراین ری‌دپلوی‌ها بدون قطعی انجام می‌شوند نه با کرش.

**استقرار روی Render در پنج گام:**

1. مخزن SubGame Lab را در GitHub خود fork کنید.
2. در داشبورد Render روی **New → Blueprint** بزنید و fork را متصل کنید؛ Render فایل `render.yaml` را خودش می‌خواند: دستور build ‏`npm ci && npm run build` و دستور اجرا ‏`node dist/server.cjs` (اجرای مستقیم Node تا سیگنال `SIGTERM` به برنامه برسد).
3. هنگام اعمال Blueprint، Render برای متغیرهای محرمانهٔ علامت‌خورده با `sync: false` (`GEMINI_API_KEY`، `ALLOW_SERVER_KEY`، `ALLOWED_ORIGIN`، `GEMINI_BASE_URL`) مقدار می‌پرسد. `PORT` توسط Render تزریق می‌شود — هرگز دستی ست نکنید.
4. Apply کنید. Render صبر می‌کند `/api/health` پاسخ 2xx بدهد و بعد ترافیک را می‌فرستد؛ سپس هر چند ثانیه چک می‌کند و نمونهٔ بی‌پاسخ را خودکار ری‌استارت می‌کند.
5. آدرس `*.onrender.com` ساخته‌شده را باز کنید و Gemini یا Custom Provider را از داخل برنامه تنظیم کنید.

نسخهٔ Node.js با متغیر `NODE_VERSION` و فایل `.node-version` روی `22.14.0` پین شده است. توجه: در پلن رایگان Render سرویس بعد از حدود ۱۵ دقیقه بی‌کاری به خواب می‌رود و اولین درخواست بعد از آن کمی کندتر است؛ برای حذف cold start پلن را در داشبورد ارتقا دهید.

**عیب‌یابی — صفحهٔ سفید بعد از دیپلوی:** صفحهٔ سفید یعنی مرورگر یک `index.html` غیر از بیلد پروداکشن گرفته است. سرور اکنون در برابر دو علتِ کلاسیک محافظت می‌شود: (۱) دستور **Build** هیچ‌وقت `npm run build` را اجرا نکرده و پوشهٔ `dist` وجود ندارد — در این حالت سرور روی مسیرهای وب یک **صفحهٔ راهنمای ۵۰۳** (فارسی/انگلیسی) برمی‌گرداند و `/api/health` سبز می‌ماند، به‌جای آن‌که بی‌صدا ریشهٔ مخزن را سرو کند؛ و (۲) دستورهای اشتباهی که برنامه را از سورس TypeScript اجرا می‌کنند. این مقادیر دقیق را در داشبورد بررسی و دوباره Deploy کنید:

| فیلد | مقدار الزامی |
|---|---|
| Build Command | `npm ci && npm run build` |
| Start Command | `node dist/server.cjs` |
| Health Check Path | `/api/health` |

هرگز `NODE_ENV` را دستی تنظیم نکنید و هرگز `npm run dev` یا `tsx server.ts` را به‌عنوان Start Command استفاده نکنید — این‌ها فقط برای توسعهٔ محلی‌اند. پاسخ `/api/health` هم راهنمای تشخیص است: `"static":"build"` یعنی فرانت‌اند بیلدشده سرو می‌شود و `"static":"missing"` یعنی خروجی بیلد روی سرور نیست. به‌عنوان سخت‌سازی امنیتی، سرور دیگر ریشهٔ مخزن را سرو نمی‌کند؛ بنابراین فایل‌هایی مثل `server.ts` یا `package-lock.json` هرگز از طریق وب لو نمی‌روند.

### شبکه و حریم خصوصی

درخواست‌های API از سمت سرور برنامه ارسال می‌شوند. در نسخهٔ میزبانی‌شده، سرور باید به سرویس انتخابی دسترسی داشته باشد؛ در نسخهٔ محلی، Node.js روی سیستم شما باید به آن سرویس برسد. نیاز به پراکسی یا ابزار مسیریابی به محدودیت سرویس و شبکه بستگی دارد؛ دمو آنلاین دسترسی در همهٔ مناطق را تضمین نمی‌کند. از سرویس‌ها مطابق شرایط استفادهٔ خودشان بهره ببرید؛ چند کلید سهمیهٔ ارائه‌دهنده را حذف نمی‌کند.

کلیدهای BYOK در localStorage مرورگر ذخیره و برای درخواست‌ها به بک‌اند برنامه ارسال می‌شوند. نشست‌ها در IndexedDB نگه‌داری می‌شوند. از نسخهٔ میزبانی‌شدهٔ مورداعتماد استفاده کنید، روی دستگاه مشترک کلید ذخیره نکنید و مرتب خروجی پشتیبان بگیرید. پاک‌کردن داده‌های سایت می‌تواند نشست‌ها و کلیدها را حذف کند. پیش از انتشار عمومی، احراز هویت، سقف هزینه و درخواست، کنترل دسترسی خروجی شبکه و الزامات حریم خصوصی را بررسی کنید؛ حفاظت‌های فعلی جای ممیزی امنیتی کامل را نمی‌گیرند.

**فناوری‌های فعلی:** React 19، TypeScript، Vite 6، Tailwind CSS 4، Express، `@google/genai`، TanStack Virtual، Dexie/IndexedDB، ExcelJS، PapaParse و jschardet. کارایی به فایل و دستگاه وابسته است و تضمین ثابت ۵۰هزار ردیف با ۶۰ فریم ارائه نمی‌شود.

---

<a id="arabic"></a>
## 🇸🇦 العربية

### ما الجديد في هذا التحديث؟

**خيارات أوسع لمزودي الذكاء الاصطناعي، ومساحات عمل منفصلة للسينما والألعاب، وتحسينات في موثوقية الترجمة.**

- **إضافة Custom Provider وBYOK:** لم تعد ترجمة النصوص مقتصرة على Gemini. أدخل عنوان API الأساسي ومفتاحك ومعرّف النموذج للاتصال بخدمة متوافقة مع compatible text-generation API. إذا تعذر الوصول إلى Gemini، اختر مزوداً مخصصاً متوافقاً ومتاحاً لك.
- **توسيع قائمة Gemini:** تتضمن الواجهة خيارات Flash إضافية إلى جانب Pro والترجمة المتدفقة، مع دليل داخل التطبيق. المعرّف الافتراضي للترجمة في الكود الحالي هو `gemini-3.8-flash`.
- **فصل جلسات السينما والألعاب:** يحتفظ كل وضع بملفه وتنسيقه وترجماته وإعداداته. التبديل يحفظ مساحة العمل الحالية ويستعيد الأخرى، بدلاً من إظهار ملف سينمائي وكأنه ملف لعبة.
- **حفظ محلي تلقائي:** تُحفظ الجلسات في المتصفح عبر IndexedDB وDexie وتُحمّل عند بدء التطبيق. هذه ليست مزامنة سحابية ولا تغني عن تصدير نسخ احتياطية.
- **تحسينات UI/UX:** تعديل المسافات والتفاف عناصر الترويسة وظهور شارة الملف لتقليل التداخل. يعرض مؤشر التقدم اسم المزود والنموذج المخصص بدلاً من استبداله باسم Gemini.
- **طلبات Custom Provider أكثر متانة:** إضافة مهلة للترجمة وحدود لحجم الرد وتزامن الطلبات والتحقق من المخرجات ومعرّفات الطلبات والعمليات وقياسات التوقيت. تُميّز الأخطاء الدائمة، مثل الإعدادات الخاطئة والمفاتيح غير المصرح بها، عن الأعطال المؤقتة عند إعادة المحاولة.
- **الإلغاء والتحقق من الردود القديمة:** تستخدم ترجمة الدفعات AbortController ومعرّف العملية؛ كما يستجيب خادم الترجمة المخصصة لانقطاع اتصال العميل.
- **إصلاحات معالجة الملفات:** تحسين تقريب أجزاء الثانية في SRT/VTT، ومعالجة التوقيت، وبيانات WebVTT وإعدادات المقاطع، وقراءة أحداث ASS، واكتشاف ترويسة FPS في ملفات MicroDVD عند الاستيراد، ومسارات JSON المتداخلة واختيار حقل الترجمة المستهدف.
- **فحص أدق لمتغيرات الألعاب:** يقارن المحرر عدد مرات ظهور المتغيرات المعروفة ويعرض المفقود والزائد، بما في ذلك المتغيرات المتكررة.
- **مراجعة جودة حسب المزود:** تدعم المراجعة Custom Provider وGemini، وتعالج المحتوى في مجموعات أصغر.

تعكس هذه النقاط التغييرات الموجودة في المصدر المرفق، ولا تعني إصلاح جميع المشكلات أو اختبار جميع الأجهزة والخدمات. يعتمد توفر النماذج والأداء على المزود والحساب والشبكة وحجم العمل.

### القدرات الأساسية

**وضع السينما:** استيراد `.srt` و`.vtt` و`.ass` و`.ssa` و`.sub`؛ تحرير النص الأصلي والترجمة والتوقيت؛ الترجمة على دفعات؛ تصدير ترجمة ثنائية اللغة؛ ومعاينتها فوق فيديو محلي. يمكن لتفريغ الصوت عبر Gemini إنتاج SRT من الوسائط التي يستطيع المتصفح فك ترميزها؛ يختلف دعم الحاويات والترميزات بين المتصفحات.

**وضع الألعاب:** استيراد `.csv` و`.json` و`.xlsx` و`.txt`؛ تعيين أعمدة المصدر والهدف والمفتاح والسياق حيثما كان ذلك مدعوماً؛ ترجمة الحوارات ونصوص الواجهة؛ فحص تحذيرات المتغيرات؛ وتصدير التنسيقات المدعومة. راجع تطابق المخرجات مع مخطط لعبتك، خصوصاً للمصنفات المعقدة والبيانات المتداخلة.

**أدوات مشتركة:** لغات مستهدفة متعددة، نبرات ترجمة وتعليمات مخصصة، حجم دفعة وفواصل طلبات قابلة للتعديل، مفاتيح Gemini متعددة، بحث وتصفية واستبدال، إعادة ترجمة السطر، قوائم افتراضية، وواجهة عربية وفارسية وإنجليزية. ضع قواعد المصطلحات في التعليمات المخصصة عند الحاجة إلى توحيد الأسماء؛ تبقى مراجعة التزام النموذج ضرورية.

### استخدام Custom Provider بدلاً من Gemini

1. افتح إعدادات **API Key** واختر **Custom Provider**.
2. أدخل اسماً اختيارياً للمزود.
3. أدخل **Base API URL** مع مسار إصدار API وفق وثائق المزود، مثل `https://api.example.com/v1`.
4. أدخل **API Key** و**Model ID** الصحيحين للخدمة نفسها.
5. اضغط **Test Connection** وعالج أخطاء العنوان أو المصادقة أو النموذج أو الحصة.
6. اختر الملف واللغة والنبرة وابدأ الترجمة.

يستخدم التطبيق `POST /chat/completions` ومصفوفة `messages` وترويسة `Authorization: Bearer ...`. يجب أن يستطيع النموذج إرجاع بنية JSON المطلوبة للترجمة. يُضاف لاحق المسار تلقائياً إذا لم ينتهِ العنوان بـ`/chat/completions`.

**لا ارتباط بمورد واحد:** قد تعمل خدمات مثل وOpenRouter وGroq وDeepSeek وTogether أو أي مورد آخر عندما توفر endpoint متوافقاً. هذه أمثلة وليست قائمة توافق مختبرة. المفتاح وحده لا يكفي؛ يجب أن يتطابق العنوان والمصادقة والمسار والنموذج مع هذا العقد.

**حدود مهمة:**

- لا تحتاج إلى مفتاح Gemini لترجمة النصوص أو مراجعة الجودة باستخدام مزود مخصص مضبوط. تفريغ الصوت ومسار Gemini المتدفق ميزتان منفصلتان ولا تنتقلان تلقائياً إلى المزود المخصص.
- الترجمة المخصصة القياسية تتم على دفعات؛ اختيار Gemini Live Stream مع مزود مخصص يعيد العملية إلى الترجمة المخصصة القياسية.
- تمنع هذه النسخة عناوين localhost والشبكات الخاصة وخدمات metadata. لن يعمل عنوان Ollama أو LM Studio المحلي كما هو؛ لا تعطّل حماية شبكة النسخة العامة لمجرد توصيله.
- استخدم عنوان API مباشراً، ويفضل HTTPS. ترفض طلبات الترجمة إعادة التوجيه. تجنب العناوين المعتمدة على query string في هذه النسخة واتبع صيغة المسار الأساسي التي يحددها المزود.
- نجاح اختبار الاتصال لا يضمن إنتاج JSON صحيح لكل دفعة ترجمة.
- المزود البديل لا يعني استخداماً مجانياً أو حصة غير محدودة أو تجاوز سياسات الخدمة.

### خيارات Gemini المسجلة في المشروع

| الخيار | المعرّف المضبوط |
| --- | --- |
| Gemini 3.8 Flash، افتراضي الترجمة | `gemini-3.8-flash` |
| Gemini 3.7 Flash | `gemini-3.7-flash` |
| Gemini 3.6 Flash | `gemini-3.6-flash` |
| Gemini 3.5 Flash | `gemini-3.5-flash` |
| Gemini 3.1 Flash Lite | `gemini-3.1-flash-lite` |
| Gemini 3.1 Pro Preview | `gemini-3.1-pro-preview` |
| Gemini Live Stream، وضع تدفق داخل التطبيق | `gemini-live-stream` |
| إعداد تفريغ الصوت | `gemini-3.5-transcribe` |

المرجع: [`src/modelRegistry.ts`](src/modelRegistry.ts). هذه **معرّفات مسجلة في المستودع، وليست تأكيداً لإتاحة Google جميعها حالياً لكل الحسابات**. `gemini-live-stream` وضع داخلي للتطبيق وليس معرّف نموذج مستقل لدى Google. تحقق من الإتاحة الفعلية وحدّث السجل عند الحاجة. الرجوع الاحتياطي لا يضمن استمرار الخدمة ولا يتجاوز الحصص المشتركة.

### التشغيل المحلي

استخدم إصداراً حديثاً من Node.js 22 LTS وnpm:

```bash
git clone https://github.com/gguhfhu7-sketch/SubGame-Lab.git
cd SubGame-Lab
npm install
npm run dev
```

افتح `http://localhost:3000` واضبط Gemini أو Custom Provider من الواجهة. لإضافة مفتاح Gemini اختياري على الخادم، أنشئ `.env`:

```dotenv
GEMINI_API_KEY=your_gemini_api_key
PORT=3000
```

يستخدم الخادم `dotenv.config()`؛ الملف الافتراضي هو `.env` ولا يحمّل هذا الاستدعاء `.env.local` صراحةً. لا تضع مفاتيح حقيقية في المستودع.

```bash
npm run lint
npm run build
npm start
```

الأول لفحص TypeScript، والثاني لبناء الواجهة والخادم، والثالث لتشغيل نسخة الإنتاج.

### نشر النسخة الإلكترونية (Railway)

هذا المستودع جاهز مسبقاً للنشر على Railway: ملف `railway.json` في جذر المشروع يخبر Railway بكيفية بناء التطبيق وتشغيله والتحقق من صحته دون أي إعداد يدوي في لوحة التحكم. يستمع الخادم إلى منفذ `PORT` الذي تحقنه المنصة ويربط نفسه بـ `0.0.0.0`، ويقدّم الواجهة المبنية مع SPA fallback، ويوفّر المسار `/api/health` لفحص الصحة، وينفّذ إيقافاً نظيفاً عند استقبال `SIGTERM`/`SIGINT` حتى لا تظهر عمليات إعادة النشر كأعطال.

**خطوات النشر على Railway:**

1. اعمل fork لمستودع SubGame Lab في حسابك على GitHub.
2. انتقل إلى [railway.com/new](https://railway.com/new) واختر **Deploy from GitHub repo** ثم اختر الـ fork. سيبني Railway باستخدام Railpack: يكتشف Node، يثبّت الاعتماديات، ثم يشغّل `npm run build` (وفق `railway.json`).
3. في تبويب **Variables** الخاص بالخدمة أضف المتغيرات الاختيارية المذكورة أدناه. يتم حقن `PORT` تلقائياً — لا تضبطه يدوياً ولا يوجد منفذ ثابت مثل 8080 يحتاج إلى إعداد.
4. اضغط Deploy. سيشغّل Railway التطبيق بالأمر `node dist/server.cjs` (تشغيل مباشر لـ Node ليصل إشارة `SIGTERM` إلى التطبيق فيتم الإيقاف بنجاح)، ثم ينتظر حتى يرد `/api/health` برمز 2xx قبل تحويل الزيارات.
5. من **Settings → Networking → Public Networking** اضغط **Generate Domain** للحصول على رابط `*.railway.app`، ثم افتحه واضبط Gemini أو Custom Provider من داخل التطبيق.

**متغيرات الخدمة الاختيارية:**

| المتغير | الوظيفة |
| ------- | ------- |
| `GEMINI_API_KEY` | مفتاح Gemini على الخادم للاستخدام عندما لا يوفّر المستخدم مفتاحه. |
| `ALLOW_SERVER_KEY` | ضع القيمة `true` للسماح بالاعتماد على مفتاح الخادم. |
| `ALLOWED_ORIGIN` | قائمة الأصول المسموح لها باستدعاء API مفصولة بفواصل (مثل نطاق `*.railway.app`). إن تُركت فارغة يبقى السلوك المفتوح السابق. |
| `AI_RATE_LIMIT` | حد الطلبات في الدقيقة لكل IP على نقاط AI (الافتراضي `30`، و`0` يعطّله). |
| `GEMINI_BASE_URL` | بروكسي/عنوان أساسي اختياري لطلبات Gemini. |

مسار فحص الصحة (`/api/health`) وأمر التشغيل معرّفان في `railway.json` بحيث يتحقق Railway من كل نشر جديد قبل توجيه الزيارات إليه. لإعادة النشر يكفي الدفع (push) إلى الـ fork؛ ولتغيير سلوك البناء/التشغيل عدّل ملف `railway.json` أو إعدادات الخدمة.

### النشر على Render (بديل)

المستودع جاهز بنفس الدرجة للنشر على Render: ملف Blueprint باسم `render.yaml` في جذر المشروع يعرّف خدمة الويب بالكامل، فتتمكن من النشر دون تعبئة حقول لوحة التحكم يدوياً. يستمع الخادم إلى `0.0.0.0:$PORT` (تحقن Render قيمة `PORT`؛ الافتراضي `10000`)، ويقدّم الواجهة المبنية مع SPA fallback، ويوفّر المسار `/api/health` (رد 2xx خلال نافذة فحص الصحة التي تبلغ 5 ثوانٍ)، وينفّذ إيقافاً نظيفاً عند `SIGTERM` — ضمن مهلة الإيقاف التي تبلغ 30 ثانية بكثير — فتصبح عمليات إعادة النشر دون انقطاع بدل الأعطال.

**خطوات النشر على Render:**

1. اعمل fork لمستودع SubGame Lab في حسابك على GitHub.
2. في لوحة تحكم Render اضغط **New → Blueprint** واربط الـ fork؛ سيقرأ Render ملف `render.yaml` تلقائياً: أمر البناء `npm ci && npm run build` وأمر التشغيل `node dist/server.cjs` (تشغيل مباشر لـ Node ليصل إشارة `SIGTERM` إلى التطبيق).
3. عند تطبيق الـ Blueprint ستسأل Render عن المتغيرات السرية المعلّمة بـ `sync: false` (`GEMINI_API_KEY` و`ALLOW_SERVER_KEY` و`ALLOWED_ORIGIN` و`GEMINI_BASE_URL`). يتم حقن `PORT` تلقائياً — لا تضبطه يدوياً أبداً.
4. اضغط Apply. ينتظر Render حتى يرد `/api/health` برمز 2xx قبل توجيه الزيارات، ثم يفحص كل بضع ثوانٍ ويعيد تشغيل النسخ غير المستجيبة تلقائياً.
5. افتح رابط `*.onrender.com` الناتج واضبط Gemini أو Custom Provider من داخل التطبيق.

نسخة Node.js مثبّتة على `22.14.0` عبر متغير `NODE_VERSION` وملف `.node-version`. ملاحظة: في الخطة المجانية من Render تدخل الخدمة في وضع السكون بعد نحو 15 دقيقة من الخمول ويصبح الطلب الأول بعدها أبطأ قليلاً؛ قم بترقية الخطة لتجنب ذلك.

**استكشاف الأخطاء — صفحة بيضاء بعد النشر:** الصفحة البيضاء تعني أن المتصفح استلم ملف `index.html` ليس من بناء الإنتاج. الخادم الآن محمي من السببين الكلاسيكيين: (1) أمر **البناء** لم ينفّذ `npm run build` أبداً فتكون مجلد `dist` مفقوداً — عندها يُرجع الخادم **صفحة إرشادية 503** (بالفارسية والإنجليزية) على مسارات الويب بينما يبقى `/api/health` سليماً، بدلاً من تقديم جذر المستودع بصمت؛ و(2) أوامر خاطئة تشغّل التطبيق من مصدر TypeScript مباشرة. تحقق من هذه القيم في لوحة التحكم ثم أعد النشر:

| الحقل | القيمة المطلوبة |
|---|---|
| Build Command | `npm ci && npm run build` |
| Start Command | `node dist/server.cjs` |
| Health Check Path | `/api/health` |

لا تضبط `NODE_ENV` يدوياً أبداً، ولا تستخدم `npm run dev` أو `tsx server.ts` كأمر تشغيل — فهي للتطوير المحلي فقط. يفيدك رد `/api/health` في التشخيص أيضاً: `"static":"build"` تعني أن الواجهة المبنية تُقدَّم، و`"static":"missing"` تعني غياب مخرجات البناء على الخادم. وكتحصين إضافي، لم يعد الخادم يقدّم جذر المستودع، لذلك لا يمكن أبداً تسريب ملفات مثل `server.ts` أو `package-lock.json` عبر الويب.

### الشبكة والخصوصية

تخرج طلبات API من خادم التطبيق. يجب أن يصل المضيف إلى المزود في النسخة المنشورة، أو أن تصل عملية Node.js المحلية إليه عند التشغيل المحلي. تعتمد الحاجة إلى بروكسي أو توجيه خاص على الشبكة وقيود المزود؛ العرض المباشر ليس ضماناً للوصول من كل المناطق. استخدم الخدمات وفق شروطها؛ المفاتيح المتعددة لا تلغي الحصص.

تُحفظ مفاتيح BYOK في localStorage وتُرسل إلى خلفية التطبيق لتنفيذ الطلبات. تُحفظ الجلسات في IndexedDB. استخدم استضافة موثوقة، ولا تحفظ المفاتيح على أجهزة مشتركة، وصدّر العمل المهم بانتظام. قد يؤدي مسح بيانات الموقع إلى حذف الجلسات والمفاتيح. قبل النشر العام، راجع المصادقة وحدود التكلفة والطلبات وضوابط الاتصالات الصادرة ومتطلبات الخصوصية؛ الحماية المضمنة ليست تدقيقاً أمنياً شاملاً.

**التقنيات الحالية:** React 19، TypeScript، Vite 6، Tailwind CSS 4، Express، `@google/genai`، TanStack Virtual، Dexie/IndexedDB، ExcelJS، PapaParse وjschardet. يعتمد الأداء على الملف والجهاز؛ لا يوجد وعد ثابت بعرض 50,000 صف بسرعة 60 إطاراً في الثانية.

---

### Project links · لینک‌های پروژه · روابط المشروع

- **Repository:** https://github.com/gguhfhu7-sketch/SubGame-Lab
- **Live demo:** https://subgame-lab-production-5e1a.up.railway.app/
- **Telegram community and announcements:** https://t.me/MySaeedLab

