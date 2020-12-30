---
layout: post
title: "Reading: Beginning PHP Part2 03"
date: 2020-12-18
categories: [PHP]
---

Notes taking from: Beginning PHP and MySQL From Novice to Professional

Covers:
- Ch18 Web Services
- Ch19 Secure PHP Programming

## Ch18 Web Services
It is common to apply the **Representational State Transfer (REST)** architecture or **RESTful** web services using HTTP protocol to exchange information between multiple systems.

### Getting Started with APIs
- json_encode()
- json_decode()

```php
// object to JSON string
$a = ['apple', 'orange', 'pineapple', 'pear'];
header('Content-Type: application/json');
echo json_encode($a);

// JSON string to object
$json ='["apple","orange","pineapple","pear"]';
print_r(json_decode($json));
```

Example: Get weather info
```php
$OpenWeather = ['api_key' => '<API KEY>'];
// zip code 98109 (Seattle, WA)
$zip = "98109";

$base_url = "https://api.openweathermap.org/data/2.5";
$weather_url = "/weather?zip=" . $zip;
$api_key = "&appid={$OpenWeather['api_key']}";

$api_url = $base_url . $weather_url . $api_key;
$weather = json_decode(file_get_contents($api_url));

print_r($weather);
```

### API Security
It is necessary to keep the APIs more secure to prevent anyone with access to the GET or POST URL to interact with the endpoint.

#### Sign signature with Secret string
The secret will never be passed along with the request, but it is used to create a signature in the form of
a hash that can be re-created on the server from the parameters included in the request.

Generate Secret with PHP `hash_hmac()`:
```php
hash_hmac(string $algo, string $data, string $key [, bool $raw_output])
```

- $algos: select the hashing algorithm to use<br>
  allowed values can be found by calling the `hash_hmac_algos()` function

```php
$url = "http://webservices.amazon.com/onca/xml";
$param = "AWSAccessKeyId=AKIAIOSFODNN7EXAMPLE&AssociateTag=mytag-20&ItemId=
0679722769&Operation=ItemLookup&ResponseGroup=Images%2CItemAttributes%2COff
ers%2CReviews&Service=AWSECommerceService&Timestamp=2014-08-18T12%3A00%3A00
Z&Version=2013-08-01";

$data = " GET webservices.amazon.com/onca/xml" . $param;
$key = "1234567890";
$Signature = base64_encode(hash_hmac("sha256", $param, $key, true));

$request = $url . "?" . $param . "&Signature=" . $Signature;
echo $request;
```

### Create an API
1. Add authentication to decide on how the users are identified when they interact with the service. (name secret)
2. Define how the signature is generated and passed to the server on every request.
3. Validate the signature.
4. Valid requests lead to JSON responses.

```php
class logService {
    private function authenticate()
    {
        if (
            empty($_GET['AppId']) || 
            empty($_GET['Timestamp']) || 
            empty($_GET['Signature'])
        ) {
            return false;
        } else {
            $Secret = null;

            if ($_GET['AppId'] == 'MyApplication') {
                $Secret = '1234567890';
            }
            
            if ($Secret) {
                $params = "AppId={$_GET['AppId']}&Timestamp={$_GET['Timestamp']}";
                $Signature = base64_encode(
                    hash_hmac(
                        "sha256", 
                        $param, 
                        $Secret,
                        true
                    )
                );

                if ($Signature == $_GET['Signature']) {
                    return $_GET['AppId'];
                } else {
                    return false;
                }
            }
        }
    }
    public function addEvent()
    {
        if ($filename = $this->authenticate()) {
            $entry = gmdate('Y/m/d H:i:s') . 
                     ' ' . $_SERVER['REMOTE_ADDR'] . 
                     ' ' . $_GET['Msg']);
                     
            file_put_contents('/log/' . $filename .'.log', $entry . "\n", FILE_APPEND);
            
            header('Content-Type: application/json');
            echo json_encode(true);
        } else {
            header('Content-Type: application/problem+json');
            echo json_encode(false);
        }
    }
    public function getEvents()
    {
        if ($filename = $this->authenticate()) {
            header('Content-Type: text/plain');
            readfile('/log/' . $filename .'.log');
        } else {
            header('Content-Type: application/problem+json');
            echo json_encode(false);
        }
    }
};
```

---

Expose endpoints of API:

```php
// add_event.php
require "log_service.php";
$log = new logService();
$log->addEvent();

// get_events.php
require "log_service.php";
$log = new logService();
$log->getEvents();
```

---

Call API: Add Event

```php
$AppId = 'MyApplication';
$Secret = '1234567890';
$url = 'https://logservice.com/api/add_event.php';
$Timestamp = time();
$Msg = 'Testing of the logging Web Service';
$params = "AppId={$AppId}&Timestamp={$Timestamp}";
$Signature = base64_encode(hash_hmac("sha256", $params, $Secret, true));

$QueryString = $params . '&Msg=' . urlencode($Msg) . '&Signature=' . urlencode($Signature);

echo file_get_contents($url . '?' . $QueryString);
```

Call API: Get Event

```php
$AppId = 'MyApplication';
$Secret = '1234567890';
$url = 'https://logservice.com/api/get_events.php';
$Timestamp = time();
$params = "AppId={$AppId}&Timestamp={$Timestamp}";
$Signature = base64_encode(hash_hmac("sha256", $params, $Secret, true));

$QueryString = $params . '&Signature=' . urlencode($Signature);

echo file_get_contents($url . '?' . $QueryString);
```

## Ch19 Secure PHP Programming
Potential vulnerabilities:
- Software vulnerabilities<br>
  Keep all your software up to date.
- User input
- Poorly protected data

### Security-Related Configuration Parameters
#### disable_functions = string
Scope: PHP_INI_SYSTEM;  Default value: NULL

```yaml
# disable fopen(), popen(), and file() functions
disable_functions = fopen,popen,file
```

#### disable_classes = string
Scope: PHP_INI_SYSTEM;  Default value: NULL

#### display_errors = On | Off
Scope: PHP_INI_ALL;  Default value: On

Remember to disable this directive when in the production environment.

#### max_execution_time = integer
Scope: PHP_INI_ALL;  Default value: 30

This directive specifies how many seconds a script can execute before being terminated.<br>
If `max_execution_time` is set to 0 and no time limit will be set.

#### memory_limit = integerM
Scope: PHP_INI_ALL; Default value: 128M

Always follow the number with an M. <br>
This directive is only applicable if `--enablememory-limit` is enabled.

#### open_basedir = string
Scope: PHP_INI_ALL; Default value: NULL

This prevents users from entering otherwise restricted areas of the server.

#### user_dir = string
Scope: PHP_INI_SYSTEM; Default value: NULL

This directive specifies the name of the directory in a user’s home directory where PHP scripts must be placed in order to be executed.<br>
This directive is typically used in conjunction with Apache’s `UserDir` configuration directive.

### Hiding Apache
- ServerSignature
- ServerTokens

#### Apache’s ServerSignature Directive
Consider disabling the `ServerSignature` directive by setting it to Off.<br>
If for some reason ServerSignature must be enabled, consider setting the directive to `Prod`.

#### Apache’s ServerTokens Directive
The `ServerTokens` directive determines what degree of server details is provided if the `ServerSignature` directive is enabled.

- Full: Apache/2.4.18 (Ubuntu) PHP/7.2.1 Server
- Major: Apache/2 Server
- Minimal: Apache/2.4.18 Server
- Minor: Apache/2.4 Server
- OS: Apache/2.4.18 (Ubuntu) Server
- Prod: Apache Server

### Hiding PHP
`expose_php` prevents PHP version details from being appended to the web server signature.<br>
Blocking access to `phpinfo()` prevents attackers from learning your software infomation.

#### expose_php = 1|0
Scope: PHP_INI_SYSTEM; Default value: 1

#### Remove All Instances of phpinfo() Calls
It is a good idea to disable this function in production environments using the `disable_functions` directive.

#### Change the Document Extension
Change `httpd.conf`:
```yaml
# default
AddType application/x-httpd-php .php

# change to html
AddType applicaion/x-httpd-php .html
```

#### Hiding the Document Root
In Apache’s `httpd.conf` file is a configuration directive named `DocumentRoot`.<br>
This is the path that the server recognizes as the public HTML directory.

```yaml
DocumentRoot C:/apache2/htdocs   # Windows
DocumentRoot /www/apache/home    # Linux
```

#### Denying Access to Certain File Extensions
Denying all accesses to files with extension `.inc`:
```yaml
# httpd.conf
<Files *.inc>
    Order allow, deny
    Deny from all
</Files>
```

### Data Encryption
Having an encrypted website that uses HTTPS protocol instead of HTTP.

#### Hashing Data with `hash()` Function
Hashing data is a non-reversible way of encoding the data so they are no longer readable.

```php
string hash(string algo, string data [, bool raw_output])
```

A full list of the supported algorithms can be obtained with the `hash_algos()` function.<br>
FYR: [PHP Document: Hash](https://www.php.net/hash)

MD5 function is no longer considered safe for hashing of passwords or creating signatures, but it can be used to create a hash of the content of a file.<br>
The hash can be stored in a database and when a hash is created for another file, it’s easy to compare if that file is seen before.

PHP provides a special hashing function for dealing with passwords called `password_hash()`.<br>
This function will handle the salt value and hashing algorithm.<br>
The string returned for the same password will never be the same password value.<br>
In order to compare a password against a saved password, you will have to call the function `password_verify()`.

#### Encrypting Data Using OpenSSL
`OpenSSL` allows you to encrypt and decrypt values using encryption keys.

- a symmetrical key where the same key is used for both encryption and decryption
- a public and private key pair where one key is used for encryption and the other for decryption

`openssl_encrypt()` and `openssl_decrypy() functions`:
- Both take three mandatory parameters (**$data**, **$cipher**, and **$key**) and five optional parameters  including `$options` and `$iv`.
    - The cipher value is used to select the encryption method to be used. (default: AES-128-CBC).<br>
      A full list of available ciphers can be obtained by calling the `openssl_get_cipher_methods()` function.
    - The `$iv` parameter is the initialization vector.<br>
      generated as a number of random byte values corresponding to the length of the selected cipher.<br>
      prepended to the encrypted string along with a signature hash that can be used when decrypting to ensure the value was unchanged.
    - The functions `openssl_cipher_iv_length()` and `openssl_random_pseudo_bytes()` are used to get the length and the list of random bytes.

```php
class AES {
    private $key = null;
    private $cipher = "AES-128-CBC";
    
    function __construct($key, $cipher = "AES-128-CBC") 
    {
        $this->key = $key;
        $this->cipher = $cipher;
    }

    function encrypt($data) 
    {
        if (in_array($this->cipher, openssl_get_cipher_methods())) {
            $ivlen = openssl_cipher_iv_length($this->cipher);
            $iv = openssl_random_pseudo_bytes($ivlen);
            $encrypted = openssl_encrypt($data, $this->cipher, $this->key, OPENSSL_RAW_DATA, $iv);
            $hmac = hash_hmac('sha256', $encrypted, $this->key, true);

            return base64_encode($iv.$hmac.$encrypted);
        }　else {
            return null;
        }
    }

    function decrypt($data) {
        $c = base64_decode($data);
        $ivlen = openssl_cipher_iv_length($this->cipher);
        $iv = substr($c, 0, $ivlen);
        $hmac = substr($c, $ivlen, $sha2len=32);
        $encrypted = substr($c, $ivlen+$sha2len);
        $hmac_check = hash_hmac('sha256', $encrypted, $this->key, true);
        
        if (hash_equals($hmac, $hmac_check)) {
            return openssl_decrypt($encrypted, $this->cipher, $this->key,
            OPENSSL_RAW_DATA, $iv);
        }　else {
            return null;
        }
    }
}
```

