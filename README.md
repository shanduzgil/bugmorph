🐛 bugmorph

موتور جهش و تحلیل تطبیقی برای کشف آسیب‌پذیری‌های تزریق در وب‌اپلیکیشن‌ها

"bugmorph" یک ابزار متن‌باز، آسنکرون و سبک برای کشف نقاط ضعف امنیتی در پارامترها، هدرها، کوکی‌ها و ورودی‌های API است. این ابزار با تولید payloadهای جهشی، تحلیل رفتار پاسخ سرور و امتیازدهی هوشمند به یافته‌ها، به شما کمک می‌کند نشانه‌های آسیب‌پذیری‌هایی مانند SQLi، XSS، Command Injection، LFI و SSTI را سریع‌تر شناسایی کنید.

---

ویژگی‌ها

- موتور جهش تطبیقی برای تولید payloadهای متنوع و ترکیب خانواده‌های مختلف ورودی
- Crawler داخلی برای کشف خودکار فرم‌ها، لینک‌ها و endpointها
- تحلیل context برای تشخیص نوع پارامتر و انتخاب payload مناسب
- تشخیص پاسخ‌های مشکوک بر پایه‌ی reflection، خطاهای سمت سرور، تغییر طول پاسخ و تأخیر زمانی
- Resume و deduplication با ذخیره‌سازی وضعیت اسکن برای جلوگیری از تست تکراری
- گزارش‌های چندفرمتی شامل JSON، HTML، Markdown، CSV و SARIF
- CLI حرفه‌ای با گزینه‌های کامل برای کنترل دقیق اسکن
- رابط ترمینالی برای تجربه‌ی تعاملی‌تر در محیط خط فرمان
- HTTP API برای استفاده در اتوماسیون و کار تیمی
- سیستم افزونه برای سفارشی‌سازی رفتار اسکن
- پشتیبانی از پروکسی برای استفاده همراه با Burp Suite یا OWASP ZAP
- نصب ساده با pip و بدون وابستگی‌های سنگین

---

نصب

پیش‌نیاز: Python 3.10+

pip install bugmorph

برای نصب همراه با رابط گرافیکی/ترمینالی:

pip install bugmorph[gui]

---

شروع سریع

برای اسکن یک آدرس مشخص:

bugmorph scan https://example.com

برای محدود کردن اسکن به یک دامنه:

bugmorph scan https://example.com --scope example.com

برای ذخیره‌ی خروجی در فایل:

bugmorph scan https://example.com --format json --report report.json

---

نمونه اسکن کامل

bugmorph scan https://target.com \
  --scope target.com \
  --workers 12 \
  --timeout 10 \
  --max-pages 80 \
  --max-depth 3 \
  --payload-limit 60 \
  --delay-ms 150 \
  --follow-redirects \
  --format html \
  --report full_report.html

توضیح گزینه‌ها

گزینه| توضیح
"--workers 12"| تعداد درخواست‌های هم‌زمان
"--timeout 10"| حداکثر زمان انتظار برای هر پاسخ (ثانیه)
"--max-pages 80"| حداکثر تعداد صفحات قابل خزیدن
"--max-depth 3"| حداکثر عمق دنبال‌کردن لینک‌ها
"--payload-limit 60"| تعداد جهش‌ها برای هر پارامتر
"--delay-ms 150"| تأخیر بین درخواست‌ها
"--follow-redirects"| دنبال کردن ریدایرکت‌ها
"--format html"| نوع خروجی
"--report full_report.html"| مسیر فایل گزارش

---

فرمت‌های خروجی

- "console" — نمایش در ترمینال
- "json" — مناسب برای پردازش با کد
- "html" — گزارش خوانا و قابل ارائه
- "md" — خروجی Markdown
- "csv" — مناسب برای Excel و ابزارهای تحلیلی
- "sarif" — سازگار با GitHub Code Scanning

نمونه:

bugmorph scan https://api.example.com --format sarif --report api_scan.sarif

---

اسکن صفحات احراز هویت‌شده

اگر برنامه نیاز به ورود دارد، می‌توانید کوکی و هدرهای دلخواه را ارسال کنید:

bugmorph scan https://app.example.com/dashboard \
  --scope example.com \
  --cookies "session=abc123; csrf=xyz" \
  --headers '{"Authorization":"Bearer token"}'

---

تست APIهای POST و JSON

ارسال فرم

bugmorph scan https://api.example.com/login \
  --method POST \
  --data "username=admin&password=123" \
  --format json \
  --report login.json

ارسال JSON

bugmorph scan https://api.example.com/v1/users \
  --method POST \
  --json-body '{"id":1,"name":"test"}' \
  --format json \
  --report api_users.json

---

حالت کم‌سر‌وصدا

برای کاهش نرخ درخواست‌ها و کنترل بهتر رفتار اسکن:

bugmorph scan https://target.com \
  --workers 5 \
  --delay-ms 500 \
  --timeout 15 \
  --max-pages 30 \
  --payload-limit 20 \
  --proxy http://127.0.0.1:8080 \
  --format json \
  --report stealth.json

همچنین می‌توانید User-Agent سفارشی تعریف کنید:

bugmorph scan https://target.com \
  --headers '{"User-Agent":"Mozilla/5.0 (compatible; Bugmorph/1.0)"}'

---

بارگذاری Payload Pack سفارشی

می‌توانید خانواده‌های payload را در یک فایل JSON تعریف کنید:

{
  "sqli": ["' OR 1=1--", "admin'--"],
  "xss": ["<img src=x onerror=alert(1)>"],
  "lfi": ["../../etc/passwd"]
}

سپس آن را به اسکنر بدهید:

bugmorph scan https://target.com --payload-pack my_payloads.json

---

رابط ترمینالی

برای اجرای تعاملی‌تر در ترمینال:

bugmorph gui

در این حالت می‌توانید URL را وارد کنید، اسکن را اجرا کنید و نتیجه را به‌صورت زنده ببینید.

---

HTTP API

برای راه‌اندازی سرور REST:

bugmorph serve --host 0.0.0.0 --port 8080 --api-token my_secret

ایجاد یک اسکن از طریق "curl":

curl -X POST http://localhost:8080/scan \
  -H "Authorization: Bearer my_secret" \
  -H "Content-Type: application/json" \
  -d '{"url":"https://example.com","scope":{"allow_domains":["example.com"]}}'

گزارش‌ها از مسیر API مربوطه قابل دریافت هستند.

---

سیستم افزونه

برای سفارشی‌سازی رفتار ابزار، می‌توانید افزونه‌های پایتونی خودتان را اضافه کنید.

bugmorph scan https://target.com --plugin-dir ./my_plugins

نمونه افزونه:

from mutiny.core.plugins import BasePlugin


class LogPlugin(BasePlugin):
    async def on_finding(self, finding):
        print(f"🔥 {finding.severity} found: {finding.param}")

---

نکات امنیتی و قانونی

این ابزار فقط برای تست نفوذ مجاز و محیط‌هایی که اجازه‌ی بررسی آن‌ها را دارید طراحی شده است.
استفاده از آن روی سامانه‌هایی که مالک آن‌ها نیستید یا مجوز صریح برای تست ندارید، می‌تواند غیرقانونی باشد و پیامدهای جدی داشته باشد.

---

مشارکت

گزارش باگ، پیشنهاد قابلیت جدید و Pull Request همیشه خوش‌آمد هستند.
اگر قصد مشارکت دارید، لطفاً ابتدا ساختار پروژه و شیوه‌ی ارسال تغییرات را بررسی کنید.

---

مجوز

این پروژه تحت مجوز MIT منتشر شده است.
