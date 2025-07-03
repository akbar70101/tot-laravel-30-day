# جزوه حرفه‌ای Laravel Blade Components و Data Passing

## نقشه ساختار درختی مباحث

```
Laravel Blade Development
├── 1. Blade Components (پیشرفته)
│   ├── Props و Attributes
│   ├── Default Values
│   └── Expression Binding (:)
├── 2. Conditional Rendering
│   ├── @if/@else/@endif
│   ├── Dynamic Tag Generation
│   └── Type-based Component Rendering
├── 3. Route to View Data Passing
│   ├── Simple Variable Passing
│   ├── Array Data Structures
│   └── Complex Data Binding
├── 4. Blade Directives
│   ├── @foreach Loops
│   ├── Variable Extraction
│   └── Template Iteration
└── 5. Route Management
    ├── Dynamic Route Creation
    ├── View Function Parameters
    └── Data Flow Architecture
```

---

## 1. Laravel Blade Components - Props و Expression Binding

### قسمت اول: فلوی گرامری

1. تعریف prop در component با استفاده از آرایه
2. استفاده از colon (:) برای expression binding
3. تمایز بین string و boolean values
4. استفاده از request helper برای URL detection

### قسمت دوم: قطعه کد آموزشی

**فایل: `resources/views/components/nav-link.blade.php`**

```php
@props(['active' => false])

{{-- Component که active prop را دریافت می‌کند --}}
<a {{ $attributes }} 
   class="{{ $active ? 'bg-gray-900 text-white' : 'text-gray-300 hover:text-white' }} px-3 py-2 rounded-md">
    {{ $slot }}
    {{-- $slot محتوای داخل component را نمایش می‌دهد --}}
</a>
```

**فایل: `resources/views/components/layout.blade.php`**

```php
{{-- استفاده از component با expression binding --}}
<x-nav-link href="/" :active="request()->is('/')">Home</x-nav-link>
<x-nav-link href="/about" :active="request()->is('about')">About</x-nav-link>
<x-nav-link href="/contact" :active="request()->is('contact')">Contact</x-nav-link>

{{-- خروجی: Navigation links با active state مناسب --}}
```

### قسمت سوم: فلوی معنایی و توضیح خروجی

1. `@props(['active' => false])` یک prop با default value false تعریف می‌کند
2. `:active="request()->is('/')"` یک PHP expression ارسال می‌کند نه string
3. `request()->is('/')` چک می‌کند که آیا URL فعلی "/" است
4. شرط ternary CSS class مناسب را برمی‌گرداند
5. خروجی: لینک‌های navigation که link فعلی highlight شده است

### قسمت چهارم: نکات فنی کلیدی

1. **Expression Binding**: `:` استفاده کنید تا PHP expressions ارسال کنید
2. **Props vs Attributes**: Props باید صراحت تعریف شوند، attributes خودکار هستند
3. **Default Values**: همیشه default value برای props تعریف کنید
4. **Request Helper**: `request()->is()` برای URL matching استفاده کنید
5. **Truthy Values**: strings همیشه truthy هستند، حتی "false"

### قسمت پنجم: نقشه ارتباط فایل‌ها

```
nav-link.blade.php (Component)
    ↑ استفاده می‌شود در
layout.blade.php (Layout)
    ↑ extend می‌شود در
home.blade.php, about.blade.php, contact.blade.php (Views)
    ↑ render می‌شوند از
web.php (Routes)
```

---

## 2. Conditional Rendering و Dynamic Components

### قسمت اول: فلوی گرامری

1. استفاده از @if/@else/@endif برای شرطی سازی
2. ایجاد prop جدید برای type component
3. تولید dynamic HTML tags
4. مدیریت conditional attributes

### قسمت دوم: قطعه کد آموزشی

**فایل: `resources/views/components/nav-link.blade.php`**

```php
@props(['active' => false, 'type' => 'a'])

{{-- Conditional rendering برای tag type --}}
@if($type === 'a')
    <a {{ $attributes }} 
       class="{{ $active ? 'bg-gray-900 text-white' : 'text-gray-300 hover:text-white' }}">
        {{ $slot }}
    </a>
@else
    <button {{ $attributes }} 
            class="{{ $active ? 'bg-gray-900 text-white' : 'text-gray-300 hover:text-white' }}">
        {{ $slot }}
    </button>
@endif

{{-- خروجی: یا anchor tag یا button tag بسته به type prop --}}
```

**فایل: `resources/views/components/layout.blade.php`**

```php
{{-- استفاده از component با type مختلف --}}
<x-nav-link href="/" :active="request()->is('/')">Home</x-nav-link>
<x-nav-link href="/about" :active="request()->is('about')">About</x-nav-link>
<x-nav-link href="/contact" type="button" :active="request()->is('contact')">Contact</x-nav-link>

{{-- خروجی: دو anchor و یک button element --}}
```

### قسمت سوم: فلوی معنایی و توضیح خروجی

1. `type` prop تعیین می‌کند که چه نوع HTML element تولید شود
2. `@if($type === 'a')` چک می‌کند که آیا type برابر 'a' است
3. `@else` برای تمام حالات دیگر (در اینجا button) اجرا می‌شود
4. attributes و classes در هر دو حالت حفظ می‌شوند
5. خروجی: flexible component که می‌تواند anchor یا button باشد

### قسمت چهارم: نکات فنی کلیدی

1. **Blade Directives**: `@if/@else/@endif` بهتر از PHP tags هستند
2. **Type Safety**: همیشه strict comparison (`===`) استفاده کنید
3. **DRY Principle**: کد تکراری را کاهش دهید
4. **Attribute Inheritance**: `{{ $attributes }}` همه attributes را منتقل می‌کند
5. **Default Behavior**: type پیش‌فرض 'a' برای backward compatibility

### قسمت پنجم: نقشه ارتباط فایل‌ها

```
nav-link.blade.php (Flexible Component)
    ↑ Props: type, active
layout.blade.php (Implementation)
    ↑ مشخص می‌کند که کدام link چه type دارد
Views (home, about, contact)
    ↑ inherit layout behavior
```

---

## 3. Route to View Data Passing

### قسمت اول: فلوی گرامری

1. استفاده از آرایه به عنوان دومین پارامتر view()
2. تبدیل array keys به variables در view
3. ارسال ساده‌ترین نوع داده (string)
4. دسترسی به variables در template

### قسمت دوم: قطعه کد آموزشی

**فایل: `routes/web.php`**

```php
<?php
use Illuminate\Support\Facades\Route;

{{-- Route با data passing ساده --}}
Route::get('/', function () {
    return view('home', [
        'greeting' => 'Hello',        // string variable
        'name' => 'Larry Robot'       // string variable
    ]);
    {{-- خروجی: view با دو متغیر greeting و name --}}
});
```

**فایل: `resources/views/home.blade.php`**

```php
<x-layout>
    {{-- دسترسی به variables ارسال شده از route --}}
    <h1>{{ $greeting }} {{ $name }}</h1>
    
    {{-- خروجی: "Hello Larry Robot" --}}
</x-layout>
```

### قسمت سوم: فلوی معنایی و توضیح خروجی

1. `view('home', [...])` view را با data load می‌کند
2. Array keys ('greeting', 'name') به variables تبدیل می‌شوند
3. `{{ $greeting }}` متغیر greeting را در template نمایش می‌دهد
4. Variables در تمام view قابل دسترسی هستند
5. خروجی: صفحه با dynamic content

### قسمت چهارم: نکات فنی کلیدی

1. **Data Binding**: array keys خودکار به variables تبدیل می‌شوند
2. **Variable Scope**: variables در تمام view در دسترس هستند
3. **Type Safety**: هر نوع داده PHP قابل ارسال است
4. **Template Engine**: Blade خودکار variables را extract می‌کند
5. **Security**: همیشه از `{{ }}` برای safe output استفاده کنید

### قسمت پنجم: نقشه ارتباط فایل‌ها

```
web.php (Route Definition)
    ↓ ارسال data
home.blade.php (View)
    ↓ استفاده از
layout.blade.php (Layout Component)
    ↓ contains
nav-link.blade.php (Sub-components)
```

---

## 4. Complex Data Structures و Loops

### قسمت اول: فلوی گرامری

1. ایجاد arrays پیچیده با nested structures
2. استفاده از @foreach برای iteration
3. دسترسی به array elements در loop
4. ترکیب HTML با dynamic data

### قسمت دوم: قطعه کد آموزشی

**فایل: `routes/web.php`**

```php
Route::get('/jobs', function () {
    return view('jobs', [
        'jobs' => [                           // Array از jobs
            [
                'title' => 'Director',        // Job title
                'salary' => '$50,000'         // Job salary
            ],
            [
                'title' => 'Programmer', 
                'salary' => '$10,000'
            ],
            [
                'title' => 'Teacher',
                'salary' => '$40,000'
            ]
        ]
    ]);
    {{-- خروجی: view با array پیچیده از job listings --}}
});
```

**فایل: `resources/views/jobs/index.blade.php`**

```php
<x-layout>
    <h1>Available Jobs</h1>
    
    <ul>
        {{-- Loop through jobs array --}}
        @foreach($jobs as $job)
            <li>
                <strong>{{ $job['title'] }}</strong>: 
                Pays {{ $job['salary'] }} per year
            </li>
            {{-- خروجی: هر job به صورت list item --}}
        @endforeach
    </ul>
</x-layout>
```

### قسمت سوم: فلوی معنایی و توضیح خروجی

1. `$jobs` یک array از associative arrays است
2. `@foreach($jobs as $job)` روی هر element iterate می‌کند
3. `$job['title']` به title هر job دسترسی دارد
4. `@endforeach` پایان loop را مشخص می‌کند
5. خروجی: لیست HTML از تمام job listings

### قسمت چهارم: نکات فنی کلیدی

1. **Associative Arrays**: از key-value pairs برای structured data استفاده کنید
2. **Blade Loops**: `@foreach` بهتر از PHP foreach است
3. **Array Access**: bracket notation (`$job['key']`) برای array access
4. **Template Structure**: HTML structure را با dynamic content ترکیب کنید
5. **End Directives**: همیشه Blade directives را close کنید

### قسمت پنجم: نقشه ارتباط فایل‌ها

```
web.php (Complex Data Source)
    ↓ $jobs array
jobs/index.blade.php (List View)
    ↓ extends
layout.blade.php (Base Layout)
    ↓ includes
nav-link.blade.php (Navigation)
```

---

## 5. Advanced Route Management و View Architecture

### قسمت اول: فلوی گرامری

1. تغییر route URLs برای بهتر organization
2. انتقال data بین routes مختلف
3. جداسازی concerns برای maintainability
4. ایجاد dedicated views برای specific functionality

### قسمت دوم: قطعه کد آموزشی

**فایل: `routes/web.php`**

```php
<?php
use Illuminate\Support\Facades\Route;

{{-- Home route - simple welcome page --}}
Route::get('/', function () {
    return view('home');
    {{-- خروجی: صفحه اصلی ساده --}}
});

{{-- Jobs route - complex data structure --}}
Route::get('/jobs', function () {
    $jobs = [                                 // Structured job data
        ['title' => 'Director', 'salary' => '$50,000'],
        ['title' => 'Programmer', 'salary' => '$10,000'], 
        ['title' => 'Teacher', 'salary' => '$40,000']
    ];
    
    return view('jobs.index', compact('jobs'));
    {{-- خروجی: jobs listing page با dynamic data --}}
});
```

**فایل: `resources/views/jobs/index.blade.php`**

```php
<x-layout>
    <div class="container mx-auto px-4">
        <h1 class="text-3xl font-bold mb-6">Job Listings</h1>
        
        @if(count($jobs) > 0)
            <div class="grid gap-4">
                @foreach($jobs as $job)
                    <div class="border rounded-lg p-4 shadow-sm">
                        <h2 class="text-xl font-semibold">{{ $job['title'] }}</h2>
                        <p class="text-gray-600">Salary: {{ $job['salary'] }} per year</p>
                    </div>
                    {{-- خروجی: هر job در card زیبا --}}
                @endforeach
            </div>
        @else
            <p class="text-gray-500">No jobs available at the moment.</p>
        @endif
    </div>
</x-layout>
```

### قسمت سوم: فلوی معنایی و توضیح خروجی

1. Routes جداگانه برای concerns مختلف تعریف شده‌اند
2. `compact('jobs')` معادل `['jobs' => $jobs]` است
3. `@if(count($jobs) > 0)` چک می‌کند که آیا job وجود دارد
4. Grid layout برای responsive design استفاده شده
5. خروجی: صفحه professional job listings با error handling

### قسمت چهارم: نکات فنی کلیدی

1. **Route Organization**: routes مختلف برای functionality مختلف
2. **compact() Helper**: راه مختصر برای passing variables
3. **Conditional Rendering**: `@if/@else` برای edge cases
4. **Responsive Design**: CSS classes برای mobile-friendly UI
5. **Error Handling**: empty state برای بهتر UX

### قسمت پنجم: نقشه ارتباط فایل‌ها

```
Application Architecture:

web.php (Route Layer)
    ├── / → home.blade.php
    └── /jobs → jobs/index.blade.php
                    ↓ extends
                layout.blade.php (Layout Layer)
                    ↓ includes
                nav-link.blade.php (Component Layer)

Data Flow:
Route → Controller Logic → View → Components → Browser
```

---

## خلاصه معماری کلی

این جزوه پوشش کامل Laravel Blade ecosystem را ارائه می‌دهد که شامل:

1. **Component Architecture**: قابلیت استفاده مجدد و maintainable components
2. **Data Flow**: از routes تا views با type safety
3. **Template Engine**: Blade directives برای clean syntax  
4. **Responsive Design**: modern UI patterns
5. **Best Practices**: industry-standard approaches

این approach در شرکت‌های tech بزرگ مانند Facebook، Google، و Amazon به عنوان standard professional development محسوب می‌شود.