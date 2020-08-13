# PHP Developer Coding Exemplar

## Instructions
This coding exemplar is designed to highlight your skills and areas of expertise with the PHP language in general. We rely mainly on PHP as our web technology of choice. Because of this, it's important that developers have a well-rounded understanding of the PHP language.

Please complete the following questions to the best of your ability. If you are unable to solve a question, please indicate as such. You should feel free to use the PHP manual and other resources to solve these questions; after all, we expect that our developers will use their problem-solving skills at work! Some questions are intended to be difficult, while others are meant to be easy or obvious. Please post your answers in a Gist, using Markdown format, and send the link for review.

This exercise should take approximately one hour to complete.

Good luck!

## Question 1
A client has called and said that they're noticing performance problems on their database when searching for a user by email address. You've checked, and the following query is running:

```
SELECT * FROM users WHERE email = 'user@test.com';
```

You run the EXPLAIN command and get the following results:

```
+----+-------------+-------+------+---------------+------+---------+------+-------+-------------+
| id | select_type | table | type | possible_keys | key  | key_len | ref  | rows  | Extra       |
+----+-------------+-------+------+---------------+------+---------+------+-------+-------------+
|  1 | SIMPLE      | users | ALL  | NULL          | NULL | NULL    | NULL | 10320 | Using where |
+----+-------------+-------+------+---------------+------+---------+------+-------+-------------+
```

Offer a theory as to why the performance is slow.


### *Solution*

If you look at the EXPLAIN result, you can notice that there is no key for the query and the SELECT query will check 10320 rows which will cause performance issue. 

To optimize the SELECT query, you can just add an index to the "email" field using the below query:

```sql
CREATE INDEX user_email ON users (email);
```

## Question 2
You're given a sorted index array that contians no keys. The array contains only integers, and your task is to identify whether or not the integer you're looking for is in the array. Write a function that searches for the integer and returns true or false based on whether the integer is present. Describe how you arrived at your solution.

### *Solution*

```php

function search($array, $value) {
    $left = 0;
    $right = count($array) - 1;
 
    while ($left <= $right) {
        $mid = (int) floor(($left + $right) / 2);
        if ($array[$mid] < $value) {
            $left = $mid + 1;
        } elseif ($array[$mid] > $value) {
            $right = $mid - 1;
        } else {
            return true;
        }
    }
    return false;
}
```

Since array is already sorted, you can use Binary search algorithm to get the best performance.
Average performance of binary search algorithm is O(log n). 
Above is the implementation of binary search function.

## Question 3
During a large data migration, you get the following error: Fatal error: Allowed memory size of 134217728 bytes exhausted (tried to allocate 54 bytes). You've traced the problem to the following snippet of code:

```php

$stmt = $pdo->prepare('SELECT * FROM largeTable');
$stmt->execute();
$results = $stmt->fetchAll(PDO::FETCH_ASSOC);
foreach ($results as $result) {
	// manipulate the data here
}
```
Refactor this code so that it stops triggering the memory error.


### *Solution*

fetchAll function loads all data at once and is memory inefficient for large data sets.
You can just use fetch instead of fetchAll 

```php

$stmt = $pdo->prepare('SELECT * FROM largeTable');
$stmt->execute();
while ($result = $smtp->fetch()) {
    // manipulate the data here
}
```

## Question 4
Write a function that takes a phone number in any form and formats it using a delimiter supplied by the developer. The delimiter is optional; if one is not supplied, use a dash (-). Your function should accept a phone number in any format (e.g. 123-456-7890, (123) 456-7890, 1234567890, etc) and format it according to the 3-3-4 US block standard, using the delimiter specified. Assume foreign phone numbers and country codes are out of scope.

*Note:* This question CAN be solved using a regular expression, but one is not REQUIRED as a solution. Focus instead on cleanliness and effectiveness of the code, and take into account phone numbers that may not pass a sanity check.

### *Solution*

```php

function formatPhoneNumber($phoneNumber, $delimiter = '-') {
    // Check if phone number is valid
    if(!preg_match("/^\(?(\d{3})\)? *[-\.]? *(\d{3}) *[-\.]? *(\d{4})$/i", $phoneNumber)) {
        return false;
    }

    // Format phone number
    $phoneNumber = preg_replace('/[^\d]/', '', $phoneNumber);
    $formatted = substr($phoneNumber, 0, 3) . $delimiter . 
                  substr($phoneNumber, 3, 3) . $delimiter . 
                  substr($phoneNumber, -4);

    return $formatted;
}
```

## Question 5
In production, we'll be caching to memcache. On staging, we'll be caching to APC. In development, we won't be caching at all. Design a library that allows you to store and retrieve data from the cache (only two methods required) and fits the requirements of all three environments. Consider making use of anything appropriate (e.g. traits, classes, interfaces, abstract classes, closures, etc) to solve this problem.

Note: This is an architecture question. Please focus on the design of your library, rather than implementation or the specific caches I've described.

### *Solution*

```php

interface Cache
{
    public function get($key);
    public function set($key, $value);
}

class ProductionCache implements Cache {
    function __construct($host) {
        $this->cache = new Memcache;
        $this->cache->addServer($host);
    }

    public function get($key) {
        return $this->cache->get($key);
    }

    public function set($key, $value) {
        $this->cache->set($key, $value);
    }
}

class StagingCache implements Cache {
    public function get($key) {
        if (apc_exists($key)) {
            return apc_fetch($key);
        }
        return false;
    }

    public function set($key, $value) {
        apc_add($key, $value);
    }
}

class DevelopmentCache implements Cache {
    public function get($key) {
        return false
    }

    public function set($key, $value) {
    }
}

class CacheFactory {
    public function createCache($env = "production") {
        $cache = false;
        if ($env == "production") {
            $cache = new ProductionCache('127.0.0.1');
        } else if ($env == "staging") {
            $cache = new StagingCache();
        } else if ($env == "development") {
            $cache = new DevelopmentCache();
        }
        return false;
    }
}

// Create appropriate cache according to the environment.
$cacheFactory = new CacheFactory();
$cache = $cacheFactory->createCache($environment);
```

## Question 6
Write a complete set of unit tests for the following code:

```php

function fizzBuzz($start = 1, $stop = 100)
{
	$string = '';
	
	if($stop < $start || $start < 0 || $stop < 0) {
		throw new InvalidArgumentException;
	}
	
	for($i = $start; $i <= $stop; $i++) {
		if($i % 3 == 0 && $i % 5 == 0) {
			$string .= 'FizzBuzz';
			continue;
		}
		
		if($i % 3 == 0) {
			$string .= 'Fizz';
			continue;
		}
		
		if ($i % 5 == 0) {
			$string .= 'Buzz';
			continue;
		}
		
		$string .= $i;
	}
	
	return $string;
}
```

### *Solution*

```php

use PHPUnit\Framework\TestCase;

class FizzBuzzTest extends TestCase
{
    public function testCorrect() {
        $cases = [
            [
                'start' => 1,
                'stop' => 2,
                'result' => '12'
            ],
            [
                'start' => 4,
                'stop' => 8,
                'result' => '4BuzzFizz78'
            ],
            [
                'start' => 6,
                'stop' => 20,
                'result' => 'Fizz78FizzBuzz11Fizz1314FizzBuzz1617Fizz19Buzz'
            ],
        ]
        foreach ($cases as $case) {
            $this->assertSame($case['result'], fizzBuzz($case['start'], $case['stop']));
        }
    }

    public function testDefaultArguments() {
        $this->assertSame(fizzBuzz(), fizzBuzz(1, 100));
        $this->assertSame(fizzBuzz(5), fizzBuzz(5, 100));
    }

    public function testExceptionWhenStartBelowZero()
    {
        $this->expectException(InvalidArgumentException::class);
        fizzBuzz(-10, 20);
    }

    public function testExceptionWhenStopBelowZero()
    {
        $this->expectException(InvalidArgumentException::class);
        fizzBuzz(10, -20);
    }

    public function testExceptionWhenStartGreaterThanStop()
    {
        $this->expectException(InvalidArgumentException::class);
        fizzBuzz(50, 20);
    }
}
```

## Question 7
I've developed a class called Select to represent the SELECT statements I'd normally write for a database. I want to be able to use the Select objects as queries and automatically cast them to strings, but when I use them in PDOStatement::execute() I get the following error: Catchable fatal error: Object of class Select could not be converted to string. What should I change in my Select class so that this error goes away?

### *Solution*

To solve the issue, you need to add the __toString method to the Select class that returns the sql query in string format.

```php

class Select
{
    public function __toString(){
        // here returns the query
        $generated_sql_query = ...
        return $generated_sql_query;
    }
}
```

## Question 8

Refactor the following old legacy classes. Don't go too deep, estimate up to 15 minutes of work.
The code shouldn't be ideal, rather adequate for the first step of the refactoring.
Feel free to leave comments in places which can be improved in the future if you see a possibility of that.

```php
<?php

class Document {

    public $user;

    public $name;

    public function init($name, User $user) {
        assert(strlen($name) > 5);
        $this->user = $user;
        $this->name = $name;
    }

    public function getTitle() {
        $db = Database::getInstance();
        $row = $db->query('SELECT * FROM document WHERE name = "' . $this->name . '" LIMIT 1');
        return $row[3]; // third column in a row
    }

    public static function getAllDocuments() {
        // to be implemented later
    }
}

class User {

    public function makeNewDocument($name) {
        if(!strpos(strtolower($name), 'senior')) {
            throw new Exception('The name should contain "senior"');
        }

        $doc = new Document();
        $doc->init($name, $this);
        return $doc;
    }

    public function getMyDocuments() {
        $list = array();
        foreach (Document::getAllDocuments() as $doc) {
            if ($doc->user == $this)
                $list[] = $doc;
        }
        return $list;
    }
}
```

### *Solution*


```php

class Document extends Model {
    public $id;
    public $title;
    public $userID;
    public $name

    function __construct($id, $title, $name, $userID) {
        $this->id = $id;
        $this->title = $title;
        $this->name = $name;
        $this->userID = $userID;
    }

    public static function getAllDocuments() {
        // here returns all documents
    }

    public static function getUserDocuments($userID) {
        // here returns the all documents for specific user
        $db = Database::getInstance();
        $rows = $db->query('SELECT * FROM document WHERE name = "' . $this->name . '"');
        $documents = [];
        foreach ($rows as $row) {
            $documents[] = new Document($row[0], $row[1], $row[2], $row[3]);
        }

        return $documents;
    }

    public static function getDocument($document_id) {
        // here get specific document with $document_id
    }

    // other functions here
}

class User extends Model {
    public $id;
    public $name;
    
    function __construct($id, $name) {
        $this->id = $id;
        $this->name = $name;
    }

    public static function getAllUsers() {
        // here returns all users
    }

    // other functions here
}
```
