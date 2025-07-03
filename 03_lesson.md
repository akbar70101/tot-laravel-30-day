# جزوه آموزشی حرفه‌ای Laravel: کامپوننت‌های Blade و مدیریت Layout

## 🗺️ نقشه ساختار درختی مباحث

```
Laravel Blade Components & Layout Management
├── 1. تنظیم Layout با Tailwind CSS
│   ├── ایجاد Layout Component
│   ├── اتصال Tailwind CSS
│   └── پیکربندی HTML Structure
├── 2. مفهوم Slots در Blade
│   ├── Default Slot
│   ├── Named Slots
│   └── تفاوت Props و Attributes
├── 3. مدیریت Navigation
│   ├── ایجاد Navigation Links
│   ├── Active State Management
│   └── Laravel Request Helper
├── 4. ایجاد کامپوننت‌های قابل استفاده مجدد
│   ├── NavLink Component
│   ├── Props Declaration
│   └── Conditional Rendering
└── 5. بهینه‌سازی و Best Practices
    ├── Accessibility Features
    ├── Component Organization
    └── Responsive Design
```

---

## 1️⃣ تنظیم Layout با Tailwind CSS

### 🔤 فلوی گرامری:
1. ایجاد فایل Layout Component در مسیر `resources/views/components/`
2. تعریف ساختار HTML پایه با استفاده از Tailwind Classes
3. اتصال Tailwind CSS از طریق CDN
4. پیکربندی Height و Background برای HTML/Body

### 📝 قطعه کد آموزشی:

```html
<!-- resources/views/components/layout.blade.php -->
<!DOCTYPE html>
<html class="h-full bg-gray-50">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Laravel App</title>
    <!-- اتصال Tailwind CSS از CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="h-full">
    <!-- Navigation Header -->
    <nav class="bg-gray-800">
        <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
            <!-- Navigation Content -->
            <div class="flex h-16 items-center justify-between">
                <!-- نمایش محتوای Navigation -->
            </div>
        </div>
    </nav>
    
    <!-- Main Content Area -->
    <main>
        <!-- محل قرارگیری محتوای صفحه -->
        {{ $slot }}
    </main>
</body>
</html>
```

### 🔍 فلوی معنایی و خروجی:
1. **HTML Structure**: ایجاد ساختار پایه صفحه با Navigation و Main Content
2. **Tailwind Classes**: استفاده از کلاس‌های `h-full` برای تنظیم ارتفاع کامل
3. **Background**: تنظیم رنگ پس‌زمینه با `bg-gray-50`
4. **خروجی**: صفحه‌ای با Navigation بالا و محتوای اصلی در پایین

### ⚙️ نکات فنی کلیدی:
1. **CDN Usage**: استفاده از CDN فقط برای مراحل توسعه، در production از build tools استفاده کنید
2. **Responsive Classes**: `sm:px-6 lg:px-8` برای پاسخگویی در اندازه‌های مختلف
3. **Container Max Width**: `max-w-7xl` برای محدود کردن عرض محتوا
4. **Height Management**: `h-full` روی html و body برای تنظیم ارتفاع کامل صفحه

### 🗺️ نقشه ارتباط فایل‌ها:
```
layout.blade.php (کامپوننت اصلی)
    ↓ استفاده در
home.blade.php, about.blade.php, contact.blade.php
    ↓ ارتباط با
web.php (routes) → Controllers → Views
```

---

## 2️⃣ مفهوم Slots در Blade

### 🔤 فلوی گرامری:
1. تعریف Default Slot با `{{ $slot }}`
2. ایجاد Named Slots با `<x-slot:name>`
3. استفاده از Named Slots در صفحات مختلف
4. تفکیک بین محتوای ثابت و متغیر

### 📝 قطعه کد آموزشی:

```html
<!-- resources/views/components/layout.blade.php -->
<div class="min-h-full">
    <header class="bg-white shadow">
        <div class="mx-auto max-w-7xl px-4 py-6 sm:px-6 lg:px-8">
            <!-- Named Slot برای عنوان صفحه -->
            <h1 class="text-3xl font-bold tracking-tight text-gray-900">
                {{ $heading }}
            </h1>
        </div>
    </header>
    
    <main>
        <div class="mx-auto max-w-7xl py-6 sm:px-6 lg:px-8">
            <!-- Default Slot برای محتوای اصلی -->
            {{ $slot }}
        </div>
    </main>
</div>
```

```html
<!-- resources/views/home.blade.php -->
<x-layout>
    <!-- Named Slot برای عنوان -->
    <x-slot:heading>
        Home Page
    </x-slot:heading>
    
    <!-- Default Slot برای محتوا -->
    <div class="bg-white">
        <p>Welcome to the home page!</p>
    </div>
</x-layout>
```

### 🔍 فلوی معنایی و خروجی:
1. **Named Slot**: `{{ $heading }}` محتوای عنوان را از `<x-slot:heading>` دریافت می‌کند
2. **Default Slot**: `{{ $slot }}` محتوای اصلی صفحه را نمایش می‌دهد
3. **Dynamic Content**: هر صفحه می‌تواند عنوان و محتوای مختلف داشته باشد
4. **خروجی**: صفحه‌ای با عنوان "Home Page" و محتوای "Welcome to the home page!"

### ⚙️ نکات فنی کلیدی:
1. **Slot Naming**: نام‌گذاری slots با `:` مانند `<x-slot:heading>`
2. **Variable Access**: دسترسی به named slots با `{{ $variableName }}`
3. **Content Separation**: تفکیک محتوای static از dynamic
4. **Flexibility**: امکان استفاده مجدد layout در صفحات مختلف

### 🗺️ نقشه ارتباط فایل‌ها:
```
layout.blade.php (تعریف slots)
    ↑ استفاده از
home.blade.php (پر کردن slots)
about.blade.php (پر کردن slots)
contact.blade.php (پر کردن slots)
```

---

## 3️⃣ مدیریت Navigation و Active States

### 🔤 فلوی گرامری:
1. ایجاد Navigation Links در Layout
2. استفاده از Laravel Request Helper
3. پیاده‌سازی Conditional Styling
4. تشخیص صفحه فعال با `request()->is()`

### 📝 قطعه کد آموزشی:

```html
<!-- resources/views/components/layout.blade.php -->
<nav class="bg-gray-800">
    <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
        <div class="flex h-16 items-center justify-between">
            <div class="flex items-center">
                <div class="flex-shrink-0">
                    <img class="h-8 w-8" src="/images/logo.png" alt="Logo">
                </div>
                <div class="hidden md:block">
                    <div class="ml-10 flex items-baseline space-x-4">
                        <!-- Home Link -->
                        <a href="/" 
                           class="{{ request()->is('/') ? 'bg-gray-900 text-white' : 'text-gray-300 hover:bg-gray-700 hover:text-white' }} rounded-md px-3 py-2 text-sm font-medium"
                           aria-current="{{ request()->is('/') ? 'page' : 'false' }}">
                            Home
                        </a>
                        
                        <!-- About Link -->
                        <a href="/about" 
                           class="{{ request()->is('about') ? 'bg-gray-900 text-white' : 'text-gray-300 hover:bg-gray-700 hover:text-white' }} rounded-md px-3 py-2 text-sm font-medium"
                           aria-current="{{ request()->is('about') ? 'page' : 'false' }}">
                            About
                        </a>
                        
                        <!-- Contact Link -->
                        <a href="/contact" 
                           class="{{ request()->is('contact') ? 'bg-gray-900 text-white' : 'text-gray-300 hover:bg-gray-700 hover:text-white' }} rounded-md px-3 py-2 text-sm font-medium"
                           aria-current="{{ request()->is('contact') ? 'page' : 'false' }}">
                            Contact
                        </a>
                    </div>
                </div>
            </div>
        </div>
    </div>
</nav>
```

### 🔍 فلوی معنایی و خروجی:
1. **Request Helper**: `request()->is('/')` بررسی می‌کند که آیا URL فعلی با الگوی داده شده مطابقت دارد
2. **Conditional Classes**: اگر صفحه فعال باشد کلاس‌های `bg-gray-900 text-white` اعمال می‌شود
3. **Hover States**: برای صفحات غیرفعال hover effects با `hover:bg-gray-700` فعال است
4. **خروجی**: Navigation با highlighting صحیح صفحه فعال

### ⚙️ نکات فنی کلیدی:
1. **Ternary Operator**: استفاده از `condition ? true_value : false_value`
2. **Accessibility**: `aria-current="page"` برای screen readers
3. **Responsive Design**: `hidden md:block` برای مخفی کردن در موبایل
4. **Pattern Matching**: `request()->is()` از regex patterns پشتیبانی می‌کند

### 🗺️ نقشه ارتباط فایل‌ها:
```
layout.blade.php (navigation logic)
    ↓ استفاده از
Laravel Request Object
    ↓ بررسی
web.php (routes: /, /about, /contact)
    ↓ رندر
home.blade.php, about.blade.php, contact.blade.php
```

---

## 4️⃣ ایجاد کامپوننت NavLink قابل استفاده مجدد

### 🔤 فلوی گرامری:
1. ایجاد کامپوننت جداگانه برای Navigation Links
2. تعریف Props و Attributes
3. استفاده از `@props` directive
4. پیاده‌سازی logic تشخیص active state

### 📝 قطعه کد آموزشی:

```php
{{-- resources/views/components/nav-link.blade.php --}}
@props(['active' => false])

<a {{ $attributes }}
   class="{{ $active ? 'bg-gray-900 text-white' : 'text-gray-300 hover:bg-gray-700 hover:text-white' }} rounded-md px-3 py-2 text-sm font-medium"
   aria-current="{{ $active ? 'page' : 'false' }}">
    {{ $slot }}
</a>
```

```html
<!-- resources/views/components/layout.blade.php - استفاده از NavLink -->
<div class="ml-10 flex items-baseline space-x-4">
    <!-- استفاده از کامپوننت NavLink -->
    <x-nav-link href="/" :active="request()->is('/')">
        Home
    </x-nav-link>
    
    <x-nav-link href="/about" :active="request()->is('about')">
        About
    </x-nav-link>
    
    <x-nav-link href="/contact" :active="request()->is('contact')">
        Contact
    </x-nav-link>
</div>
```

### 🔍 فلوی معنایی و خروجی:
1. **Props Declaration**: `@props(['active' => false])` تعریف می‌کند که کامپوننت یک prop به نام `active` دارد
2. **Attribute Merging**: `{{ $attributes }}` تمام attributes مانند `href` را به anchor tag اضافه می‌کند
3. **Dynamic Binding**: `:active="request()->is('/')"` مقدار boolean به prop منتقل می‌کند
4. **خروجی**: Navigation links با کد تمیز و قابل استفاده مجدد

### ⚙️ نکات فنی کلیدی:
1. **Props vs Attributes**: Props برای logic، Attributes برای HTML attributes
2. **Default Values**: تنظیم مقدار پیش‌فرض `false` برای prop
3. **Dynamic Binding**: استفاده از `:` برای ارسال expressions به props
4. **Component Reusability**: کاهش تکرار کد و افزایش maintainability

### 🗺️ نقشه ارتباط فایل‌ها:
```
nav-link.blade.php (کامپوننت قابل استفاده مجدد)
    ↑ استفاده در
layout.blade.php (navigation section)
    ↑ استفاده در
تمام صفحات (home, about, contact)
    ↓ داده‌ها از
Laravel Request Object (route detection)
```

---

## 5️⃣ بهینه‌سازی و Best Practices

### 🔤 فلوی گرامری:
1. تفکیک منطق component از presentation
2. پیاده‌سازی Accessibility features
3. مدیریت responsive design
4. سازماندهی component structure

### 📝 قطعه کد آموزشی:

```php
{{-- resources/views/components/nav-link.blade.php - نسخه بهینه --}}
@props([
    'active' => false,
    'href' => '#'
])

@php
    // تعریف classes در PHP برای خوانایی بهتر
    $classes = $active 
        ? 'bg-gray-900 text-white' 
        : 'text-gray-300 hover:bg-gray-700 hover:text-white';
    
    $baseClasses = 'rounded-md px-3 py-2 text-sm font-medium transition-colors duration-200';
    $finalClasses = $baseClasses . ' ' . $classes;
@endphp

<a href="{{ $href }}"
   class="{{ $finalClasses }}"
   aria-current="{{ $active ? 'page' : 'false' }}"
   {{ $attributes->except(['active', 'href']) }}>
    {{ $slot }}
</a>
```

```html
<!-- resources/views/components/layout.blade.php - Mobile Navigation -->
<!-- Mobile menu button -->
<div class="md:hidden">
    <button type="button" 
            class="inline-flex items-center justify-center rounded-md bg-gray-800 p-2 text-gray-400 hover:bg-gray-700 hover:text-white"
            aria-controls="mobile-menu" 
            aria-expanded="false">
        <span class="sr-only">Open main menu</span>
        <!-- Menu Icon -->
        <svg class="block h-6 w-6" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" d="M3.75 6.75h16.5M3.75 12h16.5m-16.5 5.25h16.5" />
        </svg>
    </button>
</div>

<!-- Mobile Navigation Menu -->
<div class="md:hidden" id="mobile-menu">
    <div class="space-y-1 px-2 pb-3 pt-2 sm:px-3">
        <x-nav-link href="/" :active="request()->is('/')" class="block">
            Home
        </x-nav-link>
        <x-nav-link href="/about" :active="request()->is('about')" class="block">
            About
        </x-nav-link>
        <x-nav-link href="/contact" :active="request()->is('contact')" class="block">
            Contact
        </x-nav-link>
    </div>
</div>
```

### 🔍 فلوی معنایی و خروجی:
1. **Class Management**: تفکیک classes به base و conditional برای مدیریت بهتر
2. **Transition Effects**: اضافه کردن `transition-colors duration-200` برای smooth animations
3. **Attribute Filtering**: `$attributes->except()` برای جلوگیری از duplicate attributes
4. **خروجی**: Navigation responsive با mobile menu و animations

### ⚙️ نکات فنی کلیدی:
1. **Performance**: استفاده از CSS transitions به جای JavaScript animations
2. **Accessibility**: `aria-controls`, `aria-expanded`, `sr-only` برای screen readers
3. **Responsive Strategy**: Mobile-first approach با `md:hidden` و `md:block`
4. **Code Organization**: تفکیک logic در `@php` blocks برای خوانایی

### 🗺️ نقشه ارتباط فایل‌ها:
```
Project Structure:
├── resources/views/components/
│   ├── layout.blade.php (اصلی)
│   └── nav-link.blade.php (قابل استفاده مجدد)
├── resources/views/
│   ├── home.blade.php
│   ├── about.blade.php
│   └── contact.blade.php
├── routes/web.php (تعریف routes)
└── app/Http/Controllers/ (controllers)

Data Flow:
Request → Router → Controller → View → Components → Output
```

---

## 📋 خلاصه و نکات کلیدی پروژه

### 🎯 اهداف محقق شده:
1. **Component-Based Architecture**: ایجاد components قابل استفاده مجدد
2. **Dynamic Navigation**: سیستم navigation با active state detection
3. **Responsive Design**: پشتیبانی از desktop و mobile
4. **Clean Code**: کد تمیز و maintainable با best practices

### 🔧 ابزارها و تکنولوژی‌های استفاده شده:
- **Laravel Blade**: Template engine برای components
- **Tailwind CSS**: Utility-first CSS framework
- **Laravel Request Helper**: برای route detection
- **Accessibility Features**: ARIA attributes برای screen readers

این جزوه رویکردی مدرن و حرفه‌ای برای توسعه وب با Laravel ارائه می‌دهد که در شرکت‌های بزرگ فناوری قابل استفاده است.