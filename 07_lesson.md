# هندبوک حرفه‌ای Laravel: Database Migrations و Eloquent ORM

## نقشه ساختار درختی مباحث

```
Laravel Database Management
├── 1. Database Migrations
│   ├── 1.1 مفهوم و کاربرد Migrations
│   ├── 1.2 ساختار فایل Migration
│   ├── 1.3 دستورات Migration
│   └── 1.4 مدیریت تغییرات جدول
│
├── 2. Eloquent ORM
│   ├── 2.1 مفهوم Object-Relational Mapping
│   ├── 2.2 ساختار Model Classes
│   ├── 2.3 Conventions و Naming
│   └── 2.4 Basic Query Operations
│
├── 3. Database Connection
│   ├── 3.1 تنظیمات Database
│   ├── 3.2 Database GUI Tools
│   └── 3.3 Table Management
│
├── 4. Data Seeding
│   ├── 4.1 Manual Data Entry
│   └── 4.2 Automated Seeding
│
└── 5. Best Practices
    ├── 5.1 Migration Best Practices
    ├── 5.2 Model Conventions
    └── 5.3 Data Management
```

---

## 1. Database Migrations - مفهوم و کاربرد

### 1.1 توضیحات جامع (حداقل 10 خط)

Database Migrations در Laravel یکی از قدرتمندترین ابزارهای مدیریت پایگاه داده است که امکان version control برای ساختار دیتابیس را فراهم می‌کند. این مکانیزم به شما اجازه می‌دهد تا تغییرات ساختار دیتابیس را به صورت کدی و قابل ردیابی مدیریت کنید. هر Migration یک فایل PHP است که شامل دو متد اصلی `up()` و `down()` می‌باشد. متد `up()` برای اعمال تغییرات و متد `down()` برای برگشت دادن تغییرات استفاده می‌شود.

مزیت اصلی Migrations این است که تیم توسعه می‌تواند تغییرات دیتابیس را به صورت همزمان و بدون تضاد اعمال کند. به جای اینکه هر developer مجبور باشد تغییرات SQL را دستی اعمال کند، تنها کافی است دستور `php artisan migrate` را اجرا کند تا تمام تغییرات جدید به صورت خودکار اعمال شود.

Laravel تمام migrations اجرا شده را در جدول `migrations` ذخیره می‌کند تا بتواند تشخیص دهد کدام migrations قبلاً اجرا شده‌اند. این سیستم از rollback نیز پشتیبانی می‌کند که امکان برگشت به وضعیت قبلی دیتابیس را فراهم می‌کند. فایل‌های migration با timestamp نام‌گذاری می‌شوند تا ترتیب اجرا حفظ شود.

### 1.2 قطعه کد آموزشی

**فایل: `database/migrations/2024_01_01_000000_create_job_listings_table.php`**

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * اجرای migration - ایجاد جدول
     */
    public function up(): void
    {
        Schema::create('job_listings', function (Blueprint $table) {
            $table->id();                    // ستون ID اصلی (Primary Key)
            $table->string('title');         // عنوان شغل (VARCHAR)
            $table->string('salary');        // حقوق (VARCHAR)
            $table->timestamps();            // created_at و updated_at
            
            // خروجی: جدول job_listings با 5 ستون ایجاد می‌شود
        });
    }

    /**
     * برگشت migration - حذف جدول
     */
    public function down(): void
    {
        Schema::dropIfExists('job_listings');
        // خروجی: جدول job_listings حذف می‌شود
    }
};
```

### 1.3 فلوی معنایی و گرامری

1. **Class Declaration**: کلاس migration از `Migration` ارث‌بری می‌کند
2. **up() Method**: متد اصلی برای اعمال تغییرات دیتابیس
3. **Schema::create()**: متد ایجاد جدول جدید
4. **Blueprint $table**: شیء برای تعریف ساختار جدول
5. **$table->id()**: ایجاد ستون ID با auto-increment
6. **$table->string()**: ایجاد ستون نوع VARCHAR
7. **$table->timestamps()**: ایجاد ستون‌های created_at و updated_at
8. **down() Method**: متد برای برگشت تغییرات
9. **Schema::dropIfExists()**: حذف جدول در صورت وجود

### 1.4 نکات فنی کلیدی

1. **Timestamp Naming**: فایل‌های migration با timestamp شروع می‌شوند برای حفظ ترتیب اجرا
2. **Blueprint Pattern**: استفاده از الگوی Builder برای ساخت ساختار جدول
3. **Reversible Operations**: هر migration باید قابل برگشت باشد
4. **Atomic Transactions**: تمام عملیات migration در یک transaction اجرا می‌شوند
5. **Migration State**: Laravel وضعیت migrations را در جدول `migrations` ذخیره می‌کند

### 1.5 نقشه ارتباط با سایر فایل‌ها

```
Migration File
├── Artisan Commands
│   ├── php artisan migrate
│   ├── php artisan migrate:rollback
│   └── php artisan migrate:refresh
│
├── Database Tables
│   ├── Target Table (job_listings)
│   └── migrations Table (tracking)
│
├── Model Classes
│   └── App\Models\JobListing.php
│
└── Configuration
    └── config/database.php
```

---

## 2. Eloquent ORM - Object-Relational Mapping

### 2.1 توضیحات جامع

Eloquent ORM یکی از برجسته‌ترین ویژگی‌های Laravel است که الگوی Active Record را پیاده‌سازی می‌کند. این سیستم امکان تعامل با دیتابیس را به صورت شیء‌گرا و بدون نوشتن SQL خام فراهم می‌کند. هر جدول دیتابیس با یک Model کلاس نمایش داده می‌شود که شامل تمام متدها و ویژگی‌های لازم برای کار با آن جدول است.

Eloquent بر اساس conventions کار می‌کند، به این معنی که اگر قوانین نام‌گذاری Laravel را رعایت کنید، نیازی به تنظیمات اضافی نخواهید داشت. مثلاً اگر Model شما `User` باشد، Eloquent به طور خودکار با جدول `users` کار می‌کند.

این ORM امکانات پیشرفته‌ای مانند Relationships، Query Builder، Scopes، Mutators و Accessors را ارائه می‌دهد. همچنین از Mass Assignment Protection، Model Events و Soft Deletes پشتیبانی می‌کند.

Eloquent همچنین Collection هایی را برمی‌گرداند که قابلیت‌های پیشرفته‌ای برای manipulation داده‌ها دارند. این Collections از متدهای utility زیادی برای فیلتر کردن، مرتب کردن و تبدیل داده‌ها استفاده می‌کنند.

### 2.2 قطعه کد آموزشی

**فایل: `app/Models/Job.php`**

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Job extends Model
{
    /**
     * نام جدول در دیتابیس
     * چون جدول ما job_listings است، باید مشخص کنیم
     */
    protected $table = 'job_listings';

    /**
     * ستون‌های قابل Mass Assignment
     */
    protected $fillable = [
        'title',
        'salary'
    ];

    /**
     * نوع داده ستون‌ها
     */
    protected $casts = [
        'created_at' => 'datetime',
        'updated_at' => 'datetime'
    ];

    // خروجی: Model آماده برای تعامل با جدول job_listings
}
```

### 2.3 فلوی معنایی و گرامری

1. **Namespace Declaration**: تعریف فضای نام برای Model
2. **Class Extension**: کلاس Job از Model ارث‌بری می‌کند
3. **$table Property**: مشخص کردن نام جدول مرتبط
4. **$fillable Property**: تعریف ستون‌های قابل Mass Assignment
5. **$casts Property**: تعریف نوع داده ستون‌ها
6. **Model Conventions**: Laravel نام جدول را از نام Model حدس می‌زند
7. **Active Record Pattern**: هر instance از Model یک رکورد دیتابیس را نمایش می‌دهد

### 2.4 نکات فنی کلیدی

1. **Naming Conventions**: Model های singular و جدول‌های plural
2. **Mass Assignment Protection**: استفاده از `$fillable` برای امنیت
3. **Automatic Timestamps**: Laravel به طور خودکار created_at و updated_at را مدیریت می‌کند
4. **Type Casting**: تبدیل خودکار نوع داده‌ها با `$casts`
5. **Primary Key**: به طور پیش‌فرض ستون `id` به عنوان Primary Key در نظر گرفته می‌شود

### 2.5 نقشه ارتباط با سایر فایل‌ها

```
Job Model
├── Database Table
│   └── job_listings
│
├── Routes
│   └── web.php (using Job::all())
│
├── Controllers
│   └── JobController.php
│
├── Views
│   └── Blade Templates
│
└── Migrations
    └── create_job_listings_table.php
```

---

## 3. Database Query Operations - عملیات پایگاه داده

### 3.1 توضیحات جامع

عملیات Query در Eloquent امکان دسترسی و manipulation داده‌ها را به صورت بسیار ساده فراهم می‌کند. متدهای اصلی شامل `all()` برای دریافت تمام رکوردها، `find()` برای یافتن رکورد با ID، `where()` برای فیلتر کردن و `create()` برای ایجاد رکورد جدید می‌باشد.

Eloquent Collections برمی‌گرداند که قابلیت‌های پیشرفته‌ای دارند. این Collections از LazyCollection استفاده می‌کنند که برای حجم بالای داده بسیار بهینه هستند. امکان استفاده از متدهای `map()`, `filter()`, `reduce()` و دیگر متدهای functional programming روی Collections وجود دارد.

Query Builder الگوی Fluent Interface را پیاده‌سازی می‌کند که امکان زنجیره کردن متدها را فراهم می‌کند. این امکان کدهای خواناتر و قابل نگهداری‌تر ایجاد می‌کند.

Eloquent همچنین از Eager Loading پشتیبانی می‌کند که مشکل N+1 Query را حل می‌کند. امکان تعریف Scopes برای query های مکرر و پیچیده نیز وجود دارد.

### 3.2 قطعه کد آموزشی

**فایل: `routes/web.php`**

```php
<?php

use App\Models\Job;
use Illuminate\Support\Facades\Route;

Route::get('/', function () {
    // 1. دریافت تمام job ها
    $jobs = Job::all();
    // خروجی: Collection شامل تمام رکوردهای job_listings
    
    // 2. دریافت اولین job
    $firstJob = $jobs[0];
    // خروجی: Instance از Job Model
    
    // 3. دسترسی به attributes
    $title = $firstJob->title;      // "Director"
    $salary = $firstJob->salary;    // "$50,000"
    
    // 4. تبدیل به Array برای نمایش
    dd([
        'total_jobs' => $jobs->count(),
        'first_job' => [
            'title' => $title,
            'salary' => $salary
        ]
    ]);
    
    // خروجی نمونه:
    // array:2 [
    //   "total_jobs" => 3
    //   "first_job" => array:2 [
    //     "title" => "Director"
    //     "salary" => "$50,000"
    //   ]
    // ]
});
```

### 3.3 فلوی معنایی و گرامری

1. **Model Import**: وارد کردن Model Job
2. **Route Definition**: تعریف route برای تست
3. **Job::all()**: فراخوانی static method برای دریافت تمام رکوردها
4. **Collection Access**: دسترسی به عناصر Collection با index
5. **Attribute Access**: دسترسی به ستون‌های جدول از طریق properties
6. **Magic Methods**: Laravel از `__get()` برای دسترسی به attributes استفاده می‌کند
7. **Collection Methods**: استفاده از متدهای Collection مانند `count()`
8. **Debug Output**: استفاده از `dd()` برای نمایش داده‌ها

### 3.4 نکات فنی کلیدی

1. **Lazy Loading**: Collections به صورت lazy load می‌شوند
2. **Memory Efficiency**: برای حجم بالای داده از `chunk()` استفاده کنید
3. **Type Safety**: استفاده از Type Hints برای بهتر شدن کد
4. **Query Optimization**: برای relationships از `with()` استفاده کنید
5. **Collection vs Array**: Collection قابلیت‌های بیشتری نسبت به Array دارد

### 3.5 نقشه ارتباط با سایر فایل‌ها

```
Route Handler
├── Job Model
│   └── Database Query
│       └── job_listings table
│
├── Collection Response
│   ├── Individual Records
│   └── Attribute Access
│
└── Debug Output
    └── dd() function
```

---

## 4. Migration Commands - دستورات مدیریت Migration

### 4.1 توضیحات جامع

Laravel مجموعه‌ای از دستورات Artisan برای مدیریت Migrations ارائه می‌دهد که هر کدام کاربرد خاصی دارند. دستور `php artisan migrate` برای اجرای migrations جدید، `migrate:rollback` برای برگشت آخرین batch، و `migrate:refresh` برای reset کردن و اجرای مجدد تمام migrations استفاده می‌شود.

دستور `make:migration` برای ایجاد فایل migration جدید استفاده می‌شود. این دستور فایل را با timestamp و نام مناسب در پوشه `database/migrations` ایجاد می‌کند. نام‌گذاری migrations باید descriptive و واضح باشد.

`migrate:refresh` دستور خطرناکی است که تمام جدول‌ها را drop کرده و دوباره می‌سازد. این دستور فقط در development environment استفاده می‌شود و هرگز در production نباید اجرا شود.

`migrate:status` وضعیت تمام migrations را نمایش می‌دهد و مشخص می‌کند کدام migrations اجرا شده‌اند. این دستور برای debugging و بررسی وضعیت دیتابیس مفید است.

### 4.2 قطعه کد آموزشی

**فایل: `Terminal Commands`**

```bash
# 1. ایجاد Migration جدید
php artisan make:migration create_job_listings_table
# خروجی: فایل migration در database/migrations ایجاد می‌شود

# 2. اجرای migrations جدید
php artisan migrate
# خروجی: تمام migrations اجرا نشده اجرا می‌شوند

# 3. برگشت آخرین batch
php artisan migrate:rollback
# خروجی: آخرین گروه migrations برگشت داده می‌شوند

# 4. Reset و اجرای مجدد (فقط Development)
php artisan migrate:refresh
# خروجی: تمام جدول‌ها drop و دوباره ساخته می‌شوند

# 5. بررسی وضعیت migrations
php artisan migrate:status
# خروجی: لیست migrations با وضعیت اجرا

# 6. اجرای migration خاص
php artisan migrate --path=/database/migrations/specific_migration.php
# خروجی: فقط migration مشخص شده اجرا می‌شود
```

### 4.3 فلوی معنایی و گرامری

1. **Artisan Command**: استفاده از CLI tool Laravel
2. **make:migration**: ایجاد skeleton فایل migration
3. **Timestamp Generation**: Laravel به طور خودکار timestamp اضافه می‌کند
4. **Migration Execution**: اجرای متد `up()` در فایل‌های migration
5. **Batch Tracking**: Laravel migrations را در batch هایی دسته‌بندی می‌کند
6. **Rollback Logic**: اجرای متد `down()` برای برگشت
7. **Status Checking**: بررسی جدول `migrations` برای وضعیت

### 4.4 نکات فنی کلیدی

1. **Environment Safety**: هرگز `migrate:refresh` را در production اجرا نکنید
2. **Backup Strategy**: قبل از migration های مهم backup بگیرید
3. **Batch Management**: Laravel migrations را در batch هایی مدیریت می‌کند
4. **Naming Conventions**: نام migrations باید descriptive باشد
5. **Dependency Management**: ترتیب اجرای migrations بر اساس timestamp است

### 4.5 نقشه ارتباط با سایر فایل‌ها

```
Artisan Commands
├── Migration Files
│   └── database/migrations/
│
├── Database Tables
│   ├── Target Tables
│   └── migrations (tracking)
│
├── Schema Builder
│   └── Blueprint Objects
│
└── Configuration
    └── config/database.php
```

---

## 5. Database Connection و Table Management

### 5.1 توضیحات جامع

مدیریت اتصال به پایگاه داده در Laravel از طریق فایل کانفیگ `config/database.php` انجام می‌شود. Laravel از multiple database connections پشتیبانی می‌کند و می‌تواند همزمان با چندین دیتابیس مختلف کار کند.

استفاده از GUI tools مانند TablePlus، phpMyAdmin، یا MySQL Workbench برای مدیریت و visualize کردن داده‌ها بسیار مفید است. این ابزارها امکان مشاهده ساختار جدول‌ها، اجرای query های مستقیم، و مدیریت داده‌ها را فراهم می‌کنند.

Laravel tables مخصوص framework دارد که شامل `migrations`, `sessions`, `jobs`, `failed_jobs` و `users` می‌باشد. این جدول‌ها توسط framework به طور داخلی استفاده می‌شوند و در اکثر موارد نیازی به دستکاری دستی آنها نیست.

Connection pooling و persistent connections برای بهینه‌سازی performance در production environments مهم هستند. Laravel از connection pooling داخلی پشتیبانی می‌کند.

### 5.2 قطعه کد آموزشی

**فایل: `config/database.php`**

```php
<?php

return [
    'default' => env('DB_CONNECTION', 'mysql'),

    'connections' => [
        'mysql' => [
            'driver' => 'mysql',
            'host' => env('DB_HOST', '127.0.0.1'),
            'port' => env('DB_PORT', '3306'),
            'database' => env('DB_DATABASE', 'forge'),
            'username' => env('DB_USERNAME', 'forge'),
            'password' => env('DB_PASSWORD', ''),
            'charset' => 'utf8mb4',
            'collation' => 'utf8mb4_unicode_ci',
            'prefix' => '',
            'strict' => true,
            'engine' => null,
        ],
        
        // اتصال دوم برای تست
        'testing' => [
            'driver' => 'sqlite',
            'database' => ':memory:',
            'prefix' => '',
        ],
    ],
    
    // خروجی: کانفیگ اتصال به MySQL و SQLite
];
```

**فایل: `.env`**

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=example_database
DB_USERNAME=jeffrey
DB_PASSWORD=secret_password

# خروجی: متغیرهای محیطی برای اتصال به دیتابیس
```

### 5.3 فلوی معنایی و گرامری

1. **Environment Configuration**: استفاده از `.env` برای sensitive data
2. **Connection Array**: تعریف multiple database connections
3. **Driver Selection**: انتخاب database driver (MySQL, SQLite, PostgreSQL)
4. **Credential Management**: مدیریت امن اطلاعات اتصال
5. **Charset Configuration**: تنظیم character set و collation
6. **Default Connection**: انتخاب connection پیش‌فرض
7. **Environment Variables**: استفاده از `env()` helper function

### 5.4 نکات فنی کلیدی

1. **Security Best Practices**: هرگز credentials را در کد commit نکنید
2. **Connection Pooling**: Laravel به طور خودکار connections را مدیریت می‌کند
3. **SSL Configuration**: برای production از SSL استفاده کنید
4. **Timeout Settings**: تنظیم timeout برای جلوگیری از hanging connections
5. **Error Handling**: مدیریت خطاهای connection در production

### 5.5 نقشه ارتباط با سایر فایل‌ها

```
Database Configuration
├── Environment File
│   └── .env
│
├── Models
│   └── Eloquent Models
│
├── Migrations
│   └── Schema Builder
│
├── GUI Tools
│   ├── TablePlus
│   ├── phpMyAdmin
│   └── MySQL Workbench
│
└── Application
    └── Database Queries
```

---

## خلاصه و نتیجه‌گیری

این هندبوک جامع تمام جنبه‌های اساسی کار با Database Migrations و Eloquent ORM در Laravel را پوشش می‌دهد. از مفاهیم پایه تا تکنیک‌های پیشرفته، هر مبحث به صورت کامل و با مثال‌های عملی توضیح داده شده است.

نکات کلیدی که باید به خاطر داشته باشید:
- همیشه از Migrations برای مدیریت ساختار دیتابیس استفاده کنید
- Eloquent conventions را رعایت کنید تا کد تمیزتر باشد
- در production environments محتاط باشید
- از GUI tools برای debugging و مدیریت استفاده کنید
- همیشه backup بگیرید قبل از تغییرات مهم

این اصول و تکنیک‌ها در سطح شرکت‌های بزرگ فناوری استاندارد هستند و رعایت آنها کیفیت و maintainability کد شما را تضمین می‌کند.