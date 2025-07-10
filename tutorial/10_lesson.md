# جزوه حرفه‌ای روابط Eloquent در Laravel

## 🗂️ نقشه ساختار درختی مباحث

```
Laravel Eloquent Relationships
├── 1. BelongsTo Relationship
│   ├── تعریف و مفهوم
│   ├── پیاده‌سازی در Model
│   ├── استفاده از Lazy Loading
│   └── اتصال به Foreign Key
├── 2. HasMany Relationship
│   ├── تعریف و مفهوم
│   ├── پیاده‌سازی در Model
│   ├── کار با Collection
│   └── دسترسی به رکوردهای متعدد
└── 3. BelongsToMany Relationship
    ├── تعریف و مفهوم
    ├── ایجاد Pivot Table
    ├── تنظیم Foreign Key Constraints
    ├── پیاده‌سازی در Model
    └── کار با Many-to-Many Relations
```

---

## 📋 بخش اول: BelongsTo Relationship

### a. توضیح ساده مبحث

رابطه `BelongsTo` زمانی استفاده می‌شود که یک رکورد به رکورد دیگری تعلق دارد. مثلاً هر شغل (`Job`) به یک کارفرما (`Employer`) تعلق دارد. این رابطه یک به یک معکوس است.

### b. قطعه کد آموزشی

```php
// app/Models/Job.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;

class Job extends Model
{
    protected $table = 'job_listings';
    
    protected $fillable = [
        'title',
        'salary',
        'employer_id'
    ];

    /**
     * هر Job به یک Employer تعلق دارد
     * @return BelongsTo
     */
    public function employer(): BelongsTo
    {
        return $this->belongsTo(Employer::class);
        // Laravel خودکار employer_id را به عنوان foreign key شناسایی می‌کند
    }
}

// استفاده در Tinker یا Controller:
// $job = Job::first();
// $employer = $job->employer; // Lazy Loading اتفاق می‌افتد
// echo $employer->name; // خروجی: نام کارفرما
```

### c. ساختار درختی تغییرات فایل‌ها

```
Project Structure Changes:
├── app/Models/Job.php
│   ├── [تغییر] اضافه کردن متد employer()
│   └── [تغییر] import کردن BelongsTo class
├── database/migrations/
│   └── [موجود] جدول job_listings با employer_id
└── app/Models/Employer.php
    └── [آماده] برای رابطه معکوس
```

### d. نقشه ارتباط بین فایل‌ها

```
File Relationships Map:
Job Model ←→ Employer Model
    ↓           ↓
job_listings ←→ employers
table        table
    ↓           ↓
employer_id  →  id (Primary Key)
(Foreign Key)

Query Flow:
Job::first() → job_listings table
     ↓
job->employer → SELECT * FROM employers WHERE id = job.employer_id
```

### e. فلوی معنایی و گرامری

**فلوی معنایی:**
1. درخواست یک Job از دیتابیس
2. دسترسی به خصوصیت employer
3. Laravel تشخیص می‌دهد که employer یک رابطه است
4. Query جدید برای یافتن Employer اجرا می‌شود (Lazy Loading)

**نکات کلیدی:**
- `belongsTo` همیشه Foreign Key را در جدول جاری نگه می‌دارد
- Lazy Loading تا زمان دسترسی واقعی Query اجرا نمی‌شود
- می‌توان از Eager Loading برای بهبود performance استفاده کرد

---

## 📋 بخش دوم: HasMany Relationship

### a. توضیح ساده مبحث

رابطه `HasMany` زمانی استفاده می‌شود که یک رکورد می‌تواند چندین رکورد دیگر داشته باشد. مثلاً یک کارفرما (`Employer`) می‌تواند چندین شغل (`Job`) داشته باشد.

### b. قطعه کد آموزشی

```php
// app/Models/Employer.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasMany;

class Employer extends Model
{
    protected $fillable = [
        'name'
    ];

    /**
     * هر Employer می‌تواند چندین Job داشته باشد
     * @return HasMany
     */
    public function jobs(): HasMany
    {
        return $this->hasMany(Job::class);
        // Laravel خودکار employer_id را در جدول jobs جستجو می‌کند
    }
}

// استفاده در Tinker یا Controller:
// $employer = Employer::first();
// $jobs = $employer->jobs; // برمی‌گرداند: Eloquent Collection
// $firstJob = $employer->jobs->first(); // دسترسی به اولین job
// echo $jobs->count(); // خروجی: تعداد jobs
```

### c. ساختار درختی تغییرات فایل‌ها

```
Project Structure Changes:
├── app/Models/Employer.php
│   ├── [تغییر] اضافه کردن متد jobs()
│   └── [تغییر] import کردن HasMany class
├── Eloquent Collection
│   ├── [استفاده] برای نگهداری چندین Job
│   └── [قابلیت] متدهای filter, map, count, etc.
└── database/
    └── [بدون تغییر] ساختار جداول همان است
```

### d. نقشه ارتباط بین فایل‌ها

```
File Relationships Map:
Employer Model ←→ Job Model
      ↓             ↓
  employers ←→ job_listings
   table        table
      ↓             ↓
  id (PK)    ←  employer_id (FK)

Collection Flow:
Employer::first() → employers table
        ↓
employer->jobs → SELECT * FROM job_listings WHERE employer_id = employer.id
        ↓
Eloquent Collection [Job1, Job2, Job3, ...]
```

### e. فلوی معنایی و گرامری

**فلوی معنایی:**
1. درخواست یک Employer از دیتابیس
2. دسترسی به خصوصیت jobs
3. Laravel Query برای یافتن همه Jobs مربوط به این Employer اجرا می‌کند
4. نتیجه در قالب Eloquent Collection برمی‌گردد

**نکات کلیدی:**
- `hasMany` همیشه Foreign Key را در جدول مرتبط جستجو می‌کند
- نتیجه یک Collection است نه یک Model منفرد
- Collection شامل متدهای قدرتمند برای کار با آرایه‌ها است

---

## 📋 بخش سوم: BelongsToMany Relationship

### a. توضیح ساده مبحث

رابطه `BelongsToMany` برای روابط چند به چند استفاده می‌شود. مثلاً یک شغل (`Job`) می‌تواند چندین برچسب (`Tag`) داشته باشد و هر برچسب می‌تواند به چندین شغل تعلق داشته باشد. این رابطه نیاز به جدول pivot دارد.

### b. قطعه کد آموزشی

```php
// database/migrations/xxxx_create_tags_and_job_tag_table.php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateTagsAndJobTagTable extends Migration
{
    public function up()
    {
        // جدول tags
        Schema::create('tags', function (Blueprint $table) {
            $table->id();
            $table->string('name'); // نام برچسب مثل "programming"
            $table->timestamps();
        });

        // جدول pivot برای رابطه many-to-many
        Schema::create('job_tag', function (Blueprint $table) {
            $table->id();
            $table->foreignId('job_listing_id')
                  ->constrained('job_listings')
                  ->cascadeOnDelete(); // حذف cascade
            $table->foreignId('tag_id')
                  ->constrained('tags')
                  ->cascadeOnDelete(); // حذف cascade
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('job_tag');
        Schema::dropIfExists('tags');
    }
}
```

```php
// app/Models/Job.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsToMany;

class Job extends Model
{
    protected $table = 'job_listings';

    /**
     * هر Job می‌تواند چندین Tag داشته باشد
     * @return BelongsToMany
     */
    public function tags(): BelongsToMany
    {
        return $this->belongsToMany(Tag::class, 'job_tag', 'job_listing_id', 'tag_id');
        // پارامتر اول: Model مرتبط
        // پارامتر دوم: نام جدول pivot
        // پارامتر سوم: Foreign key برای model جاری
        // پارامتر چهارم: Foreign key برای model مرتبط
    }
}

// app/Models/Tag.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsToMany;

class Tag extends Model
{
    protected $fillable = ['name'];

    /**
     * هر Tag می‌تواند به چندین Job تعلق داشته باشد
     * @return BelongsToMany
     */
    public function jobs(): BelongsToMany
    {
        return $this->belongsToMany(Job::class, 'job_tag', 'tag_id', 'job_listing_id');
    }
}

// استفاده:
// $job = Job::first();
// $tags = $job->tags; // Collection از Tags
// $tag = Tag::first();
// $jobs = $tag->jobs; // Collection از Jobs
```

### c. ساختار درختی تغییرات فایل‌ها

```
Project Structure Changes:
├── app/Models/
│   ├── Job.php
│   │   ├── [تغییر] اضافه کردن متد tags()
│   │   └── [تغییر] import BelongsToMany
│   ├── Tag.php
│   │   ├── [جدید] ایجاد Tag Model
│   │   └── [جدید] متد jobs()
│   └── Employer.php [بدون تغییر]
├── database/migrations/
│   └── [جدید] create_tags_and_job_tag_table.php
└── database/
    ├── tags table [جدید]
    └── job_tag table [جدید] (Pivot Table)
```

### d. نقشه ارتباط بین فایل‌ها

```
File Relationships Map:
Job Model ←→ Tag Model
    ↓         ↓
job_listings ←→ tags
    ↓         ↓
    └─→ job_tag ←─┘
        (Pivot Table)
        ├── job_listing_id (FK)
        └── tag_id (FK)

Query Flow:
Job::first() → job_listings table
     ↓
job->tags → SELECT tags.* FROM tags 
           INNER JOIN job_tag ON tags.id = job_tag.tag_id 
           WHERE job_tag.job_listing_id = job.id
     ↓
Collection of Tags
```

### e. فلوی معنایی و گرامری

**فلوی معنایی:**
1. درخواست یک Job از دیتابیس
2. دسترسی به خصوصیت tags
3. Laravel Query پیچیده با JOIN بر روی جدول pivot اجرا می‌کند
4. نتیجه به صورت Collection از Tags برمی‌گردد

**نکات کلیدی:**
- `belongsToMany` در هر دو Model یکسان است
- جدول pivot باید نام‌گذاری استاندارد داشته باشد (الفبایی)
- `cascadeOnDelete` برای حفظ یکپارچگی داده‌ها
- می‌توان اطلاعات اضافی در جدول pivot ذخیره کرد

---

## 🔑 خلاصه و نکات مهم

### بهترین practices:

1. **استفاده از Type Hints**: همیشه return type را مشخص کنید
2. **Eager Loading**: برای بهبود performance از `with()` استفاده کنید
3. **Naming Convention**: نام‌گذاری استاندارد برای جداول و متدها
4. **Foreign Key Constraints**: برای یکپارچگی داده‌ها
5. **Documentation**: کامنت‌گذاری مناسب برای متدهای relationship

### مقایسه سریع:
- `belongsTo`: یک به یک (معکوس)
- `hasMany`: یک به چند
- `belongsToMany`: چند به چند (نیاز به pivot table)

این سه نوع رابطه 85% از نیازهای پروژه‌های واقعی را پوشش می‌دهند و پایه‌ای محکم برای درک روابط پیچیده‌تر فراهم می‌کنند.