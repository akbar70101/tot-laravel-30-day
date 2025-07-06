# جزوه حرفه‌ای Laravel - مباحث پیشرفته

## نقشه ساختار درختی مباحث

```
Laravel Advanced Concepts
├── 1. Namespaces & Autoloading (PSR-4)
│   ├── مفهوم Namespace
│   ├── استاندارد PSR-4
│   └── Composer Autoloading
├── 2. Models & Data Encapsulation
│   ├── ایجاد Model Class
│   ├── Static Methods
│   └── Type Hinting
├── 3. Error Handling & HTTP Status Codes
│   ├── Exception Handling
│   ├── 404 Error Management
│   └── Laravel abort() Helper
├── 4. Database Configuration
│   ├── Environment Variables
│   ├── Database Connections
│   └── SQLite Configuration
└── 5. Database Management Tools
    ├── Artisan Commands
    ├── Migration System
    └── Database GUI Tools
```

---

## 1. Namespaces & PSR-4 Autoloading

### توضیحات جامع مبحث

مفهوم Namespace در Laravel یکی از اصولی‌ترین مباحث توسعه نرم‌افزار مدرن است که به سازماندهی کد کمک می‌کند. این مفهوم مشابه سازماندهی پوشه‌های موسیقی در کامپیوتر عمل می‌کند، به طوری که هر فایل در یک مسیر منحصر به فرد قرار می‌گیرد. Laravel از استاندارد PSR-4 برای بارگذاری خودکار فایل‌ها استفاده می‌کند که در فایل composer.json تعریف شده است. این سیستم باعث می‌شود تا نام‌های کلاس‌ها تکراری نباشند و هر کلاس در مسیر مشخصی قرار گیرد. برای مثال، کلاس Job می‌تواند در چندین مکان مختلف وجود داشته باشد (مانند Queue Job، Interface، یا Model) اما با استفاده از Namespace هر کدام مسیر منحصر به فردی خواهند داشت. این روش باعث می‌شود تا کد خواناتر، قابل نگهداری‌تر و استانداردتر باشد.

### قطعه کد آموزشی

```php
// فایل: composer.json
{
    "autoload": {
        "psr-4": {
            "App\\": "app/"  // تعریف namespace اصلی
        }
    }
}

// فایل: app/Models/Job.php
<?php

namespace App\Models;  // تعریف namespace برای Model

use Illuminate\Support\Arr;  // Import کردن کلاس مورد نیاز

class Job
{
    // Static method برای دریافت تمام jobها
    public static function all(): array
    {
        return [
            ['id' => 1, 'title' => 'Director'],
            ['id' => 2, 'title' => 'Programmer'],
            ['id' => 3, 'title' => 'Teacher']
        ];
    }
}

// فایل: routes/web.php
<?php

use App\Models\Job;  // استفاده از namespace برای import

Route::get('/jobs', function () {
    $jobs = Job::all();  // فراخوانی static method
    return view('jobs', compact('jobs'));
});
```

### فلوی معنایی و گرامری

1. **تعریف Namespace در composer.json**: Laravel با استفاده از `"App\\": "app/"` مشخص می‌کند که namespace اصلی `App` در پوشه `app` قرار دارد
2. **تعریف Namespace در فایل PHP**: با `namespace App\Models;` مشخص می‌کنیم که این کلاس در زیر مجموعه Models قرار دارد
3. **Import کردن کلاس‌های مورد نیاز**: با `use Illuminate\Support\Arr;` کلاس‌های Laravel را وارد می‌کنیم
4. **استفاده از Namespace در Routes**: با `use App\Models\Job;` کلاس Job را برای استفاده در routes وارد می‌کنیم
5. **فراخوانی کلاس**: با `Job::all()` متد static کلاس را فراخوانی می‌کنیم

### نکات فنی کلیدی

1. **استاندارد PSR-4**: این استاندارد مشخص می‌کند که نام namespace باید با ساختار پوشه‌ها مطابقت داشته باشد
2. **Autoloading**: Composer به طور خودکار فایل‌ها را بارگذاری می‌کند بدون نیاز به require یا include
3. **Case Sensitivity**: نام‌های namespace به حروف کوچک و بزرگ حساس هستند
4. **Backslash Usage**: در PHP برای جداسازی namespace از `\` استفاده می‌شود
5. **Import vs Full Path**: می‌توان از `use` برای import یا از مسیر کامل استفاده کرد

### نقشه ارتباط با سایر فایل‌ها

```
Project Structure:
├── composer.json (تعریف PSR-4 mapping)
├── app/
│   └── Models/
│       └── Job.php (کلاس اصلی)
├── routes/
│   └── web.php (استفاده از Job model)
└── resources/views/
    └── jobs.blade.php (نمایش داده‌ها)

Data Flow:
composer.json → Auto-register namespaces
Job.php → Define model with namespace
web.php → Import and use Job model
jobs.blade.php → Display job data
```

---

## 2. Models & Data Encapsulation

### توضیحات جامع مبحث

مفهوم Model در Laravel قلب معماری MVC محسوب می‌شود و وظیفه مدیریت داده‌ها و منطق کسب‌وکار را بر عهده دارد. Data Encapsulation یکی از اصول اساسی برنامه‌نویسی شی‌گرا است که به معنای کپسوله‌سازی داده‌ها و رفتارهای مرتبط در یک کلاس است. در Laravel، Model ها نه تنها داده‌ها را ذخیره می‌کنند بلکه متدهای مرتبط با آن داده‌ها را نیز در خود جای می‌دهند. این رویکرد باعث می‌شود تا کد تمیزتر، قابل تست‌تر و قابل نگهداری‌تر باشد. Static methods در Model ها برای عملیاتی استفاده می‌شوند که نیاز به instance کردن کلاس ندارند. Type hinting در PHP کمک می‌کند تا خطاها زودتر شناسایی شوند و کد قابل اعتمادتر باشد. این مفهوم باعث جداسازی منطق کسب‌وکار از لایه‌های دیگر برنامه می‌شود.

### قطعه کد آموزشی

```php
// فایل: app/Models/Job.php
<?php

namespace App\Models;

use Illuminate\Support\Arr;

class Job
{
    // Static method برای دریافت تمام jobs
    public static function all(): array
    {
        return [
            ['id' => 1, 'title' => 'Director', 'salary' => '$50,000'],
            ['id' => 2, 'title' => 'Programmer', 'salary' => '$60,000'],
            ['id' => 3, 'title' => 'Teacher', 'salary' => '$40,000']
        ];
    }

    // Static method برای پیدا کردن یک job خاص
    public static function find(int $id): ?array
    {
        $jobs = static::all();  // فراخوانی method دیگر از همین کلاس
        
        // استفاده از Laravel Collection برای جستجو
        return Arr::first($jobs, function ($job) use ($id) {
            return $job['id'] == $id;
        });
        
        // خروجی: array یا null در صورت عدم وجود
    }
}

// فایل: routes/web.php
<?php

use App\Models\Job;

Route::get('/jobs/{id}', function ($id) {
    $job = Job::find($id);  // فراخوانی متد find
    
    // بررسی وجود job
    if (!$job) {
        abort(404);  // نمایش صفحه 404
    }
    
    return view('job', compact('job'));
});
```

### فلوی معنایی و گرامری

1. **تعریف کلاس Model**: کلاس Job به عنوان Model برای مدیریت داده‌های job تعریف می‌شود
2. **Static Methods**: متدهای `all()` و `find()` به صورت static تعریف شده‌اند تا بدون instance کردن قابل استفاده باشند
3. **Type Hinting**: `int $id` مشخص می‌کند که پارامتر باید عدد صحیح باشد
4. **Return Type**: `: ?array` نشان می‌دهد که خروجی یا array یا null است
5. **Static Reference**: `static::all()` برای فراخوانی متد دیگر از همین کلاس استفاده می‌شود
6. **Laravel Helper**: `Arr::first()` برای جستجو در array استفاده می‌شود

### نکات فنی کلیدی

1. **Static vs Instance Methods**: Static methods برای عملیات کلی، Instance methods برای عملیات روی یک رکورد خاص
2. **Type Safety**: Type hinting کمک می‌کند خطاها زودتر شناسایی شوند
3. **Null Safety**: با `?array` مشخص می‌کنیم که null نیز خروجی قابل قبولی است
4. **Single Responsibility**: هر متد یک وظیفه مشخص دارد
5. **Laravel Collections**: استفاده از Helper های Laravel برای manipulation داده‌ها

### نقشه ارتباط با سایر فایل‌ها

```
Model Architecture:
├── Job.php (Business Logic)
│   ├── all() → Return all jobs
│   └── find($id) → Find specific job
├── web.php (Route Handler)
│   ├── Import Job model
│   ├── Call Job::find()
│   └── Handle response/error
└── Views/
    ├── jobs.blade.php (List view)
    └── job.blade.php (Single job view)

Data Flow:
Request → Route → Job::find() → Data/null → View/404
```

---

## 3. Error Handling & HTTP Status Codes

### توضیحات جامع مبحث

مدیریت خطا یکی از اصولی‌ترین مباحث در توسعه نرم‌افزار است که تعیین می‌کند برنامه در مواجهه با شرایط غیرمنتظره چگونه رفتار کند. در Laravel، سیستم مدیریت خطا بسیار پیشرفته و قدرتمند است. HTTP Status Codes استانداردهای بین‌المللی هستند که نشان می‌دهند درخواست کاربر چه نتیجه‌ای داشته است. کد 404 به معنای "Not Found" است و زمانی استفاده می‌شود که منبع درخواستی وجود نداشته باشد. Laravel با helper function به نام `abort()` این فرآیند را بسیار ساده کرده است. این تابع نه تنها HTTP status code را تنظیم می‌کند بلکه صفحه خطای مناسب را نیز نمایش می‌دهد. مدیریت صحیح خطا باعث بهبود تجربه کاربری و افزایش امنیت برنامه می‌شود. همچنین به SEO و قابلیت نگهداری برنامه کمک زیادی می‌کند.

### قطعه کد آموزشی

```php
// فایل: routes/web.php
<?php

use App\Models\Job;

Route::get('/jobs/{id}', function ($id) {
    // تلاش برای پیدا کردن job
    $job = Job::find($id);
    
    // بررسی وجود job - راه اول
    if (is_null($job)) {
        abort(404);  // نمایش صفحه 404
    }
    
    // یا می‌توان از روش کوتاه‌تر استفاده کرد
    // if (!$job) {
    //     abort(404, 'Job not found');
    // }
    
    return view('job', compact('job'));
});

// فایل: app/Models/Job.php
<?php

namespace App\Models;

use Illuminate\Support\Arr;

class Job
{
    public static function find(int $id): ?array
    {
        $jobs = static::all();
        
        // جستجو در jobs
        $job = Arr::first($jobs, function ($job) use ($id) {
            return $job['id'] == $id;
        });
        
        // خروجی: array یا null
        return $job;
    }
    
    // متد جایگزین با Exception handling
    public static function findOrFail(int $id): array
    {
        $job = static::find($id);
        
        if (!$job) {
            abort(404, "Job with ID {$id} not found");
        }
        
        return $job;
    }
}
```

### فلوی معنایی و گرامری

1. **دریافت پارامتر**: `$id` از URL دریافت می‌شود
2. **فراخوانی متد جستجو**: `Job::find($id)` برای یافتن job اجرا می‌شود
3. **بررسی null**: `is_null($job)` یا `!$job` برای بررسی وجود نتیجه
4. **اجرای abort()**: در صورت عدم وجود، `abort(404)` اجرا می‌شود
5. **Laravel Exception Handling**: Laravel به طور خودکار Exception را handle می‌کند
6. **نمایش صفحه خطا**: صفحه 404 مناسب نمایش داده می‌شود

### نکات فنی کلیدی

1. **HTTP Status Codes**: 404 برای Not Found، 500 برای Server Error، 403 برای Forbidden
2. **Laravel abort() Helper**: تابع قدرتمند برای مدیریت خطا با پیام اختیاری
3. **Exception Bubbling**: Laravel خطاها را به سطح بالاتر منتقل می‌کند
4. **Custom Error Pages**: می‌توان صفحات خطای سفارشی ایجاد کرد
5. **Logging**: Laravel تمام خطاها را به طور خودکار log می‌کند

### نقشه ارتباط با سایر فایل‌ها

```
Error Handling Flow:
├── routes/web.php (Error Detection)
│   ├── Check for null/empty data
│   └── Call abort() helper
├── Laravel Core (Exception Handling)
│   ├── Catch abort() exception
│   ├── Set HTTP status code
│   └── Render error page
└── resources/views/errors/
    ├── 404.blade.php (Custom 404 page)
    └── 500.blade.php (Server error page)

Error Flow:
Request → Route → Model::find() → null → abort(404) → Laravel Handler → 404 Page
```

---

## 4. Database Configuration & Environment Variables

### توضیحات جامع مبحث

مدیریت پیکربندی دیتابیس یکی از اساسی‌ترین مباحث در توسعه نرم‌افزار است که امنیت و قابلیت انطباق برنامه را تضمین می‌کند. در Laravel، تمام تنظیمات حساس و قابل تغییر در فایل `.env` نگهداری می‌شوند. این فایل حاوی اطلاعات مهمی مانند اتصال دیتابیس، کلیدهای API، تنظیمات debug و سایر پیکربندی‌های محیطی است. Laravel به طور پیش‌فرض از SQLite استفاده می‌کند که یک دیتابیس فایل محور است و برای پروژه‌های کوچک و متوسط بسیار مناسب است. مزیت اصلی SQLite عدم نیاز به سرور دیتابیس جداگانه است. Environment Variables امکان تغییر تنظیمات در محیط‌های مختلف (Development, Testing, Production) را بدون تغییر کد فراهم می‌کند. این روش از اصول Twelve-Factor App پیروی می‌کند و امنیت برنامه را افزایش می‌دهد.

### قطعه کد آموزشی

```php
// فایل: .env
APP_NAME=Laravel
APP_ENV=local
APP_KEY=base64:generated_key_here
APP_DEBUG=true
APP_URL=http://localhost

# Database Configuration
DB_CONNECTION=sqlite
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=/path/to/database/database.sqlite
DB_USERNAME=
DB_PASSWORD=

# Cache Configuration
CACHE_DRIVER=file
SESSION_DRIVER=file

# API Keys (example)
API_KEY=your_secret_api_key_here

// فایل: config/database.php
<?php

return [
    'default' => env('DB_CONNECTION', 'mysql'),
    
    'connections' => [
        'sqlite' => [
            'driver' => 'sqlite',
            'database' => env('DB_DATABASE', database_path('database.sqlite')),
            'prefix' => '',
            'foreign_key_constraints' => env('DB_FOREIGN_KEYS', true),
        ],
        
        'mysql' => [
            'driver' => 'mysql',
            'host' => env('DB_HOST', '127.0.0.1'),
            'port' => env('DB_PORT', '3306'),
            'database' => env('DB_DATABASE', 'forge'),
            'username' => env('DB_USERNAME', 'forge'),
            'password' => env('DB_PASSWORD', ''),
        ],
    ],
];

// فایل: app/Services/ExampleService.php
<?php

namespace App\Services;

class ExampleService
{
    private $apiKey;
    
    public function __construct()
    {
        // دریافت API Key از environment variable
        $this->apiKey = env('API_KEY');
    }
    
    public function callExternalAPI()
    {
        // استفاده از API Key در درخواست
        $headers = [
            'Authorization' => 'Bearer ' . $this->apiKey,
            'Content-Type' => 'application/json'
        ];
        
        return "API call with secure key";
    }
}
```

### فلوی معنایی و گرامری

1. **تعریف متغیرهای محیطی**: در `.env` متغیرهای مختلف تعریف می‌شوند
2. **خواندن متغیرها**: `env()` helper برای خواندن متغیرهای محیطی استفاده می‌شود
3. **تنظیمات پیش‌فرض**: مقدار دوم `env()` به عنوان پیش‌فرض استفاده می‌شود
4. **آرایه پیکربندی**: تنظیمات در config files به صورت آرایه تعریف می‌شوند
5. **دریافت در کلاس**: `env('API_KEY')` برای دریافت کلید API استفاده می‌شود
6. **استفاده ایمن**: اطلاعات حساس به صورت مخفی در service ها استفاده می‌شوند

### نکات فنی کلیدی

1. **Security**: فایل `.env` هرگز در version control قرار نگیرد
2. **Environment Separation**: تنظیمات متفاوت برای محیط‌های مختلف
3. **Default Values**: همیشه مقدار پیش‌فرض در `env()` تعریف کنید
4. **Config Caching**: در production از `config:cache` استفاده کنید
5. **Validation**: همیشه متغیرهای محیطی را اعتبارسنجی کنید

### نقشه ارتباط با سایر فایل‌ها

```
Environment Configuration:
├── .env (Environment Variables)
│   ├── Database settings
│   ├── Application settings
│   └── Third-party API keys
├── config/
│   ├── database.php (Database config)
│   ├── app.php (Application config)
│   └── services.php (Service configs)
└── app/
    ├── Services/ (Business logic)
    └── Models/ (Database models)

Config Flow:
.env → config/database.php → Laravel Database → Models → Application
```

---

## 5. Database Management & Artisan Commands

### توضیحات جامع مبحث

سیستم مدیریت دیتابیس Laravel شامل ابزارهای قدرتمندی برای ایجاد، مدیریت و نگهداری دیتابیس است. Artisan CLI یکی از قدرتمندترین ابزارهای Laravel محسوب می‌شود که دسترسی به صدها دستور مختلف برای مدیریت پروژه فراهم می‌کند. Migration سیستم version control برای دیتابیس است که امکان ایجاد، تغییر و مدیریت جداول را فراهم می‌کند. هنگام نصب Laravel، migration های پیش‌فرض به طور خودکار اجرا می‌شوند تا جداول اساسی ایجاد شوند. برای مدیریت دیتابیس، ابزارهای GUI مانند TablePlus توصیه می‌شوند که رابط کاربری بصری و قدرتمند برای کار با انواع دیتابیس ارائه می‌دهند. این ابزارها از MySQL، PostgreSQL، SQLite و حتی Redis پشتیبانی می‌کنند. درک صحیح این ابزارها برای توسعه‌دهندگان حرفه‌ای ضروری است.

### قطعه کد آموزشی

```bash
# Terminal Commands

# نمایش اطلاعات دیتابیس
php artisan db:show

# اجرای migration ها
php artisan migrate

# نمایش وضعیت migration ها
php artisan migrate:status

# برگرداندن migration ها
php artisan migrate:rollback

# تازه‌سازی کامل دیتابیس
php artisan migrate:fresh

# ایجاد migration جدید
php artisan make:migration create_jobs_table
```

```php
// فایل: database/migrations/2024_01_01_000001_create_jobs_table.php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * اجرای migration
     */
    public function up(): void
    {
        Schema::create('jobs', function (Blueprint $table) {
            $table->id();                    // Primary key
            $table->string('title');         // Job title
            $table->text('description');     // Job description
            $table->string('salary');        // Salary range
            $table->string('location');      // Job location
            $table->timestamps();            // created_at, updated_at
        });
    }

    /**
     * برگرداندن migration
     */
    public function down(): void
    {
        Schema::dropIfExists('jobs');
    }
};

// فایل: database/seeders/JobSeeder.php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class JobSeeder extends Seeder
{
    public function run(): void
    {
        // Insert sample data
        DB::table('jobs')->insert([
            [
                'title' => 'Software Developer',
                'description' => 'Develop amazing applications',
                'salary' => '$60,000 - $80,000',
                'location' => 'Remote',
                'created_at' => now(),
                'updated_at' => now(),
            ],
            [
                'title' => 'Project Manager',
                'description' => 'Lead development teams',
                'salary' => '$70,000 - $90,000',
                'location' => 'New York',
                'created_at' => now(),
                'updated_at' => now(),
            ]
        ]);
    }
}
```

### فلوی معنایی و گرامری

1. **اجرای دستور Artisan**: `php artisan db:show` برای نمایش اطلاعات دیتابیس
2. **Migration Class**: کلاس Migration برای تعریف تغییرات دیتابیس
3. **متد up()**: برای اعمال تغییرات (ایجاد جدول)
4. **متد down()**: برای برگرداندن تغییرات (حذف جدول)
5. **Schema Builder**: `Schema::create()` برای ایجاد جدول جدید
6. **Column Definition**: `$table->string('title')` برای تعریف ستون
7. **Seeder Execution**: `DB::table()->insert()` برای درج داده‌های نمونه

### نکات فنی کلیدی

1. **Migration Naming**: نام migration ها باید توضیحی و منطقی باشد
2. **Rollback Safety**: همیشه متد `down()` را پیاده‌سازی کنید
3. **Data Types**: از مناسب‌ترین نوع داده برای هر ستون استفاده کنید
4. **Index Management**: برای بهبود performance از index استفاده کنید
5. **Seeder Organization**: داده‌های نمونه را در seeder ها سازماندهی کنید

### نقشه ارتباط با سایر فایل‌ها

```
Database Architecture:
├── database/
│   ├── migrations/
│   │   └── create_jobs_table.php
│   ├── seeders/
│   │   └── JobSeeder.php
│   └── database.sqlite
├── app/Models/
│   └── Job.php (Eloquent Model)
├── config/
│   └── database.php (DB Configuration)
└── .env (Environment Variables)

Migration Flow:
Migration File → Schema Builder → Database Table → Model → Application

Artisan Commands:
php artisan migrate → Execute migrations
php artisan db:seed → Run seeders
php artisan make:model → Create new model
```

---

## خلاصه و نتیجه‌گیری

این جزوه مباحث پیشرفته Laravel را بر اساس استانداردهای شرکت‌های بزرگ فناوری پوشش داده است. هر بخش شامل مفاهیم نظری، کد عملی، و نکات فنی کلیدی است که برای توسعه‌دهندگان حرفه‌ای ضروری محسوب می‌شود. رعایت این اصول و استانداردها باعث ایجاد کد قابل نگهداری، مقیاس‌پذیر و ایمن خواهد شد.