# راهنمای جامع Laravel Model Factories و Eloquent Relationships

## 📋 نقشه ساختار درختی مباحث

```
Laravel Advanced Concepts
├── Model Factories
│   ├── Factory Pattern در Laravel
│   ├── Faker Library Integration
│   ├── Factory States
│   ├── Factory Relationships
│   └── Factory Best Practices
├── Database Relationships
│   ├── Foreign Keys
│   ├── Model Relationships Setup
│   └── Migration Strategies
└── Eloquent Relationships
    ├── Belongs To Relationship
    ├── Has Many Relationship
    └── Relationship Loading
```

---

## 🏭 مبحث اول: Model Factories در Laravel

### a. توضیح ساده مفهوم

Model Factory در Laravel یک الگوی طراحی (Design Pattern) است که برای تولید داده‌های جعلی (Fake Data) در محیط توسعه و تست استفاده می‌شود. این ابزار به شما کمک می‌کند تا به راحتی و به صورت خودکار، داده‌های شبیه‌سازی شده برای مدل‌های Eloquent خود تولید کنید.

### b. قطعه کد آموزشی

```php
// database/factories/UserFactory.php
<?php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;
use Illuminate\Support\Str;

class UserFactory extends Factory
{
    /**
     * تعریف state پیش‌فرض مدل
     */
    public function definition(): array
    {
        return [
            'name' => fake()->name(),          // تولید نام جعلی
            'email' => fake()->unique()->safeEmail(),  // تولید ایمیل منحصر به فرد
            'email_verified_at' => now(),      // تنظیم زمان تأیید ایمیل
            'password' => '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi', // password
            'remember_token' => Str::random(10),
        ];
    }

    /**
     * State برای کاربران تأیید نشده
     */
    public function unverified(): static
    {
        return $this->state(fn (array $attributes) => [
            'email_verified_at' => null,
        ]);
    }

    /**
     * State برای کاربران ادمین
     */
    public function admin(): static
    {
        return $this->state(fn (array $attributes) => [
            'is_admin' => true,
        ]);
    }
}

// استفاده در PHP Artisan Tinker:
// User::factory()->create()              // تولید یک کاربر
// User::factory(100)->create()           // تولید 100 کاربر
// User::factory()->unverified()->create() // تولید کاربر تأیید نشده
// User::factory()->admin()->create()     // تولید کاربر ادمین
```

### c. ساختار درختی فایل‌های مرتبط

```
project/
├── app/
│   └── Models/
│       └── User.php (+ HasFactory trait)
├── database/
│   ├── factories/
│   │   └── UserFactory.php (جدید)
│   └── migrations/
│       └── 2024_01_01_000000_create_users_table.php
├── tests/
│   └── Feature/
│       └── UserTest.php (استفاده از Factory)
└── artisan (دستور tinker)
```

### d. نقشه ارتباط بین فایل‌ها

```
UserFactory.php
    ↓ (تولید داده)
User Model
    ↓ (ذخیره در)
users table
    ↑ (خواندن از)
Migration File
    ↓ (استفاده در)
Test Files & Tinker
```

### e. فلوی معنایی و گرامری

**فلوی معنایی:**
1. Factory تعریف می‌شود → داده‌های جعلی تولید می‌شوند → در دیتابیس ذخیره می‌شوند
2. State‌ها برای سناریوهای مختلف تعریف می‌شوند
3. Faker library برای تولید داده‌های واقع‌گرا استفاده می‌شود

**نکات فنی کلیدی:**
- Factory باید از کلاس Factory ارث‌بری کند
- Method definition() باید آرایه‌ای از attributes برگرداند
- State‌ها با method state() تعریف می‌شوند
- HasFactory trait در مدل ضروری است

---

## 🔧 مبحث دوم: Factory برای Job Listings

### a. توضیح ساده مفهوم

برای مدل‌های جدید، Factory جداگانه ایجاد می‌کنیم تا بتوانیم داده‌های مخصوص آن مدل را تولید کنیم. در این مثال، برای مدل Job، factory مخصوص می‌سازیم.

### b. قطعه کد آموزشی

```php
// app/Models/Job.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Job extends Model
{
    use HasFactory;  // این trait ضروری است

    protected $fillable = [
        'title',
        'salary',
        'employer_id'
    ];
}

// database/factories/JobFactory.php
<?php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;

class JobFactory extends Factory
{
    /**
     * تعریف state پیش‌فرض مدل Job
     */
    public function definition(): array
    {
        return [
            'title' => fake()->jobTitle(),     // تولید عنوان شغل جعلی
            'salary' => 50000,                 // حقوق ثابت (می‌توان dynamic کرد)
            'employer_id' => \App\Models\Employer::factory(), // ارتباط با Employer
        ];
    }

    /**
     * State برای مشاغل با حقوق بالا
     */
    public function highSalary(): static
    {
        return $this->state(fn (array $attributes) => [
            'salary' => fake()->numberBetween(80000, 150000),
        ]);
    }
}

// استفاده:
// Job::factory()->create()           // تولید یک شغل
// Job::factory(300)->create()        // تولید 300 شغل
// Job::factory()->highSalary()->create() // تولید شغل با حقوق بالا
```

### c. ساختار درختی فایل‌های مرتبط

```
project/
├── app/
│   └── Models/
│       ├── Job.php (+ HasFactory trait)
│       └── Employer.php (مدل جدید)
├── database/
│   ├── factories/
│   │   ├── JobFactory.php (جدید)
│   │   └── EmployerFactory.php (جدید)
│   └── migrations/
│       ├── create_jobs_table.php (اصلاح شده)
│       └── create_employers_table.php (جدید)
└── artisan commands
```

### d. نقشه ارتباط بین فایل‌ها

```
JobFactory.php
    ↓ (وابستگی به)
EmployerFactory.php
    ↓ (تولید)
Job Model + Employer Model
    ↓ (ذخیره در)
jobs table + employers table
    ↑ (رابطه foreign key)
Migration Files
```

### e. فلوی معنایی و گرامری

**فلوی معنایی:**
1. Factory برای Job تعریف می‌شود
2. ارتباط با Employer از طریق factory مشخص می‌شود
3. Laravel خودکار Employer ایجاد می‌کند و ID آن را استفاده می‌کند

**نکات فنی کلیدی:**
- `php artisan make:factory JobFactory` برای ایجاد factory
- `HasFactory` trait باید در مدل اضافه شود
- Factory relationships با `Model::factory()` تعریف می‌شوند

---

## 🔗 مبحث سوم: Database Relationships و Foreign Keys

### a. توضیح ساده مفهوم

در دیتابیس، relationships (روابط) بین جداول از طریق foreign keys تعریف می‌شوند. یک job متعلق به یک employer است، پس در جدول jobs باید ستون employer_id وجود داشته باشد.

### b. قطعه کد آموزشی

```php
// database/migrations/xxxx_xx_xx_create_jobs_table.php
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
            $table->id();                    // Primary Key
            $table->string('title');         // عنوان شغل
            $table->integer('salary');       // حقوق
            
            // روش اول: تعریف foreign key به صورت دستی
            $table->unsignedBigInteger('employer_id');
            
            // روش دوم: استفاده از helper method (بهتر)
            // $table->foreignId('employer_id')->constrained();
            
            $table->timestamps();
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

// database/migrations/xxxx_xx_xx_create_employers_table.php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('employers', function (Blueprint $table) {
            $table->id();                    // Primary Key
            $table->string('name');          // نام شرکت
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('employers');
    }
};

// اجرای migrations:
// php artisan migrate:fresh  (حذف و بازسازی همه جداول)
// php artisan migrate        (اجرای migrations جدید)
```

### c. ساختار درختی فایل‌های مرتبط

```
database/
├── migrations/
│   ├── xxxx_create_employers_table.php (اول اجرا شود)
│   ├── xxxx_create_jobs_table.php (دوم اجرا شود)
│   └── xxxx_create_users_table.php
├── factories/
│   ├── EmployerFactory.php
│   └── JobFactory.php
└── seeders/
    └── DatabaseSeeder.php
```

### d. نقشه ارتباط بین فایل‌ها

```
employers table
    ↓ (id)
jobs table (employer_id)
    ↑ (foreign key constraint)
Migration Files
    ↓ (ایجاد)
Database Schema
    ↓ (استفاده در)
Eloquent Models
```

### e. فلوی معنایی و گرامری

**فلوی معنایی:**
1. Migration برای employers اجرا می‌شود
2. Migration برای jobs با foreign key اجرا می‌شود
3. رابطه بین دو جدول برقرار می‌شود

**نکات فنی کلیدی:**
- Foreign key باید همان نوع primary key باشد (unsignedBigInteger)
- `foreignId()->constrained()` روش مدرن تعریف foreign key است
- ترتیب اجرای migrations مهم است

---

## 🏢 مبحث چهارم: Employer Factory و Advanced Relationships

### a. توضیح ساده مفهوم

برای تکمیل رابطه بین Job و Employer، باید EmployerFactory نیز ایجاد کنیم تا بتوانیم داده‌های جعلی شرکت‌ها را تولید کنیم.

### b. قطعه کد آموزشی

```php
// app/Models/Employer.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Employer extends Model
{
    use HasFactory;

    protected $fillable = [
        'name'
    ];

    /**
     * رابطه: یک employer می‌تواند چندین job داشته باشد
     */
    public function jobs()
    {
        return $this->hasMany(Job::class);
    }
}

// database/factories/EmployerFactory.php
<?php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;

class EmployerFactory extends Factory
{
    /**
     * تعریف state پیش‌فرض مدل Employer
     */
    public function definition(): array
    {
        return [
            'name' => fake()->company(),  // تولید نام شرکت جعلی
        ];
    }

    /**
     * State برای شرکت‌های تکنولوژی
     */
    public function tech(): static
    {
        return $this->state(fn (array $attributes) => [
            'name' => fake()->company() . ' Tech',
        ]);
    }
}

// استفاده کامل:
// Employer::factory()->create()                    // تولید یک شرکت
// Job::factory(10)->create()                      // تولید 10 شغل (با 10 شرکت)
// Job::factory(10)->recycle(Employer::factory()->create())->create() // 10 شغل برای یک شرکت
```

### c. ساختار درختی فایل‌های مرتبط

```
project/
├── app/
│   └── Models/
│       ├── Job.php (+ belongsTo relationship)
│       └── Employer.php (+ hasMany relationship)
├── database/
│   ├── factories/
│   │   ├── JobFactory.php (+ employer_id)
│   │   └── EmployerFactory.php (کامل)
│   └── migrations/
│       ├── create_employers_table.php
│       └── create_jobs_table.php (+ foreign key)
└── Commands/
    └── php artisan make:model Employer -f
```

### d. نقشه ارتباط بین فایل‌ها

```
EmployerFactory.php
    ↓ (تولید)
Employer Model
    ↓ (رابطه hasMany)
Job Model
    ↑ (رابطه belongsTo)
JobFactory.php
    ↓ (استفاده از)
Database Tables
```

### e. فلوی معنایی و گرامری

**فلوی معنایی:**
1. EmployerFactory تعریف می‌شود
2. JobFactory به EmployerFactory وابسته می‌شود
3. هر بار که Job ایجاد می‌شود، Employer هم ایجاد می‌شود

**نکات فنی کلیدی:**
- `php artisan make:model Employer -f` برای ایجاد مدل + factory
- `fake()->company()` برای تولید نام شرکت
- `recycle()` method برای استفاده مجدد از یک نمونه

---

## 🔄 مبحث پنجم: Eloquent Relationships Implementation

### a. توضیح ساده مفهوم

Eloquent Relationships به ما اجازه می‌دهد تا روابط بین مدل‌ها را در کد PHP تعریف کنیم. با این کار می‌توانیم به راحتی از یک Job به Employer آن دسترسی پیدا کنیم.

### b. قطعه کد آموزشی

```php
// app/Models/Job.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Job extends Model
{
    use HasFactory;

    protected $fillable = [
        'title',
        'salary',
        'employer_id'
    ];

    /**
     * رابطه: یک job متعلق به یک employer است
     */
    public function employer()
    {
        return $this->belongsTo(Employer::class);
    }
}

// app/Models/Employer.php - کامل شده
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Employer extends Model
{
    use HasFactory;

    protected $fillable = ['name'];

    /**
     * رابطه: یک employer می‌تواند چندین job داشته باشد
     */
    public function jobs()
    {
        return $this->hasMany(Job::class);
    }
}

// استفاده در کد:
// $job = Job::first();
// echo $job->employer->name;     // دسترسی به نام شرکت
// 
// $employer = Employer::first();
// $jobs = $employer->jobs;       // دسترسی به تمام مشاغل شرکت
// echo $employer->jobs->count(); // تعداد مشاغل
```

### c. ساختار درختی فایل‌های مرتبط

```
app/Models/
├── Job.php
│   ├── belongsTo(Employer::class)
│   └── HasFactory trait
├── Employer.php
│   ├── hasMany(Job::class)
│   └── HasFactory trait
└── User.php
    └── HasFactory trait

database/
├── factories/
│   ├── JobFactory.php
│   ├── EmployerFactory.php
│   └── UserFactory.php
└── migrations/
    ├── create_jobs_table.php
    ├── create_employers_table.php
    └── create_users_table.php
```

### d. نقشه ارتباط بین فایل‌ها

```
Job Model
    ↓ (belongsTo)
Employer Model
    ↓ (hasMany)
Job Collection
    ↑ (استفاده در)
Controllers/Views
    ↓ (نمایش داده)
User Interface
```

### e. فلوی معنایی و گرامری

**فلوی معنایی:**
1. در مدل Job، method employer() تعریف می‌شود
2. در مدل Employer، method jobs() تعریف می‌شود
3. Laravel خودکار foreign key را تشخیص می‌دهد
4. دسترسی به روابط از طریق properties امکان‌پذیر می‌شود

**نکات فنی کلیدی:**
- `belongsTo()` برای رابطه یک به یک (از سمت child)
- `hasMany()` برای رابطه یک به چند (از سمت parent)
- Laravel خودکار foreign key را از نام مدل + _id تشخیص می‌دهد
- Relationships به صورت lazy loading کار می‌کنند

---

## 🎯 نکات کلیدی و Best Practices

### نکات مهم Factory:
1. همیشه از `HasFactory` trait استفاده کنید
2. Factory states برای سناریوهای مختلف تعریف کنید
3. از `recycle()` برای بهینه‌سازی استفاده کنید
4. Factory relationships را به درستی تعریف کنید

### نکات مهم Relationships:
1. Foreign keys را به درستی تعریف کنید
2. Convention naming را رعایت کنید
3. Inverse relationships را فراموش نکنید
4. از eager loading برای بهینه‌سازی استفاده کنید

### نکات مهم Migration:
1. ترتیب اجرای migrations مهم است
2. از `migrate:fresh` در توسعه استفاده کنید
3. Foreign key constraints را به درستی تعریف کنید
4. Rollback strategies را در نظر بگیرید

### Performance Tips:
1. از `with()` برای eager loading استفاده کنید
2. Factory count را محدود کنید
3. Database indexes را فراموش نکنید
4. از `recycle()` برای کاهش queries استفاده کنید