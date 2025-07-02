# جزوه آموزشی حرفه‌ای Laravel

## ساختار درختی پروژه Laravel

```
laravel-project/
├── app/
│   ├── Http/
│   │   ├── Controllers/
│   │   ├── Middleware/
│   ├── Models/
│   ├── Providers/
├── bootstrap/
├── config/
├── database/
│   ├── factories/
│   ├── migrations/
│   ├── seeders/
├── public/
├── resources/
│   ├── css/
│   ├── js/
│   ├── views/
├── routes/
│   ├── web.php
│   ├── api.php
├── storage/
├── tests/
└── vendor/
```

## فصل اول: مقدمه و نصب Laravel

### توضیحات و فلوی پیاده‌سازی

Laravel یک فریم‌ورک PHP با معماری MVC است که به دلیل سادگی، انعطاف‌پذیری و قدرت آن، به یکی از محبوب‌ترین فریم‌ورک‌های توسعه وب تبدیل شده است. فرآیند نصب و راه‌اندازی Laravel بسیار ساده است و با استفاده از ابزارهای مدرن، می‌توان در کمترین زمان یک پروژه Laravel را راه‌اندازی کرد.

### روش‌های نصب Laravel

#### 1. استفاده از Laravel Herd (توصیه شده برای تازه‌کاران)

Laravel Herd یک ابزار one-click برای راه‌اندازی محیط توسعه Laravel است که شامل تمام ابزارهای مورد نیاز مانند PHP، Laravel و Composer می‌باشد.

```bash
# پس از نصب Laravel Herd، می‌توانید یک پروژه جدید ایجاد کنید:
laravel new project-name
```

#### 2. روش‌های جایگزین

* **XAMPP/WAMP/MAMP**: محیط‌های توسعه یکپارچه برای PHP
* **Homeبrew** (macOS): `brew install php mysql`
* **Laravel Valet** (macOS): محیط توسعه سبک برای macOS
* **Laravel Sail**: محیط توسعه Docker-based

### نکات کلیدی

- Laravel Herd تمام ابزارهای مورد نیاز را فراهم می‌کند و نیازی به نصب جداگانه PHP یا Composer نیست
- با Laravel Herd می‌توانید همزمان چندین نسخه PHP را مدیریت کنید
- دامنه‌های `.test` به صورت خودکار توسط Herd پیکربندی می‌شوند
- برای راه‌اندازی یک سرور محلی بدون استفاده از Herd، می‌توانید از دستور `php artisan serve` استفاده کنید

---

## فصل دوم: مسیریابی (Routing) در Laravel

### فلوی گرامری مسیریابی

مسیریابی در Laravel فرآیند تعیین پاسخ برنامه به یک درخواست URL خاص است. تمامی مسیرها در پوشه `routes` تعریف می‌شوند، با `web.php` به عنوان فایل اصلی برای مسیرهای وب.

#### ساختار پایه مسیریابی:

```php
// routes/web.php
Route::get('/path', function() {
    // پاسخ به درخواست
});
```

#### انواع پاسخ‌های مسیریابی:

1. **پاسخ متنی ساده**:

```php
// routes/web.php
Route::get('/about', function() {
    return 'About page';
});
```

2. **پاسخ JSON**:

```php
// routes/web.php
Route::get('/api/data', function() {
    return ['foo' => 'bar'];
});
```

3. **بازگرداندن View**:

```php
// routes/web.php
Route::get('/', function() {
    return view('welcome');
});
```

### رابطه بین مسیرها و View‌ها

وقتی از تابع `view()` استفاده می‌کنید، Laravel به دنبال فایل مربوطه در مسیر `resources/views` می‌گردد:

```php
// routes/web.php
Route::get('/', function() {
    return view('welcome'); // به دنبال resources/views/welcome.blade.php می‌گردد
});

Route::get('/about', function() {
    return view('about'); // به دنبال resources/views/about.blade.php یا about.php می‌گردد
});
```

### نکات کلیدی مسیریابی

- مسیرهای وب عادی در `routes/web.php` تعریف می‌شوند
- مسیرهای API در `routes/api.php` تعریف می‌شوند
- می‌توانید از متدهای `get`، `post`، `put`، `patch`، `delete` و غیره استفاده کنید
- پاسخ‌های آرایه‌ای به صورت خودکار به JSON تبدیل می‌شوند
- پسوند فایل‌های view می‌تواند `.blade.php` یا `.php` باشد

---

## فصل سوم: View‌ها و موتور قالب Blade

### فلوی گرامری View‌ها

View‌ها در Laravel مسئول نمایش داده‌ها به کاربر هستند. Laravel از موتور قالب Blade استفاده می‌کند که امکانات قدرتمندی برای ایجاد قالب‌های پویا فراهم می‌کند.

#### ساختار پایه View:

```php
<!-- resources/views/welcome.blade.php -->
<!DOCTYPE html>
<html>
<head>
    <title>Laravel App</title>
</head>
<body>
    <h1>Hello World</h1>
</body>
</html>
```

#### ایجاد View ساده (بدون Blade):

```php
<!-- resources/views/about.php -->
<!DOCTYPE html>
<html>
<head>
    <title>About Page</title>
</head>
<body>
    <h1>About Page</h1>
    <p>Hello from the about page</p>
</body>
</html>
```

### رابطه بین مسیرها و View‌ها با جزئیات

1. **مسیر درخواست**: کاربر به URL خاصی مانند `example.test/about` درخواست می‌دهد.
2. **شناسایی مسیر**: Laravel در فایل `routes/web.php` به دنبال مسیر مطابق با درخواست می‌گردد.
3. **اجرای Closure یا Controller**: کد مرتبط با مسیر اجرا می‌شود.
4. **بازگرداندن View**: اگر از تابع `view()` استفاده شود، Laravel به دنبال فایل مربوطه در `resources/views` می‌گردد.
5. **پردازش View**: اگر فایل دارای پسوند `.blade.php` باشد، موتور Blade آن را پردازش می‌کند.
6. **ارسال پاسخ**: محتوای HTML به مرورگر کاربر ارسال می‌شود.

```php
// routes/web.php - تعریف مسیر
Route::get('/about', function() {
    return view('about');
});

// resources/views/about.php - فایل view
// این فایل توسط Laravel پیدا و بازگردانده می‌شود
```

### نکات کلیدی View‌ها

- فایل‌های view در مسیر `resources/views` قرار می‌گیرند
- Blade یک موتور قالب قدرتمند با امکانات اضافی نسبت به PHP خالص است
- می‌توانید از هر دو پسوند `.blade.php` یا `.php` برای view‌ها استفاده کنید
- برای سادگی در مراحل اولیه یادگیری، می‌توانید از PHP خالص استفاده کنید

---

## فصل چهارم: مفاهیم پیشرفته مسیریابی

### پارامترهای مسیر

می‌توانید پارامترهای پویا در مسیرها تعریف کنید:

```php
// routes/web.php
Route::get('/user/{id}', function($id) {
    return 'User ID: ' . $id;
});
```

### پارامترهای اختیاری

برای تعریف پارامترهای اختیاری، از علامت `?` استفاده کنید:

```php
// routes/web.php
Route::get('/user/{name?}', function($name = 'Guest') {
    return 'Hello, ' . $name;
});
```

### محدودیت‌های پارامتر

می‌توانید با استفاده از `where` محدودیت‌هایی برای پارامترها تعریف کنید:

```php
// routes/web.php
Route::get('/user/{id}', function($id) {
    return 'User ID: ' . $id;
})->where('id', '[0-9]+');
```

### گروه‌بندی مسیرها

برای اعمال ویژگی‌های مشترک به چندین مسیر، می‌توانید آنها را گروه‌بندی کنید:

```php
// routes/web.php
Route::prefix('admin')->group(function() {
    Route::get('/dashboard', function() {
        return 'Admin Dashboard';
    });
    Route::get('/users', function() {
        return 'Admin Users';
    });
});
```

### نکات کلیدی مسیریابی پیشرفته

- نام‌گذاری مسیرها با متد `name()` باعث سهولت در استفاده از آنها می‌شود
- گروه‌بندی مسیرها باعث کاهش کد تکراری می‌شود
- می‌توانید middleware‌ها را به مسیرها یا گروه‌ها اعمال کنید
- فایل `routes/web.php` برای مسیرهای عمومی وب و `routes/api.php` برای API‌ها استفاده می‌شود

---

## فصل پنجم: جریان داده در Laravel

### چرخه درخواست تا پاسخ

1. **درخواست HTTP**: کاربر URL را در مرورگر وارد می‌کند
2. **مسیریابی**: Laravel مسیر مناسب را پیدا می‌کند
3. **میان‌افزار (Middleware)**: درخواست از زنجیره‌ای از میان‌افزارها عبور می‌کند
4. **کنترلر یا Closure**: منطق اصلی برنامه اجرا می‌شود
5. **مدل (اختیاری)**: تعامل با پایگاه داده انجام می‌شود
6. **View (اختیاری)**: داده‌ها به قالب HTML تبدیل می‌شوند
7. **پاسخ HTTP**: پاسخ به مرورگر کاربر ارسال می‌شود

### ارسال داده به View‌ها

می‌توانید داده‌ها را به View‌ها ارسال کنید:

```php
// routes/web.php
Route::get('/greeting', function() {
    return view('greeting', ['name' => 'James']);
});
```

```php
<!-- resources/views/greeting.blade.php -->
<h1>Hello, {{ $name }}</h1>
```

### نکات کلیدی جریان داده

- تمامی درخواست‌ها از طریق `public/index.php` وارد می‌شوند
- Laravel از الگوی MVC (Model-View-Controller) پیروی می‌کند
- Controller‌ها منطق اصلی برنامه را مدیریت می‌کنند
- View‌ها مسئول نمایش داده‌ها هستند
- Model‌ها تعامل با پایگاه داده را مدیریت می‌کنند

---

## جمع‌بندی

Laravel یک فریم‌ورک قدرتمند و انعطاف‌پذیر است که با ارائه ابزارهای مناسب، توسعه برنامه‌های وب را آسان می‌کند. با شروع از مفاهیم پایه مانند مسیریابی و View‌ها، می‌توانید به تدریج با پیچیدگی‌های بیشتر فریم‌ورک آشنا شوید. روند یادگیری Laravel باید گام به گام باشد و نیازی به یادگیری تمامی ویژگی‌ها به صورت همزمان نیست.

### نکات نهایی مهم

- همیشه به روز‌ترین نسخه مستندات Laravel را مطالعه کنید
- Laravel یک اکوسیستم غنی از ابزارها و پکیج‌ها دارد که می‌تواند کار شما را ساده‌تر کند
- تمرین مداوم و ساخت پروژه‌های کوچک بهترین راه یادگیری Laravel است
- مشارکت در انجمن‌های Laravel می‌تواند به پیشرفت سریع‌تر شما کمک کند
- با ساخت پروژه‌های واقعی، می‌توانید به درک عمیق‌تری از فریم‌ورک دست یابید

این جزوه پایه و اساس کار با Laravel را پوشش می‌دهد و با تمرین و تجربه بیشتر، می‌توانید به یک توسعه‌دهنده ماهر Laravel تبدیل شوید.
