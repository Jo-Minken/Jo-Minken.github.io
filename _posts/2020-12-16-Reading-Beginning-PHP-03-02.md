---
layout: post
title: "Reading: Beginning PHP Part3 02"
date: 2020-12-16
categories: [PHP]
---

Notes taking from: Beginning PHP and MySQL From Novice to Professional<br>
Covers:
- Ch28 Introducing PDO
- Ch34 Transactions

## Ch28 Introducing PDO
PDO: [PHP Data Objects](https://www.php.net/pdo)

### PDO’s Database Driver
- 4D
- CUBRID
- Firebird / InterBase 6
- IBM DB2
- Informix
- Microsoft SQL Server
- MySQL
- ODBC
- Oracle
- PostgreSQL
- SQLite

### Installation
PDO is enabled by default as of version PHP 5.1; however, MySQL PDO driver is not.<br>
For PHP 5.1 or newer on the Windows platform, you need to add references to the PDO and driver extensions within the `php.ini file`.

```
extension=php_pdo.dll
extension=php_pdo_mysql.dll
```

### Connecting to a Database Server and Selecting a Database
```php
PDO PDO::__construct(
    string DSN [, 
    string username [, 
    string password [, 
    array driver_opts]]]
)
```

- DSN (Data Source Name) parameter
  - the desired database driver name
  - any necessary database connection variables such as the hostname, port, and database name
- The username and password parameters
- driver_opts array specifies any additional options

```php
$dbh = new PDO('mysql:host=localhost;dbname=chp28', 'webuser', 'secret');

// Placing the Parameters in a File
$dbh = new PDO('uri:file://usr/local/mysql.dsn');
```

#### Referring to php.ini File
```
# php.ini file
pdo.dsn.mysqlpdo = 'mysql:dbname=chp28;host=localhost'
```

In php code:
```php
$dbh = new PDO('mysqlpdo', 'webuser', 'secret');
```

Username and password are not allowed to be included in the DSN.

### Using PDO's Connection-Related Options
- PDO::ATTR_AUTOCOMMIT<br>
  This attribute determines whether PDO will commit each query as it’s executed or will wait for the `commit()` method
- PDO::ATTR_CASE<br>
  This attribute converts the character casing. <br>
  Casing options: `PDO::CASE_UPPER`, `PDO::CASE_LOWER`, `PDO::CASE_NATURAL`
- PDO::ATTR_EMULATE_PREPARES<br>
  Enable this option to take advantage of MySQL's query cache
- PDO::ATTR_ERRMODE<br>
  PDO supports three error-reporting modes, `PDO::ERRMODE_EXCEPTION`, `PDO::ERRMODE_SILENT`, and  `PDO::ERRMODE_WARNING`
- PDO::ATTR_ORACLE_NULLS<br>
  When set to TRUE, this attribute causes empty strings to be converted to NULL<br>
  By default this is set to FALSE
- PDO::ATTR_PERSISTENT<br>
  determines whether the connection is persistent<br>
  By default this is set to FALSE
- PDO::ATTR_PREFETCH<br>
  Prefetching retrieves several rows even if the client is requesting one row at a time<br>
  This option sets the prefetch size in kilobytes, for drivers that support this feature
- PDO::ATTR_TIMEOUT<br>
  This option sets the number of seconds to wait before timing out.<br>
  MySQL currently does not support this option
- PDO::DEFAULT_FETCH_MODE<br>
  Use this option to set default fetching mode: associative arrays, indexed arrays or objects

Attributes exist for helping you learn more about the client, server, and connection status.
The attributes can be retrieved using the method `getAttribute()`.

- PDO::ATTR_SERVER_INFO<br>
  Contains database-specific server information like data pertinent to server uptime, total queries, the average number of queries executed per second ...etc
- PDO::ATTR_SERVER_VERSION<br>
  Database server’s version number
- PDO::ATTR_CLIENT_VERSION<br>
  Database client’s version number
- PDO::ATTR_CONNECTION_STATUS<br>
  Contains database-specific information about the connection status

### Handling Connection Errors
In the case of a connection error, the script immediately terminates unless the returned `PDOException` object is properly caught.

```php
try {
    $dbh = new PDO('mysql:host=localhost;dbname=chp28', 'webuser', 'secret');
} catch (PDOException $exception) {
    echo "Connection error: " . $exception->getMessage();
}
```

PDO offers three error modes:
- PDO::ERRMODE_EXCEPTION<br>
  Throws an exception using the `PDOException` class, which immediately halts script execution and offers information pertinent to the problem.
- PDO::ERRMODE_SILENT<br>
  The default setting. Does nothing if an error occurs.
- PDO::ERRMODE_WARNING<br>
  Produces a PHP `E_WARNING` message if a PDO-related error occurs.

Set error mode:
```php
$dbh->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
```

#### Retrieving SQL Error Codes
The SQL standard offers a list of diagnostic codes used to signal the outcome of SQL queries, known as `SQLSTATE codes`.<br>
The `errorCode()` method is used to return this standard SQLSTATE code.

```php
int PDOStatement::errorCode()
```

#### Retrieving SQL Error Messages
The `errorInfo()` method produces an array consisting of error information pertinent to the most recently executed database operation.

```php
array PDOStatement::errorInfo()
```

The array consists of three values, each referenced by a numerically indexed value between 0 and 2:
- 0: Stores the SQLSTATE code as defined in the SQL standard
- 1: Stores the database driver-specific error code
- 2: Stores the database driver-specific error message

### Getting and Setting Attributes
#### Retrieving Attributes
The `getAttribute()` method retrieves the value of the attribute specified by attribute.

```
mixed PDOStatement::getAttribute(int attribute)
```

---

```php
$dbh = new PDO('mysql:host=localhost;dbname=chp28', 'webuser', 'secret');
echo $dbh->getAttribute(PDO::ATTR_CONNECTION_STATUS);

// localhost via TCP/IP
```

#### Setting Attributes
```
boolean PDOStatement::setAttribute(int attribute, mixed value)
```

---

```php
$dbh->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
```

### Executing Queries
- Executing a query with no result set<br>
  When executing queries with no returned result set, the `exec()` method returns the number of rows affected by the query.
- Executing a query a single time<br>
  When executing a query that returns a result set, the `query()` method should be used.
- Executing a query multiple times:<br>
  Using a *prepared* statement is more efficient.

#### Adding, Modifying, and Deleting Table Data
Pass a query to the `exec()` method, which executes a query and returns the number of rows affected.

```
int PDO::exec(string query)
```

---

```php
$query = "UPDATE products SET title='Painful Aftershave' WHERE sku='ZP457321'";
$affected = $dbh->exec($query);
echo "Total rows affected: $affected";
```

#### Selecting Table Data
The `query()` method executes a query, returning the data as a `PDOStatement object`.

```
PDOStatement query(string query)
```

---

```php
$query = 'SELECT sku, title FROM products ORDER BY id';
// Be aware of SQL injections when building query strings
foreach ($dbh->query($query) AS $row) {
    printf("Product: %s (%s) <br />", $row['title'], $row['sku']);
}
```

If you use `query()` and would like to learn more about the total number of rows affected, use the `rowCount()` method.

### Prepared Statements
- security<br>
  Help guard against SQL injection
- improving performance

Prepared statements are accomplished using two methods
- prepare()<br>
  responsible for readying the query for execution
- execute()
  used to repeatedly execute the query using a provided set of column parameters

#### Using Prepared Statements
```
PDOStatement PDO::prepare(string query [, array driver_options])
```

Two syntax variations are supported, `named parameters` and `question mark parameters`.

```sql
# named parameters:
INSERT INTO products SET sku =:sku, name =:name;

# question mark parameters:
INSERT INTO products SET sku = ?, name = ?;
```

---

```php
$dbh = new PDO('mysql:host=localhost;dbname=chp28', 'webuser', 'secret');
$query = "INSERT INTO products SET sku =:sku, name =:name";
$stmt = $dbh->prepare($query);
```

Then, execute the prepared query by `execute()` method.

```
boolean PDOStatement::execute([array input_parameters])
```

This method requires the input parameters that should be substituted with each iterative execution.
- Pass the values into the method as an array
- Bind the values to their respective variable name or positional offset in the query using the `bindParam()` method

```php
$dbh = new PDO('mysql:host=localhost;dbname=chp28', 'webuser', 'secret');


$query = "INSERT INTO products SET sku =:sku, title =:title";
$stmt = $dbh->prepare($query);

$stmt->execute( [':sku' => 'MN873213', ':title' => 'Minty Mouthwash'] );
$stmt->execute( [':sku' => 'AB223234', ':title' => 'Lovable Lipstick'] );
```

#### Binding Parameters
```php
boolean PDOStatement::bindParam(
    mixed parameter, 
    mixed &variable [, 
    int datatype [, 
    int length [, 
    mixed driver_options]]]
)
```

Parameter Datatypes:
- PDO::PARAM_BOOL<br>
  SQL BOOLEAN
- PDO::PARAM_INPUT_OUTPUT<br>
  Used when the parameter is passedinto a stored procedure and therefore could be changed after the procedure executes
- PDO::PARAM_INT<br>
  SQL INTEGER
- PDO::PARAM_NULL<br>
  SQL NULL
- PDO::PARAM_LOB<br>
  SQL large object
- PDO_PARAM_STMT<br>
  PDO Statement object
- PDO::PARAM_STR<br>
  SQL STRING

The optional length parameter specifies the datatype’s length.<br>
It’s only required when assigning it the `PDO::PARAM_INPUT_OUTPUT` datatype.

```php
$dbh = new PDO('mysql:host=localhost;dbname=chp28', 'webuser', 'secret');

$query = "INSERT INTO products SET sku =:sku, title =:title";
$stmt = $dbh->prepare($query);
$sku = 'MN873213';
$title = 'Minty Mouthwash';


$stmt->bindParam(':sku', $sku);
$stmt->bindParam(':title', $title);

$stmt->execute();

$sku = 'AB223234';
$title = 'Lovable Lipstick';
$stmt->bindParam(':sku', $sku);
$stmt->bindParam(':title', $title);

$stmt->execute();
```

---

```php
$query = "INSERT INTO products SET sku = ?, title = ?";

$stmt->bindParam(1, $sku);
$stmt->bindParam(2, $title);
$stmt->execute();

$stmt->bindParam(1, $sku);
$stmt->bindParam(2, $title);
$stmt->execute();
```

### Retrieving Data
The `columnCount()` method returns the total number of columns returned in the result set.

```php
integer PDOStatement::columnCount()
```

---

```php
$query = 'SELECT sku, title FROM products ORDER BY title';
$result = $dbh->query($query);

printf("There were %d product fields returned.", $result->columnCount());
```

#### Retrieving the Next Row in Result Set
The `fetch()` method returns the next row from the result set, or `FALSE` if the end of the result set has been reached.

```php
mixed PDOStatement::fetch(
    [int fetch_style [, 
    int cursor_orientation[, 
    int cursor_offset]]]
)
```

`fetch_style` parameter:
- PDO::FETCH_ASSOC<br>
  retrieve an array of values indexed by the column name
- PDO::FETCH_BOTH<br>
  default setting.<br>
  retrieve an array of values indexed by both the column name and the numerical offset of the column in the row.
- PDO::FETCH_BOUND<br>
  return TRUE and instead assign the retrieved column values to the corresponding variables as specified in the bindParam() method
- PDO::FETCH_CLASS<br>
  populate an object by assigning the result set’s columns to identically named class properties
- PDO::FETCH_INTO<br>
  Retrieves the column values into an existing instance of a class
- PDO::FETCH_LAZY<br>
  Creates associative and indexed arrays, in addition to an object containing the column properties
- PDO::FETCH_NUM<br>
  retrieve an array of values indexed by the numerical o#set of the column in the row
- PDO::FETCH_OBJ<br>
  create an object consisting of properties matching each of the retrieved column names

The `cursor_orientation` parameter determines which row is retrieved if the object is a scrollable cursor, a result set that allows you to iterate over the rows without fetching all rows.

The `cursor_offset` parameter is an integer value representing the offset of the row to be retrieved relative to the present cursor position.

```php
$dbh = new PDO("mysql:host=localhost;dbname=chp28", "webuser", "secret");
$stmt = $dbh->query('SELECT sku, title FROM products ORDER BY title');
while ($row = $stmt->fetch(PDO::FETCH_ASSOC)) {
    printf("Product: %s (%s) <br />", $row['title'], $row['sku']);
}
```

#### Simultaneously Returning All Result Set Rows
The `fetchAll()` method retrieves all rows in the result set and returns an array.

```php
array PDOStatement::fetchAll([int fetch_style])
```

---

```php
function formatRow($row) {
    return sprintf("Product: %s (%s) <br />", $row[1], $row[0]);
}
$stmt = $dbh->query('SELECT sku, title FROM products ORDER BY title');
$rows = $stmt->fetchAll();

echo explode(array_map('formatRow', $rows));
```

#### Fetching a Single Column
The `fetchColumn()` method returns a single column value located in the next row of the result set.

```php
string PDOStatement::fetchColumn([int column_number])
```

---

```php
$result = $dbh->query('SELECT sku, title FROM products ORDER BY title');

// Fetch the first row first column
$sku = $result->fetchColumn(0);
// Fetch the second row second column
$title = $result->fetchColumn(1);

echo "Product: $title ($sku)";
```

### Setting Bound Columns
The `bindColumn()` method is used to match a column name to a desired variable name.

```php
boolean PDOStatement::bindColumn(
    mixed column, 
    mixed &param [, 
    int type [, 
    int maxlen [, 
    mixed driver_options]]]
)
```

---

```php
$dbh = new PDO('mysql:host=localhost;dbname=chp28', 'webuser', 'secret');
$query = 'SELECT sku, title FROM products WHERE id=2';
$stmt = $dbh->prepare($query);
$stmt->execute();

// Bind according to column offset
$stmt->bindColumn(1, $sku);
// Bind according to column title
$stmt->bindColumn('title', $title);

$row = $stmt->fetch(PDO::FETCH_BOUND);
printf("Product: %s (%s)", $title, $sku);
```

### Working with Transactions
Three PDO methods facilitate transactional tasks: 
- beginTransaction()
- commit()
- rollback()

#### Beginning a Transaction
The `beginTransaction()` method disables autocommit mode.

```php
boolean PDO::beginTransaction()
```

Once either `commit()` or `rollback()` is executed, autocommit mode will be enabled again.

#### Committing a Transaction
The `commit()` method commits the transaction.

```php
boolean PDO::commit()
```

#### Rolling Back a Transaction
The `rollback()` method negates any database changes made since `beginTransaction()` was executed.

```php
boolean PDO::rollback()
```

## Ch34 Transactions
### What’s a Transaction?
A transaction is an ordered group of database operations that are treated as a single unit.
- If all operations complete successfully, the transaction will be committed
- If an operation fails, the transaction will be rolled back

A transaction is defined by its ability to follow four tenets ACID:
- Atomicity: All steps of the transaction must be successfully completed<br>
  otherwise, none of the steps will be committed
- Consistency: All steps of the transaction must be successfully completed<br>
  otherwise, all data will revert to the state that it was in before the transaction began
- Isolation: The steps carried out by any as-of-yet incomplete transaction must remain isolated from the system until the transaction has been deemed complete
- Durability: All committed data must be saved by the system in such a way that, in the event of a system failure, the data can be successfully returned to a valid state

### MySQL’s Transactional Capabilities
Transactions are supported by two of MySQL’s storage engines: InnoDB and NDB.

#### System Requirements
Use `SHOW ENGINES;` command to review all of the storage engines supported by your MySQL server.

```sql
SHOW ENGINES;
```

<br>

| Engine | Support | Transactions | XA  | Savepoints |
|:------:|:-------:|:------------:|:---:|:----------:|
| InnoDB | DEFAULT |     YES      | YES |    YES     |

### Table Creation
The table type is the default on all platforms, which means that no special action is required to create an InnoDB table.<br>
If you want to be explicit when creating the table, you can add the ENGINE keyword.

```sql
CREATE TABLE customers (
    id SMALLINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL
) ENGINE=InnoDB;
```

### A Sample Project
This example demonstrates how two swap-meet participants would go about exchanging an item for cash.
1. Participant Jason requests an item, say the abacus located in participant Jon’s virtual trunk.
2. Participant Jason transfers a cash amount of $12.99 to participantJon’s account.<br>
   The effect of this is the debiting of the amount from Jason’s account and the crediting of an equivalent amount to Jon’s account.
3. Ownership of the abacus is transferred to Jason.

#### Creating Tables and Adding Sample Data
Create the participants Table:
```sql
CREATE TABLE participants (
    id SMALLINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(35) NOT NULL,
    email VARCHAR(45) NOT NULL,
    cash DECIMAL(5,2) NOT NULL
) ENGINE=InnoDB;
```

Create the trunks Table:
```sql
CREATE TABLE trunks (
    id SMALLINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    owner SMALLINT UNSIGNED NOT NULL REFERENCES participants(id),
    name VARCHAR(25) NOT NULL,
    price DECIMAL(5,2) NOT NULL,
    description MEDIUMTEXT NOT NULL
) ENGINE=InnoDB;
```

Add Sample Data:
```sql
INSERT INTO participants SET name="Jason", 
                             email="jason@example.com", 
                             cash="100.00";
INSERT INTO participants SET name="Jon", 
                             email="jon@example.com", 
                             cash="150.00";

INSERT INTO trunks SET owner=2, 
                       name="Abacus", 
                       price="12.99", 
                       description="Low on computing power? Use an abacus!";
INSERT INTO trunks SET owner=2, 
                       name="Magazines", 
                       price="6.00",
                       description="Stack of computer magazines.";
INSERT INTO trunks SET owner=1, 
                       name="Used Lottery ticket",
                       price="1.00",
                       description="Great gift for the eternal optimist.";
```

#### Executing an Example Transaction
```sql
START TRANSACTION;

// Deduct $12.99 from Jason’s account 
UPDATE participants SET cash=cash-12.99 WHERE id=1;

// Credit $12.99 to Jon’s account
UPDATE participants SET cash=cash+12.99 WHERE id=2;

// Transfer ownership of the abacus to Jason
UPDATE trunks SET owner=1 WHERE name="Abacus" AND owner=2;

// Commit the changes
COMMIT;
```
`BEGIN` is an alias of `START TRANSACTION`.
BEGIN / START TRANSACTION accomplish the same task. <br>
It’s recommended to use the latter because it conforms to SQL-99 syntax.

#### Usage Tips
- Issuing the `START TRANSACTION` command is the same as setting the `AUTOCOMMIT` variable to 0. <br>
  The default is `AUTOCOMMIT=1`, which means that each statement is committed as soon as it’s successfully executed.
- Only use transactions when it’s critical that the entire process executes successfully.
- Data-definition language statements (create/drop a database, create/drop/alter tables) cannot be roll back.
- Transactions cannot be nested.
- Take regular snapshots of your InnoDB data and logs by backing up the binary log files, as well as using `mysqldump` to take a snapshot of the data found in each table.

### Building Transactional Applications with PHP
```php
$db = new mysqli("localhost","website","secret","chapter34");

// Disable the autocommit feature
$db->autocommit(FALSE);

// Debit buyer's account
$stmt = $db->prepare("UPDATE participants SET cash = cash - ? WHERE id = ?");
$stmt->bind_param('di', $price, $buyerID);
$stmt->execute();

// Credit seller's account
$query = $db->prepare("UPDATE participants SET cash = cash + ? WHERE id = ?");
$stmt->bind_param('di', $price, $sellerID);
$stmt->execute();

// Update trunk item ownership
$stmt = $db->prepare("UPDATE trunks SET owner = ? WHERE id = ?");
$stmt->bind_param('ii', $buyerID, $itemID);
$stmt->execute();

if ($db->commit()) {
    echo "The swap took place! Congratulations!";
} else {
    echo "There was a problem with the swap!";
}
```
