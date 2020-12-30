---
layout: post
title: "Reading: PHP Objects, Patterns, and Practice Part1 01"
date: 2020-11-24
categories: [PHP]
---

A note taking from PHP Objects, Patterns, and Practice.<br>
Covers: Part I Objects
- Ch1 PHP: Design and Management
- Ch2 PHP and Objects
- Ch3 Object Basics
- Ch4 Advanced Features

# Part I: Objects
## Ch1 PHP: Design and Management
Overview of the history of PHP and the book.

## Ch2 PHP and Objects
Overview of the support of OOP in PHP5 and PHP7.

PHP5:
- Remove the default pass-by-reference behavior
- Support Namespaces to create named scope for classes and functions
- Class type declaration (type hints)

PHP7:
- Scalar type declarations
- Enforced return types

## Ch3 Object Basics
Topics:
- Classes and Objects: declaring classes and instantiating objects
- Constructor methods: automating the setup of objects
- Primitive and class types: why type matters
- Inheritance: why we need it and how to use it
- Visibility: streamlining object interfaces and protecting mothods and properties from meddling

### Classes and Objects
A class is a code template used to generate objects.
```php
class ShopProduct
{
    // Property scope: public, private, protected
    public $title;
    public $producerMainName;
    public $producerFirstName;
    float $price;

    // constructor method
    public function __construct(
        $title="default title",
        $firstName="default firstname",
        $mainName="default mainname",
        $price=0
    ) {
        $this->title = $title;
        $this->producerFirstName = $firstName;
        $this->producerMainName = $mainName;
        $this->price = $price;
    }

    public function getProducer()
    {
        return $this->producerFirstName." ".$this->producerMainName;
    }
}

$product1 = new ShopProduct();
$product2 = new ShopProduct();

// Get properties' value
print $product1->title;

// Set properties' value
$product1->title = "new title"

//Dynamically add propertices (bad practice)
$product1->arbitraryAddition ="treehouse";

// print the objects
var_dump($product1);
var_dump($product2);
```

### Type Hints: class type declaration
```php
class ShopProductWriter
{
    public function write(ShopProduct $shopProduct)
    {
        $str = $shopProduct->title.": ".$shopProduct->getProducer()." (".$shopProduct->price.")\n";
        print $str;
    }
}
```
Passing non-shopProduct instance as a argument to the write function will cause a fatal error.

### Constructors and Inheritance
Call the constructor function of parent class by **parent::__construct()**.

### Visibility Keywords
- Public properties and methods can be accessed from any context
- Private properties and methods can only be accessed from within the enclosing class
- Protected properties and methods can only be accessed from within either the enclosing class or subclasses.

### Accessor Methods
Denying direct access to properties and providing methods instead that relay the needed values are known as accessors(getters and setters).

Set the class properties to be private and provide public methods to access the properties.

## Ch4 Advanced Features
Topics:
- Static methods and properties: accessing data and functionality through class
- Abstract classes and interfaces: separating design from implementation
- Traits: sharing implementation between class hierarchies
- Error handling: introducing exceptions
- Final classes and methods: limiting inheritance
- Interceptor methods: automating delegation
- Destructor methods: cleaning up after objects
- Cloning objects: making object copies
- Resolving objects to strings: creating a summary method
- Callbacks: adding functionality to components with anonymous functions and classes

### Static Methods and Properties
Class methods and properties could be accessed directly instead instantiating a instance by declaring static methods / properties.

```php
class StaticExample
{
    static public $aNum = 0;
    static public function sayHello()
    {
        self::$aNum++;
        print "Hello!(".self::$aNum.")\n";
    } 
}

// Access the static properties and methods
print StaticExample::$aNum;
StaticExample::sayHello();
```

### Constant Properties
```php
class ShopProduct
{
    const AVAILABLE = 0;
    const OUT_OF_STOCK = 1;
}

// Access the class constant
print ShopProduct::AVAILABLE;
```

### Abstract Classes
An abstract class cannot be instantiated but defines (partially implements) the interface for classes that might extend the abstract class.

Any attempt to instantiate an abstract object will cause an error:
```
Error: Cannot instantiate abstract class
```

- In most cases, an abstract class will contain at least one abstract method
- The abstract methods cannot have an implementation in the abstract class
- Classes that extend an abstract class must implement all abstract methods

### Interfaces
Interfaces are pure templates and can only define functionality.

```php
interface Chargeable
{
    public function getPrice(): float;
}

// create a class implementing the interface
class ShopProduct implements Chargeable
{
    protected $price;

    public function getPrice(): float
    {
        return $this->price;
    }
}
```

- A class can both extend a superclass and implement more than one interfaces
- The `extends` clause should precede the `implements` clause

```php
class Consultancy extends TimedService implements Bookable, Chargeable
{
    // ...
}
```

### Traits
A trait is a class-like structure that cannot be instantiated but can be incorporated into classes.<br>
Traits allow to share implementations across inheritance hierarchies.

```php
trait PriceUtilities
{
    private $taxrate = 17;

    public function calculateTax(float $price): float
    {
        return (($this->taxrate / 100) * $price);
    }
}

trait IdentityTrait
{
    public function generateId(): string
    {
        return uniqid();
    }
}

# class ShopProduct uses trait PriceUtilities
class ShopProduct
{
    use PriceUtilities;
}

# class UtilityService inherits abstract class service and uses trait PriceUtilities
abstract class Service
{
    // ...
}

class UtilityService extends Service
{
    use PriceUtilities;
}

# class ShopProduct uses multiple traits
class ShopProduct
{
    use PriceUtilities, IdentityTrait;
}

```

#### Combining Traits and Interfaces
```php
interface IdentityObject
{
    public function generateId(): string;
}

trait IdentityTrait
{
    public function generateId(): string
    {
        return uniqid();
    }
}

class ShopProduct implements IdentityObject
{
    use Pricetilities, IdentityTrait;
}
```

#### Managing Method Name Conflicts with `insteadof`
```php
trait TaxTools
{
    function calculateTax(float $price): float
    {
        return 222;
    }
}

trait PriceUtilities
{
    private $taxrate = 17;
    public function calculateTax(float $price): float
    {
        return (($this->taxrate / 100) * $price);
    }
}

class UtilityService extends Service
{
    use PriceUtilities, TaxTools {
        TaxTools::calculateTax insteadof PriceUtilities;
    }
}
```

#### Aliasing overridden trait methods
```php
class UtilityService extends Service
{
    use PriceUtilities, TaxTools {
        TaxTools::calculateTax insteadof PriceUtilities;
        PriceUtilities::calculateTax as basicTax;
    }
}

$u = new UtilityService();
print $u->calculateTax(100); // 222
print $u->basicTax(100);     // 17
```

#### Using staic methods in traits
```php
trait PriceUtilities
{
    private static $taxrate = 17;
    public static function calculateTax(float $price): float
    {
        return (($this->taxrate / 100) * $price);
    }
}

$u = new UtilityService();
print $u::calculateTax(100); // 17
```

#### Accessing Host Class Properties
Properties and methods in host class are all accessible from Traits.<br>
Limit the scope by declaring abstract methods in Traits.

##### Defining Abstract Methods in Traits
```php
trait PriceUtilities
{
    function calculateTax(float $price): float
    {
        return (($this->getRate()/100) * $price);
    }

    abstract function getTaxRate(): float;
}

class UtilityService extends Service
{
    use PriceUtilities;

    public function getTaxRate(): float
    {
        return 17;
    }
}
```

##### Changing Access Rights to Trait Methods
```php
trait PriceUtilities
{
    public function calculateTax(float $price): float
    {
        return (($this->getTaxRate / 100) * $price);
    }
    public abstract function getTaxRate(): float;
}

class UtilityService extends Service
{
    use PriceUtilities {
        PriceUtilities::calculateTax as private;
    }

    private $price;

    public function __construct(float $price)
    {
        $this->price = $price;
    }

    public function getTaxRate(): float
    {
        return 17;
    }

    public function getFinalPrice(): float
    {
        return ($this->price + $this->calculateTax($this->price));
    }
}

$u = new UtilityService(100);
print $u->getFinalPrice();
// print the final price

print $u->calculateTax();
// will generate an error
// Error: Call to private method UtilityService::calculateTax() from context...
```

### Late Static Bindings: static Keyword
The `static` keyword can be used as an identifier for static method calls, even from a non-static context.

```php
abstract class DomainObject
{
    public static function create(): DomainObject
    {
        return new static();
    }
}

class User extends DomainObject
{
    // ...
}

class Document extends DomainObject
{
    // ...
}

print_r(Document::create());
```

### Handling Errors
#### Exceptions
An exception is a special object instantiated from the built-in Exception class.<br>
Exception objects are designed to hold and report error information.

Exception class constructor accepts two optional arguments:
- message string
- error code

<img src="/images/PHP Objects.assets/4-1.PNG" alt="Exception Class Public Methods" />

#### Throw an Exception
```php
if (! file_exists($file)) {
    throw new \Exception("file '$file' does not exist");
}
```

Handle exceptions by try and catch clause:
```php
try {
    $conf = new Conf(__DIR__."/conf01.xml");
    print "user: ". $conf->get("user") ."\n";
    print "host: ". $conf->get("host") ."\n";

    $conf->set("pass", "newpass");
    $conf->write();
} catch (\Exception $e) {
    die($e->__toString());
}

// catch exception by class
try {
    $conf = new Conf(__DIR__."/conf01.xml");
    print "user: ". $conf->get("user") ."\n";
    print "host: ". $conf->get("host") ."\n";

    $conf->set("pass", "newpass");
    $conf->write();
} catch (FileException $e) {
    // permission issue or non-existent file
} catch (XmlException $e) {
    // broken xml
} catch (ConfException $e) {
    // wrong kind of XML file
} catch (\Exception $e) {
    // backstop
}
```

#### Subclassing Exception
```php
class XmlException extends \Exception
{
    private $error;
    
    public function __construct(\LibXmlError $error)
    {
        $shortfile = basename($error->file);
        $msg = "[{$shortfile}, line {$error->line}, col {$error->column}] {$error->message}";
        $this->error = $error;
        parent::__construct($msg, $error->code);
    }

    public function getLibXmlError()
    {
        return $this->error;
    }
}

// Throw exception
$xml = simplexml:load_file($file, null, LIBXML_NOERROR);
if (! is_object($xml)) {
    throw new XmlException(libxml:get_last_error());
}
```

#### Cleaning Up try/catch Clauses with `finally`
```php
$fh = fopen(__DIR__."/log.txt", "a");
try {
    // ...
} catch ($e) {
    // ...
} finally {
    fputs($fh, "end\n");
    fclose($fh);
}
```

Calling `die()` or `exit()` in *try* or *catch* block will end script execution and *finally* clause will not be run.

#### The Internal Error Class
```php
try {
    eval("illegal code");
} catch (\Error $e) {
    print get_class($e);
} catch (\Exception $e) {
    // Handle the exception
}

// output:
// ParseError
```

Built-in Error classes in PHP 7
<img src="/images/PHP Objects.assets/4-2.PNG" alt="Built-in Error classes in PHP 7" />

### Final Classes and Methods
*final* puts a stop to inheritance. A final class cannot be subclassed and a final method cannot be overridden.

### Working with Interceptors
PHP provides built-in intercepters that can intercept messages sent to undefined methods and properties also known as **overloading**.

<img src="/images/PHP Objects.assets/4-3.PNG" alt="Interceptor Methods" />

```php
class Person
{
    public function __get(string $property)
    {
        $method = "get{$property}";
        if (method_exists($this, $method)) {
            return $this->method();
        }
        // if the method does not exist, the value would be NULL
    }

    public function getName(): string
    {
        return "Bob";
    }

    public function getAge(): int
    {
        return 44;
    }
}

$p = new Person();
print $p->name;
```

### Defining Destructor Methods
PHP 5 introduced `__destrct()` method invoked before an object is garbage-collected.<br>
The `__destruct()` method will be triggered when `unset()` function is called.

### Copying Objects with __clone()
```php
class CopyMe
{
    // ...
}

$first = new CopyMe();
$second = $first;

// PHP 4: $second and $first are two distinct objects
// PHP 5+: $second and $first refer to one object 
```

Equivalence tests would tell you whether all fields were the same(\=\=) or whether both variables were object(\=\=\=), <br>
but not whether they pointed to the same object.

Copy by value in PHP 5+ by using `clone`:
```php
class CopyMe
{
    // ...
}

$first = new CopyMe();
$second = clone $first;

// PHP 5+: $second and $first are two distinct objects
```

#### `__clone()` method
`__clone()` method is called when clone keyword is invoked on an object.<br>
`__clone()` is run on the copied object, not the original one.

```php
class Account
{
    public $balance;
    public function __constrct(float $balance)
    {
        $this->balance = $balance;
    }
}

class Person
{
    private $name;
    private $age;
    private $id;
    public  $account;

    public function __construct(string $name, int $age)
    {
        $this->name = $name;
        $this->age = $age;
        $this->account = $account;
    }

    public function setId(int $id)
    {
        $this->id = $id;
    }

    public function __clone()
    {
        $this->id = 0;
        // account would be refered to the clone object by default
        // to avoid the cloned person accessing the account of original object,
        // make sure to clone the account as well
        $this->account = clone $this->account;
    }
}
```

### Defining String Values for Your Objects
By implementing `___toString()` method, you can control how your objects represent themselves when printed.

```php
class StringThing
{
    // ...
}

$st = new StringThing();
print $st;

/* PHP 5.2-:*/
// Object id #1

/* PHP 5.2 */
// object of class could not be converted to string
```

### Callbacks, Anonymous Functions and Closures 
```php
class Product {
    public $name;
    public $price;

    public function __construct(string $name, float $price)
    {
        $this->name = $name;
        $this->price = $price;
    }
}

class ProcessSale
{
    private $callbacks;

    public function registerCallback(callable $callback)
    {
        if(! is_callable($callback)) {
            throw new Exception("callback not callable");
        }
        $this->callbacks[] = $callback;
    }

    public function sale(Product $product)
    {
        print "{$product->name}: processing \n";
        foreach ($this->callbacks as $callback) {
            call_user_func($callback, $product);
        }
    }
}
```
FYR: [call_user_func](https://www.php.net/manual/ja/function.call-user-func.php)

Make anonymous function track variables from its wider scope with `use` clause:

```php
class Totalizer
{
    public static function warnAmount($amt)
    {
        $count = 0;
        return function ($product) use ($amt, &$count) {
            $count += $product->price;
            print "    count: $count\n";
            if ($count > $amt) {
                print "    high price reached: {$count}\n";
            }
        };
    }
}
```

FYR: [PHP Document - 無名関数(クロージャ)](https://www.php.net/manual/ja/functions.anonymous.php)

### Anonymous Classes
PHP 7: you can declare anonymous classes as functions.

FYR: [PHP Document - 無名クラス](https://www.php.net/manual/ja/language.oop5.anonymous.php)
