---
layout: post
title: "Laravel Tutorial Notes-01"
date: 2020-11-10
categories: [PHP, Laravel]
---

## Laravel Tutorial Notes
A note taking from [Laravel Tutorial (tutorialspoint.com)](https://www.tutorialspoint.com/laravel/index.htm)

Tools:
- Composer: tool manager for the dependencies of libraries. (composer.json)
- Artisan: command line interface for Laravel (incorporated from Symphony framework)

### Installation
A note taking from [Laravel Tutorial - Installation](https://www.tutorialspoint.com/laravel/laravel_installation.htm)

01. Install [Composer](https://getcomposer.org/download/)
02. Create new folder for Laravel project
03. Install Laravel `$ composer create-project laravel/laravel –-prefer-dist`
04. Start local server by `$ php artisan serve`
05. Check the development website: http://localhost:8000

### Application Structure
A note taking from [Laravelプロジェクトを新規作成したときに出来るディレクトリとファイルをすべて解説する](https://www.hypertextcandy.com/explaining-laravel-project-directories-and-files)

```
- app / dir
  models and controllers: bussiness logic except the migration files

  -- Console / dir
     for command line applications eg. batch programs
     --- Commands
         define the class-based console commands by "$ php artisan make:command"
     --- Kernel.php

  -- Exceptions / dir
     for handlers or other individual classes
     --- Handler.php
         for Laravel error handling

  -- Http / dir
    --- Controllers / dir
        ---- Controller.php
             Parent class for all controllers
        ---- Auth
             ------ ForgotPasswordController.php
             ------ LoginController.php
             ------ RegisterController.php
             ------ ResetPasswordController.php
             ------ VerificationController.php
    --- Middleware / dir
        ---- Authenticate.php
        ---- CheckForMaintenanceMode.php
        ---- EncryptCookies.php
        ---- RedirectIfAuthenticated.php
        ---- TrimStrings.php
        ---- TrustProxies.php
             “
             ロードバランサ(Load Balancer)やゲートウェイキャッシュ(Gateway cache)など
	     リバースプロキシ(Reverse Proxy)の背後でアプリケーションを動作させる際のセッション管理を行います。
	     こちらも実処理は親クラスが持っています。
             -- Laravelプロジェクトを新規作成したときに出来るディレクトリとファイルをすべて解説する
                (https://www.hypertextcandy.com/explaining-laravel-project-directories-and-files)
             ”
        ---- VerifyCsrfToken.php
    --- Requests/Back / dir
    --- Routes / dir
    --- Kernel.php

  -- Providers / dir
     --- AppServiceProvider.php
     --- AuthServiceProvider.php
     --- BroadcastServiceProvider.php
         “
         WebSocket を用いたサーバプッシュ配信についてルーティング設定の読み込みと登録処理を行います。
         -- Laravelプロジェクトを新規作成したときに出来るディレクトリとファイルをすべて解説する
         (https://www.hypertextcandy.com/explaining-laravel-project-directories-and-files)
         ”
     --- EventServiceProvider.php
     --- RouteServiceProvider.php

- bootstrap / dir
  For Laravel instances (uneditable)
  -- app.php
  -- cache / dir

- config / dir
  -- app.php
  -- auth.php
  -- broadcasting.php
     config file for Server-side push setting
  -- cache.php
  -- database.php
  -- filesystems.php
     define save path (local or AWS S3 infos) for uploaded files
  -- hashing.php
     define encrypting methed
  -- logging.php
  -- mail.php
  -- queue.php
  -- services.php
  -- session.php
  -- view.php


- database / dir
  migration records and seeds
  -- factories / dir
     classes for test data
     --- UserFactory.php
  -- migrations
  -- seeds
     --- DatabaseSeeder.php

- public / dir
  opened files eg. index.php and compiled static files
  -- .htaccess / file
     URL rewrite setting file for Apache
  -- favicon.ico
  -- index.php
  -- robots.txt
  -- web.config
     URL rewrite setting file for IIS

- resources / dir
  uncompiled static files
  -- js / dir
  -- lang / dir
  -- sass / dir
  -- views /dir
     for template files
     --- welcome.blade.php

- routes / dir
  -- api.php
     define the routes for API
  -- channels.php
     define the channel for WebSocket
  -- console.php
     define the closure-based console commands (for class-based console commands: app/Console/Commands)
  -- web.php
     define the routes for web pages

- storage / dir
  -- app
     store the uploaded files for users
     --- public
  -- framework
     --- cache
     --- sessions
     --- testing
     --- views
         for compiled template files
  -- logs

- tests / dir
-- Feature / dir
   tests features with HTTP requests
-- Unit / dir
   --- CreatesApplication.php
       define methods trait for creating test application and would be used by TestCase.php
   --- TestCase.php
       parent class for all tests

- vendor/ dir
  PHP plugins and libraries (unediable)

- .env / file

- .gitignore / file

- .editorconfig / file

- artisan / file
  CLI tool for Laravel

- composer.json / file
  setting file for Composer (package management tool for PHP)

- package.json / file
  setting file for npm (package management tool for JavaScript)

- phpunit.xml / file
  setting file for PHPUnit (testing tool)

- server.php / file
  setting up script file for local server

- webpack.mix.js / file
  setting file for Laravel Mix (compiler for static files)
```

FYR: [rdehnhardt / skeleton](https://github.com/rdehnhardt/skeleton)<br>
[Laravel - Application Structure](https://www.tutorialspoint.com/laravel/laravel_application_structure.htm)

### Configuration
A note taking from [Laravel Tutorial - Configuration](https://www.tutorialspoint.com/laravel/laravel_configuration.htm)

```
# config/.env

APP_ENV = local
APP_DEBUG = true
APP_KEY = base64:ZPt2wmKE/X4eEhrzJU6XX4R93rCwYG8E2f8QUA7kGK8 =
APP_URL = http://localhost
DB_CONNECTION = mysql
DB_HOST = 127.0.0.1
DB_PORT = 3306
DB_DATABASE = homestead
DB_USERNAME = homestead
DB_PASSWORD = secret
CACHE_DRIVER = file
SESSION_DRIVER = file
QUEUE_DRIVER = sync
REDIS_HOST = 127.0.0.1
REDIS_PASSWORD = null
REDIS_PORT = 6379
MAIL_DRIVER = smtp
MAIL_HOST = mailtrap.ioMAIL_PORT = 2525
MAIL_USERNAME = null
MAIL_PASSWORD = null
MAIL_ENCRYPTION = null
```

!Noted: NEVER include `.env` file in to version control tool

#### Retrievel and accessing of Environment Variables
> Environment variables can be accessible by `env-helper` and are also listed into `$_ENV` global variable.
> --[Learn Laravel](https://www.tutorialspoint.com/laravel/laravel_configuration.htm)
 
```php
//  To set the default time zone
config(["app.timezone" => "Asia/Kolkata"]);
```

#### Cached the Configuration
```
$ php artisan config:cache
``` 

#### Maintenance Mode
> Applications in maintenance mode will throw an exception namely **MaintenanceModeException** with a status code of `503`.

Enable maintenance mode:
```
$ php artisan down
```

Disable maintenance mode:
```
$ php artisan up
```

### Routing
A note taking from [Laravel Tutorial - Routing](https://www.tutorialspoint.com/laravel/laravel_routing.htm)

```php
# app/Http/routes.php

Route::get("/", function() {
   return view("welcome");
});
```
---
```html
# resources/view/welcome.blade.php

<!DOCTYPE html>
<html>
   <head>
      <title>Laravel</title>
   </head>
   
   <body>
      <div class = "container">
         <div class = "content">
            <div class = "title">Laravel 5.1</div>
         </div>
      </div>
   </body>
</html>
```

#### Route Parameters
##### Required Parameters
```php
Route::get("path-to-show-by-id/{id}", function($id) {
   echo "ID: ".$id;
});
```
##### Optional Parameters
```php
Route::get("user/{name?}", function($name="TutorialsPoint") {
   return $name;
});
```

#### Named Routes
```php
Route::get("user/profile", "UserController@showProfile")->name("profile");

Route::get("user/profile", ["as" => "profile", function () {
   // ...
}]);
```

### Middleware
A note taking from [Laravel Tutorial - Middleware](https://www.tutorialspoint.com/laravel/laravel_middleware.htm)

> Middleware acts as a bridge between a request and a response.
> -- [Learn Laravel - Middleware](tutorialspoint.com/laravel/laravel_middleware.htm)

#### New a Middleware
```
$ php artisan make:middleware <middleware-name>
```

Example: AgeMiddleware
```
$ php artisan make:middleware AgeMiddleware
```
---
```php
# app/Http/Middleware/AgeMiddleware.php

<?php

namespace App\Http\Middleware;
use Closure;

class AgeMiddleware {
   public function handle($request, Closure $next) {
      return $next($request);
   }
}
```

#### Register Middleware
Middlewares can be registered at **app/Http/Kernel.php**.
- Global Middleware: run on every HTTP request -> registered as `$middleware`
- Routes Middleware: be assigned to a specific route -> registered as `$routeMiddleware`

```php
# app/Http/Kernel.php

// Global Middleware Register
protected $middleware = [
   \Illuminate\Foundation\Http\Middleware\CheckForMaintenanceMode::class,
   \App\Http\Middleware\EncryptCookies::class,
   \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
   \Illuminate\Session\Middleware\StartSession::class,
   \Illuminate\View\Middleware\ShareErrorsFromSession::class,
   \App\Http\Middleware\VerifyCsrfToken::class,

   \Path\to\Your\Middleware::class,
];

// Route Middleware Register
protected $routeMiddleware = [
   "auth" => \App\Http\Middleware\Authenticate::class,
   "auth.basic" => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
   "guest" => \App\Http\Middleware\RedirectIfAuthenticated::class,

   "name_of_your_middleware" => \Path\to\Your\Middleware::class,
];
```

#### Middleware Parameters
Custom arguments can be passed after the `$next` argument.

Example: RoleMiddleware
```php
# app/Http/Middleware/RoleMiddleware.php

<?php

namespace App\Http\Middleware;
use Closure;

class RoleMiddleware {
   public function handle($request, Closure $next, $role) {
      echo "Role: ".$role;
      return $next($request);
   }
}
```
---
```php
# app/Http/Kernel.php

protected $routeMiddleware = [
   "auth" => \App\Http\Middleware\Authenticate::class,
   "auth.basic" => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
   "guest" => \App\Http\Middleware\RedirectIfAuthenticated::class,

   "Role" => \App\Http\Middleware\RoleMiddleware::class,
];
```

Use RoleMiddleware in routes:
```php
# app/Http/routes.php

Route::get("role",[
   "middleware" => "Role:editor",
   "uses" => "TestController@index",
]);
```

#### Example: Terminable Middleware
> Terminable middleware performs some task after the response has been sent to the browser.
> [Laravel Tutorial - Middleware](https://www.tutorialspoint.com/laravel/laravel_middleware.htm)

```
$ php artisan make:middleware TerminateMiddleware
```
---
```php
# app/Http/Middleware/TerminateMiddleware.php

<?php

namespace App\Http\Middleware;
use Closure;

class TerminateMiddleware {
   public function handle($request, Closure $next) {
      echo "Executing statements of handle method of TerminateMiddleware.";
      return $next($request);
   }
   
   public function terminate($request, $response) {
      echo "<br>Executing statements of terminate method of TerminateMiddleware.";
   }
}
```

Then register TerminateMiddleware:
```php
# app/Http/Kernel.php

protected $routeMiddleware = [
   "auth" => \App\Http\Middleware\Authenticate::class,
   "auth.basic" => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
   "guest" => \App\Http\Middleware\RedirectIfAuthenticated::class,

   "Role" => \App\Http\Middleware\RoleMiddleware::class,
   "terminate" => \App\Http\Middleware\TerminateMiddleware::class,
];
```

Use TerminalMiddleware in routes:
```php
Route::get("terminate",[
   "middleware" => "terminate",
   "uses" => "ABCController@index",
]);
```

Check the page **http://localhost:8000/terminate**:
```
# ... Result of ABCController/index
Executing statements of terminate method of TerminateMiddleware.
```

### Namespaces
A note taking from [Laravel Tutorial - Namespaces](https://www.tutorialspoint.com/laravel/laravel_namespaces.htm)

#### Declaration of namespace
> The **use** keyword allows the developers to shorten the namespace.
> [Laravel Tutorial - Namespaces](https://www.tutorialspoint.com/laravel/laravel_namespaces.htm)

```php
use <namespace-name>;
```

> プログラムにおいて、クラスや関数などの名前の衝突を防ぐために名前空間を使用して、それぞれグループ化することがあります。
> PHPでは、namespaceやuseといったキーワードを使用して名前空間を使用します。
> -- [【PHP入門】名前空間(namespace/use)の使い方をわかりやすく解説！](https://www.sejuku.net/blog/23555) 

```php
<?php

require_once "tokyo.php";

use asia\japan\kanto as area;

$a = new area\tokyo\pref;
echo $a->getPref();


require_once "tokyo.php";

use asia\japan\kanto\tokyo\pref;

$b = new pref;
echo $b->getPref();
```

FYR: [【PHP超入門】名前空間（namespace・use）について](https://qiita.com/7968/items/1e5c61128fa495358c1f)<br>
[PHP Documentation - Namespaces](https://www.php.net/manual/en/language.namespaces.php)

### Controllers
A note taking from [Laravel Tutorial - Controllers](https://www.tutorialspoint.com/laravel/laravel_controllers.htm)

#### Create a Controller
```
$ php artisan make:controller <controller-name> --plain
```

Route mapping:
```php
Route::get( "base URI", "controller-name@method" );
```

#### Assign Middleware to Controller
- in Route setting
- in controller"s constructor

Assign Middleware in Route
```php
Route::get("profile", [
   "middleware" => "auth",
   "uses" => "UserController@showProfile"
]);
```

Assign Middleware in controller"s constructor
```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Http\Requests;
use App\Http\Controllers\Controller;

class UserController extends Controller {
   public function __construct() {
      $this->middleware("auth");
   }
}
```

#### Restful Resource Controllers
```
$ php artisan make:controller MyController
```
---
```php
# app/Http/Controllers/MyController.php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Http\Requests;
use App\Http\Controllers\Controller;

class MyController extends Controller {
   public function index() {
      // GET
      echo "index";
   }
   public function create() {
      // GET
      echo "create";
   }
   public function store(Request $request) {
      // POST
      echo "store";
   }
   public function show($id) {
      // GET
      echo "show";
   }
   public function edit($id) {
      // GET
      echo "edit";
   }
   public function update(Request $request, $id) {
      // PUT/PATCH
      echo "update";
   }
   public function destroy($id) {
      // DELETE
      echo "destroy";
   }
}
```
---
```php
# app/Http/routes.php

Route::resource("my","MyController");
```

#### Implicit Controllers
> Implicit Controllers allow you to define a single route to handle every action in the controller.
> The method name of the controller should start with HTTP verb.
> -- [Laravel Tutorial - Controllers](https://www.tutorialspoint.com/laravel/laravel_controllers.htm)

```php
# app/Http/Controllers/ImplicitController.php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Http\Requests;
use App\Http\Controllers\Controller;

class ImplicitController extends Controller {
   /*
      * Responds to requests to GET /test
   */
   public function getIndex() {
      echo "index method";
   }
   
   /*
      * Responds to requests to GET /test/show/1
   */
   public function getShow($id) {
      echo "show method";
   }
   
   /*
      * Responds to requests to GET /test/admin-profile
   */
   public function getAdminProfile() {
      echo "admin profile method";
   }
   
   /*
      * Responds to requests to POST /test/profile
   */
   public function postProfile() {
      echo "profile method";
   }
}
```
---
```php
# app/Http/routes.php

Route::controller("test","ImplicitController");
```

#### Constructor Injection
> The Laravel `service container` is used to resolve all Laravel controllers.
> -- [Laravel Tutorial - Controllers](https://www.tutorialspoint.com/laravel/laravel_controllers.htm)

FYR: [【Laravel】サービスコンテナとは？２つの強力な武器を持ったインスタンス化マシーン。簡単に解説。](https://qiita.com/minato-naka/items/afa4b930a2afac23261b)<br>
[Laravel サービスコンテナの理解を深める](https://reffect.co.jp/laravel/laravel-service-container-understand)
[Service Container](https://laravel.com/docs/8.x/container)

> 「依存性の注入」（Dependency injection）をご存知でしょうか。あるクラスが依存している別のオブジェクトを外部から渡すことで、クラス間の依存度を下げる設計パターンです。
> Laravelにはサービスコンテナと呼ばれる機能が備わっており、依存性注入を簡単に扱うことができます。
> --[Laravelで始める依存性の注入（DI）](https://qiita.com/harunbu/items/079ea728d2c9cf4f44d5)

```php
# app/Http/routess.php

class MyClass{
   public $foo = "bar";
}

Route::get("/myclass", "ImplicitController@index");
```
---
```php
# app/Http/Controllers/ImplicitController.php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Http\Requests;
use App\Http\Controllers\Controller;

class ImplicitController extends Controller {
   private $myclass;
   
   public function __construct(\MyClass $myclass) {
      $this->myclass = $myclass;
   }
   public function index() {
      dd($this->myclass);
   }
}
```

Visit `http://localhost:8000/myclass` to check the result:
```
MyClass {
   +foo: "bar"
}
```

#### Method Injection
```php
# app/Http/routes.php

class MyClass{
   public $foo = "bar";
}

Route::get("/myclass","ImplicitController@index");
```
---
```php
# app/Http/Controllers/ImplicitController.php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Http\Requests;
use App\Http\Controllers\Controller;

class ImplicitController extends Controller {
   public function index(\MyClass $myclass) {
      dd($myclass);
   }
} 
```

Visit `http://localhost:8000/myclass` to check the result:
```
MyClass {
   +foo: "bar"
}
```

#### Pagination Customizations
A note taking from [Laravel Tutorial - Pagination Customizations](https://www.tutorialspoint.com/laravel/laravel_pagination_customizations.htm)

> Laravel paginator is integrated with the query builder and Eloquent ORM.
> -- [Laravel Tutorial - Pagination Customizations](https://www.tutorialspoint.com/laravel/laravel_pagination_customizations.htm)

```php
<?php
namespace App\Http\Controllers;
use Illuminate\Support\Facades\DB;
use App\Http\Controllers\Controller;
class UserController extends Controller{
   /*
   * Show all of the users for the application.
   *
   * @return Response
   */
   public function index() {
      $users = DB::table('users')->paginate(15);
      return view("user.index", ["users" => $users]);
   }
}
```

### Request
A note taking from [Laravel Tutorial - Request](https://www.tutorialspoint.com/laravel/laravel_request.htm)
#### Retrieving the Request URI
- `path` method: retrieve the requested URI
- `url` method: retrieve the requested URI which matches the particular pattern specified in the argument of the method
- `is` method: get the full URL

```php
// Usage of path method
$path = $request->path();
echo "Path Method: ".$path;

// Usage of url method
$url = $request->url();
echo "URL method: ".$url;

// Usage of is method
$pattern = $request->is("foo/*");
echo "is Method: ".$pattern;
```

#### Retrieving Input
- `input()` method
- properties of Request instance

```php
$name = $request->input("username");
$name = $request->username;
```

### Cookie
A note taking from [Laravel Tutorial - Cookie](https://www.tutorialspoint.com/laravel/laravel_cookie.htm)

#### Creating a Cookie
> Cookie can be created by global cookie helper of Laravel.
> An instance of **Symfony\Component\HttpFoundation\Cookie**.
> Cookie generated by Laravel are encrypted and signed. (can not be modified or read by the client)
> -- [Laravel Tutorial - Cookie](https://www.tutorialspoint.com/laravel/laravel_cookie.htm)

`cookie()` method takes 3 arguments:
- name of the cookie
- value of the cookie
- duration of the cookie

```php
//Create a response instance
$response = new Illuminate\Http\Response("Hello World");

//Call the withCookie() method with the response method
$response->withCookie(cookie("name", "value", $minutes));

//return the response
return $response;
```

Cookie can be set forever:
```php
$response->withCookie(cookie()->forever("name", "value"));
```

#### Retrieving a Cookie
```php
$request->cookie("name-of-cookie");
```
---
```php
# app/Http/Controllers/CookieController.php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Http\Response;
use App\Http\Requests;
use App\Http\Controllers\Controller;

class CookieController extends Controller {
   public function setCookie(Request $request) {
      $minutes = 1;
      $response = new Response("Hello World");
      $response->withCookie(cookie("name", "virat", $minutes));
      return $response;
   }
   public function getCookie(Request $request) {
      $value = $request->cookie("name");
      echo $value;
   }
}
```

### Response
A note taking from [Laravel Tutorial - Response](https://www.tutorialspoint.com/laravel/laravel_response.htm)

#### Basic Response
Responses can be sent from routes or controllers.

A response sent from route
```php
# app/Http/routes.php

Route::get("/", function () {
   return "Hello World";
});
```

#### Attaching Headers
```php
return response($content,$status)
       ->header("Content-Type", $type)
       ->header("X-Header-One", "Header Value")
       ->header("X-Header-Two", "Header Value");
```

Example: Sending a response from route:
```php
Route::get("/header",function() {
   return response("Hello", 200)->header("Content-Type", "text/html");
});
```

Example: sending a response with cookie:
```php
Route::get("/cookie",function() {
   return response("Hello", 200)->header("Content-Type", "text/html")
          ->withcookie("name","Virat Gandhi");
});
```

#### JSON Response
Send a JSON response using `json()` method.
`Content-Type` in header will set to be `application/json` automatically.

```php
Route::get("json",function() {
   return response()->json(["name" => "Virat Gandhi", "state" => "Gujarat"]);
});
```

### Views
A note taking from [Laravel Tutorial - Views](https://www.tutorialspoint.com/laravel/laravel_views.htm)

Example: Return a view in route:
```html
# resources/views/example.php

<html>
   <body>
      <h1>Hello, World</h1>
   </body>
</html>
```
---
```php
# app/Http/routes.php

Route::get("/example", function() {
   return view("example");
});
```

#### Passing data to views
```php
# app/Http/routes.php

Route::get("/example", function() {
   return view("example", ["name" => "Virat Gandhi"]);
});
```
---
```html
# resources/views/example.php

<html>
   <body>
      <h1><?php echo $name; ?></h1>
   </body>
</html>
```

#### Sharing data with all views
`share(key, value)` method can be used to pass data to all the views.
It can be called from boot method of service provider.

FYR: [Laravel サービスプロバイダーついに理解](https://reffect.co.jp/laravel/laravel-service-provider-understand)

```php
# app/Providers/AppServiceProvider.php

<?php

namespace App/Providers;
use Illuminate/Support/ServiceProvider;

class AppServiceProvider extends ServiceProvider {
   /*
      * Bootstrap any application services.
      *
      * @return void
   */

   public function boot() {
      view()->share("name", "Virat Gandhi");
   }

   /*
      * Register any application services.
      *
      * @return void
   */

   public function register() {
      // ...
   }
}
```

### Action URL
A note taking from [Laravel Tutorial - Action URL](https://www.tutorialspoint.com/laravel/laravel_action_url.htm)
```php
<?php
$url = action("UserController@profile", ["id" => 1]);

<?php
$url = action([PostsController::class, "index"]);
```

### Blade Templates
A note taking from [Laravel Tutorial - Blade Templates](https://www.tutorialspoint.com/laravel/laravel_blade_templates.htm)

> Laravel 5.1 introduces the concept of using Blade, a templating engine to design a unique layout.
> -- [Laravel Tutorial - Blade Templates](https://www.tutorialspoint.com/laravel/laravel_blade_templates.htm)

#### Master Template

Create a master layout:
```html
# resources/views/master.blade.php

<html>
   <head>
      <title>Laravel - @yield("title")</title>
   </head>
   <body>
      @yield("content")
   </body>
</html>
```
Extend the layout:
```html
# resources/views/child.blade.php

@extends("layouts.app")
@section("title", "Page Title")
@section("sidebar")
   @parent
<p>This refers to the master sidebar.</p>
@endsection
@section("content")
<p>This is my body content.</p>
@endsection
```

FYR: [Laravel Document - Blade Templates](https://laravel.com/docs/8.x/blade)

### Redirections
A note taking from [Laravel Tutorial - Redirections](https://www.tutorialspoint.com/laravel/laravel_redirections.htm)

```php
# app/Http/routes.php

Route::get("redirect", function() {
   return redirect()->route("name-of-route");
});
```

Redirecting to Controller Actions
```php
# app/Http/routes.php

Route::get("redirect", function() {
   return redirect()->action("NameOfController@methodName", [parameters]);
});
```

### Working With Database
A note taking from [Laravel Tutorial - Working With Database](https://www.tutorialspoint.com/laravel/laravel_working_with_database.htm)

Databases that Laravel supports:
- MySQL
- Postgres
- SQLite
- SQL Server

#### Insert Records
```php
# app/Http/Controllers/StudViewController.php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use DB;
use App\Http\Requests;
use App\Http\Controllers\Controller;

class StudInsertController extends Controller {
   public function insertform() {
      return view("stud_create");
   }
	
   public function insert(Request $request) {
      $name = $request->input("stud_name");
      DB::insert("insert into student (name) values(?)",[$name]);
      echo "Record inserted successfully.<br/>";
      echo "<a href = "/insert">Click Here</a> to go back.";
   }
}
```

#### Retrieve Records
```php
# app/Http/Controllers/StudViewController.php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use DB;
use App\Http\Requests;
use App\Http\Controllers\Controller;

class StudViewController extends Controller {
   public function index() {
      $users = DB::select("select * from student");
      return view("stud_view",["users"=>$users]);
   }
}
```
---
```html
<html>
   
   <head>
      <title>View Student Records</title>
   </head>
   
   <body>
      <table>
         <tr>
            <td>ID</td>
            <td>Name</td>
         </tr>
         @foreach ($users as $user)
         {% raw %}
         <tr>
            <td>{{ $user->id }}</td>
            <td>{{ $user->name }}</td>
         </tr>
         {% endraw %}
         @endforeach
      </table>
   </body>
</html>
```

#### Update Records
```php
# app/Http/Controllers/StudUpdateController.php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use DB;
use App\Http\Requests;
use App\Http\Controllers\Controller;

class StudUpdateController extends Controller {
   public function index() {
      $users = DB::select("select * from student");
      return view("stud_edit_view",["users"=>$users]);
   }
   public function show($id) {
      $users = DB::select("select * from student where id = ?",[$id]);
      return view("stud_update",["users"=>$users]);
   }
   public function edit(Request $request,$id) {
      $name = $request->input("stud_name");
      DB::update("update student set name = ? where id = ?",[$name,$id]);
      echo "Record updated successfully.<br/>";
      echo "<a href = "/edit-records">Click Here</a> to go back.";
   }
}
```

#### Delete Records
```php
# app/Http/Controllers/StudDeleteController.php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use DB;
use App\Http\Requests;
use App\Http\Controllers\Controller;

class StudDeleteController extends Controller {
   public function index() {
      $users = DB::select("select * from student");
      return view("stud_delete_view",["users"=>$users]);
   }
   public function destroy($id) {
      DB::delete("delete from student where id = ?",[$id]);
      echo "Record deleted successfully.<br/>";
      echo "<a href = "/delete-records">Click Here</a> to go back.";
   }
}
```

### Forms
A note taking from [Laravel Tutorial - Forms](https://www.tutorialspoint.com/laravel/laravel_forms.htm)

Add **HTML Service Provider** and aliases to config:
```php
# config/app.php

"providers" => [

   /*
    * Laravel Framework Service Providers
   */

   // ...

   Illuminate\Html\HtmlServiceProvider::class,
]

"aliases" => [
   // ...

   "Form"      => Illuminate\Html\FormFacade::class,
   "Html"      => Illuminate\Html\HtmlFacade::class,
]
```

Laravel form tags:
```php
// opening a form
Form::open(array("url" => "foo/bar"))
   // ...
Form::close()

// generating a label
echo Form::label("email", "E-Mail Address");

// generating a text input
echo Form::text("username");
echo Form::text("email", "example@gmail.com"); // text input with default value

// generating a password input
echo Form::password("password");

// generating a file input
echo Form::file("image");

// generating a checkbox / radio input
echo Form::checkbox("name", "value");
echo Form::radio("name", "value");
echo Form::checkbox("name", "value", true);    // checked checkbox
echo Form::radio("name", "value", true);       // checked radio input

// generating a select list
echo Form::select("size", array("L" => "Large", "S" => "Small"));

// generating a submit button
echo Form::submit("Click Me!");
```

Form example:
```html
# resources/views/form.php

<html>
   <body>
      
      <?php
         echo Form::open(array("url" => "foo/bar"));
            echo Form::text("username","Username");
            echo "<br/>";
            
            echo Form::text("email", "example@gmail.com");
            echo "<br/>";
     
            echo Form::password("password");
            echo "<br/>";
            
            echo Form::checkbox("name", "value");
            echo "<br/>";
            
            echo Form::radio("name", "value");
            echo "<br/>";
            
            echo Form::file("image");
            echo "<br/>";
            
            echo Form::select("size", array("L" => "Large", "S" => "Small"));
            echo "<br/>";
            
            echo Form::submit("Click Me!");
         echo Form::close();
      ?>
   
   </body>
</html>
```

FYR: [Laravel Collective（Formファサード まとめ）](https://laraweb.net/practice/7965/)

### Localization
A note taking from [Laravel Tutorial - Localization](https://www.tutorialspoint.com/laravel/laravel_localization.htm)

Language files should locate under the **resources/langs** directory and return arrays of keyed strings.

Example: Create 3 files for languages
```php
# resources/lang/en/lang.php
<?php
   return [
      "msg" => "Laravel Internationalization example."
   ];
?>

# resources/lang/fr/lang.php
<?php
   return [
      "msg" => "Exemple Laravel internationalisation."
   ];
?>

# resources/lang/de/lang.php
<?php
   return [
      "msg" => "Laravel Internationalisierung Beispiel." 
   ];
?>
```

Set locale in Controller and route:
```php
# app/Http/Controllers/LocalizationController.php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Http\Requests;
use App\Http\Controllers\Controller;

class LocalizationController extends Controller {
   public function index(Request $request, $locale) {
      // set application’s locale
      app()->setLocale($locale);
      
      // get the translated message
      echo trans("lang.msg");
   }
}
```
---
```php
# app/Http/routes.php

Route::get("localizaion/{locale}", "LocalizaionController@index");
```

### Session
A note taking from [Laravel Tutorial - Session](https://www.tutorialspoint.com/laravel/laravel_session.htm)

> Sessions are used to store information about the user across the requests. Laravel provides various drivers like file, cookie, apc, array, Memcached, Redis, and database to handle session data.
> Session can be configured in the file stored at **config/session.php**.
> -- [Laravel Tutorial - Session](https://www.tutorialspoint.com/laravel/laravel_session.htm)

```php
// check session existence
if (session()->exists("key")) {
   // ...
}

// check sessin existence and not null
if (session()->has("key")) {
    // ...
}

// access session data by key
$value = $request->session()->get("key");

// get all session data
$value = $request->session()->all();

// store session data
$request->session()->put("key", "value";);

// delete session data
$request->session()->forget("key");

// delete all session data
$request->session()->flush();

// retrieve data from session and delete it
$value = $request->session()->pull("key", "value";);
```

### Validation
A note taking from [Laravel Tutorial - Validation](https://www.tutorialspoint.com/laravel/laravel_validation.htm)

> By default, base controller class uses a `ValidatesRequests` trait which provides a convenient method to validate incoming HTTP requests with a variety of powerful validation rules.
> -- [Laravel Tutorial - Validation](https://www.tutorialspoint.com/laravel/laravel_validation.htm)

#### Available Validation Rules
<table>
  <thead>
    <tr>
      <th colspan="6" style="text-align:center;">
        Available Validation Rules in Laravel
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Accepted</td>
      <td>Active URL</td>
      <td>After (Date)</td>
      <td>Alpha</td>
      <td>Alpha Dash</td>
      <td>Alpha Numeric</td>
    </tr>
    <tr>
      <td>Array</td>
      <td>Before (Date)</td>
      <td>Between</td>
      <td>Boolean</td>
      <td>Confirmed</td>
      <td>Date</td>
    </tr>
    <tr>
      <td>Date Format</td>
      <td>Different</td>
      <td>Digits</td>
      <td>Digits Between</td>
      <td>E-Mail</td>
      <td>Exists (Database)</td>
    </tr>
    <tr>
      <td>Image (File)</td>
      <td>In</td>
      <td>Integer</td>
      <td>IP Address</td>
      <td>JSON</td>
      <td>Max</td>
    </tr>
    <tr>
      <td>MIME Types(File)</td>
      <td>Min</td>
      <td>Not In</td>
      <td>Numeric</td>
      <td>Regular Expression</td>
      <td>Required</td>
    </tr>
    <tr>
      <td>Required If</td>
      <td>Required Unless</td>
      <td>Required With</td>
      <td>Required With All</td>
      <td>Required Without</td>
      <td>Required Without All</td>
    </tr>
    <tr>
      <td>Same</td>
      <td>Size</td>
      <td>String</td>
      <td>Timezone</td>
      <td>Unique (Database)</td>
      <td>URL</td>
    </tr>
  </tbody>
</table>

Example:
```php
# app/Http/Controllers/ValidationController.php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Http\Requests;
use App\Http\Controllers\Controller;

class ValidationController extends Controller {
   public function showform() {
      return view("login");
   }
   public function validateform(Request $request) {
      print_r($request->all());
      $this->validate($request, [
         "username"=>"required|max:8",
         "password"=>"required"
      ]);
   }
}
```

#### Error messages
`Error message` is an instance of **Illuminate\Support\MessageBag**

Showing error messages:
```html
@if (count($errors) > 0)
   <div class = "alert alert-danger">
      <ul>
         @foreach ($errors->all() as $error)
            <li>{% raw %}{{ $error }}{% endraw %}</li>
         @endforeach
      </ul>
   </div>
@endif
```

### File Uploading
A note taking from [Laravel Tutorial - File Uploading](https://www.tutorialspoint.com/laravel/laravel_file_uploading.htm)

```php
# resources/views/uploadfile.php

<html>
   <body>
      <?php
         echo Form::open(array("url" => "/uploadfile","files"=>"true"));
         echo "Select the file to upload.";
         echo Form::file("image");
         echo Form::submit("Upload File");
         echo Form::close();
      ?>
   </body>
</html>
```
---
```php
# app/Http/Controllers/UploadFileController.php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Http\Requests;
use App\Http\Controllers\Controller;

class UploadFileController extends Controller {
   public function index() {
      return view("uploadfile");
   }
   public function showUploadFile(Request $request) {
      $file = $request->file("image");
   
      //Display File Name
      echo "File Name: ".$file->getClientOriginalName();
      echo "<br>";
   
      //Display File Extension
      echo "File Extension: ".$file->getClientOriginalExtension();
      echo "<br>";
   
      //Display File Real Path
      echo "File Real Path: ".$file->getRealPath();
      echo "<br>";
   
      //Display File Size
      echo "File Size: ".$file->getSize();
      echo "<br>";
   
      //Display File Mime Type
      echo "File Mime Type: ".$file->getMimeType();
   
      //Move Uploaded File
      $destinationPath = "uploads";
      $file->move($destinationPath, $file->getClientOriginalName());
   }
}
```

### Sending Email
A note taking from [Laravel Tutorial - Sending Email](https://www.tutorialspoint.com/laravel/laravel_sending_email.htm)

`SwiftMailer` is used in Laravel to send emails.

```php
Mail::send(["text"=>"text.view"], $data, $callback);
```

First, set up the environment file:
```php
# .env

MAIL_DRIVER = smtp
MAIL_HOST = smtp.gmail.com
MAIL_PORT = 587
MAIL_USERNAME = your-gmail-username
MAIL_PASSWORD = your-application-specific-password
MAIL_ENCRYPTION = tls
```

Clear cache:
```
$ php artisan config:cache
```

Set up MailController:
```php
<?php

namespace App\Http\Controllers;
use Illuminate\Http\Request;
use Mail;

use App\Http\Requests;
use App\Http\Controllers\Controller;

class MailController extends Controller {
   public function basic_email() {
      $data = array("name"=>"Virat Gandhi");
   
      Mail::send(["text"=>"mail"], $data, function($message) {
         $message->to("abc@gmail.com", "Tutorials Point")
                 ->subject("Laravel Basic Testing Mail");
         $message->from("xyz@gmail.com","Virat Gandhi");
      });

      echo "Basic Email Sent. Check your inbox.";
   }

   public function html_email() {
      $data = array("name"=>"Virat Gandhi");

      Mail::send("mail", $data, function($message) {
         $message->to("abc@gmail.com", "Tutorials Point")
                 ->subject("Laravel HTML Testing Mail");
         $message->from("xyz@gmail.com","Virat Gandhi");
      });

      echo "HTML Email Sent. Check your inbox.";
   }

   public function attachment_email() {
      $data = array("name"=>"Virat Gandhi");
      Mail::send("mail", $data, function($message) {
         $message->to("abc@gmail.com", "Tutorials Point")
                 ->subject("Laravel Testing Mail with Attachment");
         $message->attach("file-path");
         $message->from("xyz@gmail.com","Virat Gandhi");
      });

      echo "Email Sent with attachment. Check your inbox.";
   }
}
```

New mail template:
```html
# resources/views/mail.blade.php

<h1>Hi, {% raw %}{{ $name }}{% endraw %}</h1>
l<p>Sending Mail from Laravel.</p>
```

### Ajax
A note taking from [Laravel Tutorial - Ajax](https://www.tutorialspoint.com/laravel/laravel_ajax.htm)

jQuery `json()` function syntax:
```javascript
json(string|array $data = array(), int $status = 200, array $headers = array(), int $options);
```

Ajax Example:
```html
<html>
   <head>
      <title>Ajax Example</title>
      
      <script>
         function getMessage() {
            $.ajax({
               type:"POST",
               url:"/getmsg",
               data:"_token = <?php echo csrf_token() ?>",
               success:function(data) {
                  $("#msg").html(data.msg);
               }
            });
         }
      </script>
   </head>
   
   <body>
      <div id = "msg">This message will be replaced using Ajax. 
         Click the button to replace the message.</div>
      <?php
         echo Form::button("Replace Message",["onClick"=>"getMessage()"]);
      ?>
   </body>

</html>
```

Create a controller and set the route for the Ajax calls:
```php
# app/Http/Controllers/AjaxController.php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Http\Requests;
use App\Http\Controllers\Controller;

class AjaxController extends Controller {
   public function index() {
      $msg = "This is a simple message.";
      return response()->json(array("msg"=> $msg), 200);
   }
}
```
---
```php
# app/Http/routes.php

Route::get("ajax",function() {
   return view("message");
});
Route::post("/getmsg","AjaxController@index");
```

### Errors and Logging
A note taking from [Laravel Tutorial - Errors and Logging](https://www.tutorialspoint.com/laravel/laravel_errors_and_logging.htm)

#### Debug mode
Set `APP_DEBUG` in the environment file **.env**:
- Production: false, hiding error messages for debugging
- Development: true

#### Logging
Settings are in **config/app.php** file.
```php
# config/app.php

"log" => "daily"
```

> Laravel supports different logging modes eg `single`, `daily`, `syslog`, and `errorlog` mode. 
> The generated log entries are located in `storage/logs/laravel.log` file.
> -- [Laravel Tutorial - Errors and Logging](https://www.tutorialspoint.com/laravel/laravel_errors_and_logging.htm)


### Error Handling
A note taking from [Laravel Tutorial - Error Handling](https://www.tutorialspoint.com/laravel/laravel_error_handling.htm)

> Laravel logs errors and exceptions in the **App\Exceptions\Handler** class, by default.
> -- [Laravel Tutorial - Error Handling](https://www.tutorialspoint.com/laravel/laravel_error_handling.htm)

#### Error log
Configuration file for the Laravel application: **config/app.php**.

- Laravel uses `monolog` PHP logging library
- The logging parameters used for error tracking are `single`, `daily`, `syslog` and `errorlog`
  ```php
  "log" => env("APP_LOG", "daily");
  ```
- If the `daily` log mode is taken as the parameter, Laravel takes error log for a period of 5 days by default. 
  You have to set the parameter of `log_max_files` in the configuration file to change the maximum number of log files.
  ```php
  "log_max_files" => 25;
  ```
- Severity levels available are `error`, `critical`, `alert` and `emergency messages`.
  ```php
  "log_level" => env("APP_LOG_LEVEL", "error");
  ```

### Event Handling
A note taking from [Laravel Tutorial - Event Handling](https://www.tutorialspoint.com/laravel/laravel_event_handling.htm)

> Events provide a simple observer implementation which allows users to subscribe and listen to events.
> One event can have multiple listeners which are independent of each other.
> -- [Laravel Tutorial - Event Handling](https://www.tutorialspoint.com/laravel/laravel_event_handling.htm)

- events: /app/Events
- listeners: /app/Listeners

Generate a event/listener:
```
$ php artisan event:generate
```

Example: Event
```php
<?php

namespace App\Events;

use App\Events\Event;
use Illuminate\Queue\SerializesModels;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;

class SomeEvent extends Event{
   use SerializesModels;
   /*
      * Create a new event instance.
      *
      * @return void
   */
   
   public function __construct() {
      // ...
   }
   
   /*
      * Get the channels the event should be broadcast on.
      *
      * @return array
   */
   
   public function broadcastOn() {
      return [];
   }
}
```

Example: Listener
```php
<?php

namespace App\Listeners;

use App\Events\SomeEvent;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Contracts\Queue\ShouldQueue;

class EventListener{
   /*
      * Create the event listener.
      *
      * @return void
   */
   
   public function __construct() {
      // ...
   }

   /*
      * Handle the event.
      *
      * @param SomeEvent $event
      * @return void
   */
   
   public function handle(SomeEvent $event) {
      // ...
   }
}
```
