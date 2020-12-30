---
layout: post
title: "Reading: Beginning PHP Part1 02"
date: 2020-11-01
categories: [PHP]
---

Notes taking from: Beginning PHP and MySQL From Novice to Professional<br>
Covers:
- Ch6 Object-Oriented PHP
- Ch7 Advanced OOP Features

## CH6 Object-Oriented PHP

Encapsulation
Programmers generally enjoy taking things apart and learning how all of the little pieces work together. Although gratifying, attaining such in-depth knowledge of an item’s inner workings isn’t a precursory requirement to programming proficiency. 

Inheritance
The many objects constituting your environment can be modeled using a well-defined set of requirements.

Polymorphism
Polymorphism, a term originating from the Greek language that means “having multiple forms,” defines OOP’s ability to redefine, or morph, a class’s characteristic or behavior depending upon the context in which it is used.

### Key OOP Concepts

#### Class

```php
class Class_Name
{
    // Property declarations defined here
    // Method declarations defined here
}
```

Example

```php
class Employee
{
  private $name;
  private $title;
  
  public function getName() {
    return $this->name;
  }
  public function setName($name) {
    $this->name = $name;
  }
  public function sayHello() {
    echo "Hi, my name is {$this->getName()}.";
  } 
}
```

#### Object

Example

```php
$employee = new Employee;
```

#### Property

declaring

```php
class Employee
{
  public $name = "John";
  private $wage;
}
```

invoking

```php
$employee->name 
$employee->title 
$employee->wage
```

#### Property Scope

PHP supports three class property scopes: `public`, `private`, and `protected`.

##### Public Properties

```php
class Employee
{
  public $name;
}

$employee = new Employee(); 
$employee->name = "Mary Swanson"; 
$name = $employee->name;
echo "New employee: $name";
```

##### Private Properties

```php
class Employee
{
  private $name;
  private $telephone;
}
```

Properties designated as private are only directly accessible by an object instantiated from the class, but are they are not available to objects instantiated from child classes (the concept of a child class is introduced in the next chapter). If you want to make these properties available to child classes, consider using the protected scope instead.

Note that private properties must be accessed via publicly exposed interfaces, which satisfies one of OOP’s main tenets.

```php
class Employee
{
  private $name;
  public function setName($name) {
    $this->name = $name; 
  }
}

$employee = new Employee; 
$employee->setName("Mary");
```

##### Protected Property

```php
class Employee
{
  protected $wage;
}

class Programmer extends Employee
{
  public function bonus($percent) {
    echo "Bonud = " . $this->wage * $percent / 100;
  } 
}
```

### Property Overloading

#### Setting Properties with the __set() Method

```php
class Employee
{
  public $name;
  function __set($propName, $propValue)
  {
    echo "Nonexistent variable: \$$propName!"; 
  }
}

$employee = new Employee; 
$employee->name = "Mario"; 
$employee->title = "Executive Chef";

// Nonexistent variable: $title!
```

extend the class with new properties:

```php
class Employee
{
  public $name;
  public function __set($propName, $propValue)
  {
    $this->$propName = $propValue;
  }
}

$employee = new Employee; 
$employee->name = "Mario"; 
$employee->title = "Executive Chef"; 

echo "Name: {$employee->name}<br />"; 
// Name: Mario
echo "Title: {$employee->title}";
// Title: Executive Chef
```

#### Getting Properties with the __get() Method

```php
class Employee
{
  public $name;
  public $city;
  protected $wage;
  public function __get($propName)
  {
    echo "__get called!<br />";
    $vars = array("name", "city"); 
    if (in_array($propName, $vars)) {
      return $this->$propName; 
    } else {
      return "No such variable!"; 
    }
  } 
}
```

#### Custom Getters and Setters

```php
class Employee
{
  private $name;
  
  // Getter
  public function getName() {
    return $this->name; 
  }
  
  // Setter
  public function setName($name) {
    $this->name = $name; 
  }
}
```

There are a number of reserved method names that are used for methods with a special purpose. These are referred to as magic methods and the names are: `__ construct()`, `__destruct()` , `__call()`, `__callStatic()`, `__get()`, `__set()`, `__ isset()`, `__unset()`, `__sleep()`,  `__wakeup()`, `__toString()`, `__invoke()`, `__set_ state()`, `__clone()`,  and `__debugInfo()` . 

### Method Scopes
PHP supports three method scopes: public, private, and protected. 

#### Public Methods
Public methods can be accessed from anywhere at any time. 

#### Private Methods
Methods marked as private are available for use only within methods defined in the same class but are not available to methods defined on a child class. Methods solely intended to be helpers for other methods located within the class should be marked
as private.

#### Protected Methods
Class methods marked as protected are available only to the originating class and its child classes. Such methods might be used for helping the class or subclass perform internal computations.

#### Abstract Methods
Abstract methods are special in that they are declared only within a parent class but are implemented in child classes. Only classes declared as abstract can contain abstract methods, and abstract classes can’t be instantiated. They serve as a base definition for sub or child classes. You might declare an abstract method if you want to define an application programming interface (API) that can later be used as a model for implementation. 

```php
abstract class Employee
{
  abstract function hire();
  abstract function fire();
  abstract function promote();
  abstract function demote();
}
```

#### Final Methods
Marking a method as final prevents it from being overridden by a subclass.

```php
class Employee
{
  final function getName() {
    // ...
  }
}
```

Attempts to later override a finalized method result in a fatal error.

### Constructors and Destructors

#### Constructors
You often want to initialize certain properties and even trigger the execution of
methods found when an object is newly instantiated.

```php
class Employee
{
  protected $name;
  protected $title;
  function __construct()
  {
    echo "Employee constructor called! "; 
  }
}

class Manager extends Employee
{
  function __construct()
  {
    parent::__construct();
    echo "Manager constructor called!"; 
  }
}

$employee = new Manager();
// Employee constructor called!
// Manager constructor called!

/*
Neglecting to include the call to parent::__construct() results in the invocation of
only the Manager constructor
*/

// Manager constructor called!
```

#### Destructors
Just as you can use constructors to customize the object creation process, so can you use destructors to modify the object destruction process. 

```php
class Book {
  private $title;
  private $isbn;
  private $copies;
  function __construct($isbn)
  {
    echo "Book class instance created. "; 
  }
  function __destruct()
  {
    echo "Book class instance destroyed."; 
  }
}

$book = new Book("0615303889");
// Book class instance created.
// Book class instance destroyed.
```

Even though the destructor is not called directly by the script, it is called when the script ends and PHP is freeing up the memory used by the objects.

### Static Class

Sometimes it’s useful to create properties and methods that are not invoked by any particular object but rather are pertinent to and are shared by all class instances. 

```php
class Visitor
{
  private static $visitors = 0;
  function __construct()
  {
    self::$visitors++;
  }
  static function getVisitors()
  {
    return self::$visitors;
  }
}
// Instantiate the Visitor class. 
$visits = new Visitor();
echo Visitor::getVisitors()."<br />";
// 1

// Instantiate another Visitor class. 
$visits2 = new Visitor();
echo Visitor::getVisitors()."<br />";
// 2
```

Because the `$visitors` property was declared as **static**, any changes made to its value (in this case via the class constructor) are reflected across all instantiated objects. Also note that static properties and methods are referred to using the `self` keyword, sope resolution operator (`::`) and class name, rather than via `$this` and arrow operators.

### Helper Functions

- Determining Object Context
  The `get_class()` function returns the name of the class to which object belongs and returns FALSE if object is not an object.

- Learning About Class Methods
  The `get_class_methods()` function returns an array containing method names defined by the class class_name (which can be identified either by the class name or by passing in an object).

- Learning About Class Properties
  The `get_class_vars()` function returns an associative array containing the names of all properties and their corresponding values defined within the class specified by class_name. 

- Learning About Declared Classes
  The function `get_declared_classes()` returns an array containing the names of all classes defined within the currently executing script, including any standard class defined by PHP and any extension loaded.

- Learning About Object Properties
  The function `get_object_vars()` returns an associative array containing the available non-static properties available to objects restricted by scope and their corresponding values.

- Determining an Object’s Parent Class
  The `get_parent_class()` function returns the name of the parent of the class to which the object belongs. If object’s class is a base class, that class name will be returned.

- Determining Object Type

  The `is_a()` function returns TRUE if object belongs to a class of type class_name or if it belongs to a class that is a child of class_name.
  
- Determining Object Subclass Type
  The `is_subclass_of()` function returns TRUE if object (which can be passed in as type string or object) belongs to a class inherited from class_name and returns FALSE otherwise.

- Determining Method Existence
  The `method_exists()` function returns TRUE if a method named method_name is available to object and returns FALSE otherwise.

### Traits

Traits are a way to implement code reuse where multiple classes implement the
same functionality.

Traits are defined in a similar way to classes but use the keyword trait instead
of class. They can contain both properties and methods but can’t be instantiated to objects.

```php
trait Log {
  function writeLog($message) {
    file_put_contents("log.txt", $message . "\n", FILE_APPEND);
  } 
}

class A {
  function __construct() {
    $this->WriteLog("Constructor A called"); 
  }
  use Log; 
}

class B {
  function __construct() {
    $this->WriteLog("Constructor B called"); 
  }
  use Log; 
}
```

## CH7 Advanced OOP Features

- Object cloning
- Inheritance
- Interfaces
  An interface is a collection of unimplemented method definitions and constants that serves as a class blueprint. Interfaces define exactly what can be done with the class, without getting bogged down with implementation-specific details. 
- Abstract classes
  An abstract class is a class that cannot be instantiated. Abstract classes are intended to be inherited by a class that can be instantiated, better known as a concrete class. Abstract classes can be fully implemented, partially implemented, or not implemented at all.
- Namespaces
  Namespaces help you to more effectively manage your code base by compartmentalizing various libraries and classes according to context.

Advanced OOP Features Not Supported by PHP

- Method overloading
- Operator overloading
- Multiple inheritance

### Object Cloning

```php
class Employee {
  private $name;
  function setName($name) {
    $this->name = $name; 
  }
  function getName() { 
    return $this->name;
  } 
}

$emp1 = new Employee(); 
$emp1->setName('John Smith'); 

$emp2 = $emp1; 
$emp2->setName('Jane Smith');

echo "Employee 1 = {$emp1->getName()}\n"; 
// Employee 1 = Jane Smith
echo "Employee 2 = {$emp2->getName()}\n";
// Employee 2 = Jane Smith
```

using clone keyword

```php
class Employee {
  private $employeeid;
  private $tiecolor;
  // Define a setter and getter for $employeeid
  function setEmployeeID($employeeid) {
    $this->employeeid = $employeeid; 
  }
  function getEmployeeID() { 
    return $this->employeeid;
  }
  
  // Define a setter and getter for $tiecolor
  function setTieColor($tiecolor) {
    $this->tiecolor = $tiecolor; 
  }
  function getTieColor() { 
    return $this->tiecolor;
  } 
}

// Create new Employee object 
$employee1 = new Employee();
// Set the $employee1 employeeid property 
$employee1->setEmployeeID("12345");
// Set the $employee1 tiecolor property 
$employee1->setTieColor("red");

// Clone the $employee1 object 
$employee2= clone $employee1;
// Set the $employee2 employeeid property 
$employee2->setEmployeeID("67890");

// Output the $employee1and $employee2employeeid properties
printf("Employee 1 employeeID: %d <br />", $employee1->getEmployeeID()); 
printf("Employee 1 tie color: %s <br />", $employee1->getTieColor());
// Employee1 employeeID: 12345
// Employee1 tie color: red

printf("Employee 2 employeeID: %d <br />", $employee2->getEmployeeID()); 
printf("Employee 2 tie color: %s <br />", $employee2->getTieColor());
// Employee2 employeeID: 67890
// Employee2 tie color: red
```

### Inheritance

#### Class Inheritance

a class that inherits from another class is known as a child class, or a subclass. the class from which the child class inherits is known as the parent, or base class.

```php
// Define a base Employee class
class Employee {
  private $name;
  
  // Define a setter for the private $name property. 
  function setName($name) {
    if ($name == "") echo "Name cannot be blank!";
    else $this->name = $name; 
  }
  
  // Define a getter for the private $name property
  function getName() {
    return "My name is ".$this->name."<br />"; 
  }
}

// Define an Executive class that inherits from Employee
class Executive extends Employee {
  // Define a method unique to Employee
  function pillageCompany() {
    echo "I'm selling company assets to finance my yacht!"; 
  }
}

// Create a new Executive object 
$exec = new Executive();
// Call the setName() method, defined in the Employee class 
$exec->setName("Richard");

$exec->getName();
// My name is Richard.

$exec->pillageCompany();
// I'm selling company assets to finance my yacht!
```

#### Inheritance and Constructors

If a parent class offers a constructor, it does execute when the child class is instantiated, provided that the child class does not also have a constructor.

Child class constructor

```php
function __construct($name) { 
  parent::__construct($name);
  echo "<p>CEO object created!</p>";
}
```

When` parent::__construct()` was encountered, PHP began a search upward through the parent classes for an appropriate constructor.

### Interfaces
An interface defines a general specification for implementing a particular service, declaring the required functions and constants without specifying exactly how it must be implemented. Implementation details aren’t provided because different entities might need to implement the published method definitions in different ways. 

```php
interface iMyInterface
{
  CONST 1; 
  ... 
  CONST N;
  
  function methodName1(); 
  ...
  function methodNameN();
}


class Class_Name implements iMyInterface
{
  function methodName1()
  {
    // methodName1() implementation
  }
  function methodNameN()
  {
    // methodNameN() implementation
  }
}
```

An interface is a collection of method definitions (names and parameter list) that is used as a form of contract when a class implements one or more interfaces. The contract is completed when a class implements the interface via the implements keyword. All methods must be implemented with the same signature as defined in the interface, or the implementing class must be declared abstract.

#### Implementing a Single Interface

```php
interface iPillage
{
  function emptyBankAccount();
  function burnDocuments();
}

class Executive extends Employee implements iPillage
{
  private $totalStockOptions;
  function emptyBankAccount() {
    echo "Call CFO and ask to transfer funds to Swiss bank account."; 
  }
  function burnDocuments() {
    echo "Torch the office suite."; 
  }
}

class Assistant extends Employee implements iPillage
{
  function takeMemo() {
    echo "Taking memo...";
  }
  function emptyBankAccount() {
    echo "Go on shopping spree with office credit card."; 
  }
  function burnDocuments() {
    echo "Start small fire in the trash can."; 
  }
}
```

#### Implementing Multiple Interfaces

```php
interface iEmployee {...}
interface iDeveloper {...}
interface iPillage {...}
class Employee implements IEmployee, IDeveloper, iPillage { 
  ...
}
class Contractor implements iEmployee, iDeveloper { 
  ...
}
```

#### Determining Interface Existence

The `interface_exists()` function determines whether an interface exists, returning TRUE if it does and FALSE otherwise. 

### Abstract Classes

An abstract class is a class that really isn’t supposed to ever be instantiated but instead serves as a base class to be inherited by other classes. 

```php
abstract class Media
{
  private $title;
  function setTitle($title) {
    $this->title = $title; 
  }
  abstract function setDescription($description)
}

class Newspaper extends Media
{
  function setDescription($description) {
  }
  function setSubscribers($subscribers) {
  } 
}
```

- If you intend to create a model that will be assumed by a number of closely related objects, use an abstract class. If you intend to create functionality that will subsequently be embraced by a number of unrelated objects, use an interface.
- If your object must inherit behavior from a number of sources, use an interface. php classes can implement multiple interfaces but can only extend single (abstract) classes.
- If you know that all classes will share a common behavior implementation, use an abstract class and implement the behavior there. You cannot implement behavior in an interface.
- If multiple classes share the exact same code, use traits.

### Namespaces

It’s not possible to use two classes of the same name within the same script. This is similar to a file system where you can’t have two files with the same name in a directory, but they can exist in two different directories.
There’s a simple way to resolve this issue by using namespaces. All you need to do is assign a namespace to each class.

```php
namespace Aws\S3;
namespace Aws\S3\Command;
namespace Aws\S3\Enum;
namespace Aws\S3\Exception;
namespace Aws\S3\Exception\Parser;
namespace Aws\S3\Iterator;
namespace Aws\S3\Model;
namespace Aws\S3\Model\MultipartUpload;
namespace Aws\S3\Sync;
```
```php
use Aws\S3\Command;
$cmd = new Aws\S3\Command\S3Command();

use Aws\S3\Command as Cmd; 
$cmd = new Cmd\S3Command();
```

Another example:

```php
namespace MyNamespace;

/* This function is MyNamespace\getFile() */
function getFile($path) {
  $content = \file_get_contents($path); 
  return $content;
}
```
