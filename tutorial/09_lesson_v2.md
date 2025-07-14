ابتدا فایل آموزشی را به صورت کامل مرور می‌کنم تا تمام مباحث را درک کنم.

Read file: tutorial/09.txt
حالا که فایل آموزشی را به صورت کامل مرور کردم، نقشه دقیق ساختار درختی مباحث را ارائه می‌دهم:

# 🎯 نقشه دقیق ساختار درختی مباحث Laravel Model Factories

```
�� Laravel Model Factories & Relationships
├── 🏭 Model Factories
│   ├── 📋 تعریف و مفهوم
│   ├── 🔧 ایجاد Factory
│   │   ├── دستور artisan make:factory
│   │   ├── تنظیم attributes
│   │   └── استفاده از Faker
│   ├── ⚙️ Factory States
│   │   ├── تعریف state های سفارشی
│   │   ├── unverified state
│   │   └── admin state
│   └── �� استفاده از Factory
│       ├── create() - ایجاد و ذخیره
│       ├── make() - ایجاد بدون ذخیره
│       └── count() - ایجاد چندین رکورد
├── 🔗 Database Relationships
│   ├── �� Foreign Keys
│   │   ├── unsignedBigInteger
│   │   └── foreignIdFor()
│   ├── �� Employer Model
│   └── 💼 Job-Employer Relationship
└── 🔄 Factory Relationships
    ├── Factory در Factory
    └── ایجاد رکوردهای مرتبط
```

---

# 📚 جزوه فوق حرفه‌ای Laravel Model Factories

## 🏭 بخش اول: Model Factories - تعریف و مفهوم

### a. معنای مبحث و قطعه کد مرتبط

**Model Factory** یک ابزار قدرتمند در Laravel است که برای تولید داده‌های تستی و دمو استفاده می‌شود. این ابزار به شما امکان می‌دهد تا به سرعت رکوردهای fake در دیتابیس ایجاد کنید.

### b. قطعه کد آموزشی

```php
// database/factories/UserFactory.php
<?php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Str;

class UserFactory extends Factory
{
    public function definition(): array
    {
        return [
            'name' => fake()->name(),           // تولید نام تصادفی
            'email' => fake()->unique()->safeEmail(), // ایمیل یکتا
            'email_verified_at' => now(),       // تایید شده
            'password' => Hash::make('password'), // رمز عبور
            'remember_token' => Str::random(10), // توکن یادآوری
        ];
    }
}
```

**خروجی:**
```
User {
    id: 1,
    name: "John Doe",
    email: "john.doe@example.com",
    email_verified_at: "2024-01-15 10:30:00",
    password: "$2y$10$...",
    remember_token: "abc123def4"
}
```

### c. ساختار درختی قطعه کدهای مرتبط

```
📁 UserFactory.php
├── 🔧 definition() method
│   ├── fake()->name() - تولید نام
│   ├── fake()->unique()->safeEmail() - ایمیل یکتا
│   ├── Hash::make() - رمزنگاری
│   └── Str::random() - تولید توکن
├── ⚙️ HasFactory trait (در Model)
└── 🔄 Factory usage
    ├── User::factory()->create()
    ├── User::factory()->make()
    └── User::factory(100)->create()
```

### d. نقشه ارتباط با سایر فایل‌ها

```
📁 app/Models/User.php
├── use HasFactory; ← Factory trait
└── factory() method ← دسترسی به Factory

�� database/factories/UserFactory.php
├── extends Factory ← کلاس پایه
└── definition() ← تنظیمات پیش‌فرض

�� config/app.php
└── Faker locale ← تنظیم زبان
```

### e. فلوی معنایی و گرامری

**فلوی معنایی:**
1. تعریف Factory → تنظیم attributes → استفاده از Faker → تولید داده
2. Model ← HasFactory trait ← Factory class ← definition method

**فلوی گرامری:**
```php
User::factory()           // دسترسی به Factory
    ->create()            // ایجاد و ذخیره در DB
    ->make()              // ایجاد بدون ذخیره
    ->count(100)          // تعداد مشخص
```

**نکات فنی مهم:**
- Factory باید `HasFactory` trait را در Model داشته باشد
- `fake()` helper برای دسترسی به Faker library
- `unique()` برای جلوگیری از تکرار
- `definition()` متد اصلی برای تعریف attributes

---

## 🔧 بخش دوم: ایجاد Factory جدید

### a. معنای مبحث و قطعه کد مرتبط

ایجاد Factory جدید برای مدل‌های مختلف با استفاده از Artisan commands و تنظیم attributes مناسب.

### b. قطعه کد آموزشی

```php
// دستور ایجاد Factory
// php artisan make:factory JobFactory

// database/factories/JobFactory.php
<?php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;

class JobFactory extends Factory
{
    public function definition(): array
    {
        return [
            'title' => fake()->jobTitle(),      // عنوان شغل تصادفی
            'salary' => 50000,                  // حقوق ثابت
            // یا برای تنوع:
            // 'salary' => fake()->numberBetween(30000, 100000),
        ];
    }
}
```

**خروجی:**
```
Job {
    id: 1,
    title: "Software Engineer",
    salary: 50000,
    created_at: "2024-01-15 10:30:00",
    updated_at: "2024-01-15 10:30:00"
}
```

### c. ساختار درختی قطعه کدهای مرتبط

```
�� JobFactory.php
├── 🔧 definition() method
│   ├── fake()->jobTitle() - عنوان شغل
│   ├── salary: 50000 - مقدار ثابت
│   └── fake()->numberBetween() - محدوده تصادفی
├── ⚙️ Job Model
│   └── use HasFactory; ← اضافه کردن trait
└── 🔄 Usage
    ├── Job::factory()->create()
    ├── Job::factory(300)->create()
    └── Job::factory()->make()
```

### d. نقشه ارتباط با سایر فایل‌ها

```
�� app/Models/Job.php
├── use HasFactory; ← اضافه کردن trait
└── factory() method ← دسترسی

�� database/factories/JobFactory.php
├── extends Factory ← کلاس پایه
└── definition() ← تنظیمات

�� routes/web.php
└── تست Factory در routes
```

### e. فلوی معنایی و گرامری

**فلوی معنایی:**
1. `php artisan make:factory JobFactory` → ایجاد فایل
2. تنظیم attributes در `definition()` → اضافه کردن `HasFactory` به Model
3. استفاده از Factory در Tinker یا کد

**فلوی گرامری:**
```php
// ایجاد Factory
php artisan make:factory JobFactory

// اضافه کردن trait به Model
use Illuminate\Database\Eloquent\Factories\HasFactory;

// استفاده
Job::factory()->create()
```

**نکات فنی مهم:**
- قبل از استفاده، `HasFactory` trait را به Model اضافه کنید
- Tinker را restart کنید بعد از تغییرات
- `fake()->jobTitle()` برای تولید عنوان شغل واقعی
- می‌توانید مقادیر ثابت یا تصادفی استفاده کنید

---

## ⚙️ بخش سوم: Factory States

### a. معنای مبحث و قطعه کد مرتبط

Factory States برای ایجاد رکوردهایی در حالت‌های مختلف (مثل کاربر تایید نشده، ادمین) استفاده می‌شود.

### b. قطعه کد آموزشی

```php
// database/factories/UserFactory.php
<?php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Str;

class UserFactory extends Factory
{
    public function definition(): array
    {
        return [
            'name' => fake()->name(),
            'email' => fake()->unique()->safeEmail(),
            'email_verified_at' => now(),       // پیش‌فرض: تایید شده
            'password' => Hash::make('password'),
            'remember_token' => Str::random(10),
        ];
    }

    // State: کاربر تایید نشده
    public function unverified(): static
    {
        return $this->state(fn (array $attributes) => [
            'email_verified_at' => null,        // تایید نشده
        ]);
    }

    // State: کاربر ادمین
    public function admin(): static
    {
        return $this->state(fn (array $attributes) => [
            'is_admin' => true,                 // ادمین
        ]);
    }
}
```

**خروجی:**
```
// User::factory()->create() - کاربر عادی
User { email_verified_at: "2024-01-15 10:30:00", is_admin: false }

// User::factory()->unverified()->create() - تایید نشده
User { email_verified_at: null, is_admin: false }

// User::factory()->admin()->create() - ادمین
User { email_verified_at: "2024-01-15 10:30:00", is_admin: true }
```

### c. ساختار درختی قطعه کدهای مرتبط

```
📁 UserFactory.php
├── 🔧 definition() method
│   └── email_verified_at: now() ← پیش‌فرض
├── ⚙️ unverified() state
│   └── email_verified_at: null ← تایید نشده
├── ⚙️ admin() state
│   └── is_admin: true ← ادمین
└── �� Usage
    ├── User::factory()->unverified()->create()
    ├── User::factory()->admin()->create()
    └── User::factory()->unverified()->admin()->create()
```

### d. نقشه ارتباط با سایر فایل‌ها

```
📁 database/migrations/create_users_table.php
├── email_verified_at column ← nullable
└── is_admin column ← boolean

📁 app/Models/User.php
├── HasFactory trait ← دسترسی به states
└── factory() method ← استفاده از states

�� tests/Feature/
└── تست states مختلف
```

### e. فلوی معنایی و گرامری

**فلوی معنایی:**
1. تعریف state method → تنظیم attributes خاص → استفاده با `->state()`
2. State ها attributes پیش‌فرض را override می‌کنند
3. می‌توان چندین state را ترکیب کرد

**فلوی گرامری:**
```php
// تعریف state
public function stateName(): static
{
    return $this->state(fn (array $attributes) => [
        'column' => 'value',
    ]);
}

// استفاده
Model::factory()->stateName()->create()
```

**نکات فنی مهم:**
- State ها با `->state()` method تعریف می‌شوند
- می‌توان چندین state را زنجیره کرد
- State ها attributes پیش‌فرض را override می‌کنند
- برای تست‌های مختلف بسیار مفید هستند

---

## 🔗 بخش چهارم: Database Relationships

### a. معنای مبحث و قطعه کد مرتبط

ایجاد روابط بین جداول با استفاده از Foreign Keys و تنظیم Factory ها برای ایجاد رکوردهای مرتبط.

### b. قطعه کد آموزشی

```php
// database/migrations/create_employers_table.php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('employers', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });
    }
};

// database/migrations/update_job_listings_table.php
public function up(): void
{
    Schema::table('job_listings', function (Blueprint $table) {
        // روش 1: تعریف دستی
        $table->unsignedBigInteger('employer_id');
        
        // روش 2: استفاده از foreignIdFor (توصیه شده)
        // $table->foreignIdFor(Employer::class);
        
        $table->foreign('employer_id')->references('id')->on('employers');
    });
}
```

**خروجی:**
```
employers table:
id | name
1  | Microsoft
2  | Google

job_listings table:
id | title | salary | employer_id
1  | Developer | 80000 | 1
2  | Designer | 70000 | 2
```

### c. ساختار درختی قطعه کدهای مرتبط

```
📁 database/migrations/
├── 📄 create_employers_table.php
│   ├── $table->id() ← primary key
│   └── $table->string('name') ← نام شرکت
├── 📄 update_job_listings_table.php
│   ├── $table->unsignedBigInteger('employer_id') ← foreign key
│   ├── $table->foreignIdFor(Employer::class) ← روش مدرن
│   └── $table->foreign() ← constraint
└── 🔄 Relationships
    ├── Job belongsTo Employer
    └── Employer hasMany Jobs
```

### d. نقشه ارتباط با سایر فایل‌ها

```
📁 app/Models/Employer.php
├── HasFactory trait ← Factory support
└── hasMany(Job::class) ← relationship

�� app/Models/Job.php
├── HasFactory trait ← Factory support
└── belongsTo(Employer::class) ← relationship

�� database/factories/
├── EmployerFactory.php ← Factory جدید
└── JobFactory.php ← update شده
```

### e. فلوی معنایی و گرامری

**فلوی معنایی:**
1. ایجاد جدول employer → اضافه کردن foreign key به jobs → تعریف relationships
2. `unsignedBigInteger` باید با نوع primary key مطابقت داشته باشد
3. `foreignIdFor()` روش مدرن و توصیه شده

**فلوی گرامری:**
```php
// ایجاد migration
php artisan make:migration create_employers_table
php artisan make:migration add_employer_id_to_job_listings_table

// تعریف foreign key
$table->unsignedBigInteger('employer_id');
$table->foreign('employer_id')->references('id')->on('employers');

// یا روش مدرن
$table->foreignIdFor(Employer::class);
```

**نکات فنی مهم:**
- نوع foreign key باید با primary key مطابقت داشته باشد
- `foreignIdFor()` به طور خودکار نام ستون را تعیین می‌کند
- بعد از تغییر migration ها، `migrate:fresh` اجرا کنید
- Foreign key constraints برای حفظ integrity ضروری هستند

---

## 🔄 بخش پنجم: Factory Relationships

### a. معنای مبحث و قطعه کد مرتبط

تنظیم Factory ها برای ایجاد رکوردهای مرتبط به صورت خودکار، به طوری که هر Job یک Employer داشته باشد.

### b. قطعه کد آموزشی

```php
// database/factories/EmployerFactory.php
<?php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;

class EmployerFactory extends Factory
{
    public function definition(): array
    {
        return [
            'name' => fake()->company(),        // نام شرکت تصادفی
        ];
    }
}

// database/factories/JobFactory.php
<?php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;

class JobFactory extends Factory
{
    public function definition(): array
    {
        return [
            'title' => fake()->jobTitle(),
            'salary' => 50000,
            'employer_id' => Employer::factory(), // ایجاد employer جدید
        ];
    }
}
```

**خروجی:**
```
// Job::factory()->create()
Job {
    id: 1,
    title: "Software Engineer",
    salary: 50000,
    employer_id: 1,
    created_at: "2024-01-15 10:30:00"
}

// Employer ایجاد شده خودکار
Employer {
    id: 1,
    name: "Microsoft Corporation",
    created_at: "2024-01-15 10:30:00"
}
```

### c. ساختار درختی قطعه کدهای مرتبط

```
📁 EmployerFactory.php
├── 🔧 definition() method
│   └── fake()->company() ← نام شرکت
└── 🔄 Usage
    ├── Employer::factory()->create()
    └── Job::factory() ← استفاده در JobFactory

�� JobFactory.php
├── 🔧 definition() method
│   ├── fake()->jobTitle() ← عنوان شغل
│   ├── salary: 50000 ← حقوق
│   └── employer_id: Employer::factory() ← رابطه
└── 🔄 Usage
    ├── Job::factory()->create() ← ایجاد job + employer
    ├── Job::factory(10)->create() ← 10 job + 10 employer
    └── Job::factory()->for(Employer::factory()) ← کنترل رابطه
```

### d. نقشه ارتباط با سایر فایل‌ها

```
📁 app/Models/Employer.php
├── HasFactory trait ← Factory support
└── hasMany(Job::class) ← relationship

�� app/Models/Job.php
├── HasFactory trait ← Factory support
└── belongsTo(Employer::class) ← relationship

�� database/factories/
├── EmployerFactory.php ← Factory جدید
└── JobFactory.php ← update شده با relationship
```

### e. فلوی معنایی و گرامری

**فلوی معنایی:**
1. تعریف EmployerFactory → تنظیم JobFactory با `Employer::factory()` → ایجاد خودکار رکوردهای مرتبط
2. هر بار که Job ایجاد می‌شود، یک Employer جدید هم ایجاد می‌شود
3. Laravel به طور خودکار ID های مرتبط را تنظیم می‌کند

**فلوی گرامری:**
```php
// تعریف relationship در Factory
'employer_id' => Employer::factory()

// استفاده
Job::factory()->create()           // job + employer جدید
Job::factory(10)->create()         // 10 job + 10 employer جدید
Job::factory()->for($employer)->create() // job + employer موجود
```

**نکات فنی مهم:**
- `Employer::factory()` در JobFactory به طور خودکار employer جدید ایجاد می‌کند
- برای استفاده از employer موجود، از `->for()` استفاده کنید
- `->recycle()` برای استفاده مجدد از employer ها
- Factory relationships برای تست‌ها بسیار مفید هستند

---

## �� خلاصه و نکات کلیدی

### نکات مهم برای استفاده در شرکت‌های بزرگ:

1. **Performance**: Factory ها برای تولید داده‌های تستی سریع هستند
2. **Consistency**: داده‌های تولید شده همیشه یکسان و قابل پیش‌بینی هستند
3. **Flexibility**: States و relationships انعطاف‌پذیری بالایی ارائه می‌دهند
4. **Maintainability**: کد تمیز و قابل نگهداری برای تست‌ها

### دستورات مفید:
```bash
# ایجاد Factory
php artisan make:factory ModelFactory

# ایجاد Model + Factory + Migration
php artisan make:model Model -mf

# اجرای migration
php artisan migrate:fresh

# استفاده در Tinker
php artisan tinker
```

این جزوه تمام مباحث مربوط به Laravel Model Factories را پوشش می‌دهد و برای استفاده در محیط‌های حرفه‌ای و شرکت‌های بزرگ طراحی شده است.