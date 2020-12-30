---
layout: post
title: "Reading: PHP Objects, Patterns, and Practice Part3 01"
date: 2020-12-14
categories: [PHP]
---

A note taking from PHP Objects, Patterns, and Practice.

Covers:
- Ch14 Good (and Bad) Practice
- Ch15 PHP Standards

# Part III: Practice

## Ch14 Good (and Bad) Practice

Topics:

- Third-party packages
- Creating and deploying packages
- Version control
- Documentation
- Unit testing
- PHP Standards
- Continuous integration

Overview from chapter 15 to chapter 21

## Ch15 PHP Standards

Topics:

- Why standards
- [PHP Standards Recommendations](https://www.php-fig.org/psr/) (PSR)
- PSR-1: The Basic Coding Standard
- PSR-2: The Coding Style Guide (marked deprecated on 2019 Aug)
  -> PSR-12: Extended Coding Style
- PSR-4: Autoloading

Accepted PHP Standards Recommendations


| PSR Number | Name | Description |
|:----------:|:-----------------------:|:-------------------------------------------------------------------------------------------:|
| 1 | Basic Coding Standard | Fundamentals such as PHP tags and basic naming conventions |
| 2 | Coding Style Guide | Code formatting including rules for placement of braces, argument lists etc. |
| 3 | Logger Interface | Rules for log levels and logger behaviors |
| 4 | Autoloading Standard | Conventions for naming classes and namespaces |
| 6 | Caching Interface | Rules for cache management, including data types, cache item lifetime, error handling, etc. |
| 7 | HTTP Message Interfaces | Conventions for HTTP requests and responses |

### [PSR-1 Basic Coding Standard](https://www.php-fig.org/psr/psr-1/)

- Opening and Closing Tags
- Side Effects

#### Opening and Closing Tags
- `<?php` and `?>`
- `<?=` and `?>`

#### Side Effects
A PHP file should:
- declare classes, interfaces or functions
- perform an action (reading or writing to a file or sending output to the browser)

However, it should not do both.

#### Naming
- Classes: studly caps (upper camel case) `class MyClassName`
- Class constants: upper case with words separated by underscores `const MY_NAME_IS = 'abc'`
- Properties: can be named in any way but keep the consistency `private $myPropertyName`
- Methods: camel case `public function myMethodName()`

### [PSR-2: Coding Style Guide](https://www.php-fig.org/psr/psr-2/)
Deprecated (2019-08-10) and PSR-12 is now recommended.

#### Starting and Ending a PHP Document
- `namespace` declarations should be followed by a blank line
- `use` declarations should be followed by a blank line
- Do not put more than one `use` declaration on the same line

#### Starting and Ending a Class
- The `class keyword`, the `class name`, and `extends` and `implements` must all be placed on the same line
- For classes implements multiple interfaces, each interface name can be included on the same line as the class declaration, or it can be placed indented on its own line

Example:
```php
class EarthGame extends Game implements
    Playable,
    Savable
{
    // class body
}

class EarthGame extends Game implements Playable, Savable
{
    // class body
}
```

##### Declaring Properties
Properties must have a declared visibility (`public`, `private`, or `protected`).
Format could be underscores, camel case, or studly case—but but have to be consistent.

#### Starting and Ending a Method
- All methods must have a declared visibility (`public,` `private`, or `protected`)
- The visibility keyword must **follow** `abstract` or `final`, but **precede** `static`
- Method arguments with default values should be placed **at the end** of the argument list

```php
// Single-line Declarations
final public static function generateTile(int $diamondCount, bool $polluted = false)
{
    // implementation
}

// Multi-line Declarations
public function __construct(
    int $size,
    string $name,
    bool $wraparound = false,
    bool $aliens = false
) {
    // implementation
}
```

#### Lines and Indentation
- Use 4 spaces rather than tabs for indentations
- Wrap each line within 120 characters

#### Calling Methods and Functions
- Do not place a space between the method name and the opening parenthesis
```php
$earthgame = new EarthGame(
    5,
    "earth",
    true,
    true
);
$earthgame::generateTile(5, true);
```

#### Flow of Control
- Flow control keywords (if, for, while, etc.) must be followed by a single space
- The opening parenthesis must not be followed by a space
```php
$tile = [];
for ($x = 0; $x < $diamondcount; $x++) {
    if ($polluted) {
        $tile[] = new PollutionDecorator(new DiamondDecorator(new Plains()));
    } else {
        $tile[] = new DiamondDecorator(new Plains());
    }
}
```

### [PSR-12: Extended Coding Style](https://www.php-fig.org/psr/psr-12/)
Code MUST follow all rules outlined in PSR-1

FYR: 
- [VS Code: PHP Intelephense](https://marketplace.visualstudio.com/items?itemName=bmewburn.vscode-intelephense-client)
- [VS Code: phpfmt - PHP formatter](https://marketplace.visualstudio.com/items?itemName=kokororin.vscode-phpfmt)
- [VS Code: PHP Sniffer](https://marketplace.visualstudio.com/items?itemName=wongjn.php-sniffer)
