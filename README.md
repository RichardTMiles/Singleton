# The Singleton (Skelleton) Container Trait


This code allows classes to hold a state by serializing it's values in the session. 

It also allows you to replaces previosly defined methods in already invoked classes.

```
// File1.php
session_start();

class Request
{
    use Singleton;
    const Singleton = true;   // This turns on data serialization (storage) for all other variables in the class
    
    public $foo;
    
    public function __construct(){
       return $foo;
    }
    
    public function $bar(){
       return $foo;
    }
}


// First run 
include 'File1.php';
$object = Request::getInstance('Hello World.');       // invokes the constructor and Sets the value of foo
print $object->foo and die;                           // prints Hello and invokes the destructor ( in Singleton ) 

// Second run 
include 'File1.php';
$object = new Request;
print $object->foo;                             // still prints Hello 
```

Using Singleton to minipulate classes quickly

``` third run 
include 'File1.php';
$object = Request::getInstance();

$object->hello = 'We're assigning a new variable.';

$object->superClosure = function () { print $this->foo . PHP_EOL . $this->hello; };

$object->superClosure();     // Output: Hello World. We're assigning a new variable.

exit(1);

//Forth execution  
include 'File1.php';
$object = Request::setInstance(new class {

    public $foo;
    public $bar;

    public function __construct(){
        print 'Fourth exe';
        $oldClass = Request::getInstance();
        $this->foo = $oldClass->foo;
        $this->bar = $oldClass->hello;
    }
});



```





