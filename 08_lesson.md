# راهنمای جامع Laravel Eloquent ORM
## نسخه Enterprise برای شرکت‌های فناوری پیشرو

---

## 🗂️ نقشه ساختار درختی مباحث

```
Laravel Eloquent ORM
├── 1. مقدمه و مفاهیم اصلی
│   ├── تعریف Eloquent
│   ├── مزایا و کاربردها
│   └── مقایسه با SQL خام
├── 2. تنظیمات و پیکربندی
│   ├── تبدیل کلاس معمولی به Eloquent Model
│   ├── تنظیمات پایگاه داده
│   └── کانکشن و تنظیمات محیط
├── 3. عملیات CRUD اصلی
│   ├── خواندن داده‌ها (Reading)
│   ├── ایجاد داده‌ها (Creating)
│   ├── به‌روزرسانی داده‌ها (Updating)
│   └── حذف داده‌ها (Deleting)
├── 4. Mass Assignment و امنیت
│   ├── مفهوم Mass Assignment
│   ├── تنظیمات fillable
│   └── محافظت در برابر حملات
├── 5. Artisan Tinker
│   ├── معرفی محیط Tinker
│   ├── تست و آزمایش کدها
│   └── دیباگینگ و عیب‌یابی
├── 6. مولدهای Artisan
│   ├── ایجاد Model جدید
│   ├── تولید Migration همراه Model
│   └── گزینه‌های مختلف تولید
└── 7. Database Factories
    ├── مفهوم و کاربرد Factory
    ├── تولید داده‌های تست
    └── کار با Faker Library
```

---

## 1️⃣ مقدمه و مفاهیم اصلی Eloquent ORM

### 📖 توضیحات جامع و کامل

Laravel Eloquent یک Object-Relational Mapping (ORM) قدرتمند و انعطاف‌پذیر است که یکی از ستون‌های اصلی framework Laravel محسوب می‌شود. این ابزار به توسعه‌دهندگان اجازه می‌دهد تا بدون نیاز به نوشتن کوئری‌های SQL پیچیده، با پایگاه داده تعامل کنند. Eloquent با استفاده از الگوی Active Record، هر جدول پایگاه داده را به یک کلاس PHP تبدیل می‌کند که امکان انجام عملیات CRUD (Create, Read, Update, Delete) را به صورت شیء‌گرا فراهم می‌کند.

این سیستم بسیار قدرتمند است زیرا نه تنها کد شما را قابل خواندن‌تر و قابل نگهداری‌تر می‌کند، بلکه از بسیاری از خطاهای رایج در نوشتن کوئری‌های SQL جلوگیری می‌کند. Eloquent به طور خودکار مدیریت connection pool، caching، و حتی بهینه‌سازی کوئری‌ها را انجام می‌دهد. این ویژگی‌ها باعث شده است که شرکت‌های بزرگ فناوری مانند فیسبوک، گوگل، و آمازون از الگوهای مشابه در سیستم‌های داخلی خود استفاده کنند.

### 💻 قطعه کد آموزشی

```php
<?php
// app/Models/Job.php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Job extends Model
{
    // تعریف جدول مربوطه (اختیاری - Laravel خودکار تشخیص می‌دهد)
    protected $table = 'jobs';
    
    // تعریف کلید اصلی (اختیاری - پیش‌فرض 'id')
    protected $primaryKey = 'id';
    
    // فعال/غیرفعال کردن timestamps خودکار
    public $timestamps = true;
    
    // مثال استفاده از Eloquent
    public static function getAllJobs()
    {
        // خواندن تمام job ها از پایگاه داده
        return self::all(); // خروجی: Collection از Job objects
    }
    
    public static function findJobById($id)
    {
        // جستجوی job بر اساس ID
        return self::find($id); // خروجی: Job object یا null
    }
}
```

### 🔄 فلوی معنایی و گرامری

1. **تعریف کلاس**: کلاس Job از Model ارث‌بری می‌کند
2. **تنظیمات اولیه**: Laravel خودکار نام جدول را از نام کلاس تشخیص می‌دهد
3. **کلید اصلی**: به طور پیش‌فرض 'id' به عنوان primary key در نظر گرفته می‌شود
4. **Timestamps**: Laravel خودکار فیلدهای created_at و updated_at را مدیریت می‌کند
5. **متد all()**: تمام رکوردهای جدول را به صورت Collection برمی‌گرداند
6. **متد find()**: رکورد خاص را بر اساس primary key جستجو می‌کند

### 🔑 نکات فنی کلیدی و مهم

1. **Convention over Configuration**: Laravel از قراردادهای از پیش تعریف شده استفاده می‌کند
2. **Active Record Pattern**: هر instance از model یک رکورد از پایگاه داده را نمایندگی می‌کند
3. **Lazy Loading**: کوئری‌ها تا زمان نیاز واقعی اجرا نمی‌شوند
4. **Type Casting**: Laravel خودکار انواع داده‌ها را تبدیل می‌کند
5. **Memory Management**: Eloquent بهینه‌سازی حافظه را خودکار انجام می‌دهد

### 🗺️ نقشه ارتباط با سایر فایل‌ها

```
app/Models/Job.php
├── 📁 database/migrations/
│   └── create_jobs_table.php (تعریف ساختار جدول)
├── 📁 app/Http/Controllers/
│   └── JobController.php (کنترل منطق برنامه)
├── 📁 resources/views/
│   └── jobs/ (نمایش داده‌ها)
├── 📁 routes/
│   └── web.php (تعریف مسیرها)
└── 📁 config/
    └── database.php (تنظیمات اتصال پایگاه داده)
```

---

## 2️⃣ عملیات خواندن داده‌ها (Reading Operations)

### 📖 توضیحات جامع و کامل

یکی از مهم‌ترین عملیات در هر برنامه وب، خواندن و بازیابی داده‌ها از پایگاه داده است. Laravel Eloquent روش‌های مختلفی برای خواندن داده‌ها ارائه می‌دهد که هر کدام برای سناریوهای خاصی بهینه شده‌اند. متد `all()` برای دریافت تمام رکوردها، `find()` برای جستجوی رکورد خاص بر اساس کلید اصلی، و `where()` برای اعمال شرایط پیچیده استفاده می‌شوند.

این عملیات به گونه‌ای طراحی شده‌اند که حداکثر کارایی را داشته باشند. Laravel از تکنیک‌های مختلفی مانند Query Builder، Connection Pooling، و Result Caching استفاده می‌کند تا عملکرد بهینه‌ای ارائه دهد. همچنین، تمام این عملیات از SQL Injection محافظت می‌کنند و به طور خودکار داده‌های ورودی را sanitize می‌کنند.

### 💻 قطعه کد آموزشی

```php
<?php
// app/Http/Controllers/JobController.php

namespace App\Http\Controllers;

use App\Models\Job;
use Illuminate\Http\Request;

class JobController extends Controller
{
    public function index()
    {
        // خواندن تمام job ها
        $jobs = Job::all();
        
        // خروجی: Illuminate\Database\Eloquent\Collection
        // حاوی تمام رکوردهای جدول jobs
        
        return view('jobs.index', compact('jobs'));
    }
    
    public function show($id)
    {
        // جستجوی job خاص بر اساس ID
        $job = Job::find($id);
        
        // بررسی وجود رکورد
        if (!$job) {
            return redirect()->route('jobs.index')
                           ->with('error', 'Job not found');
        }
        
        // خروجی: Job model instance یا null
        return view('jobs.show', compact('job'));
    }
    
    public function getHighSalaryJobs()
    {
        // جستجو با شرایط خاص
        $highSalaryJobs = Job::where('salary', '>', 50000)
                            ->orderBy('salary', 'desc')
                            ->take(10)
                            ->get();
        
        // خروجی: Collection از Job objects
        return $highSalaryJobs;
    }
}
```

### 🔄 فلوی معنایی و گرامری

1. **Job::all()**: کلاس Job را فراخوانی و تمام رکوردها را دریافت می‌کند
2. **Collection Return**: نتیجه به صورت Collection برگردانده می‌شود
3. **Job::find($id)**: جستجوی مستقیم بر اساس primary key
4. **Null Check**: بررسی وجود رکورد برای جلوگیری از خطا
5. **Method Chaining**: امکان زنجیره‌ای کردن متدها برای کوئری‌های پیچیده
6. **Query Builder**: تبدیل خودکار به SQL بهینه

### 🔑 نکات فنی کلیدی و مهم

1. **Performance**: متد `all()` تمام رکوردها را در حافظه لود می‌کند
2. **Memory Usage**: برای جداول بزرگ از pagination استفاده کنید
3. **Eager Loading**: برای جلوگیری از N+1 Problem
4. **Caching**: Laravel خودکار نتایج مشابه را cache می‌کند
5. **Database Connection**: اتصال به پایگاه داده تنها در صورت نیاز برقرار می‌شود

### 🗺️ نقشه ارتباط با سایر فایل‌ها

```
JobController.php
├── 📄 app/Models/Job.php
│   └── Eloquent Model (منطق داده)
├── 📄 resources/views/jobs/
│   ├── index.blade.php (نمایش لیست)
│   └── show.blade.php (نمایش جزئیات)
├── 📄 routes/web.php
│   └── Route::get('/jobs', [JobController::class, 'index'])
└── 📄 database/migrations/
    └── create_jobs_table.php
```

---

## 3️⃣ عملیات ایجاد داده‌ها (Creating Operations)

### 📖 توضیحات جامع و کامل

ایجاد داده‌های جدید یکی از عملیات محوری در هر سیستم است. Laravel Eloquent روش‌های مختلفی برای ایجاد رکوردهای جدید ارائه می‌دهد. متد `create()` امکان ایجاد و ذخیره همزمان را فراهم می‌کند، در حالی که می‌توان از `new` و `save()` برای کنترل بیشتر استفاده کرد. این عملیات شامل قابلیت‌های امنیتی پیشرفته‌ای مانند Mass Assignment Protection است.

Laravel برای محافظت از برنامه در برابر حملات Mass Assignment، سیستم فیلتر کردن فیلدهای قابل تنظیم را ایجاد کرده است. این سیستم از ویژگی `fillable` استفاده می‌کند تا مشخص کند کدام فیلدها می‌توانند به طور دسته‌ای تنظیم شوند. این رویکرد امنیتی در شرکت‌های بزرگ فناوری به عنوان یک استاندارد شناخته می‌شود.

### 💻 قطعه کد آموزشی

```php
<?php
// app/Models/Job.php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Job extends Model
{
    // تعریف فیلدهای قابل Mass Assignment
    protected $fillable = [
        'title',
        'salary',
        'description',
        'company_name'
    ];
    
    // متد برای ایجاد job جدید
    public static function createJob($data)
    {
        // روش اول: استفاده از create() - ایجاد و ذخیره همزمان
        $job = self::create([
            'title' => $data['title'],
            'salary' => $data['salary'],
            'description' => $data['description'] ?? null,
            'company_name' => $data['company_name']
        ]);
        
        // خروجی: Job model instance که در پایگاه داده ذخیره شده
        return $job;
    }
    
    public static function createJobManually($data)
    {
        // روش دوم: ایجاد manual با کنترل بیشتر
        $job = new self();
        $job->title = $data['title'];
        $job->salary = $data['salary'];
        $job->description = $data['description'] ?? null;
        $job->company_name = $data['company_name'];
        
        // ذخیره در پایگاه داده
        $job->save();
        
        // خروجی: Job model instance
        return $job;
    }
}
```

### 🔄 فلوی معنایی و گرامری

1. **تعریف $fillable**: مشخص کردن فیلدهای قابل Mass Assignment
2. **متد create()**: ایجاد رکورد جدید و ذخیره خودکار در پایگاه داده
3. **Array Parameter**: پارامتر ورودی به صورت آرایه associative
4. **Automatic Timestamps**: Laravel خودکار created_at و updated_at را تنظیم می‌کند
5. **Return Instance**: متد instance جدید از model را برمی‌گرداند
6. **Manual Creation**: ایجاد step-by-step با کنترل کامل

### 🔑 نکات فنی کلیدی و مهم

1. **Mass Assignment Protection**: محافظت از حملات امنیتی
2. **Validation**: همیشه داده‌های ورودی را validate کنید
3. **Database Transactions**: برای عملیات پیچیده از transaction استفاده کنید
4. **Error Handling**: مدیریت خطاهای احتمالی در عملیات ایجاد
5. **Performance**: متد `create()` برای bulk operations بهینه‌تر است

### 🗺️ نقشه ارتباط با سایر فایل‌ها

```
Job Model (create operations)
├── 📄 app/Http/Controllers/JobController.php
│   └── store() method (پردازش فرم ایجاد)
├── 📄 app/Http/Requests/
│   └── CreateJobRequest.php (اعتبارسنجی داده‌ها)
├── 📄 resources/views/jobs/
│   └── create.blade.php (فرم ایجاد)
├── 📄 database/migrations/
│   └── create_jobs_table.php (ساختار جدول)
└── 📄 routes/web.php
    └── POST /jobs (مسیر ایجاد)
```

---

## 4️⃣ Mass Assignment و تنظیمات امنیتی

### 📖 توضیحات جامع و کامل

Mass Assignment یکی از مفاهیم بسیار مهم در Laravel است که به امنیت برنامه مربوط می‌شود. این قابلیت اجازه می‌دهد که چندین فیلد را همزمان تنظیم کنیم، اما در عین حال خطر امنیتی جدی ایجاد می‌کند. تصور کنید کاربر بدخواهی فرم شما را دستکاری کند و فیلدهای اضافی مانند `is_admin` یا `user_id` را به درخواست اضافه کند. Laravel با استفاده از `fillable` این مشکل را حل می‌کند.

ویژگی `fillable` مشخص می‌کند که کدام فیلدها مجاز به Mass Assignment هستند. تمام فیلدهای دیگر نادیده گرفته می‌شوند. این رویکرد در شرکت‌های بزرگ فناوری به عنوان یک best practice شناخته می‌شود. همچنین می‌توان از `guarded` برای مشخص کردن فیلدهای محافظت شده استفاده کرد. این سیستم امنیتی باعث می‌شود که حتی اگر کاربر بدخواه بخواهد داده‌های غیرمجاز ارسال کند، سیستم آن‌ها را رد کند.

### 💻 قطعه کد آموزشی

```php
<?php
// app/Models/User.php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    // روش اول: استفاده از fillable (توصیه شده)
    protected $fillable = [
        'name',
        'email',
        'password',
        'phone'
    ];
    
    // روش دوم: استفاده از guarded
    // protected $guarded = ['id', 'is_admin', 'created_at', 'updated_at'];
    
    // مثال استفاده Mass Assignment
    public static function createUser($requestData)
    {
        // این کد فقط فیلدهای fillable را در نظر می‌گیرد
        $user = self::create($requestData);
        
        // حتی اگر $requestData شامل 'is_admin' باشد، نادیده گرفته می‌شود
        // خروجی: User instance با فیلدهای مجاز
        return $user;
    }
    
    // مثال خطرناک (بدون محافظت)
    public static function unsafeCreate($requestData)
    {
        $user = new self();
        
        // این روش خطرناک است
        foreach ($requestData as $field => $value) {
            $user->$field = $value; // هیچ محافظتی وجود ندارد
        }
        
        $user->save();
        return $user;
    }
}
```

### 🔄 فلوی معنایی و گرامری

1. **تعریف $fillable**: لیست فیلدهای مجاز برای Mass Assignment
2. **بررسی خودکار**: Laravel خودکار فیلدهای غیرمجاز را حذف می‌کند
3. **Exception Handling**: در صورت عدم تعریف fillable، خطا رخ می‌دهد
4. **Security Layer**: لایه امنیتی خودکار برای محافظت از داده‌ها
5. **Filtering Process**: فرآیند فیلتر کردن فیلدهای ورودی
6. **Safe Assignment**: انتساب امن فیلدها به model

### 🔑 نکات فنی کلیدی و مهم

1. **Security First**: همیشه امنیت را در اولویت قرار دهید
2. **Whitelist Approach**: از رویکرد لیست سفید (fillable) استفاده کنید
3. **Regular Updates**: به طور منظم لیست fillable را بروزرسانی کنید
4. **Code Review**: همیشه کدهای Mass Assignment را بررسی کنید
5. **Testing**: تست‌های امنیتی برای Mass Assignment بنویسید

### 🗺️ نقشه ارتباط با سایر فایل‌ها

```
Mass Assignment Security
├── 📄 app/Models/ (تمام Models)
│   ├── User.php (تعریف fillable)
│   ├── Job.php (تعریف fillable)
│   └── Post.php (تعریف fillable)
├── 📄 app/Http/Controllers/
│   └── *.php (استفاده از create methods)
├── 📄 app/Http/Requests/
│   └── *.php (اعتبارسنجی داده‌ها)
├── 📄 tests/Feature/
│   └── MassAssignmentTest.php (تست امنیتی)
└── 📄 config/app.php
    └── security configurations
```

---

## 5️⃣ PHP Artisan Tinker - محیط تعاملی

### 📖 توضیحات جامع و کامل

PHP Artisan Tinker یکی از قدرتمندترین ابزارهای Laravel برای توسعه‌دهندگان است. این ابزار یک محیط REPL (Read-Eval-Print Loop) فراهم می‌کند که به شما اجازه می‌دهد کدهای PHP و Laravel را به صورت تعاملی اجرا کنید. Tinker برای تست کردن کدها، دیباگینگ، آزمایش کوئری‌ها، و حتی انجام عملیات مدیریتی بر روی پایگاه داده بسیار مفید است.

این ابزار در محیط production نیز قابل استفاده است و به مدیران سیستم اجازه می‌دهد تا عملیات پیچیده را بدون نیاز به نوشتن اسکریپت‌های جداگانه انجام دهند. Tinker تمام کلاس‌های Laravel، مدل‌ها، و حتی helper functionها را در اختیار شما قرار می‌دهد. شرکت‌های بزرگ فناوری از ابزارهای مشابه برای مدیریت سیستم‌های پیچیده استفاده می‌کنند.

### 💻 قطعه کد آموزشی

```bash
# Terminal/Command Line
php artisan tinker
```

```php
<?php
// داخل محیط Tinker

// 1. تست کردن مدل‌ها
$jobs = App\Models\Job::all();
// خروجی: Collection از تمام job ها

// 2. ایجاد رکورد جدید
$job = App\Models\Job::create([
    'title' => 'Senior Laravel Developer',
    'salary' => 85000,
    'company_name' => 'Tech Company'
]);
// خروجی: Job model instance

// 3. جستجو و بررسی
$highSalaryJobs = App\Models\Job::where('salary', '>', 50000)->get();
echo $highSalaryJobs->count(); // خروجی: تعداد job های با حقوق بالا

// 4. تست متدهای مختلف
$job = App\Models\Job::find(1);
echo $job->title; // خروجی: عنوان job

// 5. حذف رکورد
$job = App\Models\Job::find(2);
$job->delete(); // خروجی: true (موفق) یا false (ناموفق)

// 6. تست کردن relationships
$user = App\Models\User::with('jobs')->first();
echo $user->jobs->count(); // خروجی: تعداد job های این user

// 7. تست کردن helper functions
echo bcrypt('password'); // خروجی: رمز عبور hash شده

// 8. کار با Collections
$collection = collect([1, 2, 3, 4, 5]);
echo $collection->sum(); // خروجی: 15

// خروج از Tinker
exit;
```

### 🔄 فلوی معنایی و گرامری

1. **ورود به Tinker**: اجرای دستور `php artisan tinker`
2. **Auto-loading**: تمام کلاس‌های Laravel خودکار لود می‌شوند
3. **Interactive Execution**: هر خط کد فوری اجرا می‌شود
4. **Real-time Results**: نتایج فوری نمایش داده می‌شوند
5. **Database Connection**: اتصال مستقیم به پایگاه داده
6. **Error Handling**: خطاها فوری نمایش داده می‌شوند

### 🔑 نکات فنی کلیدی و مهم

1. **Development Tool**: ابزار توسعه، نه production
2. **Memory Management**: مراقب استفاده از حافظه باشید
3. **Database Changes**: تغییرات real-time بر روی پایگاه داده
4. **Testing Environment**: محیط ایده‌آل برای تست
5. **Command History**: امکان استفاده از تاریخچه دستورات

### 🗺️ نقشه ارتباط با سایر فایل‌ها

```
PHP Artisan Tinker
├── 📄 app/Models/ (تمام Models)
│   └── دسترسی مستقیم به تمام مدل‌ها
├── 📄 database/
│   ├── migrations/ (تأثیر بر ساختار)
│   └── database.sqlite (اتصال مستقیم)
├── 📄 config/
│   └── database.php (تنظیمات اتصال)
├── 📄 app/Http/Controllers/
│   └── امکان تست کردن متدهای controller
└── 📄 vendor/
    └── تمام packages Laravel
```

---

## 6️⃣ مولدهای Artisan و ایجاد Model

### 📖 توضیحات جامع و کامل

Laravel Artisan یکی از قدرتمندترین ابزارهای command-line در اکوسیستم PHP است. دستورات `make` در Artisan امکان تولید سریع و استاندارد فایل‌های مختلف را فراهم می‌کنند. این ابزار نه تنها زمان توسعه‌دهندگان را صرفه‌جویی می‌کند، بلکه اطمینان می‌دهد که تمام فایل‌های تولید شده از ساختار و استانداردهای Laravel پیروی می‌کنند.

دستور `make:model` یکی از پرکاربردترین دستورات است که امکان ایجاد model همراه با فایل‌های مرتبط را فراهم می‌کند. با استفاده از گزینه‌های مختلف، می‌توان همزمان migration، controller، factory، و سایر فایل‌های مرتب

# Laravel Eloquent ORM - راهنمای پیشرفته و حرفه‌ای

## نقشه ساختار درختی مباحث

```
Laravel Eloquent ORM - مباحث پیشرفته
├── 6. مولدهای Artisan Commands
│   ├── 6.1. دستورات make و آپشن‌های مختلف
│   ├── 6.2. تولید Model با Migration همزمان
│   ├── 6.3. آپشن‌های پیشرفته (-a, -c, -m, -f, -p, -s)
│   └── 6.4. مدیریت و حذف فایل‌های تولید شده
└── 7. Database Factories
    ├── 7.1. مفهوم و کاربرد Factory
    ├── 7.2. ساختار Factory Class
    ├── 7.3. استفاده از Faker Library
    ├── 7.4. تولید داده‌های تست و محیط توسعه
    └── 7.5. تنظیمات و سفارشی‌سازی Factory
```

---

## 6. مولدهای Artisan Commands

### 6.1. توضیحات جامع مولدهای Artisan

مولدهای Artisan Commands یکی از قدرتمندترین ابزارهای Laravel برای تولید سریع و استاندارد کدها هستند. این دستورات به شما امکان تولید خودکار Models، Migrations، Controllers، Policies و سایر فایل‌های مورد نیاز را می‌دهند. در محیط‌های حرفه‌ای، استفاده از این مولدها باعث حفظ استانداردهای کدنویسی، کاهش خطاهای انسانی و افزایش سرعت توسعه می‌شود. هر مولد دارای آپشن‌های مختلفی است که امکان تولید همزمان چندین فایل مرتبط را فراهم می‌کند. این رویکرد در شرکت‌های بزرگ فناوری مانند Meta، Google و Amazon به عنوان Best Practice محسوب می‌شود زیرا باعث یکسان‌سازی ساختار پروژه و کاهش Time-to-Market می‌شود.

### 6.2. کد آموزشی: دستورات make اصلی

```php
// Terminal Commands - مشاهده تمام دستورات make
php artisan make

// مشاهده راهنمای دستور خاص
php artisan help make:model

// تولید Model ساده
php artisan make:model Comment

// تولید Model با Migration
php artisan make:model Post -m

// تولید Model با تمام فایل‌های مرتبط
php artisan make:model Product -a
```

**فایل تولید شده:** `app/Models/Comment.php`
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Comment extends Model
{
    use HasFactory;
    
    // این کلاس به صورت خودکار تولید شده است
    // و آماده برای سفارشی‌سازی می‌باشد
}
```

### 6.3. فلوی معنایی و گرامری

1. **دستور `php artisan make`**: نمایش تمام دستورات تولید موجود
2. **دستور `help`**: ارائه راهنمای کامل برای هر دستور
3. **پارامتر اول**: نام Model یا کلاس مورد نظر
4. **آپشن‌های کوتاه**: `-m` (Migration), `-c` (Controller), `-a` (All)
5. **تولید خودکار**: Laravel به صورت هوشمند فایل‌ها را در مسیرهای صحیح قرار می‌دهد
6. **Namespace**: به صورت خودکار تنظیم می‌شود
7. **استانداردسازی**: تمام فایل‌های تولیدی از الگوی یکسان پیروی می‌کنند

### 6.4. نکات فنی کلیدی

1. **آپشن `-a` (All)**: تولید Migration, Seeder, Factory, Policy, Resource Controller و Form Request
2. **آپشن `-m` (Migration)**: تولید Migration همراه با Model
3. **آپشن `-c` (Controller)**: تولید Resource Controller
4. **آپشن `-f` (Factory)**: تولید Database Factory
5. **آپشن `-p` (Policy)**: تولید Policy برای Authorization
6. **آپشن `-s` (Seeder)**: تولید Database Seeder
7. **ترکیب آپشن‌ها**: امکان استفاده همزمان از چندین آپشن

### 6.5. نقشه ارتباط فایل‌ها

```
Project Root
├── app/
│   ├── Models/
│   │   └── Comment.php ← تولید شده با make:model
│   ├── Http/Controllers/
│   │   └── CommentController.php ← تولید با -c
│   └── Policies/
│       └── CommentPolicy.php ← تولید با -p
├── database/
│   ├── migrations/
│   │   └── create_comments_table.php ← تولید با -m
│   ├── factories/
│   │   └── CommentFactory.php ← تولید با -f
│   └── seeders/
│       └── CommentSeeder.php ← تولید با -s
```

---

## 7. Database Factories

### 7.1. توضیحات جامع Database Factories

Database Factories یکی از قدرتمندترین ابزارهای Laravel برای تولید داده‌های تست و محیط توسعه هستند. این سیستم امکان تولید خودکار رکوردهای پایگاه داده با استفاده از داده‌های Fake اما منطقی را فراهم می‌کند. در محیط‌های حرفه‌ای، Factories برای تست‌نویسی، پر کردن محیط Development و Demo، و حتی Load Testing استفاده می‌شوند. آنها از کتابخانه Faker استفاده می‌کنند که قابلیت تولید انواع مختلف داده‌ها از جمله نام، ایمیل، آدرس، تاریخ و... را دارد. این رویکرد در شرکت‌های بزرگ فناوری به عنوان استاندارد طلایی برای مدیریت Test Data محسوب می‌شود و باعث افزایش کیفیت تست‌ها و کاهش وقت توسعه می‌شود.

### 7.2. کد آموزشی: ساختار Factory

**فایل:** `database/factories/UserFactory.php`
```php
<?php

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
            'first_name' => fake()->firstName(),      // تولید نام تصادفی
            'last_name' => fake()->lastName(),        // تولید نام خانوادگی
            'email' => fake()->unique()->safeEmail(), // ایمیل یکتا
            'email_verified_at' => now(),            // تاریخ تایید ایمیل
            'password' => '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi', // password
            'remember_token' => Str::random(10),     // توکن یادآوری
        ];
    }

    /**
     * تعریف حالت کاربر تایید نشده
     */
    public function unverified(): static
    {
        return $this->state(fn (array $attributes) => [
            'email_verified_at' => null,
        ]);
    }
}
```

### 7.3. کد آموزشی: استفاده از Factory در Tinker

```php
// استفاده در PHP Artisan Tinker
php artisan tinker

// تولید یک کاربر
>>> User::factory()->create()
// خروجی: App\Models\User {#4505 id: 1, first_name: "John", last_name: "Doe", ...}

// تولید 10 کاربر
>>> User::factory(10)->create()
// خروجی: مجموعه‌ای شامل 10 کاربر با داده‌های تصادفی

// تولید کاربر با مشخصات خاص
>>> User::factory()->create(['first_name' => 'Ahmad'])
// خروجی: کاربر با نام Ahmad و سایر مشخصات تصادفی

// تولید کاربر تایید نشده
>>> User::factory()->unverified()->create()
// خروجی: کاربر با email_verified_at = null
```

### 7.4. فلوی معنایی و گرامری

1. **کلاس Factory**: ارث‌بری از `Illuminate\Database\Eloquent\Factories\Factory`
2. **متد `definition()`**: تعریف ساختار پیش‌فرض داده‌ها
3. **تابع `fake()`**: دسترسی به instance از Faker
4. **متدهای Faker**: `firstName()`, `lastName()`, `safeEmail()`, `unique()`
5. **متد `create()`**: تولید و ذخیره در پایگاه داده
6. **متد `make()`**: تولید بدون ذخیره
7. **متد `state()`**: تعریف حالت‌های مختلف
8. **چین کردن متدها**: امکان ترکیب چندین متد

### 7.5. نکات فنی کلیدی

1. **HasFactory Trait**: باید در Model استفاده شود
2. **Convention over Configuration**: نام Factory باید با Model مطابقت داشته باشد
3. **State Methods**: برای تعریف حالت‌های مختلف مدل
4. **Faker Localization**: امکان تنظیم زبان داده‌های تولیدی
5. **Relationship Factory**: امکان تولید داده‌های مرتبط
6. **Factory Callbacks**: امکان تعریف عملیات پس از تولید
7. **Memory Management**: مدیریت حافظه در تولید حجم بالا داده

### 7.6. کد آموزشی پیشرفته: Factory با Relationships

**فایل:** `database/factories/PostFactory.php`
```php
<?php

namespace Database\Factories;

use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;

class PostFactory extends Factory
{
    public function definition(): array
    {
        return [
            'title' => fake()->sentence(6, true),           // عنوان 6 کلمه‌ای
            'body' => fake()->paragraphs(3, true),          // 3 پاراگراف
            'user_id' => User::factory(),                   // کاربر مرتبط
            'published_at' => fake()->dateTimeBetween('-1 year', 'now'),
            'status' => fake()->randomElement(['draft', 'published', 'archived']),
        ];
    }

    /**
     * حالت پست منتشر شده
     */
    public function published(): static
    {
        return $this->state(fn (array $attributes) => [
            'status' => 'published',
            'published_at' => now(),
        ]);
    }
}
```

### 7.7. فلوی معنایی Factory پیشرفته

1. **Relationship Factory**: استفاده از `User::factory()` برای تولید کاربر مرتبط
2. **Complex Faker**: استفاده از `paragraphs()`, `dateTimeBetween()`, `randomElement()`
3. **State Management**: تعریف حالت `published()` برای پست‌های منتشر شده
4. **Data Consistency**: حفظ منطق داده‌ها در حالت‌های مختلف
5. **Flexible Configuration**: امکان سفارشی‌سازی در زمان تولید

### 7.8. نقشه ارتباط کامل Factory System

```
Factory Ecosystem
├── Models/
│   ├── User.php (HasFactory trait)
│   └── Post.php (HasFactory trait)
├── Factories/
│   ├── UserFactory.php
│   │   ├── definition() → تولید داده پایه
│   │   └── unverified() → حالت خاص
│   └── PostFactory.php
│       ├── definition() → تولید داده پایه
│       ├── published() → حالت منتشر شده
│       └── User::factory() → ارتباط با UserFactory
├── Seeders/
│   └── DatabaseSeeder.php → استفاده از Factory
├── Tests/
│   └── Feature/PostTest.php → تست با Factory
└── Tinker Environment
    └── Interactive Factory Usage
```

---

## نتیجه‌گیری

مولدهای Artisan و Database Factories دو ابزار حیاتی برای توسعه حرفه‌ای اپلیکیشن‌های Laravel هستند. آنها نه تنها سرعت توسعه را افزایش می‌دهند، بلکه استانداردهای کدنویسی را حفظ کرده و کیفیت نهایی محصول را تضمین می‌کنند. در محیط‌های Enterprise، این ابزارها به عنوان پایه‌ای برای CI/CD، Test Automation و DevOps workflows استفاده می‌شوند.