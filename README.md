# The Singleton (Skelleton) Container Trait

Better serialization & class minipulation

This code allows classes to hold a state by serializing it's data variables in the session. 
    
It also allows you to replaces previosly defined namespaces, classes, and methods.

Class minipulation, i.e. replacing a method with a closure, will not hold state. 

```php
// File1.php
session_start();

class Request
{
    use Singleton;
    const Singleton = true;   // This turns on data serialization (storage) for all other variables in the class
                              // even if implicit, or not directly defined   
    public $foo;
    
    public function __construct($stuff=''){ 
        if (empty($stuff) && isset($this->foo))
            return;
        $this->foo = $stuff;
    }
    
    public function $bar(){
       print $foo;
    }
}
```

Proceeding files (code snippets) will represent entry points to our application 
``` php
include 'File1.php';

$object = Request::getInstance('Hello World.');    // invokes the constructor and Sets the value of foo

$object->bar();                                 // Prints $foo which is = 'Hello World'

$object->bar = 'implicit definition';           // bar was not defined prior, but will hold state between requests

print $object->foo . ' ' . $object->bar;        // Print "Hello World. implicit definition";

die;                                            // invokes the destructor in Singleton (exit/die not required) 
```

Second run instance ( a HTTP(S) / AJAX / SOCKET / ect.. request )  
```PHP
include 'File1.php';

// The following code does not use getInstance(), so our data is not retreived 
$object = new Request;        // Don't pass a value to the constructor this time..      
print $object->foo;           // prints: '' 

$object = Request::getInstance();    // invokes the constructor and Sets the value of foo

print $object->foo;           // still prints: 'Hello World' 

print $object->bar            // prints: 'implicit definition'

$object->bar();               // Prints $foo which is = 'Hello World'

$object->superClosure = function () { print $this->foo . PHP_EOL . $this->hello; };

$object->superClosure();     // Output: Hello World. We're assigning a new variable.
```

Using Singleton to minipulate classes quickly does not requre the  ~optional~  
    const Singleton = true;  // activate data serialization 

Class modifications do not hold state. The following will result in an error.
    throw new \Exception("There is valid method or closure with the given name '$methodName' to call");

To overide an existing method we must use syntax that will most likely cause your editor to yell at you.
Concider the following class

```PHP                  // remember this is the start of our app 
include 'File1.php';

class Bar () {
   use Singleton;       // we're not storing this classes data on destruct 

   private $foo;
   
   public function setFoo($foo){ 
       $this->foo = $foo;
   }
   
   private function fooToss(){
        print 'A random method implementation' . $this->foo;
   }
}

$classBar = Request::getInstance();

// Lets override a class and namespace, this my be useful when geveloping around interfaces or debugging 

$request = Request::setInstance(new class {      // Takes over the namespace

    public $foo;
    public $bar;

    public function __construct(){
        print 'Fourth exe';
        $oldClass = Request::getInstance();
        $this->foo = $oldClass->foo;
        $this->bar = $oldClass->hello;
    }
});

// after the setInstance() method is complete the previous class will destruct and store in the session
// other files may now use the getInstance() to retreive our newly created class

$request = Request::getInstance();  // Will return the new quickly created class

$request->superClosure();     // This will throw a new Exception 
        // -- There is valid method or closure with the given name 'superClosure' to call

$request->superClosure = function () { print $this->foo . PHP_EOL . $this->hello; };

$request->superClosure();     // Output: Hello World. We're assigning a new variable.

$request = Request::setInstance($classBar);     // You getting this? class bar is now retreivable with Request::getInstance()

$request->superClosure();     // This will throw a new Exception 
        // -- There is valid method or closure with the given name 'superClosure' to call

```




