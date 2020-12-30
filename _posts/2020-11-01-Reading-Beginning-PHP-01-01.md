---
layout: post
title: "Reading: Beginning PHP Part1 01"
date: 2020-11-01
categories: [PHP]
---

Notes taking from: Beginning PHP and MySQL From Novice to Professional<br>
Covers:
- Ch3 PHP Basics
- Ch4 Functions
- Ch5 Arrays

## CH3 PHP Basics

### Print

```php
<?php 
  echo "<p>Some dynamic output here</p>"; 
?>

<? echo "This is another PHP example."; ?>
  
<?
print "This is another PHP example.";
?>

<?="This is another PHP example.";?>
  
printf("%d bottles of tonic water cost $%f.", 100, 43.20);
// 100 bottles of tonic water cost $43.200000.

printf("$%.2f", 43.2); // outputs $43.20
```

**Table 3-1.** *Commonly Used Type Specifiers*
<img src="/images/Beginning PHP.assets/Table 3-1.png" alt="Table 3-1" />

### Data Types

#### Boolean
<img src="/images/Beginning PHP.assets/Boolean.png" alt="Boolean" />

#### String

```php
$a = "123"; $b = "456";

echo $a + $b . "\n"; 
// 579

echo $a . $b . "\n";
// 123456

```

#### object

```php
class Appliance {
   private $_power;
   function setPower($status) {
     $this->_power = $status; 
   }
}

$blender = new Appliance;
$blender->setPower("on");
```

### Type Casting
<img src="/images/Beginning PHP.assets/Table 3-2.png" alt="Table 3-2" />

```php
$score = (double) 13; // $score = 13.0
$score = (int) 14.8; // $score = 14

$sentence = "This is a sentence"; 
echo (int) $sentence; // returns 0

$score = 1114;
$scoreboard = (array) $score;
echo $scoreboard[0]; // Outputs 1114

$model = "Toyota";
$obj = (object) $model;
print $obj->scalar; // returns "Toyota"
```

### Type Identifier Functions

- is_array()
- is_bool()
- is_float()
- is_integer()
- is_null()
- is_numeric()
- is_object()
- is_resource()
- is_scalar()
- is_string()

### Variables
#### Declaration - Assigning Variables by Reference

```php
<?php
$value1 = "Hello";
$value2 = &$value1; // $value1 and $value2 both equal "Hello" 
$value2 = "Goodbye"; // $value1 and $value2 both equal "Goodbye"
?>
```

#### Superglobals

Superglobals are built-in variables that are always available in all scopes

[https://www.php.net/manual/en/language.variables.superglobals.php](https://www.php.net/manual/en/language.variables.superglobals.php)

#### Constant

```php
define("PI", 3.141592);

// Or by using the const keyword:
Const PI = 3.141592;
```

#### Logical Operators
<img src="/images/Beginning PHP.assets/Table 3-8.png" alt="Table 3-8" />

#### Equality Operators
<img src="/images/Beginning PHP.assets/Table 3-9.png" alt="Table 3-9" />

### File-Inclusion Statements

```php
include(/path/to/filename)
<?php
include "/usr/local/lib/php/wjgilmore/init.inc.php";
?>
  
include_once (filename)
```

If a file has already been included, include_once() will not execute. Otherwise, it will include the file as necessary. Other than this difference, include_once() operates in exactly the same way as include().

```php
require (filename)
require_once (filename)
```

The difference between require() and include() is the result when the referenced file is not found. require() will issue a compiler error and halt execution and include() will result in a warning and execution will continue.

The require_once() function ensures that the inclusion file is included only once in your script. After require_once() is encountered, any subsequent attempts to include the same file will be ignored.

## CH4 Functions

### Returning Multiple Values

```php
$colors = ["red","blue","green"]; 
list($color1, $color2, $color3) = $colors;
```

Once the list() construct executes, $color1, $color2, and $color3 will be assigned red, blue, and green, respectively. 

```php
function retrieveUserProfile()
{
    $user[] = "Jason Gilmore"; 
    $user[] = "jason@example.com"; 
    $user[] = "English";
    return $user;
}

list($name, $email, $language) = retrieveUserProfile();
echo "Name: {$name}, email: {$email}, language: {$language}";

// Name: Jason Gilmore, email: jason@example.com, language: English
```

### Using Type Declarations

```php
function processPayPalPayment(Customer $customer) { 
  // Process the customer's payment
}
```

PHP 7.0 also introduces type hinting for the return values and is done by adding :<type> after the closing parenthesis in the argument list.

```php
function processPayPalPayment(Customer $customer): bool { 
  // Process the customer's payment
}
```

### Anonymous Functions

```php
$example = function() { 
  echo "Closure";
};
$example();

function MyEcho() { 
  echo "Closure";
};
$example = "MyEcho"; 
$example();
```

Closures act as other functions when it comes to scope and access to variables outside of the function. 
In order to provide access to such variables, PHP provides the keyword `use`

```php
// $a is not accessible, causing it to be assigned 0 inside the first closure
$a = 15;
$example = function() {
  $a += 100;
  echo $a . "\n"; };
  $example(); echo $a . "\n";

  // $a is made available to the closure, but the global value is no affected
  $example = function() use ($a) { $a += 100;
  echo $a . "\n";
};
$example(); 
echo $a . "\n";

// $a is made available by reference. This causes the global value to change when the closure is executed
$example = function() use (&$a) { 
  $a += 100;
  echo $a . "\n";
};
$example(); 
echo $a . "\n";
```

## CH5 Arrays

```php
$carBrands = ["Cheverolet", "Chrysler""Ford", "Honda", "Toyota"];
$germanCars = [20 => "Audi", 22 => "Porche", 25 => "VW"];
$states = ["OH" => "Ohio", "PA" => "Pennsylvania", "NY" => "New York"];
$states = [
  "Ohio" => array("population" => "11,353,140", "capital" => "Columbus"), 
  "Nebraska" => array("population" => "1,711,263", "capital" => "Omaha")
];

```

creating arrays

```php
$state[1] = "Pennsylvania"; 
$state[2] = "New Jersey"; 
...
$state[49] = "Hawaii";

$state[] = "Pennsylvania"; 
$state[] = "New Jersey"; 
...
$state[] = "Hawaii";
```

### Populating Arrays with a Predefined Value Range

```php
$die = range(1, 6);
// Same as specifying $die = array(1, 2, 3, 4, 5, 6)

$even = range(0, 20, 2);
// $even = array(0, 2, 4, 6, 8, 10, 12, 14, 16, 18, 20);

$letters = range("A", "F");
// $letters = array("A", "B", "C", "D", "E", "F");
```

### Outputting an array

If you want to print an array of arrays or need to exercise a more exacting format standard over array output, consider using the `vprintf()` function

```php
$customers = array();
$customers[] = array("Jason Gilmore", "jason@example.com", "614-999-9999"); 
$customers[] = array("Jesse James", "jesse@example.net", "818-999-9999"); 
$customers[] = array("Donald Duck", "donald@example.org", "212-999-9999");
foreach ($customers AS $customer) {
  vprintf("<p>Name: %s<br>E-mail: %s<br>Phone: %s</p>", $customer);
}
```

#### for Testing Purposes

The print_r() function accepts a variable and sends its contents to standard output, returning TRUE on success and FALSE otherwise.

```php
print_r($states);

/*
Array (
  [Ohio] => Columbus
  [Iowa] => Des Moines 
  [Arizona] => Phoenix
)
*/
```

### Manipulation

- `array_unshift` Adding a Value to the Front of an Array
- `array_push ` Adding a Value to the End of an Array
- `array_shift` Removing a Value from the Front of an Array
- `array_pop` Removing a Value from the End of an Array

#### search an array

`in_array()` function searches an array for a specific value, returning TRUE if the value is found and FALSE otherwise.

#### Searching Associative Array Keys

The function `array_key_exists()` returns TRUE if a specified key is found in an array and FALSE otherwise.

#### Searching Associative Array Values

The `array_search()` function searches an array for a specified value, returning its key if located and FALSE otherwise.

#### Extracting Columns

```php
$simpsons = [
  ['name' => 'Homer Simpson', 'gender' => 'Male'],
  ['name' => 'Marge Simpson', 'gender' => 'Female'],
  ['name' => 'Bart Simpson', 'gender' => 'Male'] 
];
$names = array_column($simpsons, 'name'); 

print_r($names);
// Array([0] => Homer Simpson [1] => Marge Simpson [2] => Bart Simpson )
```

#### Traversing Arrays

```php
$capitals = array("Ohio" => "Columbus", "Iowa" => "Des Moines"); 
echo "<p>Can you name the capitals of these states?</p>"; 
while($key = key($capitals)) {
  printf("%s <br />", $key);
  next($capitals);
}

echo "<p>Can you name the states belonging to these capitals?</p>";
while($capital = current($capitals)) { 
  printf("%s <br />", $capital); 
  next($capitals);
}
```

#### Moving the Array Pointer

```php
$fruits = array("apple", "orange", "banana");
$fruit = next($fruits); // returns "orange" 
$fruit = next($fruits); // returns "banana"
```

The `prev()` function returns the array value residing at the location preceding the current pointer location, or FALSE if the pointer resides at the first position in the array.

The `reset()` function serves to set an array pointer back to the beginning of the array.

The `end()` function moves the pointer to the last position of an array, returning the last element.

```php
$fruits = array("apple", "orange", "banana"); 
$fruit = current($fruits); // returns "apple" 
$fruit = end($fruits); // returns "banana"
```

#### Determining the Size of an Array

The `count()` function returns the total number of values found in an array.
The `sizeof()` function is an alias of `count()`. It is functionally identical.

#### Counting Array Value Frequency

The `array_count_values()` function returns an array consisting of associative key/value pairs.

```php
$states = ["Ohio", "Iowa", "Arizona", "Iowa", "Ohio"]; 
$stateFrequency = array_count_values($states); 

print_r($stateFrequency);
// Array ( [Ohio] => 2 [Iowa] => 2 [Arizona] => 1 )
```

#### Determining Unique Array Values

The `array_unique()` function removes all duplicate values found in an array, returning an array consisting of solely unique values.

```php
$states = array("Ohio", "Iowa", "Arizona", "Iowa", "Ohio"); 
$uniqueStates = array_unique($states); 

print_r($uniqueStates);
// Array ( [0] => Ohio [1] => Iowa [2] => Arizona )
```

#### Reversing Array Element Order

The `array_reverse()` function reverses an array’s element order.

```php
$states = array("Delaware", "Pennsylvania", "New Jersey"); 
print_r(array_reverse($states));
// Array ( [0] => New Jersey [1] => Pennsylvania [2] => Delaware )
```

#### Flipping Array Keys and Values

```php
$state = array(0 => "Delaware", 1 => "Pennsylvania", 2 => "New Jersey"); 
$state = array_flip($state);

print_r($state);
// Array ( [Delaware] => 0 [Pennsylvania] => 1 [New Jersey] => 2 )
```

#### Sorting an Array

```php
$grades = array(42, 98, 100, 100, 43, 12); 
sort($grades);
print_r($grades);
// Array ( [0] => 12 [1] => 42 [2] => 43 [3] => 98 [4] => 100 [5] => 100 )

```

##### Sorting an Array While Maintaining Key/Value Pairs

The `asort()` function is identical to `sort()`, sorting an array in ascending order, except that the key/value correspondence is maintained.

#### Sorting an Array in Reverse Order

```php
$states = array("Ohio", "Florida", "Massachusetts", "Montana");
rsort($states); 
print_r($states);
```

##### Sorting an Array in Reverse Order While Maintaining Key/Value Pairs
Like `asort()`, `arsort()` maintains key/value correlation. However, it sorts the array in reverse order.

#### Sorting an Array by Keys

The `ksort()` function sorts an array by its keys, returning TRUE on success and FALSE otherwise.

##### Sorting Array Keys in Reverse Order

The `krsort()` function operates identically to `ksort()`, sorting by key, except that it sorts in reverse (descending) order.

#### Merging Arrays

The `array_merge()` function merges arrays together, returning a single, unified array.

##### Recursively Appending Arrays

```php
$class1 = array("John" => 100, "James" => 85);
$class2 = array("Micky" => 78, "John" => 45); 
$classScores = array_merge_recursive($class1, $class2); 

print_r($classScores);
/*
Array (
  [John] => Array (
    [0] => 100
    [1] => 45 
  )
  [James] => 85
  [Micky] => 78 
)
*/
```

#### Combining Two Arrays

```php
$abbreviations = array("AL", "AK", "AZ", "AR");
$states = array("Alabama", "Alaska", "Arizona", "Arkansas"); 
$stateMap = array_combine($abbreviations,$states); 

print_r($stateMap);
// Array ( [AL] => Alabama [AK] => Alaska [AZ] => Arizona [AR] => Arkansas )
```

#### Slicing an array

The array_slice() function returns a section of an array based on a starting offset and elength value.

```php
$states = array("Alabama", "Alaska", "Arizona", "Arkansas", "California", "Colorado", "Connecticut");
$subset = array_slice($states, 4); 

print_r($subset);
// Array ( [0] => California [1] => Colorado [2] => Connecticut )
```

##### Splicing an Array

The `array_splice()` function removes all elements of an array found within a specified range, replacing them with values identified by the replacement parameter and returns the removed elements in the form of an array.

```php
$states = array("Alabama", "Alaska", "Arizona", "Arkansas", "California", "Connecticut");
$subset = array_splice($states, 4); 

print_r($states);
print_r($subset);

// Array ( [0] => Alabama [1] => Alaska [2] => Arizona [3] => Arkansas ) 
// Array ( [0] => California [1] => Connecticut )
```

#### Calculating an Array Intersection

```php
$array1 = array("OH", "CA", "NY", "HI", "CT");
$array2 = array("OH", "CA", "HI", "NY", "IA");
$array3 = array("TX", "MD", "NE", "OH", "HI");
$intersection = array_intersect($array1, $array2, $array3); 

print_r($intersection);
// Array ( [0] => OH [3] => HI )
```

The `array_intersect_key()` function will return keys located in an array that is located in any of the other provided arrays. 

The function `array_intersect_assoc()` operates identically to `array_intersect()`, except that it also considers array keys in the comparison. 

```php
$array1 = array("OH" => "Ohio", "CA" => "California", "HI" => "Hawaii"); 
$array2 = array("50" => "Hawaii", "CA" => "California", "OH" => "Ohio"); 
$array3 = array("TX" => "Texas", "MD" => "Maryland", "OH" => "Ohio"); 
$intersection = array_intersect_assoc($array1, $array2, $array3); 

print_r($intersection);
// Array ( [OH] => Ohio )
```

#### Returning a Random Set of Keys

```php
$states = array("Ohio" => "Columbus", "Iowa" => "Des Moines", "Arizona" => "Phoenix");
$randomStates = array_rand($states, 2); 

print_r($randomStates);
// Array ( [0] => Arizona [1] => Ohio )
```

#### Shuffling Array Elements

```php
$cards = array("jh", "js", "jd", "jc", "qh", "qs", "qd", "qc", "kh", "ks", "kd", "kc", "ah", "as", "ad", "ac");
shuffle($cards);

print_r($cards);
/*
Array ( [0] => js [1] => ks [2] => kh [3] => jd
        [4] => ad [5] => qd [6] => qc [7] => ah
        [8] => kc [9] => qh [10] => kd [11] => as [12] => ac [13] => jc [14] => jh [15] => qs )
*/
```

#### Sum up an array

```php
$grades = array(42, "hello", "42"); 
$total = array_sum($grades);
print $total;
// 84
```

#### Subdividing an Array

```php
$cards = array("jh", "js", "jd", "jc", "qh", "qs", "qd", "qc", "kh", "ks", "kd", "kc", "ah", "as", "ad", "ac");

// shuffle the cards
shuffle($cards);

// Use array_chunk() to divide the cards into four equal "hands" 
$hands = array_chunk($cards, 4);

print_r($hands);
/*
Array ( [0] => Array ( [0] => jc [1] => ks [2] => js [3] => qd ) 
        [1] => Array ( [0] => kh [1] => qh [2] => jd [3] => kd ) 
        [2] => Array ( [0] => jh [1] => kc [2] => ac [3] => as ) 
        [3] => Array ( [0] => ad [1] => ah [2] => qc [3] => qs ) )
*/
```
