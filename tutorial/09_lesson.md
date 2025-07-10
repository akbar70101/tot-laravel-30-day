# راهنمای کامل Laravel Model Factories و Eloquent Relationships

## 📋 نقشه ساختار درختی مباحث

```
Laravel Model Factories & Eloquent Relationships
│
├── 1. مقدمه Model Factories
│   ├── مفهوم و کاربرد
│   ├── نحوه تولید داده‌های تست
│   └── کار با Faker Library
│
├── 2. ایجاد و استفاده از Factory
│   ├── دستور php artisan make:factory
│   ├── تعریف attributes در Factory
│   └── استفاده از trait HasFactory
│
├── 3. States در Factory
│   ├── تعریف State مختلف
│   ├── روش‌های تغییر وضعیت
│   └── کاربرد در تست‌ها
│
├── 4. Factory Relationships
│   ├── ایجاد روابط بین Models
│   ├── Foreign Key Management
│   └── تولید داده‌های مرتبط
│
└── 5. Eloquent Relationships
    ├── تعریف روابط در Models
    ├── استفاده از Factory در روابط
    └── بهینه‌سازی عملکرد
```

---

## 1️⃣ مقدمه Model Factories

### توضیحات جامع و کامل

Model Factories در Laravel ابزاری قدرتمند برای تولید داده‌های آزمایشی و تست هستند. این سیستم به شما امکان تولید حجم زیادی از داده‌های جعلی اما واقع‌نما را می‌دهد که برای توسعه محلی و تست‌های خودکار ضروری است. Factory ها از کتابخانه Faker استفاده می‌کنند تا داده‌های متنوع و قابل اعتماد تولید کنند. این روش به جای وارد کردن دستی داده‌ها، فرآیند توسعه را سرعت می‌بخشد و امکان تست سناریوهای مختلف را فراهم می‌کند. همچنین Factory ها قابلیت تعریف state های مختلف را دارند تا بتوانید مدل‌ها را در حالت‌های خاص تولید کنید. استفاده از Factory ها در پروژه‌های بزرگ به خصوص برای پر کردن پایگاه داده با داده‌های نمونه بسیار مفید است. این سیستم همچنین با سیستم migration ها و seeder ها یکپارچه عمل می‌کند.

### قطعه کد آموزشی

```php
<?php
// database/factories/UserFactory.php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;
use Illuminate\Support\Str;

class UserFactory extends Factory
{
    /**
     * تعریف حالت پیش‌فرض مدل
     *
     * @return array<string, mixed>
     */
    public function definition(): array
    {
        return [
            'name' => fake()->name(),           // تولید نام جعلی
            'email' => fake()->unique()->safeEmail(),  // تولید ایمیل منحصر به فرد
            'email_verified_at' => now(),       // تاریخ تایید ایمیل
            'password' => '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi', // پسورد هش شده
            'remember_token' => Str::random(10), // توکن یادآوری
        ];
    }

    /**
     * نمونه استفاده در Tinker یا Test
     * 
     * User::factory()->create()           // تولید یک کاربر
     * User::factory()->count(50)->create() // تولید 50 کاربر
     */
}
```

### فلوی معنایی و گرامری

1. **تعریف کلاس Factory**: کلاس UserFactory از Factory پایه ارث‌بری می‌کند
2. **متد definition()**: الگوی داده‌های پیش‌فرض را تعریف می‌کند
3. **استفاده از fake()**: تابع کمکی برای دسترسی به Faker library
4. **تولید داده‌های منحصر به فرد**: استفاده از unique() برای جلوگیری از تکرار
5. **بازگشت آرایه**: آرایه‌ای از key-value pairs که نمایانگر ستون‌های جدول است
6. **نام‌گذاری مناسب**: Factory ها با پسوند Factory نام‌گذاری می‌شوند

### نکات فنی کلیدی

1. **مسیر قرارگیری**: Factory ها در مسیر `database/factories/` قرار می‌گیرند
2. **نام‌گذاری**: نام Factory باید با نام Model + Factory باشد (مثل UserFactory)
3. **Faker Methods**: متدهای مختلف Faker مانند name(), email(), address() قابل استفاده هستند
4. **Performance**: Factory ها برای محیط development بهینه‌سازی شده‌اند، نه production
5. **Memory Usage**: تولید حجم زیاد داده باید با احتیاط انجام شود

### نقشه ارتباط با سایر فایل‌ها

```
UserFactory.php
    ↓
app/Models/User.php (استفاده از HasFactory trait)
    ↓
database/seeders/DatabaseSeeder.php (استفاده در seeding)
    ↓
tests/Feature/UserTest.php (استفاده در تست‌ها)
```

---

## 2️⃣ ایجاد و استفاده از Factory

### توضیحات جامع و کامل

فرآیند ایجاد Factory در Laravel شامل چندین مرحله است که باید به درستی دنبال شوند. ابتدا باید Model مورد نظر را ایجاد کرده و سپس Factory مربوطه را تولید کنید. Laravel دستورات Artisan قدرتمندی برای این کار ارائه می‌دهد. هنگام ایجاد Factory، باید trait HasFactory را به Model اضافه کنید تا بتوانید از متدهای Factory استفاده کنید. این trait امکان فراخوانی Factory را از طریق Model فراهم می‌کند. همچنین Laravel به صورت خودکار ارتباط بین Model و Factory را بر اساس نام‌گذاری تشخیص می‌دهد. اگر نام‌گذاری استاندارد را رعایت کنید، نیازی به پیکربندی اضافی نخواهید داشت. Factory ها همچنین قابلیت شخصی‌سازی بالایی دارند و می‌توانید attributes های خاص را برای هر مورد استفاده تعریف کنید.

### قطعه کد آموزشی

```php
<?php
// دستور ایجاد Factory
// Terminal: php artisan make:factory JobFactory

// database/factories/JobFactory.php
namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;

class JobFactory extends Factory
{
    /**
     * تعریف attributes برای مدل Job
     *
     * @return array<string, mixed>
     */
    public function definition(): array
    {
        return [
            'title' => fake()->jobTitle(),      // عنوان شغل جعلی
            'salary' => fake()->numberBetween(30000, 150000), // حقوق تصادفی
            'created_at' => now(),             // تاریخ ایجاد
            'updated_at' => now(),             // تاریخ آپدیت
        ];
    }
}

// app/Models/Job.php
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Job extends Model
{
    use HasFactory;    // اضافه کردن trait ضروری
    
    protected $fillable = ['title', 'salary'];
    
    /**
     * نمونه استفاده:
     * Job::factory()->create()              // یک شغل
     * Job::factory()->count(10)->create()   // 10 شغل
     */
}
```

### فلوی معنایی و گرامری

1. **ایجاد Factory**: دستور artisan برای تولید فایل Factory
2. **تعریف namespace**: مسیر صحیح namespace برای Factory
3. **ارث‌بری از Factory**: استفاده از کلاس پایه Factory
4. **تعریف definition()**: متد اصلی برای تعریف ساختار داده
5. **استفاده از fake()**: دسترسی به instance سراسری Faker
6. **اضافه کردن HasFactory**: trait ضروری در Model
7. **تست در Tinker**: استفاده از php artisan tinker برای تست

### نکات فنی کلیدی

1. **HasFactory Trait**: بدون این trait امکان استفاده از factory() method وجود ندارد
2. **Naming Convention**: Laravel به صورت خودکار JobFactory را با Job model ارتباط می‌دهد
3. **Faker Methods**: متدهای مختلف مانند jobTitle(), numberBetween(), company() قابل استفاده هستند
4. **Memory Loading**: کد در Tinker در حافظه باقی می‌ماند و نیاز به restart دارد
5. **Database Connection**: Factory ها مستقیماً با پایگاه داده کار می‌کنند

### نقشه ارتباط با سایر فایل‌ها

```
JobFactory.php
    ↕
app/Models/Job.php (HasFactory trait)
    ↕
database/migrations/create_jobs_table.php (ساختار جدول)
    ↕
Tinker Console (تست و استفاده)
```

---

## 3️⃣ States در Factory

### توضیحات جامع و کامل

State ها در Factory ها امکان تولید مدل‌ها در حالت‌های خاص را فراهم می‌کنند. این قابلیت بسیار مفید است زمانی که نیاز به تولید داده‌هایی با ویژگی‌های خاص دارید. به عنوان مثال، ممکن است نیاز به کاربرانی داشته باشید که ایمیل‌شان تایید نشده باشد یا ادمین باشند. State ها به صورت متدهایی تعریف می‌شوند که آرایه‌ای از attributes را بازمی‌گردانند تا attributes پیش‌فرض را override کنند. این روش انعطاف‌پذیری بالایی در تولید داده‌های تست فراهم می‌کند. State ها معمولاً در تست‌های خودکار استفاده می‌شوند تا سناریوهای مختلف را پوشش دهند. همچنین می‌توانید چندین State را با هم ترکیب کنید تا حالت‌های پیچیده‌تری ایجاد کنید. این قابلیت به خصوص در پروژه‌های بزرگ که نیاز به تست سناریوهای مختلف دارند بسیار مفید است.

### قطعه کد آموزشی

```php
<?php
// database/factories/UserFactory.php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;

class UserFactory extends Factory
{
    public function definition(): array
    {
        return [
            'name' => fake()->name(),
            'email' => fake()->unique()->safeEmail(),
            'email_verified_at' => now(),           // به صورت پیش‌فرض تایید شده
            'password' => bcrypt('password'),
            'is_admin' => false,                    // به صورت پیش‌فرض کاربر عادی
        ];
    }

    /**
     * State برای کاربران تایید نشده
     */
    public function unverified(): static
    {
        return $this->state(fn (array $attributes) => [
            'email_verified_at' => null,           // ایمیل تایید نشده
        ]);
    }

    /**
     * State برای کاربران ادمین
     */
    public function admin(): static
    {
        return $this->state(fn (array $attributes) => [
            'is_admin' => true,                    // کاربر ادمین
            'email_verified_at' => now(),          // ادمین ها باید تایید شده باشند
        ]);
    }

    /**
     * نمونه استفاده:
     * User::factory()->unverified()->create()        // کاربر تایید نشده
     * User::factory()->admin()->create()             // کاربر ادمین
     * User::factory()->admin()->count(5)->create()   // 5 ادمین
     */
}
```

### فلوی معنایی و گرامری

1. **تعریف متد State**: متدهای State نام‌های توصیفی دارند
2. **استفاده از state()**: متد state() آرایه‌ای از attributes را می‌پذیرد
3. **استفاده از Closure**: closure function برای محاسبه dynamic values
4. **Override کردن**: attributes جدید، مقادیر پیش‌فرض را جایگزین می‌کنند
5. **Chaining**: امکان ترکیب چندین state با هم
6. **Return Type**: متدهای state باید static را برگردانند

### نکات فنی کلیدی

1. **Method Chaining**: State ها قابل ترکیب هستند و می‌توانند پشت سر هم فراخوانی شوند
2. **Closure Functions**: استفاده از fn() برای تعریف inline functions
3. **Dynamic Values**: امکان محاسبه مقادیر بر اساس attributes فعلی
4. **Testing Use Cases**: State ها عمدتاً برای تست‌های خودکار طراحی شده‌اند
5. **Memory Efficiency**: State ها فقط attributes مورد نیاز را override می‌کنند

### نقشه ارتباط با سایر فایل‌ها

```
UserFactory.php (States)
    ↓
tests/Feature/AdminTest.php (استفاده از admin state)
    ↓
tests/Feature/EmailVerificationTest.php (استفاده از unverified state)
    ↓
database/seeders/UserSeeder.php (استفاده در seeding)
```

---

## 4️⃣ Factory Relationships

### توضیحات جامع و کامل

روابط در Factory ها یکی از قدرتمندترین قابلیت‌های Laravel هستند که امکان تولید داده‌های مرتبط را فراهم می‌کنند. زمانی که مدل‌های شما دارای foreign key هستند، Factory ها می‌توانند به صورت خودکار مدل‌های وابسته را نیز تولید کنند. این قابلیت به خصوص در پروژه‌های پیچیده که روابط زیادی بین مدل‌ها وجود دارد بسیار مفید است. برای تعریف روابط در Factory، می‌توانید از Factory های دیگر استفاده کنید. Laravel به صورت خودکار مدل مرتبط را تولید کرده و ID آن را به عنوان foreign key استفاده می‌کند. همچنین امکان استفاده از متد recycle() برای استفاده مجدد از مدل‌های موجود وجود دارد. این روش باعث بهینه‌سازی عملکرد و کاهش تعداد رکوردهای غیرضروری می‌شود. Factory relationships همچنین امکان تست سناریوهای پیچیده‌تری را فراهم می‌کنند.

### قطعه کد آموزشی

```php
<?php
// database/migrations/create_job_listings_table.php
Schema::create('job_listings', function (Blueprint $table) {
    $table->id();
    $table->string('title');
    $table->integer('salary');
    $table->foreignId('employer_id')->constrained(); // foreign key به جدول employers
    $table->timestamps();
});

// database/factories/JobFactory.php
namespace Database\Factories;

use App\Models\Employer;
use Illuminate\Database\Eloquent\Factories\Factory;

class JobFactory extends Factory
{
    public function definition(): array
    {
        return [
            'title' => fake()->jobTitle(),
            'salary' => fake()->numberBetween(30000, 120000),
            'employer_id' => Employer::factory(),    // تولید خودکار employer مرتبط
        ];
    }
}

// database/factories/EmployerFactory.php
namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;

class EmployerFactory extends Factory
{
    public function definition(): array
    {
        return [
            'name' => fake()->company(),             // نام شرکت جعلی
            'email' => fake()->companyEmail(),       // ایمیل شرکت
            'website' => fake()->url(),              // وب‌سایت
        ];
    }
}

/**
 * نمونه استفاده:
 * Job::factory()->count(10)->create()  // 10 شغل با 10 employer مختلف
 * Job::factory()->for(Employer::factory()->create())->count(5)->create() // 5 شغل برای یک employer
 */
```

### فلوی معنایی و گرامری

1. **تعریف Foreign Key**: استفاده از foreignId() در migration
2. **Reference در Factory**: استفاده از `Employer::factory()` برای تولید خودکار
3. **Automatic Resolution**: Laravel خودکار employer را تولید و ID آن را استفاده می‌کند
4. **Constraint Definition**: constrained() برای تعریف constraint در پایگاه داده
5. **Factory Chaining**: امکان ترکیب Factory ها برای روابط پیچیده
6. **Bulk Creation**: تولید انبوه داده‌های مرتبط با یک دستور

### نکات فنی کلیدی

1. **HasFactory Trait**: هر دو مدل باید این trait را داشته باشند
2. **Foreign Key Type**: نوع foreign key باید با primary key مطابقت داشته باشد
3. **Cascade Operations**: تنظیم cascade delete/update در migration ها
4. **Memory Usage**: تولید روابط زیاد می‌تواند حافظه زیادی مصرف کند
5. **Database Transactions**: در صورت خطا، تمام عملیات rollback می‌شوند

### نقشه ارتباط با سایر فایل‌ها

```
JobFactory.php
    ↓ (استفاده از)
EmployerFactory.php
    ↓ (تولید)
app/Models/Job.php ←→ app/Models/Employer.php
    ↓ (ذخیره در)
Database Tables: jobs ←→ employers
```

---

## 5️⃣ Eloquent Relationships

### توضیحات جامع و کامل

Eloquent Relationships قلب سیستم ORM در Laravel هستند که امکان تعریف و استفاده از روابط بین مدل‌ها را فراهم می‌کنند. این روابط به شما امکان دسترسی به داده‌های مرتبط را بدون نوشتن کوئری‌های پیچیده SQL می‌دهند. در Laravel انواع مختلف روابط مانند One-to-One، One-to-Many، Many-to-Many و polymorphic relationships قابل تعریف هستند. هر رابطه به صورت متدی در مدل تعریف می‌شود که نوع رابطه را مشخص می‌کند. این روابط به صورت lazy loading کار می‌کنند، یعنی تا زمانی که به داده‌های مرتبط نیاز نباشد، کوئری اجرا نمی‌شود. همچنین امکان eager loading برای بهینه‌سازی عملکرد و جلوگیری از N+1 problem وجود دارد. Eloquent relationships همچنین امکان تعریف قیود و شرایط خاص برای هر رابطه را فراهم می‌کنند. این سیستم با Factory ها به خوبی یکپارچه شده و امکان تولید داده‌های مرتبط را ساده می‌کند.

### قطعه کد آموزشی

```php
<?php
// app/Models/Job.php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;

class Job extends Model
{
    use HasFactory;

    protected $fillable = ['title', 'salary', 'employer_id'];

    /**
     * رابطه belongsTo با مدل Employer
     * هر Job متعلق به یک Employer است
     */
    public function employer(): BelongsTo
    {
        return $this->belongsTo(Employer::class);
    }

    /**
     * نمونه استفاده:
     * $job = Job::find(1);
     * echo $job->employer->name;  // دسترسی به نام کارفرما
     */
}

// app/Models/Employer.php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasMany;

class Employer extends Model
{
    use HasFactory;

    protected $fillable = ['name', 'email', 'website'];

    /**
     * رابطه hasMany با مدل Job
     * هر Employer می‌تواند چندین Job داشته باشد
     */
    public function jobs(): HasMany
    {
        return $this->hasMany(Job::class);
    }

    /**
     * نمونه استفاده:
     * $employer = Employer::find(1);
     * foreach($employer->jobs as $job) {
     *     echo $job->title;
     * }
     */
}
```

### فلوی معنایی و گرامری

1. **تعریف متد relationship**: متدهای relationship نام‌های توصیفی دارند
2. **Return Type Declaration**: مشخص کردن نوع return برای IDE support
3. **belongsTo**: رابطه‌ای که foreign key را در مدل فعلی نگه می‌دارد
4. **hasMany**: رابطه‌ای که foreign key را در مدل مرتبط نگه می‌دارد
5. **Lazy Loading**: روابط تنها زمانی load می‌شوند که به آن‌ها دسترسی پیدا کنید
6. **Magic Properties**: امکان دسترسی به روابط مانند property های معمولی

### نکات فنی کلیدی

1. **Naming Convention**: Laravel خودکار foreign key را بر اساس نام مدل تشخیص می‌دهد
2. **Eager Loading**: استفاده از with() برای جلوگیری از N+1 problem
3. **Return Types**: استفاده از return type declaration برای بهتر شدن IDE support
4. **Relationship Caching**: نتایج روابط در طول یک request cache می‌شوند
5. **Query Optimization**: امکان اضافه کردن constraint ها به روابط

### نقشه ارتباط با سایر فایل‌ها

```
app/Models/Job.php
    ↕ (belongsTo relationship)
app/Models/Employer.php
    ↕ (hasMany relationship)
database/migrations/create_jobs_table.php (foreign key definition)
    ↕
database/factories/JobFactory.php (factory relationship)
    ↕
resources/views/jobs/show.blade.php (استفاده در view)
```

---

## 🎯 خلاصه و نتیجه‌گیری

این راهنمای جامع شامل تمام جنبه‌های مهم Laravel Model Factories و Eloquent Relationships است که در توسعه حرفه‌ای پروژه‌های Laravel ضروری هستند. استفاده صحیح از این ابزارها باعث تسریع فرآیند توسعه، بهبود کیفیت تست‌ها و ایجاد کدی تمیز و قابل نگهداری می‌شود.

### نکات کلیدی برای توسعه‌دهندگان حرفه‌ای:

1. **استفاده از Factory ها در تمام مراحل توسعه**
2. **تعریف State های مختلف برای سناریوهای مختلف**
3. **بهینه‌سازی روابط با eager loading**
4. **رعایت naming convention های Laravel**
5. **استفاده از type declaration برای بهتر شدن IDE support**