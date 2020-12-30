---
layout: post
title: "Reading: Beginning PHP Part1 03"
date: 2020-11-01
categories: [PHP]
---

Notes taking from: Beginning PHP and MySQL From Novice to Professional<br>
Covers:
- Ch8 Error and Exception Handling
- Ch9 Strings and Regular Expressions

## CH8 Error and Exception Handling

### Configuring PHP’s Error Reporting Behavior
<img src="/images/Beginning PHP.assets/Table 8-1.png" alt="Table 8-1" />

```php
error_reporting = E_ALL
ini_set('error_reporting', E_ALL);
error_reporting(E_ALL);
```

### Displaying Errors Within the Browser
Enabling the display_errors directive results in the display of any errors meeting
the criteria defined by error_reporting.

Be sure to enable the display_startup_errors directive, which will display any errors encountered during initialization of the PHP engine. Like display_errors, you’ll want to be certain display_startup_errors is disabled on your production server.

### Logging errors

`syslog` is a Linux-based logging facility that offers an API for logging messages pertinent to system and application execution. These files are found in **/var/log** on most Linux systems. The Windows event log is essentially the equivalent of the Linux syslog. These logs are commonly viewed using the Event Viewer.

```
[24-Apr-2014 09:47:59] PHP Parse error: syntax error, unexpected '}' in /Applications/MAMP/htdocs/5thedition/08/first.php on line 7
```

### Creating and Logging Custom Messages

```php
error_log("New user registered");
```

```
[24-Apr-2014 12:15:07] New user registered
```

Another example:

```php
error_log("New user registered", 3, "/var/log/users.log");
```

The second parameter sets the message type

> 0 = PHP’s logging system<br>
> 1 =  Send email<br>
> 2 = no logger<br>
> 3 = Append to a file<br>
> 4 = Use the SAPI logger

while the third parameter (`/var/log/users.log`) identifies the new log file.

### Exception Handling

```php
try {
  $fh = fopen("contacts.txt", "r"); 
  if (! $fh) {
    throw new Exception("Could not open the file!"); 
  }
} catch (Exception $e) {
  echo "Error (File: ".$e->getFile().", line ".
                      $e->getLine()."): ".$e->getMessage();
}
```

The finally block works in conjunction with the try and catch blocks, executing code that is always executed after the try and catch blocks. 

```php
$fh = fopen("contacts.txt", "r"); 
try {
  if (! fwrite($fh, "Adding a new contact")) {
    throw new Exception("Could not open the file!");
  }
} catch (Exception $e) {
  echo "Error (File: ".$e->getFile().", line ". $e->getLine()."): ".$e->getMessage();
} finally {
  fclose($fh);
}
```

### Extending the Exception Class

```php
class MyException extends Exception {
  function __construct($language, $errorcode) {
    $this->language = $language;
    $this->errorcode = $errorcode; 
  }
  function getMessageMap() {
    $errors = file("errors/{$this->language}.txt"); 
    foreach($errors as $error) {
      list($key,$value) = explode(",", $error, 2);
      $errorArray[$key] = $value; 
    }
    return $errorArray[$this->errorcode]; 
  }
}

try {
  throw new MyException("english", 4);
}
catch (MyException $e) {
  echo $e->getMessageMap(); 
}
```

### Catching Multiple Exceptions

```php
/* The InvalidEmailException class notifies the
administrator if an e-mail is deemed invalid. */ 
class InvalidEmailException extends Exception {
  function __construct($message, $email) { 
    $this->message = $message; 
    $this->notifyAdmin($email);
  }
  private function notifyAdmin($email) { 
    mail("admin@example.org","INVALID EMAIL",$email, "From:web@example.com");
  } 
}

/* The Subscribe class validates an e-mail address and adds the e-mail address to the database. */
class Subscribe {
  function validateEmail($email) {
    try {
      if ($email == "") {
        throw new Exception("You must enter an e-mail address!"); 
      } else {
        list($user,$domain) = explode("@", $email); 
        if (! checkdnsrr($domain, "MX"))
          throw new InvalidEmailException( "Invalid e-mail address!", $email);
        else
          return 1;
      }
    } catch (Exception $e) {
      echo $e->getMessage();
    } catch (InvalidEmailException $e) {
      echo $e->getMessage();
      $e->notifyAdmin($email); 
    }
  }
  
  /* Add the e-mail address to the database */ 
  function subscribeUser() {
    echo $this->email." added to the database!"; 
  }
}

// Assume that the e-mail address came from a subscription form 
$_POST['email'] = "someuser@example.com";
/* Attempt to validate and add address to database. */ 
if (isset($_POST['email'])) {
  $subscribe = new Subscribe(); 
  if($subscribe->validateEmail($_POST['email'])) {
    $subscribe->subscribeUser($_POST['email']);
  }
}
```

### Standard PHP Library Exceptions

The Standard PHP Library (SPL) extends PHP by offering ready-made solutions
to commonplace tasks such as file access, iteration of various sorts, and the implementation of data structures not natively supported by PHP.

- BadFunctionCallException
  The BadFunctionCallException class should be used to handle scenarios where an undefined method
   is called, or if an incorrect number of arguments are called in conjunction with a method.
- BadMethodCallException
  The BadMethodCallException class should be used to handle scenarios where an undefined method is called, or if an incorrect number of arguments are called in conjunction with a method.
- DomainException
  The DomainException class should be used to handle scenarios where an input value falls outside of a range. For instance, if a weight-loss application includes a method
   that is intended to save a user’s current weight to a database, and the supplied value is less than zero, an exception of type DomainException should be thrown.
- InvalidArgumentException
  The InvalidArgumentException class should be used to handle situations where an argument of an incompatible type is passed to a function or method.
- LengthException
  The LengthException class should be used to handle situations where a string’s length is invalid. For instance, if an application included a method that processed a user’s social security number, and a string was passed into the method that was not exactly nine characters in length, then an exception of type LengthException should be thrown.
- LogicException
  The LogicException class is one of the two base classes from which all other SPL exceptions extend (the other base class being the RuntimeException class). You should use the LogicException class to handle situations where an application is programmed incorrectly, such as when there is an attempt to invoke a method before a class attribute has been set.
- OutOfBoundsException
  The OutOfBoundsException class should be used to handle situations where a provided value does not match any of an array’s defined keys or where defined limits of any other data structure are exceeded and there isn’t a more suitable exception (e.g., LengthException for strings).
- OutOfRangeException
  The OutOfRangeException class should be used to handle the function’s output values that fall outside of a predefined range. This differs from DomainException in that DomainException should focus on input rather than output.
- OverflowException
  The OverflowException class should be used to handle situations where an arithmetic or buffer overflow occurs. For instance, you would trigger an overflow exception when attempting to add a value to an array of a predefined size.
- RangeException
  Defined in the documentation as the runtime version of the DomainException class, the RangeException class should be used to handle arithmetic errors unrelated to overflow and underflow.
- RuntimeException
  The RuntimeException class is one of the two base classes from which all other SPL exceptions extend (the other base class being LogicException class) and is intended to handle errors that only occur at runtime.
- UnderflowException
  The UnderflowException class should be used to handle situations where an arithmetic or buffer underflow occurs. For instance, you would trigger an underflow exception when attempting to remove a value from an empty array.
- UnexpectedValueException
  The UnexpectedValueException class should be used to handle situations where a provided value does not match any of a predefined set of values.

## CH9 Strings and Regular Expressions
- Regular expressions
  PHP supports the use of regular expressions to search strings for patterns or replace elements of a string with another value based on patterns. There are several types of regular expressions, and the one supported in PHP is called Pearl style regex or PCRE.
- String manipulation

### Modifiers
<img src="/images/Beginning PHP.assets/Table 9-1.png" alt="Table 9-1" />

### Metacharacters

\A: Matches only at the beginning of the string.<br>
\b: Matches a word boundary.<br>
\B: Matches anything but a word boundary.<br>
\d: Matches a digit character. This is the same as [0-9]. <br>
\D: Matches a nondigit character.<br>
\s: Matches a whitespace character.<br>
\S: Matches a nonwhitespace character.<br>
[]: Encloses a character class.<br>
(): Encloses a character grouping or defines a back reference or the start and end of a subpattern.<br>
$: Matches the end of a line.<br>
^: Matches the beginning of the string or beginning of every line in multiline mode.<br>
.: Matches any character except for the newline.<br>
\\: Quotes the next metacharacter.<br>
\w: Matches any string containing solely underscore and alphanumeric characters. This depends on the Locale. For U.S. English this is the same as [a-zA-Z0-9_].<br>
\W: Matches a string, omitting the underscore and alphanumeric characters.

### PHP’s Regular Expression Functions (Perl Compatible)

#### Searching for a Pattern
The `preg_match()` function searches a string for a specific pattern, returning TRUE if it exists and FALSE otherwise.

```php
$line = "vim is the greatest word processor ever created! Oh vim, how I love thee!";
if (preg_match("/\bVim\b/i", $line, $match)) print "Match found!";
```

#### Matching All Occurrences of a Pattern
The `preg_match_all()` function matches all occurrences of a pattern in a string, assigning each occurrence to an array in the order you specify via an optional input parameter. 

```php
$userinfo = "Name: <b>Zeev Suraski</b> <br> Title: <b>PHP Guru</b>"; 
preg_match_all("/<b>(.*)<\/b>/U", $userinfo, $pat_array);
printf("%s <br /> %s", $pat_array[0][0], $pat_array[0][1]);

/*
Zeev Suraski
PHP Guru
*/
```

#### Searching an Array
The `preg_grep()` function searches all elements of an array, returning an array consisting of all elements matching a certain pattern. 

```php
$foods = array("pasta", "steak", "fish", "potatoes"); 
$food = preg_grep("/^p/", $foods);
print_r($food);
// Array ( [0] => pasta [3] => potatoes )
```

#### Delimiting Special Regular Expression Characters
The function `preg_quote()` inserts a backslash delimiter before every character of special significance to a regular expression syntax. These special characters include `$ ^ * ( ) + = { } [ ] | \\ : < >`.

```php
$text = "Tickets for the fight are going for $500."; 
echo preg_quote($text);
// Tickets for the fight are going for \$500\.
```

#### Replacing All Occurrences of a Pattern
The `preg_replace()` function replaces all occurrences of pattern with replacement and returns the modified result.

```php
$text = "This is a link to http://www.wjgilmore.com/.";
echo preg_replace("/http:\/\/(.*)\//", "<a href=\"\${0}\">\${0}</a>", $text);
// This is a link to <a href="http://www.wjgilmore.com/">http://www.wjgilmore.com/</a>.
```

If you pass arrays as the pattern and replacement parameters, the function will cycle through each element of each array, making replacements as they are found.

```php
$draft = "In 2010 the company faced plummeting revenues and scandal."; 
$keywords = array("/faced/", "/plummeting/", "/scandal/"); 
$replacements = array("celebrated", "skyrocketing", "expansion");
echo preg_replace($keywords, $replacements, $draft);
// In 2010 the company celebrated skyrocketing revenues and expansion.
```

#### Creating a Custom Replacement Function

```php
// This function will add the acronym's long form // directly after any acronyms found in $matches 
function acronym($matches) {
  $acronyms = array(
    'WWW' => 'World Wide Web',
    'IRS' => 'Internal Revenue Service', 
    'PDF' => 'Portable Document Format'
  );
  if (isset($acronyms[$matches[1]]))
    return $acronyms[$matches[1]] . " (" . $matches[1] . ")";
  else {
    return $matches[1];
  }
}

// The target text
$text = "The <acronym>IRS</acronym> offers tax forms in <acronym>PDF</acronym> format on the <acronym>WWW</acronym>.";

// Add the acronyms' long forms to the target text
$newtext = preg_replace_callback("/<acronym>(.*)<\/acronym>/U", 'acronym', $text);
print_r($newtext);

// The Internal Revenue Service (IRS) offers tax forms in Portable Document Format (PDF) on the World Wide Web (WWW).
```

#### Splitting a String into Various Elements Based on a Case-Insensitive Pattern
The `preg_split()` function operates exactly like `explode()`, except that the pattern can also be defined in terms of a regular expression.

```php
$delimitedText = "Jason+++Gilmore+++++++++++Columbus+++OH"; 
$fields = preg_split("/\++/", $delimitedText); 
foreach($fields as $field) echo $field."<br />";

/*
Jason
Gilmore
Columbus
OH
*/
```

### Other String-Specific Functions

The section is divided into the following topics:

- Determining string length
- Comparing two strings
- Manipulating string case
- Converting strings to and from HTML 
- Alternatives for regular expression functions 
- Padding and stripping a string
- Counting characters and words

#### Determining the Length of a String
Determining string length is a repeated action within countless applications. The PHP function `strlen()` accomplishes this task quite nicely. 

```php
$pswd = "secretpswd"; 
if (strlen($pswd) < 10)
  echo "Password is too short!"; 
else
  echo "Password is valid!";
```

#### Comparing Two Strings

PHP provides four functions for performing this task: `strcmp()`, `strcasecmp()`, `strspn()`, and `strcspn()`.

##### Comparing Two Strings’ Case Sensitively
The `strcmp()` function performs a case-sensitive comparison of two strings.

It will return one of three possible values based on the comparison outcome:

- 0 if str1 and str2 are equal
- -1 if str1 is less than str2
- 1 if str2 is less than str1

```php
$pswd = "supersecret"; 
$pswd2 = "supersecret2";

if (strcmp($pswd, $pswd2) != 0) { 
  echo "Passwords do not match!";
} else {
  echo "Passwords match!";
}
```

```php
if ($str1 == $str2)
```

While both accomplish the same goal, which is to compare two strings, keep in mind that the values they return in doing so are different.

##### Comparing Two Strings’ Case Insensitively

The `strcasecmp()` function operates exactly like `strcmp()`, except that its comparison is case insensitive. 

```php
$email1 = "admin@example.com"; 
$email2 = "ADMIN@example.com";

if (! strcasecmp($email1, $email2)) {
  echo "The email addresses are identical!";
}
```

##### Calculating the Similarity Between Two Strings
The `strspn()` function returns the length of the first segment in a string containing characters also found in another string.

```php
$password = "3312345";
if (strspn($password, "1234567890") == strlen($password)) {
  echo "The password cannot consist solely of numbers!";
}
```

##### Calculating the Difference Between Two Strings
The `strcspn()` function returns the length of the first segment of a string containing characters not found in another string. 

```php
$password = "a12345";
if (strcspn($password, "1234567890") == 0) {
  echo "Password cannot consist solely of numbers!"; 
}
```

#### Manipulating String Case

##### Converting a String to All Lowercase

The `strtolower()` function converts a string to all lowercase letters, returning the modified string. Nonalphabetical characters are not affected.

```php
$url = "http://WWW.EXAMPLE.COM/"; 
echo strtolower($url);
// http://www.example.com/
```

##### Converting a String to All Uppercase

This example uses `strtoupper()` to convert a string to all uppercase letters.

```php
$msg = "I annoy people by capitalizing e-mail text."; 
echo strtoupper($msg);
// I ANNOY PEOPLE BY CAPITALIZING E-MAIL TEXT.
```

##### Capitalizing the First Letter of a String
The `ucfirst()` function capitalizes the first letter of the string str, if it is alphabetical. 

```php
$sentence = "the newest version of PHP was released today!"; 
echo ucfirst($sentence);
// The newest version of PHP was released today!
```

The function `lcfirst()` performs the opposite action of turning the first character of a string to lowercase.

##### Capitalizing Each Word in a String
The `ucwords()` function capitalizes the first letter of each word in a string. 

```php
$title = "O'Malley wins the heavyweight championship!"; 
echo ucwords($title);
// O'Malley Wins The Heavyweight Championship!
```

#### Converting Strings to and from HTML

##### Converting Newline Characters to HTML Break Tags
The `nl2br()` function converts all `newline (\n)` characters in a string to their XHTML- compliant equivalent, `<br />`.

##### Converting Special Characters to Their HTML Equivalents
During the general course of communication, you may come across many characters that are not included in a document’s text encoding, or that are not readily available on the keyboard. 

<img src="/images/Beginning PHP.assets/Table 9-2.png" alt="Table 9-2" />

```php
$advertisement = "Coffee at 'Cafè Française' costs $2.25."; 
echo htmlentities($advertisement);
// Coffee at 'Caf&egrave; Fran&ccedil;aise' costs $2.25.
```

##### Converting Text into Its HTML Equivalent
Using `get_html_translation_table()` is a convenient way to translate text to its HTML equivalent, returning one of the two translation tables (HTML_SPECIALCHARS or HTML_ENTITIES).

```php
$string = "La pasta è il piatto più amato in Italia"; 
$translate = get_html_translation_table(HTML_ENTITIES); 
echo strtr($string, $translate);
// La pasta &egrave; il piatto pi&ugrave; amato in Italia
```

Interestingly, `array_flip()` is capable of reversing the text-to-HTML translation and vice versa. 

```php
$entities = get_html_translation_table(HTML_ENTITIES);
$translate = array_flip($entities);
$string = "La pasta &egrave; il piatto pi&ugrave; amato in Italia"; 
echo strtr($string, $translate);
// La pasta é il piatto più amato in italia
```

##### Creating a Customized Conversion List
The `strtr()` function converts all characters in a string to their corresponding match found in a predefined array.

```php
$table = array('<b>' => '<strong>', '</b>' => '</strong>'); 
$html = '<b>Today In PHP-Powered News</b>';
echo strtr($html, $table);
// <strong>Today In PHP-Powered News</strong>
```

##### Converting HTML to Plain Text
The `strip_tags()` function removes all HTML and PHP tags from a string, leaving only the text entities.

```php
$input = "Email <a href='spammer@example.com'>spammer@example.com</a>"; 
echo strip_tags($input);
// Email spammer@example.com
```

The optional **allowable_tags** parameter allows you to specify which tags you would like to be skipped during this process.

```php
$input = "This <a href='http://www.example.com/'>example</a> is <b>awesome</b>!"; 
echo strip_tags($input, "<a>");
// This <a href='http://www.example.com/'>example</a> is awesome!
```

### Alternatives for Regular Expression Functions

#### Tokenizing a String Based on Predefined Characters

```php
$info = "J. Gilmore:jason@example.com|Columbus, Ohio";
// delimiters include colon (:), vertical bar (|), and comma (,) 
$tokens = ":|,";
$tokenized = strtok($info, $tokens);

// print out each element in the $tokenized array
while ($tokenized) {
  echo "Element = $tokenized<br>";
  // Don't include the first argument in subsequent calls. 
  $tokenized = strtok($tokens);
}

/*
Element = J. Gilmore 
Element = jason@example.com 
Element = Columbus
Element = Ohio
*/
```

##### Exploding a String Based on a Predefined Delimiter
The `explode()` function divides the string str into an array of substrings.

```php
$summary = <<<summary
The most up to date source for PHP documentation is the PHP manual. It contins many examples and user contributed code and comments.
It is available on the main PHP web site
<a href="http://www.php.net">PHP’s</a>.
summary;

$words = sizeof(explode(' ',strip_tags($summary))); 
echo "Total words in summary: $words";
// Total words in summary: 46
```

The `explode()` function will always be considerably faster than `preg_split()`.
Therefore, always use it instead of the others when a regular expression isn’t necessary.

##### Converting an Array into a String

```php
$cities = array("Columbus", "Akron", "Cleveland", "Cincinnati");
echo implode("|", $cities);
// Columbus|Akron|Cleveland|Cincinnati
```

##### Performing Complex String Parsing
The `strpos()` function finds the position of the first case-sensitive occurrence of a substring in a string.

##### Finding the Last Occurrence of a String
The `strrpos()` function finds the last occurrence of a string, returning its numerical position. 

##### Replacing All Instances of a String with Another String
The `str_replace()` function case sensitively replaces all instances of a string with another. 

##### Retrieving Part of a String
The `strstr()` function returns the remainder of a string beginning with the first occurrence of a predefined string. 

This example uses the function in conjunction with the `ltrim()` function to retrieve the domain name of an e-mail address:

```php
$url = "sales@example.com";
echo ltrim(strstr($url, "@"),"@");
// example.com
```

##### Returning Part of a String Based on Predefined Offsets
The `substr()` function returns the part of a string located between a predefined starting offset and length positions. 

##### Determining the Frequency of a String’s Appearance
The `substr_count()` function returns the number of times one string occurs within another.

##### Replacing a Portion of a String with Another String
The `substr_replace()` function replaces a portion of a string with a replacement string, beginning the substitution at a specified starting position and ending at a predefined replacement length.

#### Padding and Stripping a String

##### Trimming Characters from the Beginning of a String
The `ltrim()` function removes various characters from the beginning of a string, including whitespace, the horizontal tab (\t), newline (\n), carriage return (\r), NULL (\0), and vertical tab (\x0b).

##### Trimming Characters from the End of a String
The `rtrim()` function operates identically to ltrim(), except that it removes the designated characters from the right side of a string.

##### Trimming Characters from Both Sides of a String
You can think of the `trim()` function as a combination of `ltrim()` and `rtrim()`, except that it removes the designated characters from both sides of a string.

##### Padding a String

The `str_pad()` function pads a string with a specified number of characters. 

```php
$header = "Log Report";
echo str_pad ($header, 20, "=+", STR_PAD_BOTH);
// =+=+=Log Report=+=+=
```

#### Counting Characters and Words

##### Counting the Number of Characters in a String
The function `count_chars()` offers information regarding the characters found in a string. This function only works on single byte characters.

##### Counting the Total Number of Words in a String
The function `str_word_count()` offers information regarding the total number of words found in a string.
