# Panel Technamooz v1.0.0 Stable

> پنل حرفه‌ای ساخت و مدیریت کانفیگ VLESS/XHTTP، ساخته‌شده توسط **تیم Technamooz** با مدیریت **amirparsa**.

## معرفی

Panel Technamooz یک پنل Python/FastAPI آمادهٔ استقرار روی Railway است. نسخهٔ 2.0.0 شامل بازسازی هویت بصری glassmorphism مشکی‌ـقرمز، داشبورد مدیریتی واکنش‌گرا، ورود username/password، تم دارک و لایت، تغییر زبان فارسی/انگلیسی با پیش‌فرض انگلیسی، مدیریت کامل کانفیگ‌ها و مرکز کنترل ربات Telegram است.

## قابلیت‌ها

ساخت VLESS WebSocket و XHTTP، سهمیه و انقضا، محدودیت IP و سرعت، fingerprint و ALPN، QR و export، گروه‌های subscription، صفحات عمومی اشتراک، آمار ترافیک و اتصالات، لاگ فعالیت، backup/restore، تست WebSocket، تغییر رمز، کنترل نشست‌ها و ربات تلگرام با wizard ساخت کانفیگ و مدیریت گروه‌ها در این نسخه حفظ و یکپارچه شده‌اند.

## پیش‌نیازها

Python 3.11 یا بالاتر، pip، یک سرویس Railway و برای نگهداری دائمی داده‌ها یک Volume در مسیر `/data` لازم است. مرورگر مدرن برای رابط کاربری پیشنهاد می‌شود.

## نصب محلی صفر تا صد

```bash
git clone <YOUR_REPOSITORY_URL>
cd technamooz-panel
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
export ADMIN_USERNAME='Amirparsa'
export ADMIN_PASSWORD='یک-رمز-قوی-حداقل-۱۰-کاراکتری'
export SECRET_KEY='یک-کلید-تصادفی-طولانی'
export DATA_DIR='./data'
uvicorn main:app --host 0.0.0.0 --port 8000
```

آدرس ورود `http://localhost:8000/login` است. **نام کاربری پیش‌فرض `Amirparsa` است** و رمز پیش‌فرض در صورتی که `ADMIN_PASSWORD` تنظیم نشود `Technamooz@ChangeMe` خواهد بود؛ این رمز فقط برای اجرای آزمایشی است و باید بلافاصله تغییر کند.

## استقرار Railway

یک پروژهٔ جدید بسازید، Repository را متصل کنید و یک Volume با mount path برابر `/data` اضافه کنید. سپس Variables زیر را تعریف کنید. Railway مقدار `PORT` را خودکار تنظیم می‌کند و `Procfile` با `0.0.0.0` اجرا می‌شود. فایل `railway.json` نیز healthcheck مسیر `/health` را فراهم می‌کند.

| Variable | مقدار | ضرورت |
|---|---|---|
| `ADMIN_USERNAME` | `Amirparsa` یا نام دلخواه | الزامی برای ورود |
| `ADMIN_PASSWORD` | رمز قوی | الزامی در production |
| `SECRET_KEY` | رشته تصادفی طولانی | الزامی برای ثبات نشست |
| `DATA_DIR` | `/data` | الزامی برای Volume |
| `RAILWAY_PUBLIC_DOMAIN` | دامنه عمومی | برای لینک‌های کانفیگ |
| `TELEGRAM_BOT_TOKEN` | توکن BotFather | فعال‌سازی اولیه ربات، اختیاری |
| `TELEGRAM_ADMIN_IDS` | شناسه‌های عددی با کاما | ادمین‌های مجاز ربات |

پس از Deploy، `/health` را باز کنید، وارد پنل شوید و از منوی **Telegram Bot** تنظیمات ربات را بررسی یا از داخل پنل اعمال کنید. برای جلوگیری از افشای توکن، آن را در Variableهای Railway نگهداری کنید. اگر تنظیمات از داخل پنل ذخیره شود، در state پروژه ذخیره و در startup بعدی بازیابی می‌شود؛ Volume نباید حذف شود.

## ربات Telegram

برای راه‌اندازی، در BotFather یک bot بسازید، مقدار `TELEGRAM_BOT_TOKEN` را تنظیم کنید و شناسه عددی مدیر را در `TELEGRAM_ADMIN_IDS` قرار دهید. پس از ورود به پنل، در بخش Telegram Bot می‌توانید وضعیت، Admin IDها و فعال‌سازی runtime را مدیریت کنید. ربات از long polling استفاده می‌کند و به webhook یا دامنهٔ جداگانه نیاز ندارد. قابلیت‌های آن شامل wizard ساخت کانفیگ، فهرست و جزئیات، فعال/غیرفعال‌سازی، مدیریت گروه‌های ساب و افزودن/حذف کانفیگ از گروه است.

## امنیت و داده

رمز با PBKDF2-SHA256 و salt تصادفی ذخیره می‌شود، ورود rate limit دارد و APIهای مدیریتی session لازم دارند. مقدارهای محرمانه را commit نکنید، `SECRET_KEY` را تغییر ندهید مگر اینکه قصد invalidate شدن نشست‌ها را دارید، و به‌صورت دوره‌ای از بخش تنظیمات backup بگیرید.

## ساختار فایل‌ها

| فایل | کاربرد |
|---|---|
| `main.py` | FastAPI، persistence، احراز هویت، API و کنترل ربات |
| `pages.py` | Login، dashboard و رابط کاربری دو‌زبانه |
| `telegram_bot.py` | ربات Telegram و فرمان‌های مدیریت |
| `relay_vless.py` | تونل VLESS روی WebSocket |
| `xhttp_siz10.py` | ترابرد XHTTP |
| `speed_limit.py` | کنترل سرعت |
| `Procfile` | اجرای Railway |
| `railway.json` | healthcheck و restart policy |

## پشتیبانی رسمی

تلگرام رسمی: [@technamooz](https://t.me/technamooz) · پشتیبانی مستقیم: [@technamooz_support](https://t.me/technamooz_support) · ایمیل: [Technamooz@gmail.com](mailto:Technamooz@gmail.com) · وب‌سایت: [Technamooz.ir](https://technamooz.ir) · Instagram/YouTube/X/GitHub: `@technamooz` و `amirparsa`.

© 2026 Technamooz Team · Managed by amirparsa


### تغییر مشخصات ورود

پس از ورود، از بخش تنظیمات امنیتی می‌توانید نام کاربری و رمز را تغییر دهید. رمز جدید باید حداقل ۱۰ کاراکتر داشته باشد. برای اجرای اولیه، مقادیر پیش‌فرض دقیقاً `Amirparsa` و `Technamooz` هستند؛ در محیط عمومی حتماً آن‌ها را تغییر دهید.
