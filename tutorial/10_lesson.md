بسیار خب! من فایل آموزشی شما را با دقت کامل بررسی می‌کنم و یک جزوه فوق حرفه‌ای برای مباحث Laravel Eloquent Relationships ایجاد خواهم کرد.

Read file: tutorial/10.txt
حالا که فایل آموزشی را کامل بررسی کردم، نقشه دقیق ساختار درختی مباحث را ارائه می‌دهم:

# ��️ نقشه ساختار درختی مباحث Laravel Eloquent Relationships

```
📚 Laravel Eloquent Relationships
├── 🔗 1. Basic Relationships (Day 11)
│   ├── 📋 1.1 belongsTo Relationship
│   │   ├── 🎯 مفهوم: یک مدل به مدل دیگر تعلق دارد
│   │   ├── 📝 مثال: Job belongs to Employer
│   │   ├── �� پیاده‌سازی: return $this->belongsTo(Employer::class)
│   │   └── 🚀 استفاده: $job->employer
│   │
│   ├── 📋 1.2 hasMany Relationship
│   │   ├── 🎯 مفهوم: یک مدل می‌تواند چندین مدل دیگر داشته باشد
│   │   ├── �� مثال: Employer has many Jobs
│   │   ├── �� پیاده‌سازی: return $this->hasMany(Job::class)
│   │   └── �� استفاده: $employer->jobs
│   │
│   ├── �� 1.3 Lazy Loading
│   │   ├── 🎯 مفهوم: بارگذاری رابطه در زمان نیاز
│   │   ├── 📝 SQL Query: SELECT * FROM employers WHERE id = ?
│   │   └── ⚡ بهینه‌سازی: Eager Loading
│   │
│   └── 📋 1.4 Eloquent Collections
│       ├── �� مفهوم: آرایه‌ای با قابلیت‌های پیشرفته
│       ├── 📝 متدها: first(), filter(), map()
│       └── �� استفاده: $employer->jobs->first()
│
├── 🔗 2. Many-to-Many Relationships (Day 12)
│   ├── �� 2.1 Pivot Tables
│   │   ├── 🎯 مفهوم: جدول واسط برای روابط چند به چند
│   │   ├── 📝 نامگذاری: job_tag (alphabetical order)
│   │   ├── 🔧 ساختار: job_listing_id, tag_id, timestamps
│   │   └── �� ایجاد: php artisan make:migration
│   │
│   ├── 📋 2.2 Foreign Key Constraints
│   │   ├── �� مفهوم: محدودیت‌های کلید خارجی
│   │   ├── �� Cascade Delete: حذف خودکار رکوردهای مرتبط
│   │   ├── 🔧 SQLite: PRAGMA foreign_keys = ON
│   │   └── 🚀 Laravel: فعال به صورت پیش‌فرض
│   │
│   ├── 📋 2.3 belongsToMany Relationship
│   │   ├── 🎯 مفهوم: رابطه چند به چند
│   │   ├── 📝 مثال: Job belongs to many Tags
│   │   ├── �� پیاده‌سازی: return $this->belongsToMany(Tag::class)
│   │   └── 🚀 استفاده: $job->tags
│   │
│   └── 📋 2.4 Migration Structure
│       ├── �� مفهوم: ساختار فایل‌های migration
│       ├── 📝 Grouping: امکان گروه‌بندی جداول در یک فایل
│       ├── 🔧 Foreign Keys: foreignId()->constrained()
│       └── 🚀 Timestamps: timestamps() در pivot table
│
└── 🔗 3. Advanced Concepts
    ├── 📋 3.1 Relationship Types Overview
    │   ├── hasOne: یک به یک
    │   ├── hasMany: یک به چند
    │   ├── belongsTo: چند به یک
    │   ├── belongsToMany: چند به چند
    │   └── polymorphic: چندشکلی
    │
    └── �� 3.2 Best Practices
        ├── نامگذاری متدها: employer, jobs, tags
        ├── دسترسی: به عنوان property نه method
        └── بهینه‌سازی: Eager Loading برای جلوگیری از N+1
```

---

# 📚 جزوه فوق حرفه‌ای Laravel Eloquent Relationships

## 🔗 بخش 1: روابط پایه (Basic Relationships)

### 📋 1.1 رابطه belongsTo

#### a) معنای مبحث:
رابطه `belongsTo` زمانی استفاده می‌شود که یک مدل به مدل دیگر تعلق دارد. در این رابطه، مدل فعلی دارای کلید خارجی است که به مدل دیگر اشاره می‌کند.

#### b) قطعه کد آموزشی:

```php:app/Models/Job.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Job extends Model
{
    // ... existing code ...
    
    /**
     * رابطه belongsTo برای دسترسی به کارفرما
     * یک شغل به یک کارفرما تعلق دارد
     */
    public function employer()
    {
        return $this->belongsTo(Employer::class);
        // خروجی: دسترسی به مدل Employer مرتبط با این Job
    }
}
```

```php:app/Models/Employer.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Employer extends Model
{
    // ... existing code ...
    
    /**
     * رابطه hasMany برای دسترسی به مشاغل
     * یک کارفرما می‌تواند چندین شغل داشته باشد
     */
    public function jobs()
    {
        return $this->hasMany(Job::class);
        // خروجی: Collection از Job های مرتبط با این Employer
    }
}
```

#### c) ساختار درختی قطعه کد:

```
�� app/Models/
├── �� Job.php
│   ├── 🔧 public function employer()
│   │   ├── return $this->belongsTo(Employer::class)
│   │   └── 🎯 دسترسی: $job->employer
│   └── 📋 استفاده در Tinker:
│       ├── $job = Job::first()
│       ├── $job->employer (lazy loading)
│       └── $job->employer->name
│
└── �� Employer.php
    ├── 🔧 public function jobs()
    │   ├── return $this->hasMany(Job::class)
    │   └── 🎯 دسترسی: $employer->jobs
    └── 📋 استفاده در Tinker:
        ├── $employer = Employer::first()
        ├── $employer->jobs (Eloquent Collection)
        └── $employer->jobs->first()
```

#### d) نقشه ارتباط با سایر فایل‌ها:

```
�� ارتباطات فایل‌ها:
├── �� app/Models/Job.php
│   ├── 🔗 references: app/Models/Employer.php
│   ├── �� database: job_listings table
│   └── 🔗 foreign_key: employer_id
│
├── 📄 app/Models/Employer.php
│   ├── 🔗 references: app/Models/Job.php
│   ├── 🔗 database: employers table
│   └── �� primary_key: id
│
├── 📄 database/migrations/
│   ├── create_job_listings_table.php
│   └── create_employers_table.php
│
└── �� routes/web.php
    └── 🔗 controllers استفاده از روابط
```

#### e) فلوی معنایی و گرامری:

**فلوی معنایی:**
1. درخواست Job از دیتابیس
2. Laravel مدل Job را بارگذاری می‌کند
3. هنگام دسترسی به `$job->employer`
4. Laravel تشخیص می‌دهد که property وجود ندارد
5. متد `employer()` را فراخوانی می‌کند
6. SQL Query جدید اجرا می‌شود: `SELECT * FROM employers WHERE id = ?`

**فلوی گرامری:**
```php
// 1. تعریف رابطه
public function employer()
{
    return $this->belongsTo(Employer::class);
}

// 2. استفاده به عنوان property
$job = Job::first();
$employer = $job->employer; // نه $job->employer()

// 3. دسترسی به attributes
echo $employer->name;
```

**نکات فنی مهم:**
- ✅ Lazy Loading: رابطه فقط در زمان نیاز بارگذاری می‌شود
- ✅ Property Access: به عنوان property نه method فراخوانی می‌شود
- ✅ Foreign Key: به صورت خودکار `employer_id` تشخیص داده می‌شود
- ⚠️ N+1 Problem: در حلقه‌ها ممکن است مشکل ایجاد شود

---

### �� 1.2 Lazy Loading و Eloquent Collections

#### a) معنای مبحث:
Lazy Loading به معنای بارگذاری رابطه در زمان نیاز است. Eloquent Collections کلاس‌های پیشرفته‌ای هستند که قابلیت‌های بیشتری نسبت به آرایه‌های معمولی دارند.

#### b) قطعه کد آموزشی:

```php:tinker_example.php
<?php
// مثال استفاده در Tinker

// 1. Lazy Loading Example
$job = Job::first();
// SQL: SELECT * FROM job_listings LIMIT 1

$employer = $job->employer;
// SQL: SELECT * FROM employers WHERE id = 2

// 2. Eloquent Collections Example
$employer = Employer::first();
$jobs = $employer->jobs;
// خروجی: Illuminate\Database\Eloquent\Collection

// 3. Collection Methods
$firstJob = $jobs->first();
$jobCount = $jobs->count();
$filteredJobs = $jobs->where('salary', '>', 50000);

// 4. Eager Loading (بهینه‌سازی)
$jobs = Job::with('employer')->get();
// SQL: SELECT * FROM job_listings
// SQL: SELECT * FROM employers WHERE id IN (1, 2, 3)
```

#### c) ساختار درختی قطعه کد:

```
📁 Tinker Examples
├── �� Lazy Loading
│   ├── $job = Job::first()
│   ├── $job->employer (trigger SQL query)
│   └── 🎯 نتیجه: Employer instance
│
├── 🔧 Eloquent Collections
│   ├── $employer->jobs
│   ├── Collection Methods:
│   │   ├── first()
│   │   ├── count()
│   │   ├── where()
│   │   └── filter()
│   └── 🎯 نتیجه: Collection object
│
└── 🔧 Eager Loading
    ├── Job::with('employer')->get()
    └── 🎯 نتیجه: جلوگیری از N+1 problem
```

#### d) نقشه ارتباط با سایر فایل‌ها:

```
🔄 ارتباطات Lazy Loading:
├── �� app/Models/Job.php
│   ├── �� database: job_listings table
│   └── 🔗 relationship: employer()
│
├── 📄 app/Models/Employer.php
│   ├── 🔗 database: employers table
│   └── �� relationship: jobs()
│
├── 📄 config/database.php
│   └── 🔗 connection settings
│
└── 📄 app/Providers/AppServiceProvider.php
│   └── 🔗 global query modifications
```

#### e) فلوی معنایی و گرامری:

**فلوی معنایی:**
1. درخواست مدل اصلی
2. Laravel مدل را بارگذاری می‌کند
3. هنگام دسترسی به رابطه
4. Laravel تشخیص می‌دهد که رابطه بارگذاری نشده
5. SQL Query جدید اجرا می‌شود
6. نتیجه در cache ذخیره می‌شود

**فلوی گرامری:**
```php
// Lazy Loading Pattern
$model = Model::first();
$related = $model->relationship; // trigger query

// Collection Pattern
$collection = $model->hasManyRelationship;
$item = $collection->first();
$count = $collection->count();

// Eager Loading Pattern
$models = Model::with('relationship')->get();
```

**نکات فنی مهم:**
- ✅ Lazy Loading: ساده اما ممکن است کند باشد
- ✅ Eager Loading: سریع‌تر برای داده‌های زیاد
- ✅ Collections: قابلیت‌های پیشرفته برای کار با داده‌ها
- ⚠️ N+1 Problem: در حلقه‌ها از Eager Loading استفاده کنید

---

## 🔗 بخش 2: روابط چند به چند (Many-to-Many Relationships)

### �� 2.1 Pivot Tables و Migration

#### a) معنای مبحث:
Pivot Table جدول واسطی است که برای ایجاد روابط چند به چند استفاده می‌شود. این جدول شامل کلیدهای خارجی هر دو مدل است.

#### b) قطعه کد آموزشی:

```php:database/migrations/create_tags_and_job_tag_tables.php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * ایجاد جداول tags و job_tag
     */
    public function up()
    {
        // 1. ایجاد جدول tags
        Schema::create('tags', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });

        // 2. ایجاد pivot table
        Schema::create('job_tag', function (Blueprint $table) {
            // کلید خارجی برای job_listings
            $table->foreignId('job_listing_id')
                  ->constrained('job_listings')
                  ->cascadeOnDelete();
            
            // کلید خارجی برای tags
            $table->foreignId('tag_id')
                  ->constrained('tags')
                  ->cascadeOnDelete();
            
            // timestamps برای tracking
            $table->timestamps();
            
            // خروجی: جدول job_tag با foreign key constraints
        });
    }

    public function down()
    {
        Schema::dropIfExists('job_tag');
        Schema::dropIfExists('tags');
    }
};
```

```php:app/Models/Tag.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Tag extends Model
{
    protected $fillable = ['name'];

    /**
     * رابطه belongsToMany برای دسترسی به مشاغل
     */
    public function jobs()
    {
        return $this->belongsToMany(Job::class, 'job_tag');
        // خروجی: Collection از Job های مرتبط با این Tag
    }
}
```

#### c) ساختار درختی قطعه کد:

```
📁 database/migrations/
├── 📄 create_tags_and_job_tag_tables.php
│   ├── 🔧 Schema::create('tags')
│   │   ├── $table->id()
│   │   ├── $table->string('name')
│   │   └── $table->timestamps()
│   │
│   └── 🔧 Schema::create('job_tag')
│       ├── $table->foreignId('job_listing_id')
│       │   ├── ->constrained('job_listings')
│       │   └── ->cascadeOnDelete()
│       ├── $table->foreignId('tag_id')
│       │   ├── ->constrained('tags')
│       │   └── ->cascadeOnDelete()
│       └── $table->timestamps()
│
├── �� app/Models/Tag.php
│   ├── 🔧 protected $fillable = ['name']
│   └── 🔧 public function jobs()
│       └── return $this->belongsToMany(Job::class, 'job_tag')
│
└── �� app/Models/Job.php
    └── 🔧 public function tags()
        └── return $this->belongsToMany(Tag::class, 'job_tag')
```

#### d) نقشه ارتباط با سایر فایل‌ها:

```
🔄 ارتباطات Pivot Table:
├── 📄 database/migrations/
│   ├── create_tags_and_job_tag_tables.php
│   ├── create_job_listings_table.php
│   └── create_employers_table.php
│
├── 📄 app/Models/
│   ├── Tag.php
│   ├── Job.php
│   └── Employer.php
│
├── �� database/seeders/
│   └── DatabaseSeeder.php
│
└── 📄 config/database.php
    └── �� SQLite foreign key settings
```

#### e) فلوی معنایی و گرامری:

**فلوی معنایی:**
1. ایجاد مدل Tag با `php artisan make:model Tag -m`
2. تعریف ساختار جدول tags
3. ایجاد pivot table با نام `job_tag`
4. تعریف foreign key constraints
5. اجرای migration با `php artisan migrate`

**فلوی گرامری:**
```php
// Migration Pattern
Schema::create('table_name', function (Blueprint $table) {
    $table->foreignId('foreign_key')
          ->constrained('referenced_table')
          ->cascadeOnDelete();
});

// Model Pattern
public function relationship()
{
    return $this->belongsToMany(RelatedModel::class, 'pivot_table');
}
```

**نکات فنی مهم:**
- ✅ Naming Convention: `job_tag` (alphabetical order)
- ✅ Foreign Key Constraints: جلوگیری از orphan records
- ✅ Cascade Delete: حذف خودکار pivot records
- ⚠️ SQLite: نیاز به فعال‌سازی `PRAGMA foreign_keys = ON`

---

### 📋 2.2 Foreign Key Constraints و SQLite

#### a) معنای مبحث:
Foreign Key Constraints محدودیت‌هایی هستند که از یکپارچگی داده‌ها محافظت می‌کنند. در SQLite این محدودیت‌ها به صورت پیش‌فرض غیرفعال هستند.

#### b) قطعه کد آموزشی:

```php:database/migrations/foreign_key_constraints.php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * اضافه کردن foreign key constraints
     */
    public function up()
    {
        Schema::table('job_tag', function (Blueprint $table) {
            // اضافه کردن constraint برای job_listing_id
            $table->foreign('job_listing_id')
                  ->references('id')
                  ->on('job_listings')
                  ->onDelete('cascade');
            
            // اضافه کردن constraint برای tag_id
            $table->foreign('tag_id')
                  ->references('id')
                  ->on('tags')
                  ->onDelete('cascade');
            
            // خروجی: foreign key constraints فعال
        });
    }

    public function down()
    {
        Schema::table('job_tag', function (Blueprint $table) {
            $table->dropForeign(['job_listing_id']);
            $table->dropForeign(['tag_id']);
        });
    }
};
```

```sql:sqlite_foreign_keys.sql
-- فعال‌سازی foreign key constraints در SQLite
PRAGMA foreign_keys = ON;

-- بررسی وضعیت
PRAGMA foreign_keys;

-- مثال حذف tag و cascade effect
DELETE FROM tags WHERE id = 1;
-- نتیجه: رکوردهای مربوطه در job_tag نیز حذف می‌شوند
```

#### c) ساختار درختی قطعه کد:

```
📁 Foreign Key Management
├── 🔧 Migration Constraints
│   ├── $table->foreign('job_listing_id')
│   │   ├── ->references('id')
│   │   ├── ->on('job_listings')
│   │   └── ->onDelete('cascade')
│   │
│   └── $table->foreign('tag_id')
│       ├── ->references('id')
│       ├── ->on('tags')
│       └── ->onDelete('cascade')
│
├── 🔧 SQLite Configuration
│   ├── PRAGMA foreign_keys = ON
│   ├── PRAGMA foreign_keys (check status)
│   └── �� نتیجه: constraints فعال
│
└── 🔧 Laravel Defaults
    ├── config/database.php
    ├── foreign_key_constraints: true
    └── �� نتیجه: فعال در Laravel app
```

#### d) نقشه ارتباط با سایر فایل‌ها:

```
🔄 ارتباطات Foreign Keys:
├── 📄 config/database.php
│   ├── sqlite configuration
│   └── foreign_key_constraints setting
│
├── 📄 database/migrations/
│   ├── create_tags_and_job_tag_tables.php
│   └── foreign_key_constraints.php
│
├── 📄 app/Models/
│   ├── Job.php (belongsToMany)
│   └── Tag.php (belongsToMany)
│
└── 📄 database/
    ├── tags table
    ├── job_tag table
    └── job_listings table
```

#### e) فلوی معنایی و گرامری:

**فلوی معنایی:**
1. تعریف foreign key در migration
2. Laravel constraint را ایجاد می‌کند
3. در SQLite نیاز به فعال‌سازی `PRAGMA foreign_keys = ON`
4. هنگام حذف رکورد اصلی، pivot records نیز حذف می‌شوند
5. یکپارچگی داده‌ها حفظ می‌شود

**فلوی گرامری:**
```php
// Migration Pattern
$table->foreign('column_name')
      ->references('id')
      ->on('table_name')
      ->onDelete('cascade');

// SQLite Pattern
PRAGMA foreign_keys = ON;

// Laravel Pattern
// در config/database.php
'foreign_key_constraints' => true,
```

**نکات فنی مهم:**
- ✅ Cascade Delete: حذف خودکار رکوردهای مرتبط
- ✅ Data Integrity: حفظ یکپارچگی داده‌ها
- ⚠️ SQLite Defaults: نیاز به فعال‌سازی manual
- ✅ Laravel Defaults: فعال به صورت پیش‌فرض

---

### 📋 2.3 belongsToMany Relationship

#### a) معنای مبحث:
رابطه `belongsToMany` برای روابط چند به چند استفاده می‌شود. در این رابطه، هر دو مدل می‌توانند چندین نمونه از مدل دیگر داشته باشند.

#### b) قطعه کد آموزشی:

```php:app/Models/Job.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Job extends Model
{
    // ... existing code ...
    
    /**
     * رابطه belongsToMany برای دسترسی به tags
     * یک شغل می‌تواند چندین tag داشته باشد
     */
    public function tags()
    {
        return $this->belongsToMany(Tag::class, 'job_tag');
        // خروجی: Collection از Tag های مرتبط با این Job
    }
}
```

```php:app/Models/Tag.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Tag extends Model
{
    protected $fillable = ['name'];

    /**
     * رابطه belongsToMany برای دسترسی به مشاغل
     * یک tag می‌تواند به چندین شغل مرتبط باشد
     */
    public function jobs()
    {
        return $this->belongsToMany(Job::class, 'job_tag');
        // خروجی: Collection از Job های مرتبط با این Tag
    }
}
```

```php:tinker_belongs_to_many_example.php
<?php
// مثال استفاده در Tinker

// 1. دسترسی به tags یک شغل
$job = Job::first();
$tags = $job->tags;
// خروجی: Eloquent Collection از Tag ها

// 2. دسترسی به مشاغل یک tag
$tag = Tag::first();
$jobs = $tag->jobs;
// خروجی: Eloquent Collection از Job ها

// 3. اضافه کردن tag به شغل
$job->tags()->attach($tag->id);
// خروجی: رکورد جدید در pivot table

// 4. حذف tag از شغل
$job->tags()->detach($tag->id);
// خروجی: حذف رکورد از pivot table

// 5. همگام‌سازی tags
$job->tags()->sync([1, 2, 3]);
// خروجی: فقط tags با ID های مشخص شده باقی می‌مانند
```

#### c) ساختار درختی قطعه کد:

```
📁 belongsToMany Implementation
├── �� app/Models/Job.php
│   ├── 🔧 public function tags()
│   │   ├── return $this->belongsToMany(Tag::class, 'job_tag')
│   │   └── �� دسترسی: $job->tags
│   │
│   └── 📋 Pivot Methods
│       ├── attach($tagId)
│       ├── detach($tagId)
│       └── sync($tagIds)
│
├── �� app/Models/Tag.php
│   ├── 🔧 public function jobs()
│   │   ├── return $this->belongsToMany(Job::class, 'job_tag')
│   │   └── �� دسترسی: $tag->jobs
│   │
│   └── �� Collection Methods
│       ├── first()
│       ├── count()
│       └── where()
│
└── 📄 Tinker Examples
    ├── $job->tags (Collection)
    ├── $tag->jobs (Collection)
    ├── attach/detach methods
    └── sync method
```

#### d) نقشه ارتباط با سایر فایل‌ها:

```
�� ارتباطات belongsToMany:
├── �� app/Models/Job.php
│   ├── �� app/Models/Tag.php
│   ├── �� database: job_listings table
│   └── 🔗 database: job_tag pivot table
│
├── �� app/Models/Tag.php
│   ├── �� app/Models/Job.php
│   ├── 🔗 database: tags table
│   └── 🔗 database: job_tag pivot table
│
├── 📄 database/migrations/
│   ├── create_tags_and_job_tag_tables.php
│   └── foreign_key_constraints.php
│
└── �� routes/web.php
    └── 🔗 controllers برای مدیریت tags
```

#### e) فلوی معنایی و گرامری:

**فلوی معنایی:**
1. تعریف رابطه در هر دو مدل
2. Laravel pivot table را تشخیص می‌دهد
3. هنگام دسترسی به رابطه، SQL query اجرا می‌شود
4. نتیجه به صورت Collection برگردانده می‌شود
5. امکان استفاده از متدهای attach/detach/sync

**فلوی گرامری:**
```php
// Model Definition Pattern
public function relationship()
{
    return $this->belongsToMany(RelatedModel::class, 'pivot_table');
}

// Usage Pattern
$model->relationship; // Collection
$model->relationship()->attach($id);
$model->relationship()->detach($id);
$model->relationship()->sync($ids);
```

**نکات فنی مهم:**
- ✅ Pivot Table: نامگذاری به صورت alphabetical
- ✅ Collection Return: همیشه Collection برگردانده می‌شود
- ✅ Pivot Methods: attach, detach, sync
- ✅ Timestamps: امکان tracking زمان در pivot table
- ⚠️ Performance: در داده‌های زیاد از Eager Loading استفاده کنید

---

## 🔗 بخش 3: مفاهیم پیشرفته

### 📋 3.1 مرور انواع روابط

#### a) معنای مبحث:
Laravel انواع مختلفی از روابط را پشتیبانی می‌کند که هر کدام برای سناریوهای خاصی طراحی شده‌اند.

#### b) قطعه کد آموزشی:

```php:relationship_types_overview.php
<?php
// مرور انواع روابط Laravel Eloquent

// 1. hasOne - یک به یک
class User extends Model
{
    public function profile()
    {
        return $this->hasOne(Profile::class);
        // خروجی: یک Profile برای هر User
    }
}

// 2. hasMany - یک به چند
class User extends Model
{
    public function posts()
    {
        return $this->hasMany(Post::class);
        // خروجی: Collection از Post ها برای هر User
    }
}

// 3. belongsTo - چند به یک
class Post extends Model
{
    public function user()
    {
        return $this->belongsTo(User::class);
        // خروجی: User مرتبط با این Post
    }
}

// 4. belongsToMany - چند به چند
class Post extends Model
{
    public function tags()
    {
        return $this->belongsToMany(Tag::class);
        // خروجی: Collection از Tag ها برای هر Post
    }
}

// 5. hasManyThrough - یک به چند از طریق مدل دیگر
class Country extends Model
{
    public function posts()
    {
        return $this->hasManyThrough(Post::class, User::class);
        // خروجی: تمام Post های کشور از طریق User ها
    }
}
```

#### c) ساختار درختی قطعه کد:

```
�� Relationship Types Overview
├── �� hasOne (1:1)
│   ├── User -> Profile
│   ├── return $this->hasOne(Profile::class)
│   └── �� نتیجه: یک instance
│
├── 🔧 hasMany (1:N)
│   ├── User -> Posts
│   ├── return $this->hasMany(Post::class)
│   └── �� نتیجه: Collection
│
├── 🔧 belongsTo (N:1)
│   ├── Post -> User
│   ├── return $this->belongsTo(User::class)
│   └── �� نتیجه: یک instance
│
├── �� belongsToMany (N:N)
│   ├── Post -> Tags
│   ├── return $this->belongsToMany(Tag::class)
│   └── �� نتیجه: Collection
│
└── 🔧 hasManyThrough (1:N through)
    ├── Country -> Posts (through Users)
    ├── return $this->hasManyThrough(Post::class, User::class)
    └── �� نتیجه: Collection
```

#### d) نقشه ارتباط با سایر فایل‌ها:

```
🔄 ارتباطات انواع روابط:
├── 📄 app/Models/
│   ├── User.php
│   ├── Profile.php
│   ├── Post.php
│   ├── Tag.php
│   └── Country.php
│
├── 📄 database/migrations/
│   ├── create_users_table.php
│   ├── create_profiles_table.php
│   ├── create_posts_table.php
│   ├── create_tags_table.php
│   └── create_post_tag_table.php
│
└── �� routes/web.php
    └── 🔗 controllers برای تست روابط
```

#### e) فلوی معنایی و گرامری:

**فلوی معنایی:**
1. انتخاب نوع رابطه بر اساس نیاز
2. تعریف متد در مدل
3. Laravel به صورت خودکار SQL queries را مدیریت می‌کند
4. نتیجه بر اساس نوع رابطه متفاوت است
5. امکان ترکیب انواع مختلف روابط

**فلوی گرامری:**
```php
// hasOne Pattern
return $this->hasOne(RelatedModel::class);

// hasMany Pattern
return $this->hasMany(RelatedModel::class);

// belongsTo Pattern
return $this->belongsTo(RelatedModel::class);

// belongsToMany Pattern
return $this->belongsToMany(RelatedModel::class, 'pivot_table');
```

**نکات فنی مهم:**
- ✅ hasOne/hasMany: مدل فعلی صاحب رابطه است
- ✅ belongsTo: مدل فعلی به مدل دیگر تعلق دارد
- ✅ belongsToMany: نیاز به pivot table دارد
- ✅ Foreign Keys: به صورت خودکار تشخیص داده می‌شوند
- ⚠️ Performance: انتخاب نوع رابطه بر عملکرد تأثیر دارد

---

### �� 3.2 بهترین شیوه‌ها (Best Practices)

#### a) معنای مبحث:
بهترین شیوه‌ها برای استفاده از Eloquent Relationships که عملکرد و نگهداری کد را بهبود می‌دهند.

#### b) قطعه کد آموزشی:

```php:best_practices_example.php
<?php
// بهترین شیوه‌های Eloquent Relationships

// 1. نامگذاری صحیح متدها
class Job extends Model
{
    // ✅ خوب: نام واضح و معنادار
    public function employer()
    {
        return $this->belongsTo(Employer::class);
    }
    
    // ✅ خوب: نام جمع برای hasMany
    public function tags()
    {
        return $this->belongsToMany(Tag::class);
    }
    
    // ❌ بد: نام مبهم
    // public function related()
    // {
    //     return $this->belongsTo(Employer::class);
    // }
}

// 2. استفاده از Eager Loading
class JobController extends Controller
{
    public function index()
    {
        // ✅ خوب: جلوگیری از N+1 problem
        $jobs = Job::with(['employer', 'tags'])->get();
        
        // ❌ بد: N+1 problem
        // $jobs = Job::all();
        // foreach ($jobs as $job) {
        //     echo $job->employer->name; // هر بار SQL query
        // }
    }
}

// 3. استفاده از Local Scopes
class Job extends Model
{
    public function scopeActive($query)
    {
        return $query->where('status', 'active');
    }
    
    public function scopeWithEmployer($query)
    {
        return $query->with('employer');
    }
}

// 4. استفاده از Accessors/Mutators
class Job extends Model
{
    // Accessor برای فرمت‌بندی salary
    public function getFormattedSalaryAttribute()
    {
        return '$' . number_format($this->salary, 2);
    }
    
    // Mutator برای ذخیره salary
    public function setSalaryAttribute($value)
    {
        $this->attributes['salary'] = $value * 100; // ذخیره به صورت cents
    }
}
```

#### c) ساختار درختی قطعه کد:

```
📁 Best Practices Implementation
├── �� Naming Conventions
│   ├── ✅ employer() (belongsTo)
│   ├── ✅ tags() (belongsToMany)
│   ├── ✅ jobs() (hasMany)
│   └── ❌ related() (مبهم)
│
├── 🔧 Eager Loading
│   ├── ✅ Job::with(['employer', 'tags'])->get()
│   ├── ❌ Job::all() + foreach
│   └── 🎯 نتیجه: جلوگیری از N+1
│
├── �� Local Scopes
│   ├── scopeActive()
│   ├── scopeWithEmployer()
│   └── �� نتیجه: کد تمیزتر
│
└── �� Accessors/Mutators
    ├── getFormattedSalaryAttribute()
    ├── setSalaryAttribute()
    └── 🎯 نتیجه: منطق business در مدل
```

#### d) نقشه ارتباط با سایر فایل‌ها:

```
🔄 ارتباطات Best Practices:
├── 📄 app/Models/
│   ├── Job.php
│   ├── Employer.php
│   └── Tag.php
│
├── 📄 app/Http/Controllers/
│   ├── JobController.php
│   └── EmployerController.php
│
├── �� routes/web.php
│   └── �� route definitions
│
└── 📄 resources/views/
    ├── jobs/index.blade.php
    └── jobs/show.blade.php
```

#### e) فلوی معنایی و گرامری:

**فلوی معنایی:**
1. نامگذاری متدها بر اساس رابطه
2. استفاده از Eager Loading برای بهینه‌سازی
3. تعریف Local Scopes برای کد تمیزتر
4. استفاده از Accessors/Mutators برای منطق business
5. تست و بررسی عملکرد

**فلوی گرامری:**
```php
// Naming Pattern
public function relationshipName()
{
    return $this->relationshipType(RelatedModel::class);
}

// Eager Loading Pattern
Model::with(['relationship1', 'relationship2'])->get();

// Scope Pattern
public function scopeName($query, $parameter = null)
{
    return $query->where('column', $parameter);
}

// Accessor Pattern
public function getAttributeNameAttribute()
{
    return $this->attributes['column'];
}
```

**نکات فنی مهم:**
- ✅ Naming: نام‌های واضح و معنادار استفاده کنید
- ✅ Eager Loading: همیشه برای جلوگیری از N+1 استفاده کنید
- ✅ Scopes: برای فیلترهای پرکاربرد تعریف کنید
- ✅ Accessors: برای فرمت‌بندی و محاسبات استفاده کنید
- ⚠️ Performance: همیشه عملکرد را بررسی کنید
- ✅ Testing: روابط را تست کنید

---

## �� خلاصه و نکات کلیدی

### �� نکات مهم برای یادآوری:

1. **انواع روابط اصلی:**
   - `belongsTo`: چند به یک
   - `hasMany`: یک به چند
   - `belongsToMany`: چند به چند

2. **Lazy Loading vs Eager Loading:**
   - Lazy: بارگذاری در زمان نیاز
   - Eager: بارگذاری پیش‌فرض برای جلوگیری از N+1

3. **Pivot Tables:**
   - نامگذاری: alphabetical order
   - Foreign key constraints
   - Cascade delete

4. **بهترین شیوه‌ها:**
   - نامگذاری صحیح
   - استفاده از Eager Loading
   - تعریف Local Scopes
   - استفاده از Accessors/Mutators

این جزوه تمام مباحث مربوط به Laravel Eloquent Relationships را پوشش می‌دهد و برای استفاده در سطح حرفه‌ای طراحی شده است.