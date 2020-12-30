---
layout: post
title: "Laravel Eloquent"
date: 2020-11-13
categories: [PHP, Laravel]
---

A note taking from:
- [Laravel Eloquentの超基本的な使い方](https://qiita.com/kshiva1126/items/84d60336e5b6bafb821e)
- [Eloquent をおさらい](https://qiita.com/shosho/items/5ca6bdb880b130260586)

## Basics
Eloquent models inherit the class `Illuminate\Database\Eloquent\Model`.

### New a Model
```
$ php artisan make:model User
```

Example Model:
```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    /**
     * The table associated with the model.
     *
     * @var string
     */
    
    protected $table = "my_flights";

    // Primary Key
    protected $primaryKey = "new_key";      // default: id
    protected $incrementing = false;        // primary key increment

    // Time Stamp
    protected $timestamps = false;
    protected $dateFormat = "U";            // PHP dataformat: http://php.net/manual/ja/function.date.php
    const CREATED_AT = "creation_date";     // Rename column
    const UPDATED_AT = "last_update";

    // $fillable: set insertable data property list (whitelist)
    protected $fillable = ["name", "email"];

    // $guarded: set uninsertable data property list (blacklist)
    protected $guarded = ["id", "created_at", "updated_at"];
}
```

## Data Manipultion
### Retrieve Data
```php
/*
 * Retrieve by key
 * SQL:
 * SELECT * FROM users WHERE id = 1 LIMIT 1; 
 */
App\User::find(1);

// find multiple ids
App\User::find([1, 2, 3]);

// find first record
App\Flight::where("active", 1)->first();

/*
 * Retrieve all
 * SQL:
 * SELECT * FROM posts WHERE writer_id = 1; 
 */
App\Post::where("writer_id", 1)->get();

/*
 * Retrieve by condition: and
 * SQL:
 * SELECT * FROM favorites WHERE user_id = 1 AND post_id = 1 LIMIT 1; 
 */
App\Favorite::where([
    "user_id" => 1,
    "post_id" => 1,
])->first();

/*
 * Retrieve by condition: or
 * SQL:
 * SELECT * FROM posts WHERE title LIKE "%Lorem" OR title LIKE "%ipsum%";
 */
App\Post::where("title", "LIKE", "%Lorem")
->orWhere("title", "LIKE", "%ipsum%")
->get();

/*
 * Retrieve by condition: combo
 * SQL:
 * SELECT * FROM posts
 * WHERE (title LIKE "%Lorem" OR title LIKE "%ipsum%")
 * AND (created_at IS NULL OR updated_at IS NULL);
 */
App\Post::where(function ($query) {
    return $query
    ->where("title", "LIKE", "%Lorem")
    ->orWhere("title", "LIKE", "%ipsum%");
})
->where(function ($query) {
    return $query
    ->whereNull("created_at")
    ->orWhereNull("updated_at");
})->get();

// Calculate and Return the Result
$count = App\Flight::where("active", 1)->count();
$sum = App\Flight::where("active", 1)->sum("price");
$max = App\Flight::where("active", 1)->max("price");

// handle exceptions
// Errortype: Illuminate\Database\Eloquent\ModelNotFoundException
// status code 404 would be returned if exception is not caught
App\Flight::findOrFail(1);
App\Flight::where("legs", ">", 100)->firstOrFail();

// find first record or create/new one
$flight = App\Flight::firstOrCreate(['name' => 'Flight 10']);
$flight = App\Flight::firstOrNew(['name' => 'Flight 10']);

```

#### Use `Collection` Class
Example: reject() and map()
```php
$users = App\User::where('active', 1)->get();

$names = $users->reject(function ($user) {
    return $user->active === false;
})
->map(function ($user) {
    return $user->name;
});
```

`reject()` is used to exclude data not needed.
For the opposite, `filter()` is used to filter the data qualified.

FYR: [Eloquent - Collections](https://laravel.com/docs/5.4/eloquent-collections)

### Insert Data
```php
/*
 * Insert Data
 * SQL:
 * INSERT INTO favorites (user_id, post_id) VALUES (1, 2);
 */
App\Favorite::create([
    "user_id" => 1,
    "post_id" => 2,
]);
```

### Update Data
```php
/*
 * Update Data
 * SQL:
 * UPDATE posts SET updated_at = NOW();
 */
App\Post::query()->update([
    "updated_at" => \Carbon\Carbon::now(),
]);

// Update record and create one if not existed
$flight = App\Flight::updateOrCreate(
    ['departure' => 'Oakland', 'destination' => 'San Diego'],
    ['price' => 99]
);
```

### Delete Data
```php
/*
 * Delete Data
 * SQL:
 * DELETE FROM favorites WHERE user_id = 1 AND post_id = 1;
 */
App\Favorite::where([
    "user_id" => 1,
    "post_id" => 1,
])->delete();

// Destroy by key
App\Flight::destroy(1);
```

#### Soft Delete 論理削除（ソフトデリート）
Create Content Table:
```
php artisan make:migration create_contents_table --create=contents
```

Edit create_contents_table.php:
```php
public function up()
{
    Schema::create('contents', function (Blueprint $table) {
        $table->increments('id');
        $table->text('body');
        $table->timestamps();

        // Import softDeletes() method to table
        $table->softDeletes();
    });
}
```

Create model Content for table:
```
php artisan make:model Content
```

Edit Content Model:
```php
use Illuminate\Database\Eloquent\SoftDeletes;

class Content extends Model
{
    use SoftDeletes;

    protected $dates = ['deleted_at'];

    protected $fillable = [
        'body'
    ];
}
```

Run migration:
```
php artisan migrate
```

When `delete()` method is called, the record would be soft-deleted. 

Manipulated soft-deleted data:
```php
// Get soft-deleted data
$content = Content::onlyTrashed()->whereNotNull('id')->get();

// Get all data including soft-deleted data
$content = Content::withTrashed()->whereNotNull('id')->get();

// Force delete soft-deleted data
Content::onlyTrashed()->whereNotNull('id')->forceDelete();

// Restroe soft-deleted data
Content::onlyTrashed()->whereNotNull('id')->restore();
```

FYR: [Eloquent - Soft Deleting](https://laravel.com/docs/8.x/eloquent#soft-deleting)
