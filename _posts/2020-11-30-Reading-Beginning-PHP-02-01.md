---
layout: post
title: "Reading: Beginning PHP Part2 01"
date: 2020-11-30
categories: [PHP]
---

Notes taking from: Beginning PHP and MySQL From Novice to Professional

Covers:
- Ch13 Forms
- Ch14 Authenticating Your Users
- Ch15 Handling File Uploads

## Ch13 Forms

Topics:

- Pass data from a form to PHP script
- Validate form data
- Work with multivalued form components

### PHP and Web Forms

The first thing to think about when sending data to and from a web server is security.<br>
The HTTP protocol used by browsers is a plain text protocol.

It is relatively easy to add an SSL certificate to the server, and it can be done at no
cost by using services like LetsEncrypy (https://letsencrypt.com).

- When the server has an SSL certificate installed, the communication will be done via HTTPS where the
  server will send a public key to the browser.
- the public key is used to encrypt any data from the browser and decrypt data coming from the server.
- The server will use the matching private key to encrypt and decrypt.

In cases where you’re posting data back to the same script from which it originated, you can use the PHP superglobal variable `$_SERVER["PHP_SELF"]`.

```php
<form action="<?php echo $_SERVER['PHP_SELF']; ?>" method="post">
...
```

### Validating Form Data

Validate all user input to ensure that it’s provided in the desired format and that it is incapable of doing any harm to the website or underlying operating system.

Example: File Deletion

If `exec()` or `system()` is called in the PHP program, users can maliciously pass command like **rm -rf \***.

Example: Cross-Site Scripting

This attack involves the insertion of malicious code into a page frequented by other users (eg. an online bulletin board). Merely visiting this page can result in the transmission of data to a third-party’s site, which could allow the attacker to later return and impersonate the unwitting visitor.

Modern browsers support both `in-memory` and `http-only` cookies. It is more difficult for an attacker to get access to the cookie values from injected JavaScript.<br>
Setting the session cookie to http-only by adding **session.cookie_httponly = 1** to the php.ini file.

### Sanitizing User Input

- escapeshellarg(): delimits its arguments with single quotes and escapes quotes
- escapeshellcmd(): sanitizes potentially dangerous input program names rather than program arguments<br>
  This function operates by escaping any shell metacharacters found in the command including **\# \& \; \` \, \| \* \? \~ \< \> \^ \( \) \[ \] \{ \} \$ \\ \\x0A \\xFF**.
- htmlentities(): converts certain characters having special meaning in an HTML context to strings
- strip_tags(): removes all tags from a string

#### Validating and Sanitizing Data with the Filter Extension

A native validation features the `Filter extension` was added in version 5.2.<br>
FYR: [PHP - filter and sanitize types](https://www.php.net/manual/en/filter.filters.php)

```php
$email = "john@@example.com";
if (! filter_var($email, FILTER_VALIDATE_EMAIL))
{
    echo "INVALID E-MAIL!";
}
```

Table 13-1 Filter Extension's Validation Capabilities

|Target Data|Identifier|
|:---:|:---|
|Boolean values|FILTER_VALIdATE_BOOLEAN|
|E-mail addresses|FILTER_VALIDATE_EMAIL|
|Floating-point numbers|FILTER_VALIDATE_FLOAT|
|Integers|FILTER_VALIDATE_INT|
|IP addresses|FILTER_VALIDATE_IP|
|MAC address|FILTER_VALIDATE_MAC|
|Regular Expressions|FILTER_VALIDATE_REGEXP|
|URLs|FILTER_VALIDATE_URL|

Table 13-2 Filter Extension's Sanitization Capabilities

|Identifier|Purpose|
|:----:|:----:|
|FILTER_SANITIZE_EMAIL|Removes all characters from a string except those allowable within an e-mail address ad defined within [RFC 822](https://www.w3.org/Protocols/rfc822/)|
|FILTER_SANITIZE_ENCODED|URL encodes a string, producing output identical to that returned by the **urlencode()** function.|
|FILTER_SANITIZE_MAGIC_QUOTES|Escapes potentially dangerous characters with abackslash using the **addslashes()** function.|
|FILTER_SANITIZE_NUMBER_FLOAT|Removes any characters that would result in a Floatingpoint value not recognized by PHP|
|FILTER_SANITIZE_NUMBER_INT|Removes any characters that would result in an integer value not recognized by PHP|
|FILTER_SANITIZE_SPECIAL_CHARS|HTML-encodes any character having an ASCII value less than 32 (including a tab and backspace)|
|FILTER_SANITIZE_STRING|Strips all tags such as \<p\> and \<b\>.|
|FILTER_SANITIZE_STRIPPED|An alias of string fliter.|
|FILTER_SANITIZE_URL|Removes all characters from a string except for those allowable within a URL as defined within [RFC 3986](https://tools.ietf.org/html/rfc3986)|
|FILTER_UNSAFE_RAW|strip and encode characters|

### Working with Multivalued Form Components
Assign html form input name as array:
```php
<?php
if (isset($_POST["submit"]))
{
    echo "You like the following languages:<br>";
    if (is_array($_POST["languages"])) {
        foreach($_POST["languages"] AS $language) {
            $language = htmlentities($language);
            echo "$language<br>";
        }
    }
}
?>

<form action="<?php echo $_SERVER["PHP_SELF"]; ?>" method="post">
    What's your favorite programming language?<br> (check all that apply):<br>
    <input type="checkbox" name="languages[]" value="csharp">C#<br>
    <input type="checkbox" name="languages[]" value="javascript">JavaScript<br>
    <input type="checkbox" name="languages[]" value="perl">Perl<br>
    <input type="checkbox" name="languages[]" value="php">PHP<br>
    <input type="submit" name="submit" value="Submit">
</form>
```

## Ch14 Authenticating Your Users
Topics:
- Basic HTTP-based authentication concepts
- Authentication variables `$_SERVER['PHP_AUTH_USER']` and `$_SERVER['PHP_AUTH_PW']`
- PHP functions used to authenticate procedures
- Commonplace authentication methodologies:
  - Hard-coding the login pair directly
  - File-based authentication
  - Data-based authentication
- Recovering lost passwords using one-time URLs
- Using OAuth2 to authenticate

### HTTP Authentication Concepts
Implement a secure communications channel, typically accomplished using a `Secure Sockets Layer` (SSL) or `Transport Layer Security` (TLS).

#### Using Apache’s `.htaccess` Feature
Provide blanket protection to an entire website or specific directory.

```yaml
# .htaccess

AuthUserFile /path/to/.htpasswd
AuthType Basic
AuthName "My Files"
Require valid-user
```

`.htpasswd` file contains the username and password to access the restricted content.<br>
This file should be placed outside of the directory structure used for the website to prevent it from being accessed directly by visitors.

```yaml
# .htpasswd
# username:password pair

admin:TcmvAdAHiM7UY
client:f.i9PC3.AtcXE
```

Command to generate username:password pair
```shell
# -c option
# create a new .htpasswd file

> htpasswd -c .htpasswd client
```

FYR: [htpasswdコマンドの使い方](https://qiita.com/hana_shin/items/527d924ee4f6aa05eefa)

### Authenticating Your Users with PHP
PHP’s Authentication Variables
- $_SERVER['PHP_AUTH_USER']: stores the username
- $_SERVER['PHP_AUTH_PW']  : stores the password

#### Sending HTTP Headers with header()
The `header()` function sends a raw HTTP header to the browser.<br>
A header is additional information sent before the actual content that is seen in the browser.

```php
void header(
    string header [, 
    boolean replace [, 
    int http_response_code]]
)
```

- `replace` parameter determines whether this header should replace a previously sent one.
- `http_response_code` parameter defines a specific response code.

```php
header('WWW-Authenticate: Basic Realm="Book Projects"');
header("HTTP/1.1 401 Unauthorized");
```

#### Determining If a Variable Is Set with `isset()`
The `isset()` function determines whether a variable has been assigned a value.

```php
boolean isset(mixed var [, mixed var [,...]])
```

It returns `TRUE` if the variable is set and contains a value different from the **null** value.

```php
if (! isset($_SERVER['PHP_AUTH_USER']) || ! isset($_SERVER['PHP_AUTH_PW'])) {
    header('WWW-Authenticate: Basic Realm="Authentication"');
    header("HTTP/1.1 401 Unauthorized");
} else {
    echo "Your supplied username: {$_SERVER['PHP_AUTH_USER']}<br />";
    echo "Your password: {$_SERVER['PHP_AUTH_PW']}<br />";
}
```

#### Hard-Coded Authentication

```php
$secret = 'e5e9fa1ba31ecd1ae84f75caaa474f3a663f05f4';
if (
    ($_SERVER['PHP_AUTH_USER'] != 'client') ||
    (hash('sha1', $_SERVER['PHP_AUTH_PW']) != $secret)
) {
    header('WWW-Authenticate: Basic Realm="Secret Stash"');
    header('HTTP/1.0 401 Unauthorized');
    print('You must provide the proper credentials!');
    exit;
}
```

#### File-Based Authentication
Security consideration regarding password file should be stored outside the server document root.<br>
Or an attacker could discover the file through brute-force guessing, revealing half of the login combination and use rainbow tables, password lists, or brute forcing to discover the password as well.

- file(string filename): reads a file into an array
- explode(string separator, string string [, int limit]): splits a string into a series of substrings
- password_hash(string password, int algo): returns a string with salted hash

```php
$authorized = false;
if (isset($_SERVER['PHP_AUTH_USER']) && isset($_SERVER['PHP_AUTH_PW'])) {
    // Read the authentication file into an array
    $authFile = file("/usr/local/lib/php/site/authenticate.txt");
    
    foreach ($authFile, $line ) {
        list($user, $hash) = explode(":", $line);
        if (
            $_SERVER['PHP_AUTH_USER'] == $user && 
            password_verify($_SERVER['PHP_AUTH_PW'], trim($hash))
        ) {
            $authorized = true;
            break;
        }
    }
}

If (!$_SERVER['HTTPS']) {
    echo " Please use HTTPS when accessing this document";
    exit;
}

if (!$authorized) {
    header('WWW-Authenticate: Basic Realm="Secret Stash"');
    header('HTTP/1.0 401 Unauthorized');
    print('You must provide the proper credentials!');
    exit;
}
```

#### Database-Based Authentication
Create user table:
```sql
CREATE TABLE logins (
    id INTEGER UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(255) NOT NULL,
    pswd CHAR(40) NOT NULL
);
```

---

```php
function authenticate_user() {
    header('WWW-Authenticate: Basic realm="Secret Stash"');
    header("HTTP/1.0 401 Unauthorized");
    exit;
}

if (! isset($_SERVER['PHP_AUTH_USER'])) {
    authenticate_user();
} else {
    $db = new mysqli("localhost", "webuser", "secret", "chapter14");
    $stmt = $db->prepare("SELECT username, pswd FROM logins WHERE username=? AND pswd= ?");
    $stmt->bind_param(
        'ss', 
        $_SERVER['PHP_AUTH_USER'], 
        password_hash($_SERVER['PHP_AUTH_PW'], PASSWORD_DEFAULT)
    );
    $stmt->execute();
    $stmt->store_result();

    if ($stmt->num_rows == 0) { authenticate_user(); }
}
```

### Password Hashing
PHP 5.5 and later:
- password_hash()
- password_verify()

```php
string password_hash(string $password, integer $algo [, array $options ])
```

- string $password<br>
  the string containing the clear text password
- integer $algo<br>
  PHP supports bcrypt, Blowfish, and Argon2

FYR: [PHP Document - password hash](https://www.php.net/manual/en/function.password-hash.php)

#### One-Time URLs and Password Recovery
Generate unique id token:
```php
$id = hash('sha1', uniqid(rand(),1));

// Create unique identifier
$id = md5(uniqid(rand(),1));
```

### Using OAuth 2.0
OAuth 2.0 is an industry-standard protocol for authorization.

It is commonly used with third-party authorization services where a user is redirected to a different site where the user’s identity is validated and upon successful validation the uer is redirected back to the site and the server can get an access token from the third-party site.

## Ch15 Handling File Uploads

Topics:

- PHP's file upload configuration directives
- PHP's $_FILES superglobal array, used to handle file-upload data
- PHP's built-in file-upload functions: **is_uploaded_file()** and **move_uploaded_file()**
- A review of possible error messages returned from an upload script

### Uploading Files via HTTP

Standardized methodology for uploading within RFC 1867, [Form-Based File Upload in HTML] offered the specification for a new Internet media type, **multipart/form-data**.

```html
<form action="uploadmanager.html" enctype="multipart/form-data" method="post">
  ...
</form>
```

### Uploading Files with PHP

#### PHP's File Upload/Resource Directives

- `file_uploads = On/Off`<br>
  Scope: PHP_INI_SYSTEM<br>
  Default value: On<br>
  determines whether PHP scripts can accept file uploads
- `max_input_time = integer`<br>
  Scope: PHP_INI_ALL<br>
  Dafault value: -1<br>
  determines the maximum amount of time(seconds) that a PHP script spends attempting to parse input before registering a fatal error
- `max_file_uploads = integer`<br>
  Scope: PHP_INI_SYSTEM<br>
  Default value: 20<br>
  sets an upper limit on the number of files that can be simultaneously uploaded
- `memory_limit = integer`<br>
  Scope: PHP_INI_ALL<br>
  Dafault value: 16M<br>
  sets a maximum allowable amount of memory in megabytes that a script can allocate
- `post_max_size = integer`<br>
  Scope: PHP_INI_PERDIR<br>
  Default value: 8M<br>
  places an upper limit on the size of data submitted via the POST method
- `upload_max_filesize = integer`<br>
  Scope: PHP_INI_PERDIR<br>
  Default value: 2M<br>
  determines the maximum size of an uploaded single file
- `upload_tmp_dir = string`<br>
  Scope: PHP_INI_SYSTEM<br>
  Default value: NULL<br>
  a staging area for uploaded files to be temporarily placed until being moved to the final location<br>
  this directory must be writable by the user owning the server process<br>
  if upload_tmp_dir is undefined or set to null the system defined tmp dir(/tmp) will be used

#### The $_FILES Array

- $_FILES[form-file-input-element]["error"]<br>
  offers important information pertinent to the outcome of the upload attempt
- $_FILES[form-file-input-element]["name"]<br>
  specifies the original name of the file, including the extension
- $_FILES[form-file-input-element]["size"]<br>
  specifies the size, in bytes
- $_FILES[form-file-input-element]["tmp_name"]<br>
  specifies the temporary name assigned to the file once it has been uploaded to the server
- $_FILES[form-file-input-element]["type"]<br>
  specifies the MIME type of the file<br>
  FYR: [よくある MIME タイプ](https://developer.mozilla.org/ja/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types)

#### PHP's File-Upload Functions

- is_uploaded_file(): return boolean<br>
  determines whether a file specified by the input parameter filename is uploaded using the POST method<br>
  ```php
  if (is_uploaded_file($_FILES["classnotes"]["tmp_name"])) {
      copy($_FILES["classnotes"]["tmp_name"],
           "/www/htdocs/classnotes/".$_FILES["classnotes"]["name"]);
  } else {
      echo "<p>Potential script abuse attempt detected.</p>";
  }
  ```
- move_uploaded_file(): return boolean<br>
  provides a convenient means for moving an uploaded file from the temporary directory to a final location<br>
  Although `copy()` works equally well, `move_uploaded_file()` offers a additional check to ensure that the file was in fact uploaded via PHP’s HTTP POST upload mechanism<br>
  ```php
  move_uploaded_file($_FILES["classnotes"]["tmp_name"],
                     "/www/htdocs/classnotes/".$_FILES["classnotes"]["name"]);
  ```

#### Upload Error Messages

outcomes of $_FILES["userfile"]["error"]:

- UPLOAD_ERR_OK: a value of 0 is returned if the upload is successful
- UPLOAD_ERR_INI_SIZE: a value of 1 is returned if the file size exceeds upload_max_filesize
- UPLOAD_ERR_FORM_SIZE: a value of 2 is returned if th file size exceeds max_file_size
- UPLOAD_ERR_PARTIAL: a value of 3 is returned if a file is not completely uploaded
- UPLOAD_ERR_NO_FILE: a value of 4 is returned if the form has no files for upload
- UPLOAD_ERR_NO_TMP_DIR: a value of 6 is returned if the temporary directory does not exist
- UPLOAD_ERR_CANT_WRITE: a value of 7 is returned if the file can’t be written to the disk
- UPLOAD_ERR_EXTENSION: a value of 8 is returned if one of the installed PHP extensions caused the upload to stop
