---
layout: post
title: "Reading: Beginning PHP Part2 02"
date: 2020-12-17
categories: [PHP]
---

Notes taking from: Beginning PHP and MySQL From Novice to Professional

Covers:
- Ch17 Session Handlers

## Ch17 Session Handlers
Topics:
- How to configure PHP to most effectively use the session feature
- How to create and destroy sessions, and manage session variables
- How to manage session data in a database

### What Is Session Handling?
The Hypertext Transfer Protocol (HTTP) is a stateless protocol.<br>
Each request is processed without any knowledge of any prior or future requests.

The practice of storing bits of information on the client’s machine called `cookies` quickly gained acceptance.

Limits on cookie size, the number of cookies allowed, and security problems prompt another solution: session handling.

Session is accomplished by assigning site visitors a unique identifying attribute, known as the session ID (SID).<br>
The session ID is stored as a cookie in the browser and automatically included in every subsequent request to the server.

#### The Session-Handling Process
`session_start()` function will detect if a session cookie is already defined. 
- not defined: add a cookie header to the response
- is defined: PHP will look for the associated session file and use that to populate the `$_SESSION` super global.

### Configuration
#### Managing Session Storage Media
The `session.save_handler` directive determines how the session information will be stored.

```php
session.save_handler = files|mm|redis|sqlite|user
```

Session data can be stored in:
- flat files (files): default setting
- volatile memory (mm)<br>
  need to install the [mm](https://www.ossp.org/pkg/lib/mm/) library
- [Redis server](https://redis.io) (redis)
  - works like an in-memory solution
  - supports persistence to disk
  - allows the session data to be shared between multiple web servers
  - requires the [Redis extension](https://github.com/phpredis/phpredis)
- SQLite database (sqlite)
- user-defined functions (user)
  - most complicated to configure
  - most flexible and powerful

#### Setting Session Files Path
By default, this directive is not defined.<br>
The system will use `/tmp` as the location for session files.

```php
session.save_path = string
```

#### Automatically Enabling Sessions
By default, session will be enabled only by calling `session_start()`.

```php
session.auto_start = 0 | 1
```

Drawback of enabling auto start is that class definitions need to be loaded.

#### Setting the Session Name
By default, PHP will use a session name of `PHPSESSID`.

```php
session.name = string
```

#### Choosing Cookies or URL Rewriting
To maintain a user’s session over multiple visits to the site.

```php
session.use_cookies = 0 | 1
```

- Setting to 1 (the default) results in the use of cookies for SID propagation
- Setting to 0 causes URL rewriting that is possible to view the session ID as part of the URL

To help prevent cookie hijacking, not allowed to pass cookie as a parameter in querystrings

```php
session.use_only_cookies = 0 | 1
```

Most modern browsers allow defining cookies as http only. <br>
Doing so will prevent the cookie from being access from JavaScript.

```php
session.cookie_httponly = 0 | 1
```

Send cookies to the browser if a secure SSL connection is used.

```php
session.cookie_secure = 0 | 1
```

#### Setting the Session Cookie Lifetime
The `session.cookie_lifetime` directive determines the session cookie’s period of validity.

```php
# lifetime is specified in seconds
session.cookie_lifetime = integer
```

By default, the value is set to 0.<br>
The cookie will live until the browser is restarted.

#### Setting Session Cookie’s Valid URL Path
`session.cookie_path` determines the path in which the cookie is considered valid.

```php
session.cookie_path = string
```

By default, it is set to **/** and the cookie will be valid for the entire website.

#### Setting the Session Cookie’s Valid Domain
`session.cookie_domain` determines the domain for which the cookie is valid.

```php
session.cookie_domain = string

session.cookie_domain = www.example.com

# for site subdomains
session.cookie_domain = .example.com
```

#### Setting Caching Directions
`session.cache_limiter` modifies cache-related headers, providing instructions regarding caching preference.

```php
session.cache_limiter = string
```

available options:
- none<br>
  disables the transmission of any cache control headers
- nocache<br>
  the default setting<br>
  ensures that every request is first sent to the originating server
- private<br>
  the document will be made available only to the originating user
- private_no_expire<br>
  no document expiration date<br>
  Otherwise identical to the private setting
- public<br>
  deems all documents as cacheable<br>
  a useful choice for non-sensitive areas of your site

##### Setting Cache Expiration Time
`session.cache_expire` determines the number of seconds (180 by default)
that cached session pages are made available before new pages are created.

The setting would be ingored if `session.cache_limiter` is set to **nocache**.

```php
session.cache_expire = integer
```

##### Setting the Session Lifetime
`session.gc_maxlifetime` determines the duration, in seconds (by default
1440, 24 mins), for which session data is considered valid.

gc stands for garbage collected.

```php
session.gc_maxlifetime = integer
```

Other session garbage collection features:
- session.gc_divisor
- session.gc_probability

> session.gc_divisor と session.gc_probability の組み合わせで すべてのセッションの初期化過程で gc （ガーベッジコネクション）プロセス も始動する確率を制御します。<br>
> 確率は gc_probability/gc_divisor で計算されます。<br>
> 例えば、1/100 は各リクエスト毎に1%の確率で GC プロセスが 始動します。 <br>
> ession.gc_divisor のデフォルトは 100 です。<br>
> -- [PHP Document - Sessions 実行時設定](https://www.php.net/manual/ja/session.configuration.php)

### Working with Sessions
#### Starting a Session
Executing `session_start()` will create a new session if no SID is found, or continue a current session if an SID exists.

```php
Boolean session_start([ array $options = array() ]);
```

The optional parameter `$options` was introduced in PHP 7.0 as a way to overwrite the directives configured in `php.ini`.

Neglecting to execute it **_before_** any output has been sent to the browser will result in an error (headers already sent).

#### Destroying a Session
- session_unset()<br>
  erases all session variables stored in the current session<br>
  it will not completely remove the session from the storage mechanism
  ```php
  void session_unset()
  ```
- session_destroy()<br>
  invalidates the current session by removing the session from the storage mechanism
  ```php
  Boolean session_destroy()
  ```

#### Setting and Retrieving the Session ID
`session_id()` can both set and get the SID

- If it is passed as no parameter, the function returns the current SID
- If the optional SID parameter is included, the current SID will be replaced with that value

```php
string session_id([string sid])
```

---

```php
session_start();
echo "Your session identification number is " . session_id();

// Your session identification number is 967d992a949114ee9832f1c11c
```

#### Creating and Deleting Session Variables
```php
session_start();

// set a session variable named username
$_SESSION['username'] = "Jason";
printf("Your username is %s.", $_SESSION['username']);

// delete the variable
unset($_SESSION['username']);
```

#### Encoding and Decoding Session Data
PHP stores session data in a standardized format consisting of a single string.

Each session variable reference is separated by a semicolon and consists of three components: the name, length, and value.

```php
name|s:length:"value";

username|s:5:"jason";loggedon|s:20:"Feb 16 2011 22:32:29";
```

##### Encoding Session Data
```php
string session_encode()
```

---

```php
session_start();

$_SESSION['username'] = "jason";
$_SESSION['loggedon'] = date("M d Y H:i:s");

$sessionVars = session_encode();
echo $sessionVars;

// username|s:5:"jason";loggedon|s:20:"Feb 16 2011 22:32:29";
```

`seraialize()` function could obtain a similar result but by default `session_encode()` function will use an internal serialization format.

##### Decoding Session Data
```php
Boolean session_decode(string session_data)
```

---

```php
session_start();
$sid = session_id();

$sessionVars = username|s:5:"jason";loggedon|s:20:"Feb 16 2011 22:32:29";
session_decode($sessionVars);
echo "User ".$_SESSION['username']." logged on at ".$_SESSION['loggedon'].".";
```

#### Regenerating Session IDs
Session-fixation attacks involve an attacker obtaining an user’s SID and gaining access to sensitive information.

PHP offers `session_regenerate_id()` function that will replace the existing ID with a new one.

```php
Boolean session_regenerate_id([boolean delete_old_session])

// delete_old_session
// always pass true to make sure the old data is deleted after a new session id is created
```

### Practical Session-Handling Examples

#### Example: Automatically Logging In Returning Users
Create users Table:
```sql
CREATE TABLE users (
    id INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
    first_name VARCHAR(255) NOT NULL,
    username VARCHAR(255) NOT NULL,
    password VARCHAR(32) NOT NULL,
    PRIMARY KEY(id)
);
```

---

```php
session_start();
if (! isset($_SESSION['username'])) {
    if (isset($_POST['username'])) {
        $db = new mysqli("localhost", "webuser", "secret", "corporate");
        $stmt = $db->prepare("SELECT first_name FROM users WHERE username =? and password = ?");
        $stmt->bind_param('ss', $_POST['username'], $_POST['password']);
        $stmt->execute();
        $stmt->store_result();
        
        if ($stmt->num_rows == 1) {
            $stmt->bind_result($firstName);
            $stmt->fetch();
            $_SESSION['first_name'] = $firstName;
            header("Location: http://www.example.com/");
        }
    } else {
        require_once('login.html');
    }
} else {
    echo "You are already logged into the site.";
}
```

#### Example: Generating a Recently Viewed Document Index
Create articles Table:
```sql
CREATE TABLE articles (
    id INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
    title VARCHAR(50),
    content MEDIUMTEXT NOT NULL,
    PRIMARY KEY(id)
);
```

---

```php
session_start();

$db = new mysqli("localhost", "webuser", "secret", "corporate");
$stmt = $db->prepare("SELECT id, title, content FROM articles WHERE id = ?");
$stmt->bind_param('i', $_GET['id']);
$stmt->execute();
$stmt->store_result();
if ($stmt->num_rows == 1) {
    $stmt->bind_result($id, $title, $content);
    #stmt->fetch();
}

$articleLink = "<a href='article.php?id={$id}'>{$title}</a>";
if (! in_array($articleLink, $_SESSION['articles'])) {
    $_SESSION['articles'][] = $articleLink;
}

echo "<p>$title</p><p>$content</p>";
echo "<p>Recently Viewed Articles</p>";
echo "<ul>";
foreach($_SESSION['articles'] as $doc) {
    echo "<li>$doc</li>";
}
echo "</ul>";
```

### Creating Custom Session Handlers
`session_set_save_handler()` is used to transform PHP’s session-handler behavior into that defined by your custom-handler functions.

```php
session_set_save_handler(
    "session_open", 
    "session_close", 
    "session_read",
    "session_write", 
    "session_destroy",
    "session_garbage_collect"
);
```

- session_open($session_save_path, $session_name)<br>
  initializes any elements that may be used throughout the session process
- session_close()<br>
  closes any open resources initialized by session_open()<br>
  this function does not destroy the session
- session_read($sessionID)<br>
  reads the session data from the storage media
- session_write($sessionID, $value)<br>
  writes the session data to the storage media
- session_destroy($sessionID)<br>
  destroys the session and all relevant session variables
- session_garbage_collect($lifetime)<br>
  deletes all expired sessions

#### Example: Using Custom MySQL-Based Session Handlers
Complete two tasks before deploying MySQL-based handlers:
1. Create a table that stores the session data.
2. Create the six custom-handler functions.

Create sessioninfo Table:
```sql
CREATE TABLE sessioninfo (
    sid VARCHAR(255) NOT NULL,
    value TEXT NOT NULL,
    expiration TIMESTAMP NOT NULL,
    PRIMARY KEY(sid)
);
```

The MySQL Session Storage Handler:
```php
class MySQLiSessionHandler {
    private $_dbLink;
    private $_sessionName;
    private $_sessionTable;
    CONST SESS_EXPIRE = 3600;
    public function __construct($host, $user, $pswd, $db, $sessionName, $sessionTable)
    {
        $this->_dbLink = new mysqli($host, $user, $pswd, $db);
        $this->_sessionName = $sessionName;
        $this->_sessionTable = $sessionTable;
        
        session_set_save_handler(
            array($this, "session_open"),
            array($this, "session_close"),
            array($this, "session_read"),
            array($this, "session_write"),
            array($this, "session_destroy"),
            array($this, "session_gc")
        );
        session_start();
    }

    function session_open($session_path, $session_name) {
        $this->_sessionName = $session_name;
        return true;
    }

    function session_close() {
        return 1;
    }

    function session_write($SID, $value) {
        $stmt = $this->_dbLink->prepare("INSERT INTO {$this->_sessionTable} (sid, value) 
                                         VALUES (?, ?) ON DUPLICATE KEY
                                         UPDATE value = ?, expiration = NULL"
                                        );
        $stmt->bind_param('sss', $SID, $value, $value);
        $stmt->execute();
        session_write_close();
    }

    function session_read($SID) {
        $stmt = $this->_dbLink->prepare(
            "SELECT value FROM {$this->_sessionTable} 
             WHERE sid = ? AND UNIX_TIMESTAMP(expiration) + " 
             . self::SESS_EXPIRE . " > UNIX_TIMESTAMP(NOW())"
            );
        $stmt->bind_param('s', $SID);

        if ($stmt->execute()) {
            $stmt->bind_result($value);
            $stmt->fetch();

            if (! empty($value))
            {
                return $value;
            }
        }
    }

    public function session_destroy($SID) {
        $stmt = $this->_dbLink->prepare("DELETE FROM {$this->_sessionTable} WHERE SID = ?");
        $stmt->bind_param('s', $SID);
        $stmt->execute();
    }

    public function session_gc($lifetime) {
        $stmt = $this->_dbLink->prepare(
            "DELETE FROM {$this->_sessionTable}
             WHERE UNIX_TIMESTAMP(expiration) < " . 
             UNIX_TIMESTAMP(NOW()) - self::SESS_EXPIRE
            );
        $stmt->execute();
    }
}
```
---
```php
// usage of MySQLiSessionHandler
require "mysqlisession.php";
$sess = new MySQLiSessionHandler(
    "localhost", 
    "root", 
    "jason",
    "chapter17",
    "default",
    "sessioninfo"
);
$_SESSION['name'] = "Jason";
```

---

```shell
mysql> select * from sessioninfo;

// +-------------------------------------+---------------+-------------------+
// | SID                                 | expiration    | value             |
// +-------------------------------------+---------------+-------------------+
// | f3c57873f2f0654fe7d09e15a0554f08    | 1068488659    | name|s:5:"Jason"; |
// +-------------------------------------+---------------+-------------------+
```
