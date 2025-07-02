# Laravel Blade Layouts & Components - Professional Handbook

## 📋 درختواره ساختاری مباحث

```
Laravel Blade Layouts & Components
├── 1. مقدمات و مبانی
│   ├── 1.1 معرفی Blade Templating Engine
│   ├── 1.2 ساختار پروژه و فایل‌ها
│   └── 1.3 نحوه نام‌گذاری فایل‌ها
├── 2. Routes و Views پایه
│   ├── 2.1 تعریف Routes اولیه
│   ├── 2.2 ایجاد Views ساده
│   └── 2.3 ناوبری بین صفحات
├── 3. Laravel Blade Components
│   ├── 3.1 مفهوم و کاربرد Components
│   ├── 3.2 ایجاد Layout Components
│   └── 3.3 استفاده از Slots
├── 4. پیشرفته: Navigation Components
│   ├── 4.1 ایجاد Navigation Link Component
│   ├── 4.2 مدیریت Attributes
│   └── 4.3 Dynamic Components
└── 5. بهینه‌سازی و بهترین روش‌ها
    ├── 5.1 اصول DRY در Layout Design
    ├── 5.2 Component Architecture
    └── 5.3 Performance Considerations
```

---

## 1. مقدمات و مبانی

### 1.1 معرفی Blade Templating Engine

Blade موتور قالب‌سازی Laravel است که لایه‌ای نازک روی PHP قرار می‌گیرد و امکانات اضافی برای توسعه‌دهندگان فراهم می‌کند.

**ویژگی‌های کلیدی Blade:**
- کامپایل شدن به PHP خالص
- ارائه Directives و Helpers مفید
- پشتیبانی از Layout Files
- سیستم Component مدرن

### 1.2 ساختار پروژه و فایل‌ها

```
resources/
└── views/
    ├── components/        # فایل‌های Component
    │   └── layouts/      # Layout Components
    ├── home.blade.php    # صفحه اصلی
    ├── about.blade.php   # صفحه درباره ما
    └── contact.blade.php # صفحه تماس
```

### 1.3 نحوه نام‌گذاری فایل‌ها

**قانون مهم:** همه فایل‌های Blade باید پسوند `.blade.php` داشته باشند.

---

## 2. Routes و Views پایه

### 2.1 تعریف Routes اولیه

**فایل: `routes/web.php`**
```php
<?php

use Illuminate\Support\Facades\Route;

// صفحه اصلی
Route::get('/', function () {
    return view('home');
});

// صفحه درباره ما
Route::get('/about', function () {
    return view('about');
});

// صفحه تماس
Route::get('/contact', function () {
    return view('contact');
});

// خروجی: سه route برای سه صفحه اصلی سایت
```

### 2.2 ایجاد Views ساده

**فایل: `resources/views/home.blade.php`**
```html
<!DOCTYPE html>
<html lang="fa">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>صفحه اصلی</title>
</head>
<body>
    <nav>
        <a href="/">خانه</a>
        <a href="/about">درباره ما</a>
        <a href="/contact">تماس</a>
    </nav>
    
    <h1>سلام از صفحه اصلی</h1>
</body>
</html>

<!-- خروجی: صفحه HTML کامل با ناوبری -->
```

**فایل: `resources/views/about.blade.php`**
```html
<!DOCTYPE html>
<html lang="fa">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>درباره ما</title>
</head>
<body>
    <nav>
        <a href="/">خانه</a>
        <a href="/about">درباره ما</a>
        <a href="/contact">تماس</a>
    </nav>
    
    <h1>سلام از صفحه درباره ما</h1>
</body>
</html>

<!-- خروجی: صفحه HTML مشابه با محتوای متفاوت -->
```

**فایل: `resources/views/contact.blade.php`**
```html
<!DOCTYPE html>
<html lang="fa">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>تماس</title>
</head>
<body>
    <nav>
        <a href="/">خانه</a>
        <a href="/about">درباره ما</a>
        <a href="/contact">تماس</a>
    </nav>
    
    <h1>سلام از صفحه تماس</h1>
</body>
</html>

<!-- خروجی: صفحه HTML با بخش تماس -->
```

### 2.3 مشکل تکرار کد (Code Duplication)

**نکته کلیدی:** در کد بالا مشاهده می‌کنید که:
- ساختار HTML در همه صفحات تکرار شده
- Navigation menu در هر فایل کپی شده
- تغییر در طراحی نیازمند ویرایش همه فایل‌ها است

**این رویکرد مشکلات زیر دارد:**
- نقض اصل DRY (Don't Repeat Yourself)
- مشکل در نگهداری (Maintenance)
- احتمال خطا در هنگام بروزرسانی

---

## 3. Laravel Blade Components

### 3.1 مفهوم و کاربرد Components

**Component** = بلوک قابل استفاده مجدد از کد که می‌تواند در قسمت‌های مختلف اپلیکیشن استفاده شود.

**انواع Components:**
- Layout Components (قالب‌های کلی)
- UI Components (دکمه، کارت، منو)
- Form Components (فیلدها، ولیدیشن)

### 3.2 ایجاد Layout Component

**مرحله 1: ایجاد فایل Layout**

**فایل: `resources/views/components/layouts/app.blade.php`**
```html
<!DOCTYPE html>
<html lang="fa">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ $title ?? 'سایت من' }}</title>
</head>
<body>
    <nav>
        <a href="/">خانه</a>
        <a href="/about">درباره ما</a>
        <a href="/contact">تماس</a>
    </nav>
    
    <main>
        {{ $slot }}
    </main>
</body>
</html>

<!-- خروجی: قالب اصلی با محل مشخص برای محتوای منحصربفرد -->
```

**نکات کلیدی:**
- `{{ $slot }}`: محل قرارگیری محتوای منحصربفرد هر صفحه
- `{{ $title ?? 'سایت من' }}`: مقدار پیش‌فرض برای عنوان

**مرحله 2: بازنویسی Views با استفاده از Component**

**فایل: `resources/views/home.blade.php`**
```html
<x-layouts.app>
    <h1>سلام از صفحه اصلی</h1>
</x-layouts.app>

<!-- خروجی: محتوای صفحه اصلی داخل قالب کلی -->
```

**فایل: `resources/views/about.blade.php`**
```html
<x-layouts.app>
    <h1>سلام از صفحه درباره ما</h1>
</x-layouts.app>

<!-- خروجی: محتوای صفحه درباره ما داخل قالب کلی -->
```

**فایل: `resources/views/contact.blade.php`**
```html
<x-layouts.app>
    <h1>سلام از صفحه تماس</h1>
</x-layouts.app>

<!-- خروجی: محتوای صفحه تماس داخل قالب کلی -->
```

### 3.3 استفاده از Slots (پیشرفته)

**فایل: `resources/views/components/layouts/app.blade.php`**
```html
<!DOCTYPE html>
<html lang="fa">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ $title ?? 'سایت من' }}</title>
    
    @isset($styles)
        {{ $styles }}
    @endisset
</head>
<body>
    <nav>
        <a href="/">خانه</a>
        <a href="/about">درباره ما</a>
        <a href="/contact">تماس</a>
    </nav>
    
    <main>
        {{ $slot }}
    </main>
    
    @isset($scripts)
        {{ $scripts }}
    @endisset
</body>
</html>

<!-- خروجی: قالب با امکان اضافه کردن CSS و JS منحصربفرد -->
```

**استفاده از Named Slots:**

**فایل: `resources/views/home.blade.php`**
```html
<x-layouts.app>
    <x-slot name="styles">
        <style>
            .home-special { color: blue; }
        </style>
    </x-slot>
    
    <h1 class="home-special">سلام از صفحه اصلی</h1>
    
    <x-slot name="scripts">
        <script>
            console.log('JavaScript مخصوص صفحه اصلی');
        </script>
    </x-slot>
</x-layouts.app>

<!-- خروجی: صفحه اصلی با CSS و JS اختصاصی -->
```

---

## 4. پیشرفته: Navigation Components

### 4.1 ایجاد Navigation Link Component

**مرحله 1: ایجاد Component**

**فایل: `resources/views/components/nav-link.blade.php`**
```html
<a {{ $attributes }}>
    {{ $slot }}
</a>

<!-- خروجی: لینک ناوبری ساده با امکان دریافت attributes -->
```

**مرحله 2: بروزرسانی Layout**

**فایل: `resources/views/components/layouts/app.blade.php`**
```html
<!DOCTYPE html>
<html lang="fa">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ $title ?? 'سایت من' }}</title>
</head>
<body>
    <nav>
        <x-nav-link href="/">خانه</x-nav-link>
        <x-nav-link href="/about">درباره ما</x-nav-link>
        <x-nav-link href="/contact">تماس</x-nav-link>
    </nav>
    
    <main>
        {{ $slot }}
    </main>
</body>
</html>

<!-- خروجی: ناوبری با استفاده از component مخصوص -->
```

### 4.2 مدیریت Attributes

**نحوه عملکرد `$attributes` Object:**

**فایل: `resources/views/components/nav-link.blade.php`**
```html
<a {{ $attributes->merge(['class' => 'nav-link']) }}>
    {{ $slot }}
</a>

<!-- خروجی: لینک با class پیش‌فرض قابل ترکیب با classes اضافی -->
```

**استفاده پیشرفته:**

**فایل: `resources/views/components/layouts/app.blade.php`**
```html
<nav>
    <x-nav-link href="/" class="active">خانه</x-nav-link>
    <x-nav-link href="/about" style="color: green;">درباره ما</x-nav-link>
    <x-nav-link href="/contact" id="contact-link">تماس</x-nav-link>
</nav>

<!-- خروجی: لینک‌های ناوبری با attributes مختلف -->
```

### 4.3 Component با منطق پیچیده‌تر

**فایل: `resources/views/components/nav-link.blade.php`**
```html
@php
    $classes = 'nav-link px-3 py-2 text-decoration-none';
    
    if (request()->is(ltrim($attributes->get('href'), '/'))) {
        $classes .= ' active bg-primary text-white';
    } else {
        $classes .= ' text-dark';
    }
@endphp

<a {{ $attributes->merge(['class' => $classes]) }}>
    {{ $slot }}
</a>

<!-- خروجی: لینک ناوبری با تشخیص صفحه فعال -->
```

---

## 5. بهینه‌سازی و بهترین روش‌ها

### 5.1 اصول DRY در Layout Design

**قبل از بهینه‌سازی:**
```
❌ 3 فایل HTML کامل (450 خط کد)
❌ تکرار navigation در هر فایل
❌ مشکل در نگهداری
```

**بعد از بهینه‌سازی:**
```
✅ 1 Layout Component (50 خط)
✅ 3 View ساده (15 خط هر کدام)
✅ نگهداری آسان
```

### 5.2 Component Architecture

**ساختار پیشنهادی:**
```
resources/views/components/
├── layouts/
│   ├── app.blade.php      # Layout اصلی
│   ├── admin.blade.php    # Layout پنل مدیریت
│   └── guest.blade.php    # Layout کاربران مهمان
├── ui/
│   ├── button.blade.php   # دکمه‌های مختلف
│   ├── card.blade.php     # کارت‌های محتوا
│   └── modal.blade.php    # Modal dialogs
└── nav/
    ├── nav-link.blade.php # لینک‌های ناوبری
    └── breadcrumb.blade.php # مسیر صفحه
```

### 5.3 Performance Considerations

**نکات بهینه‌سازی عملکرد:**

1. **Blade Caching:**
```bash
# کامپایل قالب‌ها برای production
php artisan view:cache

# پاک کردن cache در development
php artisan view:clear
```

2. **Component Organization:**
```php
// ✅ صحیح: Components کوچک و متمرکز
<x-ui.button type="primary">ذخیره</x-ui.button>

// ❌ اشتباه: Component خیلی بزرگ و پیچیده
<x-entire-page-with-everything />
```

3. **Conditional Loading:**
```html
<!-- فقط در صورت نیاز CSS/JS بارگذاری شود -->
@if($needsCharts)
    <x-slot name="scripts">
        <script src="/js/charts.js"></script>
    </x-slot>
@endif
```

---

## 🔑 نکات کلیدی و مهم

### Grammar Flow خلاصه شده:

1. **Route Definition** → `web.php` تعریف مسیر
2. **View Creation** → `.blade.php` ایجاد فایل نمایش
3. **Component Creation** → `components/` directory
4. **Component Usage** → `<x-component-name>`
5. **Slot Integration** → `{{ $slot }}` برای محتوا

### بهترین روش‌ها:

✅ **DO:**
- همیشه از `.blade.php` extension استفاده کنید
- Components را کوچک و متمرکز نگه دارید
- از named slots برای انعطاف‌پذیری استفاده کنید
- attribute merging را به درستی پیاده‌سازی کنید

❌ **DON'T:**
- کد را در چندین فایل کپی نکنید
- Components خیلی پیچیده نسازید
- از inline PHP در templates سوءاستفاده نکنید
- cache را در development روشن نگذارید

### فوائد این Architecture:

1. **Maintainability**: نگهداری آسان‌تر کد
2. **Reusability**: استفاده مجدد از components
3. **Consistency**: یکنواختی در طراحی
4. **Performance**: بهینه‌سازی بهتر در production
5. **Team Collaboration**: همکاری بهتر تیم

---

*این Handbook طبق استانداردهای شرکت‌های بزرگ فناوری تهیه شده و همه مفاهیم گفته شده در ویدیو آموزشی را پوشش می‌دهد.*