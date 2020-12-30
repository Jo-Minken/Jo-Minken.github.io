---
layout: post
title: "PHP Tutorial Notes"
date: 2020-11-12
categories: [PHP]
---

### Environment Setup

A note taking from [PHP Tutorial - Environment Setup](https://www.tutorialspoint.com/php/php_environment.htm)

- web server
- database
- PHP Parser: in order to process PHP script instructions a parser must be installed to generate HTML output that can be sent to the Web Browser

#### Apache Configuration

A note taking from [PHP Tutorial - Apache Configuration for PHP](https://www.tutorialspoint.com/php/php_apache_configuration.htm)

Apache uses `httpd.conf` (under **/etc/httpd/conf**) file for global settings, and the `.htaccess` file for per-directory access settings.

#### PHP.ini Configuration

FYR: [PHP Tutorial - PHP.ini Configuration](https://www.tutorialspoint.com/php/php_ini_configuration.htm)

### Variable Types

A note taking from [PHP Tutorial - Variable Types](https://www.tutorialspoint.com/php/php_variable_types.htm)

- Integer
- Double
- Boolean
- NULL - a special type that only has the value NULL
  returns FALSE when tested with **IsSet()** function
- String

  ```php
  <?php
  $variable = "name";
  $literally = "My $variable will not print!";

  print($literally);
  print "<br>";

  $literally = "My $variable will print!";
  print($literally);

  // output:
  // My $variable will not print!
  // My name will print
  ?>
  ```

- Array
- Object
- Resource - hold references to resources external to PHP, eg database connections

#### interpreting other types as Booleans

- Numbers: 0 -> false and otherwise -> true
- Strings: "" or "0" -> false and otherwise -> true
- NULL: always false
- Arrays: blank array -> false and otherwise -> true
- Objects: no member variable has been asssigned a value -> false and otherwise -> true
- Resources: invalid resources -> false and otherwise -> true

#### Here Document

Assign multiple lines to a single string variable:

```php
<?php
   $channel =<<<_XML_

   <channel>
      <title>What"s For Dinner</title>
      <link>http://menu.example.com/ </link>
      <description>Choose what to eat tonight.</description>
   </channel>
   _XML_;

   echo <<<END
   This uses the "here document" syntax to output multiple lines with variable
   interpolation. Note that the here document terminator must appear on a line with
   just a semicolon. no extra whitespace!


   END;

   print $channel;

   /*
   * This uses the "here document" syntax to output
   * multiple lines with variable interpolation. Note
   * that the here document terminator must appear on a
   * line with just a semicolon. no extra whitespace!

   * <channel>
   * <title>What"s For Dinner<title>
   * <link>http://menu.example.com/<link>
   * <description>Choose what to eat tonight.</description>
   */
?>
```
---
```php
/* 変数を使用するより複雑な例 */
class foo
{
    var $foo;
    var $bar;

    function __construct()
    {
        $this->foo = "Foo";
        $this->bar = array("Bar1", "Bar2", "Bar3");
    }
}

$foo = new foo();
$name = "MyName";

echo <<<EOT
My name is "$name". I am printing some $foo->foo.
Now, I am printing some {$foo->bar[1]}.
This should print a capital "A": \x41
EOT;
?>

/*
* My name is "MyName". I am printing some Foo.
* Now, I am printing some Bar2.
* This should print a capital "A": A
*/
```

FYR: [PHP Document - 文字列 - ヒアドキュメント](https://www.php.net/manual/ja/language.types.string.php#language.types.string.syntax.heredoc)

#### Variable Scope

- [Local variables](https://www.tutorialspoint.com/php/php_local_variables.htm): a variable declared in a function is considered local.
- [Function variables](https://www.tutorialspoint.com/php/php_function_parameters.htm): declared after the function name and inside parentheses.
- [Global variables](https://www.tutorialspoint.com/php/php_global_variables.htm): a global variable can be accessed in any part of the program by placing the keyword **GLOBAL** in front of the variable
- [Static variables](https://www.tutorialspoint.com/php/php_static_variables.htm): a static variable will not lose its value when the function exits by placing the keyword **STATIC** in front of the variable name

#### Variable Naming

- must begin with a letter or underscore.
- can only consist of numbers, letters, underscores

### Constants Types

A note taking from [PHP Tutorial - Constants Types](https://www.tutorialspoint.com/php/php_constants.htm)

Only scalar data (boolean, integer, float and string) can be contained in constants.

By convention, constant identifiers are **always uppercase**. A constant name starts with a letter or underscore, followed by any number of letters, numbers, or underscores.

```php
<?php
    // define value of constant MINSIZE
    define("MINSIZE", 50);

    // access value of constant MINSIZE
    echo MINSIZE;
    echo constant("MINSIZE");
```

#### PHP Magic Constants

- `__LINE__`: the current line number of the file
- `__FILE__`: the full path and filename of the file
- `__CLASS__`, `__FUNCTION__`, `__METHOD__`: the name of current class / function / method

### Operator Types, Decision Making, Loop Types

Skipped: [PHP Tutorial - Operator Types](https://www.tutorialspoint.com/php/php_operator_types.htm)<br>
Skipped: [PHP Tutorial - Decision Making](https://www.tutorialspoint.com/php/php_decision_making.htm)<br>
Skipped: [PHP Tutorial - Loop Types](https://www.tutorialspoint.com/php/php_loop_types.htm)

### Arrays, Strings

Skipped: [PHP Tutorial - Arrays](https://www.tutorialspoint.com/php/php_arrays.htm)<br>
Skipped: [PHP Tutorial - Strings](https://www.tutorialspoint.com/php/php_strings.htm)

### Web Concepts

A note taking from [PHP Tutorial - Web Concepts](https://www.tutorialspoint.com/php/php_web_concepts.htm)

#### Environment variables

> PHP creates environment variables that can be seen in the **phpinfo.php** page.
> Function `getenv()` is provided to access the value of all the environment variables.

Example: Use **HTTP_USER_AGENT** to identify user"s browser and operating system

```php
<html>

<body>
    <?php
    function getBrowser()
    {
        $u_agent = $_SERVER["HTTP_USER_AGENT"];
        $bname = "Unknown";
        $platform = "Unknown";
        $version = "";

        // Get user platform
        if (preg_match("/linux/i", $u_agent)) {
            $platform = "linux";
        } elseif (preg_match("/macintosh|mac os x/i", $u_agent)) {
            $platform = "mac";
        } elseif (preg_match("/windows|win32/i", $u_agent)) {
            $platform = "windows";
        }

        // Get the name of user agent
        if (preg_match("/MSIE/i", $u_agent) && !preg_match("/Opera/i", $u_agent)) {
            $bname = "Internet Explorer";
            $ub = "MSIE";
        } elseif (preg_match("/Firefox/i", $u_agent)) {
            $bname = "Mozilla Firefox";
            $ub = "Firefox";
        } elseif (preg_match("/Chrome/i", $u_agent)) {
            $bname = "Google Chrome";
            $ub = "Chrome";
        } elseif (preg_match("/Safari/i", $u_agent)) {
            $bname = "Apple Safari";
            $ub = "Safari";
        } elseif (preg_match("/Opera/i", $u_agent)) {
            $bname = "Opera";
            $ub = "Opera";
        } elseif (preg_match("/Netscape/i", $u_agent)) {
            $bname = "Netscape";
            $ub = "Netscape";
        }

        // Get the version number
        $known = array("Version", $ub, "other");
        $pattern = "#(?<browser>" . join("|", $known) . ")[/ ]+(?<version>[0-9.|a-zA-Z.]*)#";

        if (!preg_match_all($pattern, $u_agent, $matches)) {
            // No matching ...
        }
        
        $i = count($matches["browser"]);

        if ($i != 1) {
            if (strripos($u_agent, "Version") < strripos($u_agent, $ub)) {
                $version = $matches["version"][0];
            } else {
                $version = $matches["version"][1];
            }
        } else {
            $version = $matches["version"][0];
        }
        
        if ($version == null || $version == "") {
            $version = "?";
        }
        return array(
            "userAgent" => $u_agent,
            "name"      => $bname,
            "version"   => $version,
            "platform"  => $platform,
            "pattern"   => $pattern
        );
    }

    $ua = getBrowser();
    $yourbrowser = "Your browser: " . $ua["name"] . " " . $ua["version"] .
        " on " . $ua["platform"] . " reports: <br >" . $ua["userAgent"];

    print_r($yourbrowser);
    ?>

</body>

</html>
```

Example: Browser Redirection
```php
<?php
   if( $_POST["location"] ) {
      $location = $_POST["location"];
      header( "Location:$location" );
      
      // header() function supplies raw HTTP headers to the browser 
      // and can be used to redirect it to another location

      exit();

      // exit() function is used to halt parsing of rest of the code
   }
?>
<html>
   <body>
   
      <p>Choose a site to visit :</p>
      
      <form action = "<?php $_SERVER["PHP_SELF"] ?>" method ="POST">
         <select name = "location">.
         
            <option value = "http://www.tutorialspoint.com">
               Tutorialspoint.com
            </option>
         
            <option value = "http://www.google.com">
               Google Search Page
            </option>
         
         </select>
         <input type = "submit" />
      </form>
      
   </body>
</html>
```

### GET and POST
A note taking from [PHP Tutorial - GET and POST](https://www.tutorialspoint.com/php/php_get_post.htm)

GET Method:
> GET method sends the encoded user information appended to the page request by URL encoding.
> PHP provides `$_GET` associative array to access all the sent information using GET method.
> -- [PHP Tutorial - GET and POST](https://www.tutorialspoint.com/php/php_get_post.htm)

POST Method:
> POST method transfers information via HTTP headers. 
> PHP provides `$_POST` associative array to access all the sent information using POST method.
> -- [PHP Tutorial - GET and POST](https://www.tutorialspoint.com/php/php_get_post.htm)

### File Inclusion
A note taking from [PHP Tutorial - File Inclusion](https://www.tutorialspoint.com/php/php_file_inclusion.htm)

#### include()
> The include() function takes all the text in a specified file and copies it into the file.
> A warning would be noticed if there is any problem while including a file, but the execution will be continued.
> -- [PHP Tutorial - File Inclusion](https://www.tutorialspoint.com/php/php_file_inclusion.htm)

#### require()
> The include() function takes all the text in a specified file and copies it into the file.
> A fatal error would be generated if there is any problem while including a file and the execution will be halted.
> It is recommended to use the `require()` function, because scripts should not continue executing if files are missing or misnamed.
> -- [PHP Tutorial - File Inclusion](https://www.tutorialspoint.com/php/php_file_inclusion.htm)

FYR:
- [PHPのuse, require, includeの違い](https://sussan-po.com/2017/08/25/php-use-require-include/)
- [【PHP超入門】名前空間（namespace・use）について](https://qiita.com/7968/items/1e5c61128fa495358c1f)
- [PHP Document - Using namespaces: Aliasing/Importing](https://www.php.net/manual/en/language.namespaces.importing.php)

### Files & I/O
A note taking from [PHP Tutorial - Files & I/O](https://www.tutorialspoint.com/php/php_files.htm)

#### Opening and Closing Files
`fopen(<file-name>, <open-mode>)` function is used to open a file.

File open-modes:
- r: reading only / file pointer: beginning 
- r+: reading and writing / file pointer: beginning 
- w: writing only / create a file while file not exists / file pointer: beginning 
- w+: reading and writing / create a file while file not exists / file pointer: beginning 
- a: writing only / create a file while file not exists / file pointer: end
- a+: reading and writing / create a file while file not exists / file pointer: end

> If file opening fails, **fopen** returns `false` otherwise it returns a `file pointer`.
> Close the file by **fclose()** after making changes. **fclose()** requires a file pointer as a argument and returns a boolean to indicate if closure succeeds.
> -- [PHP Tutorial - Files & I/O](https://www.tutorialspoint.com/php/php_files.htm)

#### Reading a file
- Open a file by **fopen()**
- Get the length of the file by **filesize()**
- Read the file content by **fread(\<file-pointer\>, \<file-length\>)**
- Close the file by **fclose()**

```php
$filename = "tmp.txt";
$file = fopen( $filename, "r" );

if( $file == false ) {
   echo "Error in opening file";
   exit();
}

$filesize = filesize( $filename );
$filetext = fread( $file, $filesize );
fclose( $file );

echo "File size : $filesize bytes";
echo "<pre>$filetext</pre>";
```

#### Writing a file
Write content to a file by **fwrite(\<file-pointer\>, \<content-to-be-written\>)**. 

```php
$filename = "/home/user/guest/newfile.txt";
$file = fopen( $filename, "w" );

if( $file == false ) {
   echo "Error in opening new file";
   exit();
}

fwrite( $file, "This is  a simple test\n" );
fclose( $file );
```

### Functions
A note taking from [PHP Tutorial - Functions](https://www.tutorialspoint.com/php/php_functions.htm)

#### Dynamic Function Calls
> It is possible to assign function names as strings to variables.
> -- [PHP Tutorial - Functions](https://www.tutorialspoint.com/php/php_functions.htm)

```php
function sayHello() {
   echo "Hello<br />";
}

$function_holder = "sayHello";
$function_holder();
```

### Cookies
A note taking from [PHP Tutorial - Cookies](https://www.tutorialspoint.com/php/php_cookies.htm)

> Cookies are text files stored on the client computer and they are kept of use tracking purpose.
> -- [PHP Tutorial - Cookies](https://www.tutorialspoint.com/php/php_cookies.htm)

Cookies are usually set in Http headers: (Server -> Client)
```
HTTP/1.1 200 OK
Date: Fri, 04 Feb 2000 21:03:38 GMT
Server: Apache/1.3.9 (UNIX) PHP/4.0b3
Set-Cookie: name=xyz; expires=Friday, 04-Feb-07 22:03:38 GMT; 
            path=/; domain=tutorialspoint.com
Connection: close
Content-Type: text/html
```

If the user points the browser at any page that matches the path and domain of the cookie, 
it will resend the cookie to the server: (Client -> Server)
```
GET / HTTP/1.0
Connection: Keep-Alive
User-Agent: Mozilla/4.6 (X11; I; Linux 2.2.6-15apmac ppc)
Host: zink.demon.co.uk:1126
Accept: image/gif, */*
Accept-Encoding: gzip
Accept-Language: en
Accept-Charset: iso-8859-1,*,utf-8
Cookie: name=xyz
```

#### Setting Cookies
```php
setcookie(name, value, expire, path, domain, security);
```

Arguments:
- name: used while accessing cookies and stored in environment variable **HTTP_COOKIE_VARS**
- value
- expiry: specifies future time in seconds. If expiry is not set, cookie will expire when browser is closed
- path: specifies the firectories where the cookie is valid
- domain: only valid for the host which created the cookies
- security: 0 - should be sent by HTTP / 1 - should be sent by HTTPS

```php
setcookie("name", "John Watkin", time()+3600, "/","", 0);
setcookie("age", "36", time()+3600, "/", "",  0);
```

#### Accessing Cookies
```php
echo $_COOKIE["name"]."<br />";

// equivalent to:
echo $HTTP_COOKIE_VARS["name"]. "<br />";
echo $_COOKIE["age"]."<br />";

echo $HTTP_COOKIE_VARS["age"]."<br />";

// use isset() function to check if a cookie is set or not
if( isset($_COOKIE["name"]) ) {
   echo "Welcome ".$_COOKIE["name"]."<br />";
}
```

#### Deleting Cookies
```php
setcookie( "name", "", time()- 60, "/","", 0);
setcookie( "age", "", time()- 60, "/","", 0);
```

### Sessions
A note taking from [PHP Tutorial - Sessions](https://www.tutorialspoint.com/php/php_sessions.htm)

> Sessions make data accessible across the various pages of an entire website.
> A session creates a file in a temporary directory on the server where registered session variables and their values are stored.
> The location of the temporary file is determined by a setting in the `php.ini` file called **session.save_path**.
> -- [PHP Tutorial - Sessions](https://www.tutorialspoint.com/php/php_sessions.htm)

Session workflow:
- PHP creates a unique identifier (random string of 32 hexadecimal numbers) for the session
- **PHPSESSID Cookie** is sent to client
- A temporary file is created on the server

#### Startiing a Session
**session_start()**: check if a session is already started and if not, it will start a new one.

```php
session_start();

if( isset( $_SESSION["counter"] ) ) {
   $_SESSION["counter"] += 1;
} else {
   $_SESSION["counter"] = 1;
}

$msg = "You have visited this page ". $_SESSION["counter"]."in this session.";
```

Auto Session:  set `session.auto_start` variable to **1** in **php.ini** file

#### Delete a Session
```php
unset($_SESSION["counter"]);

// destroy all sessions
session_destroy();
```

### Sending Emails
A note taking from [PHP Tutorial - Sending Emails](https://www.tutorialspoint.com/php/php_sending_emails.htm)

Configuration in **php.ini**.
- SMTP
- send_from
 
```
[mail function]
; For Win32 only.
SMTP = smtp.secureserver.net

; For win32 only
sendmail_from = webmaster@tutorialspoint.com

; For Unix only
sendmail_path = /usr/sbin/sendmail -t -i
```

#### Sending Plain Text Eail
```php
mail( to, subject, message, **headers, **parameters );
// Returns true if successful or false if it is failed.
```

#### Sending HTML Email
Specify `Mime version`, `content type` and `character set` to send an HTML email.

```php
<html>
   <head>
      <title>Sending HTML email using PHP</title>
   </head>
   
   <body>
      
      <?php
         $to = "xyz@somedomain.com";
         $subject = "This is subject";
         
         $message = "<b>This is HTML message.</b>";
         $message .= "<h1>This is headline.</h1>";
         
         $header = "From:abc@somedomain.com \r\n";
         $header .= "Cc:afgh@somedomain.com \r\n";
         $header .= "MIME-Version: 1.0\r\n";
         $header .= "Content-type: text/html\r\n";
         
         $retval = mail ($to,$subject,$message,$header);
         
         if( $retval == true ) {
            echo "Message sent successfully...";
         }else {
            echo "Message could not be sent...";
         }
      ?>
      
   </body>
</html>
```

#### Sending Email with Attachment
Set **Content-type** header to **multipart/mixed**.

```php
$from = $_REQUEST["from"];
$email = $_REQUEST["email"];
$file = $_REQUEST["file"];
   
   if ($file) {
      function mail_attachment ($from , $to, $subject, $message, $attachment){
         $fileatt = $attachment; // Path to the file
         $fileatt_type = "application/octet-stream"; // File Type 
         
         $start = strrpos($attachment, "/") == -1 ? 
            strrpos($attachment, "//") : strrpos($attachment, "/")+1;
				
         $fileatt_name = substr($attachment, $start, strlen($attachment));
         
         $email_from = $from;
         $subject = "New Attachment Message";
         
         $email_subject =  $subject;
         $email_txt = $message;
         $email_to = $to;
         
         $headers = "From: ".$email_from;
         $file = fopen($fileatt,"rb"); 
         $data = fread($file,filesize($fileatt)); 
         fclose($file); 
         
         $msg_txt="\n\n You have recieved a new attachment message from $from";
         $semi_rand = md5(time()); 
         $mime_boundary = "==Multipart_Boundary_x{$semi_rand}x"; 
         $headers .= "\nMIME-Version: 1.0\n" . "Content-Type: multipart/mixed;\n" . "
            boundary=\"{$mime_boundary}\"";
         
         $email_txt .= $msg_txt;
			
         $email_message .= <<< MSG
         This is a multi-part message in MIME format.
         
         
         --{$mime_boundary}
         
         Content-Type:text/html;charset = \"iso-8859-1\"
         Content-Transfer-Encoding: 7bit
         
         
         $email_txt
         MSG;
				
         $data = chunk_split(base64_encode($data));
         
         $email_message .= <<<MSG2
         --{$mime_boundary}
         Content-Type: {$fileatt_type};
         name = \"{$fileatt_name}\"
         Content-Disposition: attachment;
         filename = \"{$fileatt_name}\"
         Content-Transfer-Encoding: base64
         
         {$data}
         
         --{$mime_boundary}--
         
         MSG2;
         
         $ok = mail($email_to, $email_subject, $email_message, $headers);
         
         if($ok) {
            echo "File Sent Successfully.";
            unlink($attachment); // delete a file after attachment sent.
         } else {
            die("Sorry but the email could not be sent. Please go back and try again!");
         }
      }
      move_uploaded_file($_FILES["filea"]["tmp_name"],
                         "temp/".basename($_FILES["filea"]["name"]) );
			
      mail_attachment("$from", "youremailaddress@gmail.com", 
                      "subject", "message", ("temp/".$_FILES["filea"]["name"]));
   }
```

### File Uploading
A note taking from [PHP Tutorial - File Uploading](https://www.tutorialspoint.com/php/php_file_uploading.htm)

> Information in **phpinfo.php** describes the temporary directory used for file uploads as **upload_tmp_dir** and the maximum permitted size of files that can be uploaded is stated as **upload_max_filesize**. 
> These parameters are set in configuration file **php.ini**.
> -- [PHP Tutorial - File Uploading](https://www.tutorialspoint.com/php/php_file_uploading.htm)

Example: File Upload Form
```php
<?php
   if(isset($_FILES["image"])){
      $errors= array();
      $file_name = $_FILES["image"]["name"];
      $file_size =$_FILES["image"]["size"];
      $file_tmp =$_FILES["image"]["tmp_name"];
      $file_type=$_FILES["image"]["type"];
      $file_ext=strtolower(end(explode(".",$_FILES["image"]["name"])));
      
      $extensions= array("jpeg","jpg","png");
      
      if(in_array($file_ext,$extensions) === false){
         $errors[]="extension not allowed, please choose a JPEG or PNG file.";
      }
      
      if($file_size > 2097152){
         $errors[]="File size must be excately 2 MB";
      }
      
      if(empty($errors) == true){
         move_uploaded_file($file_tmp, "images/".$file_name);
         echo "Success";
      }else{
         print_r($errors);
      }
   }
?>
<html>
   <body>
      
      <form action="" method="POST" enctype="multipart/form-data">
         <input type="file" name="image" />
         <input type="submit"/>
      </form>
      
   </body>
</html>
```

### Coding Standard
A note taking from [PHP Tutorial - Coding Standard](https://www.tutorialspoint.com/php/php_coding_standard.htm)

- PHP Code Tags: Always use `<?php ?>` to delimit PHP code
- Indenting and Line Length: Use an indent of 4 spaces
- Control Structures: Should have one space between the control keyword and opening parenthesis to distinguish them from function calls.
- Function Definitions: Follow the `BSD/Allman style`. ([字下げスタイル](https://ja.wikipedia.org/wiki/%E5%AD%97%E4%B8%8B%E3%81%92%E3%82%B9%E3%82%BF%E3%82%A4%E3%83%AB#BSD/%E3%82%AA%E3%83%BC%E3%83%AB%E3%83%9E%E3%83%B3%E3%81%AE%E3%82%B9%E3%82%BF%E3%82%A4%E3%83%AB))
- Function Calls: With no spaces between the function name, the opening parenthesis, and the first parameter.
  Spaces between commas and each parameter, and no space between the last parameter, the closing parenthesis, and the semicolon.
- Comments: C style comments (/* */) and standard C++ comments (//) are both fine. Use of Perl/shell style comments (#) is discouraged.
- Variable Names:
    - All lower case letters
    - Use underline `_` as word separator
    - Global variables should be prepended with `g` and all capital letters with `_` separator
    - Static variables should be prepended with `s`
    - Make functions reentrant
    - Alignment of declaration blocks
    - One statament per line
    - Short functions / methods

FYR: [実は大切！PHPの命名規則を作って、開発効率を向上させよう](https://www.sejuku.net/blog/104248)

### Predefined Variables
A note taking from [PHP Tutorial - Predefined Variables](https://www.tutorialspoint.com/php/php_predefined_variables.htm)

#### Superglobals
<table>
   <tr>
      <th>Variable</th>
      <th>Description</th>
   </tr>
   <tr>
      <td>$GLOBALS</td>
      <td>Contains a reference to every available variable</td>
   </tr>
   <tr>
      <td>$_SERVER</td>
      <td>An array containing information such as headers, paths, and script locations created by the web server</td>
   </tr>
   <tr>
      <td>$_GET</td>
      <td>Variables passed to the current script via the HTTP GET method</td>
   </tr>
   <tr>
      <td>$_POST</td>
      <td>Variables passed to the current script via the HTTP POST method</td>
   </tr>
   <tr>
      <td>$_FILES</td>
      <td>An array of items uploaded to the current script</td>
   </tr>
   <tr>
      <td>$_REQUEST</td>
      <td>An array consisting of the contents of $_GET, $_POST, and $_COOKIE</td>
   </tr>
   <tr>
      <td>$_COOKIE</td>
      <td>An array of variables passed to the current script via HTTP cookies</td>
   </tr>
   <tr>
      <td>$_SESSION</td>
      <td>An array containing session variables available</td>
   </tr>
   <tr>
      <td>$_PHP_SELF</td>
      <td>A string containing PHP script file name in which it is called</td>
   </tr>
   <tr>
      <td>$php_errormsg</td>
      <td>A variable containing the text of the last error message generated by PHP</td>
   </tr>
</table>

### Regular Expressions 正規表現（せいきひょうげん）
Skipped: [PHP Tutorial - Regular Expressions](https://www.tutorialspoint.com/php/php_regular_expression.htm)

### Error & Exception Handling
A note taking from [PHP Tutorial - Error & Exception Handling](https://www.tutorialspoint.com/php/php_error_handling.htm)

#### die() function
Example: Open a Text File
```php
if(!file_exists("/tmp/test.txt")) {
   die("File not found");
} else {
   $file = fopen("/tmp/test.txt","r");
   print "Opend file sucessfully";
}
```

#### Defining Custom Error Handling Function
```php
error_function(error_level:int, error_message:string, **error_file, **error_line, **error_context);
```

Possible Error levels:
- 1: E_ERROR - Fatal run-time errors. Execution of the script is halted
- 2: E_WARNING - Non-fatal run-time errors. Execution of the script is not halted
- 4: E_PARSE - Compile-time parse errors.
- 8: E_NOTICE - Run-time notices.
- 16: E_CORE_ERROR - Fatal errors that occur during PHP"s initial start-up.
- 32: E_CORE_WARNING
- 256: E_USER_ERROR
- 512: E_USER_WARNING
- 1024: E_USER_NOTICE
- 2048: E_STRICT
- 4096: E_RECOVERABLE_ERROR
- 8191: E_ALL

#### Exceptions Handling
- Try: A function using an exception should be in a "try" block. 
       If the exception does not trigger, the code will continue as normal. 
- Throw: An exception triggered.
- Catch: Retrieves an exception and creates an object containing the exception information.

```php
try {
   $error = "Always throw this error";
   throw new Exception($error);
   
   // Code following an exception is not executed.
   echo "Never executed";
}catch (Exception $e) {
   echo "Caught exception: ".  $e->getMessage(). "\n";
}

// Continue execution
echo "Hello World";
```

Functions which can be used from Exception class:
- getMessage(): message of exception
- getCode(): code of exception
- getFile(): source filename
- getLine(): source line
- getTrace(): an array of the backtrace()
- getTraceAsString(): formated string of trace

#### Creating Custom Exception Handler
```php
string set_exception_handler ( callback $exception_handler )
```

Example:
```php
function exception_handler($exception) {
   echo "Uncaught exception: " , $exception->getMessage(), "\n";
}

set_exception_handler("exception_handler");
throw new Exception("Uncaught Exception");

echo "Not Executed\n";
```

### Bugs Debugging
Skipped: [PHP Tutorial - Bugs Debugging](https://www.tutorialspoint.com/php/php_bugs_debugging.htm)

### Date & Time
A note taking from [PHP Tutorial - Date & Time](https://www.tutorialspoint.com/php/php_date_and_time.htm)

#### Get Time Stamp with time()
```php
print time();
// returns an integer
// representing the number of seconds elapsed
// since GMT January 1, 1970
// (UNIX epoch)
```

#### Convert Time Stamp with getdate()
> The function getdate() optionally accepts a time stamp and returns an associative array containing information about the date.

Available array keys:
- seconds <integer>
- minutes <integer>
- hours <integer>
- mday <integer>
- wday <integer>: Day of the week 0-6
- mon <integer>
- year <integer>: 4 digits
- yday <integer>: Day of the year
- month <string>
- 0: Time Stamp

#### Convert Time Stamp with date()
```php
print date("m/d/y G:i:s", time());
// 11/13/20 6:43:08

print date("j of F Y, \a\\t g.i a", time());
// 5 2016f December 2016, at 9:29 am
```

### PHP & MySQL
A note taking from [PHP Tutorial - PHP & MySQL](https://www.tutorialspoint.com/php/php_and_mysql.htm)

#### MySQL Database Connection
A note taking from [PHP Tutorial - MySQL Database Connection](https://www.tutorialspoint.com/php/connect_to_mysql_using_php.htm)

```php
/*
 * Method mysql_connect()
 * Arguments:
 *     server - default value: lacalhost:3306
 *     user - default value: user that owns the server process
 *     passwd - empty
 *     new_link
 *     client_flags - MYSQL_CLIENT_SSL / MYSQL_CLIENT_COMPRESS / MYSQL_CLIENT_IGNORE_SPACE / MYSQL_CLIENT_INTERACTIVE
 * Return:
 *     success: MySQL link identifier
 *     failure: false
 */
connection mysql_connect(server, user, passwd, new_link, client_flag);
```

The infos of server, user and passwd can be specified in **php.ini**.

Closing Database Connection
```php
bool mysql_close(resource $link_identifier);
```

Example: MySQL Database connection:
```php
$dbhost = "localhost:3036";
$dbuser = "guest";
$dbpass = "guest123";
$conn = mysql_connect($dbhost, $dbuser, $dbpass);

if(! $conn ) {
    die("Could not connect: " . mysql_error());
}

echo "Connected successfully";
mysql_close($conn);
```

#### Manipulating MySQL Database Using PHP
A note taking from 
- [Create MySQL Database Using PHP](https://www.tutorialspoint.com/php/create_mysql_database_using_php.htm)
- [Insert Data into MySQL Database](https://www.tutorialspoint.com/php/mysql_insert_php.htm)
- [Getting Data From MySQL Database](https://www.tutorialspoint.com/php/mysql_select_php.htm)
- [Updating Data into MySQL Database](https://www.tutorialspoint.com/php/mysql_update_php.htm)
- [Deleting Data from MySQL Database](https://www.tutorialspoint.com/php/mysql_delete_php.htm)

```php
// Creating a database
bool mysql_query(sql, connection);

// Selecting a database
bool mysql_select_db(db_name, connection);
```

Example: Manipulating DataBase with PHP
```php
$dbhost = "localhost:3036";
$dbuser = "root";
$dbpass = "rootpassword";
$conn = mysql_connect($dbhost, $dbuser, $dbpass);

if(! $conn ) {
    die("Could not connect: " . mysql_error());
}

echo "Connected successfully";

/* Create employee Table */
$sql = "CREATE TABLE employee( ".
       "emp_id INT NOT NULL AUTO_INCREMENT, ".
       "emp_name VARCHAR(20) NOT NULL, ".
       "emp_address  VARCHAR(20) NOT NULL, ".
       "emp_salary   INT NOT NULL, ".
       "join_date    timestamp(14) NOT NULL, ".
       "primary key ( emp_id ))";

mysql_select_db("test_db");
$retval = mysql_query( $sql, $conn );

if(! $retval ) {
    die("Could not create table: " . mysql_error());
}

echo "Table employee created successfully\n";

/* Insert Data into employee Table */
$sql = "INSERT INTO employee ".
       "(emp_name,emp_address, emp_salary, join_date) ".
       "VALUES ( "guest", "XYZ", 2000, NOW() )";
   
mysql_select_db("test_db");
$retval = mysql_query( $sql, $conn );

if(! $retval ) {
   die("Could not enter data: " . mysql_error());
}

echo "Entered data successfully\n";

/* Fetch Data from employee Table */
$sql = "SELECT emp_id, emp_name, emp_salary FROM employee";

mysql_select_db("test_db");
$retval = mysql_query( $sql, $conn );
   
if(! $retval ) {
    die("Could not get data: " . mysql_error());
}

while($row = mysql_fetch_array($retval, MYSQL_ASSOC)) {
    echo "EMP ID :{$row["emp_id"]}  <br> ".
         "EMP NAME : {$row["emp_name"]} <br> ".
         "EMP SALARY : {$row["emp_salary"]} <br> ".
         "--------------------------------<br>";
}

// mysql_fetch_array($retval, MYSQL_ASSOC)
// is equivalent to
// mysql_fetch_assoc($retval)

// mysql_fetch_array($retval, MYSQL_NUM)
// return an array with numeric index

echo "Fetched data successfully\n";

mysql_free_result($retval);
echo "Fetched data successfully\n";
// A good practice to release cursor memory at the end of each SELECT statement

/* Update Data from employee Table */
$sql = "UPDATE employee ". 
       "SET emp_salary = "200000" ".
       "WHERE emp_id = 1" ;

mysql_select_db("test_db");
$retval = mysql_query( $sql, $conn );

if(! $retval ) {
    die("Could not update data: " . mysql_error());
}
echo "Updated data successfully\n";

/* Delete Data from employee Table */
$sql = "DELETE FROM employee WHERE emp_id = 1";

mysql_select_db("test_db");
$retval = mysql_query( $sql, $conn );

if(! $retval ) {
    die("Could not delete data: " . mysql_error());
}

echo "Deleted data successfully\n";

mysql_close($conn);
```

Pagination: [Using Paging through PHP](https://www.tutorialspoint.com/php/mysql_paging_php.htm)

#### Delete Database Tables 
A note taking from [Deleting a Database](https://www.tutorialspoint.com/php/delete_mysql_database_using_php.htm)

```php
$dbhost = "localhost:3036";
$dbuser = "root";
$dbpass = "rootpassword";
$conn = mysql_connect($dbhost, $dbuser, $dbpass);

if(! $conn ) {
    die("Could not connect: " . mysql_error());
}

// Drop Database
$sql = "DROP DATABASE test_db";
$retval = mysql_query( $sql, $conn );

if(! $retval ) {
    die("Could not delete database db_test: " . mysql_error());
}

echo "Database deleted successfully\n";

// Drop Data Table
$sql = "DROP TABLE employee";
$retval = mysql_query( $sql, $conn );

if(! $retval ) {
    die("Could not delete table employee: " . mysql_error());
}

echo "Table deleted successfully\n";
mysql_close($conn);
```

#### MySQL backup using PHP
A note taking from [Perform MySQL backup using PHP](https://www.tutorialspoint.com/php/perform_mysql_backup_php.htm)

3 ways to backup MySQL database
- SQL Command through PHP
- MySQL binary `mysqldump` through PHP
- phpMyAdmin: use the export function

##### By SQL Command
```php
$dbhost = "localhost:3036";
$dbuser = "root";
$dbpass = "rootpassword";

$conn = mysql_connect($dbhost, $dbuser, $dbpass);

if(! $conn ) {
    die("Could not connect: " . mysql_error());
}

$table_name = "employee";
$backup_file  = "/tmp/employee.sql";
$sql = "SELECT * INTO OUTFILE "$backup_file" FROM $table_name";

mysql_select_db("test_db");
$retval = mysql_query( $sql, $conn );

if(! $retval ) {
    die("Could not take data backup: " . mysql_error());
}

echo "Backedup  data successfully\n";

mysql_close($conn);
```

##### By mysqldump
```php
$dbhost = "localhost:3036";
$dbuser = "root";
$dbpass = "rootpassword";

$backup_file = $dbname . date("Y-m-d-H-i-s") . ".gz";
$command = "mysqldump --opt -h $dbhost -u $dbuser -p $dbpass ". "test_db | gzip > $backup_file";

system($command);
```

### PHP & AJAX
Skipped: [PHP Tutorial - PHP & AJAX](https://www.tutorialspoint.com/php/php_and_ajax.htm)

### PHP & XML
A note taking from [PHP Tutorial - PHP & XML](https://www.tutorialspoint.com/php/php_and_xml.htm)

Parsing an XML Document with **SimpleXML** module by PHP. (Supported by PHP above version 5)
```php
<?php
    $note=<<<XML
    
    <note>
    <to>Gopal K Verma</to>
    <from>Sairamkrishna</from>
    <heading>Project submission</heading>
    <body>Please see clearly </body>
    </note>
    
    XML;
    // use function simplexml_load_file(filename) if you have an XML file
    $xml=simplexml_load_string($note);
    print_r($xml);
?>
```

### Object Oriented Programming in PHP
Skipped: [PHP Tutorial - Object Oriented Programming in PHP](https://www.tutorialspoint.com/php/php_object_oriented.htm)

Property Scope:
- public
- private
- protected: only accessible by class itself or child class

Method Scope:
- public
- private
- protected
- abstract: define in parent class and implement by child class
- final: can not be overridden
- static: is accessible without instatiation

FYR: [Abstract Class vs Interface](https://codeinphp.github.io/post/abstract-class-vs-interface/)

### PHP - Function References
A note taking from [PHP Tutorial - Function References](https://www.tutorialspoint.com/php/php_function_reference.htm)

- [Array functions](https://www.tutorialspoint.com/php/php_array_functions.htm)
- [Calendar Functions](https://www.tutorialspoint.com/php/php_calendar_functions.htm)
- [Class/Object Functions](https://www.tutorialspoint.com/php/php_class_object_functions.htm)
- [Character Functions](https://www.tutorialspoint.com/php/php_character_functions.htm)
- [Date & Time Functions](https://www.tutorialspoint.com/php/php_date_time_functions.htm)
- [Directory Functions](https://www.tutorialspoint.com/php/php_directory_functions.htm)
- [Error & Logging Functions](https://www.tutorialspoint.com/php/php_error_handling_functions.htm)
- [Filesystem Functions](https://www.tutorialspoint.com/php/php_file_system_functions.htm)
- [Hash Functions](https://www.tutorialspoint.com/php/php_hash_functions.htm)
- [MySQLi Functions](https://www.tutorialspoint.com/php/php_mysql_functions.htm)
- [Network Functions](https://www.tutorialspoint.com/php/php_network_functions.htm)
- [ODBC Function](https://www.tutorialspoint.com/php/php_odbc_functions.htm): Open DataBase Connectivity is an Application Programming Interface (API) that allows a programmer to abstract a program from a database
- [Statistics Module](https://www.tutorialspoint.com/php/php_statistics_module.htm)
- [String Functions](https://www.tutorialspoint.com/php/php_string_functions.htm)
- [SimpleXML Functions](https://www.tutorialspoint.com/php/php_xml_functions.htm)
- [XML Parsing Functions](https://www.tutorialspoint.com/php/php_xml_parsing_functions.htm)
- [OpenSSL Functions](https://www.tutorialspoint.com/php/php_openssl_functions.htm)
