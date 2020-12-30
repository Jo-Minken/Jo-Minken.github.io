---
layout: post
title: "Laravel Tutorial Notes-02"
date: 2020-11-11
categories: [PHP, Laravel]
---

### Facades

A note taking from [Laravel Tutorial - Facades](https://www.tutorialspoint.com/laravel/laravel_facades.htm)

> Facades provide a static interface to classes that are available in the application's service container. Laravel facades serve as static proxies to underlying classes in the service container.
> -- [Laravel Tutorial - Facades](https://www.tutorialspoint.com/laravel/laravel_facades.htm)

FYR: [【Laravel】ファサードとは？何が便利か？どういう仕組みか？](https://qiita.com/minato-naka/items/095f2a1beec1d09f423e)<br>
FYR: [Laravel ファサード(Facade)を理解する](https://reffect.co.jp/laravel/laravel-facade-understanding)<br>
FYR: [Laravel Document - Facades](https://laravel.com/docs/7.x/facades)

#### How to create Facade

1. Create PHP Class file.
2. Bind the class to `Service Provider`.
3. Register the Service Provider to **Config/app.php** as providers.
4. Create a new class extends to **Illminate/Support/Facades/Facade**.
5. Register the new class to **Config/app.php** as aliases.

Step 1. Create a class called **TestFacades.php**:
```php
# App/Test/TestFacades.php

<?php
namespace App\Test;
class TestFacades{
    public function testingFacades() {
        echo "Testing the Facades in Laravel.";
    }
}
?>
```

Step 2. Create a Facade class called **TestFacades.php**:
```php
# App/Test/Facades/TestFacades.php

<?php

namespace app\Test\Facades;
use Illuminate\Support\Facades\Facade;

class TestFacades extends Facade {
    protected static function getFacadeAccessor() { 
        return 'test'; 
    }
}
```

Step 3. Create a Facade class called **TestFacadesServiceProviders.php**:
```php
# App/Providers/TestFacadesServiceProviders.php

<?php

namespace App\Providers;

use App;
use Illuminate\Support\ServiceProvider;

class TestFacadesServiceProvider extends ServiceProvider {
    public function boot() {
        // ...
    }
    public function register() {
        App::bind('test',function() {
            return new \App\Test\TestFacades;
        });
    }
}
```

Step 4. Add Service Provider to config:
```php
# config/app.php

/* Application Service Providers */

// ...

App\Providers\TestFacadesServiceProvider::class,
```

Step 5. Add an alias to **config/app.php**:
```php
# config/app.php

"TestFacades" => App\Test\Facades\TestFacades::class,
```

Step 6. Add to Route:
```php
# app/Http/routes.php

Route::get('/facadeex', function() {
   return TestFacades::testingFacades();
});
``` 

### Contracts
A note taking from [Laravel Tutorial - Contracts](https://www.tutorialspoint.com/laravel/laravel_contracts.htm)

> Laravel contracts are a set of `interfaces` with various functionalities and core services provided by the framework.
> Contract contains no implementation and new dependencies so it is easy to write.
> -- [Laravel Tutorial - Contracts](https://www.tutorialspoint.com/laravel/laravel_contracts.htm)

FYR: [Illuminate Contracts](https://github.com/illuminate/contracts)

Example: Caching ad repository
```php
<?php

namespace App\Orders;
use Illuminate\Contracts\Cache\Repository as Cache;

class Repository{
   /*
      * The cache instance.
   */
   
   protected $cache;
   
   /*
      * Create a new repository instance.
      *
      * @param Cache $cache
      * @return void
   */
   
   public function __construct(Cache $cache) {
      $this->cache = $cache;
   }
}
```

### CSRF Protection

A note taking from [Laravel Tutorial - CSRF Protection](https://www.tutorialspoint.com/laravel/laravel_csrf_protection.htm)

> CSRF refers to `Cross Site Forgery attacks` on web applications. CSRF attacks are the **unauthorized activities** which the authenticated users of the system perform.
> CSRF is implemented within HTML forms declared inside the web applications. 
> Include a hidden validated CSRF token in the form, so that the CSRF protection middleware can validate the request. 
> -- [Laravel Tutorial - CSRF Protection](https://www.tutorialspoint.com/laravel/laravel_csrf_protection.htm)

```html
<form method = "POST" action="/profile">
   {% raw %}{{ csrf_field() }}{% endraw %}
   ...
</form>
```

### Authentication

A note taking from [Laravel Tutorial - Authentication](https://www.tutorialspoint.com/laravel/laravel_authentication.htm)

> Authentication is the process of identifying the user credentials by sessions.
> -- [Laravel Tutorial - Authentication](https://www.tutorialspoint.com/laravel/laravel_authentication.htm)

#### Laravel built-in Auth
```
$ php artisan make:auth
```
This command will create a scaffold of authentication including views about login / sign up / passwords and HomeController.

> Auth Facade can help to authenticate the users manually.
> It incldes the **attempt** method to verify the email and password.
> -- [Laravel Tutorial - Authentication](https://www.tutorialspoint.com/laravel/laravel_authentication.htm)

```php
# app/Http/Controllers/LoginController.php

<?php

namespace App\Http\Controllers;
use Illuminate\Support\Facades\Auth;

class LoginController extends Controller{
   /*
      * Handling authentication request
      *
      * @return Response
   */
   
   public function authenticate() {
      if (Auth::attempt(["email" => $email, "password" => $password])) {
      
         // Authentication passed...
         return redirect()->intended("dashboard");
      }
   }
}
```

### Authorization
A note taking from [Laravel Tutorial - Authorization](https://www.tutorialspoint.com/laravel/laravel_authorization.htm)

> Authorization involves checking the rights and permissions over the resources.
> -- [Laravel Tutorial - Authorization](https://www.tutorialspoint.com/laravel/laravel_authorization.htm)

#### Laravel: Gates and Policies
- Gates are used to determine if a user is authorized to perform a specified action defined in **App/Providers/AuthServiceProvider.php**.
- Policies are declared within an array in authorization mechanism.

```php
<?php

namespace App\Providers;

use Illuminate\Contracts\Auth\Access\Gate as GateContract;
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

class AuthServiceProvider extends ServiceProvider {
   /*
      * The policy mappings for the application.
      *
      * @var array
   */
   
   protected $policies = [
      "App\Model" => "App\Policies\ModelPolicy",
   ];
   
   /*
      * Register any application authentication / authorization services.
      *
      * @param \Illuminate\Contracts\Auth\Access\Gate $gate
      * @return void
   */
   
   public function boot(GateContract $gate) {
      $this->registerPolicies($gate);
   }
}
```

#### Guest User Gates
A note taking from [Laravel Tutorial - Guest User Gates](https://www.tutorialspoint.com/laravel/laravel_guest_user_gates.htm)

> The Guest User Gates feature is an add-on to the latest 5.7 version released in September 2018. This feature is used to initiate the authorization process for specific users.
> Allow guests to go authorization checks by using the specific nullable type hint. 
> -- [Laravel Tutorial - Guest User Gates](https://www.tutorialspoint.com/laravel/laravel_guest_user_gates.htm)

```php
<?php
Gate::define('view-post', function (?User $user) {
    // $user variable will be null when a guest user is passed to the gate
});
```

### Artisan Console
A note taking from [Laravel Tutorial - Artisan Console](https://www.tutorialspoint.com/laravel/laravel_artisan_console.htm)

> Artisan is the command line interface frequently used in Laravel and it includes a set of helpful commands for developing a web application.
> -- [Laravel Tutorial - Artisan Console](https://www.tutorialspoint.com/laravel/laravel_artisan_console.htm)

- `php artisan list`: list of available Artisan commands
- `php artisan help <command name>`: view the detail document about the command

#### Customized Artisan Commands
The commands are stored in **app/console/commands/**.

```
$ php artisan make:console <command name>
```

Example: Default Command
```php
<?php

namespace App\Console\Commands;
use Illuminate\Console\Command;

class DefaultCommand extends Command {
   /*
      * The name and signature of the console command.
      *
      * @var string
   */
   
   protected $signature = "mycommand:run"; // command:name
   
   /*
      * The console command description.
      *
      * @var string
   */
   
   protected $description = "Command description";
   
   /*
      * Create a new command instance.
      *
      * @return void
   */
   
   public function __construct() {
      parent::__construct();
   }
   
   /*
      * Execute the console command.
      *
      * @return mixed
   */
   
   public function handle() {
      // ... 
   }
}
```

FYR: [コンソールコマンド Laravel](https://readouble.com/laravel/5.3/ja/artisan.html)

#### Scheduling Artisan Commands
To start scheduler, `crontab` needs to be set up.

```
* * * * * cd /path-to-your-project && php artisan schedule:run >> /dev/null 2>&1
```

Defining schedules in **Kernel.php**:

```php
# App\Console\Kernel.php

<?php

namespace App\Console;

use Illuminate\Console\Scheduling\Schedule;
use Illuminate\Foundation\Console\Kernel as ConsoleKernel;

class Kernel extends ConsoleKernel {
   /*
      * The Artisan commands provided by your application.
      *
      * @var array
   */
   
   protected $commands = [
       Commands\DefaultCommand::class
   ];
   
   /*
      * Define the application"s command schedule.
      *
      * @param \Illuminate\Console\Scheduling\Schedule $schedule
      * @return void
   */
   
   protected function schedule(Schedule $schedule) {
       $schedule->command("mycommand:run")->hourly();
   }
}
```

FYR: [Laravel Document: Task Scheduling](https://laravel.com/docs/8.x/scheduling)

### Encryption
A note taking from [Laravel Tutorial - Encryption](https://www.tutorialspoint.com/laravel/laravel_encryption.htm)

> Laravel uses AES-256 and AES-128 encrypter, which uses Open SSL for encryption. All the values included in Laravel are signed using the protocol Message Authentication Code so that the underlying value cannot be tampered with once it is encrypted.
> -- [Laravel Tutorial - Encryption](https://www.tutorialspoint.com/laravel/laravel_encryption.htm)

#### Encryption Process
> Encryption of a value can be done by using the encrypt helper in the controllers of Laravel class. These values are encrypted using OpenSSL and AES-256 cipher.
> -- [Laravel Tutorial - Encryption](https://www.tutorialspoint.com/laravel/laravel_encryption.htm)

```php
// encryption
$secret = encrypt($request->secret);

// decryption
try {
    $decrypted = decrypt($encryptedValue);
} catch (DecryptException $e) {
    // Handle the exception 
    // Errors may caused by invalid MAC (Message Authentication code) being used
}

```

### Hashing
A note taking from [Laravel Tutorial - Hashing](https://www.tutorialspoint.com/laravel/laravel_hashing.htm)

> Hashing is the process of transforming a string of characters into a shorter fixed value or a key that represents the original string.
> -- [Laravel Tutorial - Hashing](https://www.tutorialspoint.com/laravel/laravel_hashing.htm)

```php
use Illuminate\Support\Facades\Hash;

// Hash
Hash::make($request->newPassword)

// Verify
if (Hash::check('plain-text', $hashedPassword)) {
    // The passwords match...
}
```
