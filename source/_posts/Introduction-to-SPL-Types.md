title: Introduction to SPL Types
date: 2015-10-13 23:39:11
tags: php
---

### Introduction

SPL Types is a basic PHP extension that is little known by the PHP programmers and has a lack of content about, as it is yet experimental, but that implements a way to natively type your variables. It is a set of classes that act in the core and act in the concept of type immutability, where, unless unset, the type of a variable cannot change. We will deeply analyse each exported class of this library in order to explain its usage, but, first, let’s have an example of what I’m talking about:

```php
$integer = new SplInt(50);

try {
  $integer = "Binding a string to an integer will throw an exception!";
} catch (UnexpectedValueException $e) {
  echo $e->getMessage(), PHP_EOL;
}

echo $integer;
```

This example firstly defines `$integer` as a wrapped value of type `SplInt` with the inner value of `50`. The output of this should be:

```
Value not an integer
50
```

Oh, wait. Isn’t there something wrong!? No, there isn’t. We enforce `$integer` to receive an integer, why in the world are we setting a string to it? This will throw a `UnexpectedValueException` and the value remains unchangeable!

### Installation

Take in your mind that SPL Types are yet experimental and are not ready for production usage, therefore, don’t ever mind to use it in the website of the bakery of your customer in the corner. The SPL Types extension doesn’t come built-in with PHP. You need to install it as a **PECL extension**. It makes a little harder on Windows because there is no available dll of this extension, however, it becomes trivial on Linux. First, install the missing dependencies and the PHP 5 dev version:

```
$ sudo apt-get install libpcre3-dev php5-dev
```

Then, run:

```
$ sudo pecl install SPL_Types
```

After that, maybe you should enable the extension in `php.ini`. If you don’t remember where is this file, just do `php --ini | grep Path` and be happy. In my machine, it can be found under `/etc/php5/cli`. Please, note that you may have some other folders in the backward directory, such as `apache2`, where you’ll set in case you are testing in a browser. Add the following to `php.ini`:

```
extension=spl_types.so
```

If you are running as a server, it will be useful to restart the server. We already may be able to use SPL Types. Test with the code above and check if it is up and running. By default, the variables that are instances of a SPL type are objects, however, the native value of the wrapper remains inside a property called `__default` on it. Please, note that the values assigned to an instance of a `SplClass` are not subject to PHP type coercion system. Here, "4" **is not** 4!

### SplClass

This is the abstract class from where all the other types inherit. It is the basis of all the other types, the main wrapper.

#### Synopsis

```php
abstract SplType {
  const NULL __default = null;

  __construct([mixed $initial_value [, bool $strict]])
}
```
### SplInt

Works with *pure* integers. If no value is passed to the constructor, it presupposes to be `0`.

```php
$spl_int = SplInt(10);
$spl_int = 20;         // Pass
$spl_int = 20.1;       // Not an integer
$spl_int = (int) 20.1; // Pass
```

### SplFloat

Works like `SplInt`, however, it is able to cast `1` to `1.0`, different of the strictness of `SplInt`. It works for float-pointing numbers.

```php
$spl_float = SplFloat(10);
$spl_float = 20;     // Pass
$spl_float = "foo";  // Not a float
```
### SplBool

Used to enforce strong typing of the bool type. It inherits `SplEnum`, that we will see bellow. The default value is false and it carries constants named true and false with their primitive value. It is also made to, although an object, be correctly interpreted in `if` statements. The constructor is able to cast the value passed as a parameter to it.

```php
$false = new SplBool(false); // $false = new SplBool(SplBool::false);

if ($false) {
  // Althought, for PHP, objects are true-values, this is never printed.
  echo "I love you <3";
}
```

### SplString

Obviously, enforces static typing on strings. Also inherits `SplClass`. It **only** accepts strings. Any other value will make it throw an exception.

```php
$name = new SplString("Haskell");
$name = 10; // Not a string
echo $name;
```

### SplEnum

My favorite one. It allows the implementation of create enumeration objects natively in PHP, for example, if we want to represent the months of the year:

```php
class Month extends SplEnum {
  const __default = self::January; // Default value

  const January = 1;
  const February = 2;
  const March = 3;
  const April = 4;
  const May = 5;
  const June = 6;
  const July = 7;
  const August = 8;
  const September = 9;
  const October = 10;
  const November = 11;
  const December = 12;
}

$january = new Month; // new Month(Month::January) or new new Month(1)

function setMonth(Month $month) {
  // ...
}

$december = new Month(12);
$elevember = new Month(13); // Not in enum Month
```

### Conclusion

Although yet experimental, it is nice to play with homogeneously typed variables. Currently, there is no much content about SPL Types, but this tends to change with the evolution of the type system of PHP.
