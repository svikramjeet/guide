# Raw PHP coding rules

It's understandable that most programmers follow some set of specific coding rules, which he/she is used to, but to keep the whole team in good mental health, some things need to be unified. And if no one have done that before, then brace yourselved, coding rules are coming.

This section was going to be long and verbose, but to be honest, [PSR](http://www.php-fig.org) documents are your friends here, so let's begin:

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>


### Brief or verbose, that is the question

Be descriptive in naming. It's always better to see

```php
getGroupsAllowedToPost(PostType::BLOGPOST);
```

than simply

```
groups(2);
```

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>


### Namespaces and autoloading

Again, [PSR-4](http://www.php-fig.org/psr/psr-4/) covers it properly. Please read it.


### Coding style

Generally you should follow [PSR-2](http://www.php-fig.org/psr/psr-2/) rules, with some exceptions, which will make your code more readable:
 * one-line blocks for control structures (`if()`, `elseif()`, `else`, `for()`, `foreach()` etc.) don't have to be enclosed in curly braces, as long as you put them in new line after control structure, with proper indentation, which you should do either way:

 ```php
 if(!$request->isValid())
     return false; // you can enclose it in braces or leave as is
 ```

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>


### Naming conventions

Basic rules are described in [PSR-1](http://www.php-fig.org/psr/psr-1/). Please read it. Also, if you like to read, see [Naming convention page on Wikipedia](https://en.wikipedia.org/wiki/Naming_convention_(programming)). Or not.

As we agreed, we shall use the following conventions:
 * [Camel Case](https://en.wikipedia.org/wiki/Camel_case) for function names:

```php
public function thisFunctionIsNamed() {}
```

 * [Pascal Case](https://en.wikipedia.org/wiki/PascalCase) for class, trait and interface names:

```php
class VeryImportantClass extends ParentClass
{
    use AlsoVeryImportantTrait;
}
```
 * [Snake Case](https://en.wikipedia.org/wiki/Snake_case) for variables (also class properties, function arguments etc.):

```php
private $my_value;

public function setMyValue($new_value)
{
    $this->my_value = $new_value;
}
```

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>



### Declaring types and PHPDoc annotations

First one will also help catching input errors. PHP7 allows us to use strict typing, it would be shame not to use it! Second (PHPDoc) will make your work with IDE easier.

```php
/**
 * @param User $user
 * @param int $age
 * @return bool
 * @throws \InvalidArgumentException
 */
function setUserAge(User $user, int $age) : bool 
{
    if($age < 1)
        throw new \InvalidArgumentException("Negative age");
        
    return $user->setAge($age); // age setter returns boolean result for this example
}
```

Now, you must admit it's a bit more clear than:

```
function setage($u, $a) { $u->setAge($a); }
```

And yes, it's a lot more keystrokes to make. You'll thank me later. Or someone from your team, working on your code.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>


### No hardcoded values!

Never, ever use hardcoded values. If you need to check type, permission, or anything else against existing values, those values should either come from database (which means they are parametrized and changeable) or be saved in **one place only**.

So, instead of:
```php
if($user->type == "admin" && $entry->category == 2)
```

please do:
```php
#UserType.php
abstract class UserType 
{
    const ADMIN = "admin";
    const SUPERADMIN = "superadmin";
}

#EntryCategory.php
abstract class EntryCategory
{
    const BLOGPOST = 1;
    const STUB = 2;
}

#...and then...

if($user->type == UserType::ADMIN && $entry->category == EntryCategory::STUB)
```

Yes, that's also more keystrokes to make. But later you'll not have to check specific values, you will just use IDE's autocomplete.

Anyway, things mentioned above are just an example. In general you should store such data in DB.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>


### No external assets!


OK, that's not strictly PHP related, but since we're already talking about hardcoding values - never, ever use external 3rd-party sources for assets, other than trusted CDNs. They may disappear anytime and the site will not look very professional:  
![Sadly, this image doesn't exist](http://thisServerDoesntExistBut.com/AuthorOfThisGistIsAwesome.png)

See, what I mean?

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

### Prefer classes for logic containers

**Do not hesitate** to create class for any small functionality. One thing should always be remembered that the class should not just be only a set of getters and setters of database properties. Even the smallest logic can be wrapped in class. **e.g.**
 *We have DateTime functionality by default in php. But if we need to have some fixed logic for whole project related to the dates, then we can extend the class to add desired functionality in our system and we will be using that class insead of default php class. Here is a sample:*
```
    class MyDate extends DateTime {
        public function format(){
            // override the method to return your own format
        }
    }
```

### Handling large datasets
Well, This is the common mistake most of us do, we use the query _select * from <table>_ and start iterating through whole list. This point is important again in case of bulk data manipulation in which we fetch all data and iterating through them without chunks.Lets see the example:
```
$records = User::findAll();

if(count($records)>0){
    foreach($records as $record){
        $record['dummy_field1'] = 'New dummy value';
        $record['dummy_field2'] = 'New dummy value';
        $record['dummy_field3'] = 'New dummy value';
        $record['dummy_field4'] = 'New dummy value';
        $record['dummy_field5'] = 'New dummy value';
        $updated_data[] = $record;
    }
}


```

Now suppose the user records counts to million, so php's default memory ***128MB*** can be consumed so easily. And hence even CLI script would come to crash cause of memory leakage. So better way to handle is as follows:

```
$records = User::findAll()
            ->limit($size)
            ->offset($offset);

while(count($records)>0){
    foreach($records as $record){
        $record['dummy_field1'] = 'New dummy value';
        $record['dummy_field2'] = 'New dummy value';
        $record['dummy_field3'] = 'New dummy value';
        $record['dummy_field4'] = 'New dummy value';
        $record['dummy_field5'] = 'New dummy value';
        $updated_data[] = $record;
    }

    process_to_another_step($updated_data);
    unset($updated_data);
    $offset = $offset + $size;
    $records = User::findAll()
            ->limit($size)
            ->offset($offset);
}


```

In this way we are fetching the data in chunks of say $size=1000, Process them and then fetch another chunk of 1000. 

**NOTE :** Though this was the observation in php 5, but I expect we can still save a lot in php 7 as well inspite of high performance ;).

### Arrays are faster data structure than objects
Lets have a real example to ensure this:

```
 <?php
echo "Iteration time for array : ";
$s = array(); for($x=0;$x<100000;$x++){ $s[] = array("name"=>"Adam","age"=>35); }; echo memory_get_peak_usage();

echo "\nIteration time for objects: ";

$s = array(); for($x=0;$x<100000;$x++){ $o = new ArrayObject; $o->name = "Adam";  $o->age = 35;  $s[] = $o;} echo memory_get_peak_usage();
echo "\n\n";
```

OUTPUT: 
```
Iteration time for array  : 42367400
Iteration time for objects: 61807784

```

Having said this, now we can conclude that toArray() in laravel or any such functionality in any other php framework is better always.


### Avoid using Regex much in your system. 
Regex provides us with a lot of flexibility without second thought, but Every good thing has a dark side.Regex expression have their own runtime engine and it takes mastery to understand it and make performant regex.So the things that can be achieved via php's default string functions, always go for that. In other words, the requirements which can not be achieved via default PHP string function as we don't have other option then choose Regex.

 *According to the official php docs for preg_match:*

``` 
    Do not use preg_match() if you only want to check if one string is contained in
    another string. Use  strpos() or strstr() instead as they will be faster.
```


### Miscellaneous
 * In controllers, we need to process some data and need to echo some output without using some views
    ```
    echo '<hello world>';
    echo '<bye world>';

    ```

    Instead of this, we can concat the output to a single variable and then send to browser in single go. e.g
        ```
        $output = '<hello world>';
        $output .= '<bye world>';
        echo $output;
        ```
    Though Laravel handles content of views very well but sometimes as per needs we need to go for some raw methods. In that case even, it should be handled properly. The logic behind it is that after first echo statement, the headers are sent to the browsers and echoing data multiple times in such splitted ways can lead to some undesired behave. and our desired headers are not set.

 * **Frameworks** are always for our **help**, but they always comes with a **cost**. It always **takes some memory to load the engine of the framework**. So where-ever we find that engine is not going to be involved, then we can skip it. **e.g.**
_Suppose We need to iterate millions of records for some update and it demands performance as its not a one time task, then instead of using it with orm and active records, we can use the direct php connections to fetch and udpate the data._
 * **do not** use [error-supressing `@` operator](http://php.net/manual/en/language.operators.errorcontrol.php#language.operators.errorcontrol).
 * **do not** push (to the repo) code which contains semi-debug functions in it, like `print_r()` and `var_dump()` or `/test/` routes and similar things
 * if possible, rather use `'single quotes'` than `"double quotes"`, PHP will not have to parse them seeking variables used inside.
 * it's good to know, how `array_map()` and `array_walk()` work, but remember that `foreach()` is usually faster
 * when facing the necessity of multi-conditional expression, use `switch()` instead of multiple `if()` instructions. Remeber of `break` - it **may or may not** be necessary at the end of the `case`
 * in less complex conditional return and/or assignment statements, remember that you can use operators like:
   * [Ternary operator](http://php.net/manual/en/language.operators.comparison.php#language.operators.comparison.ternary) (since PHP7 you can ommit the second expression!)
   * [Null coalescing operator](http://php.net/manual/en/language.operators.comparison.php#language.operators.comparison.coalesce) (PHP7)
   * [Spaceship operator](http://php.net/manual/en/migration70.new-features.php#migration70.new-features.spaceship-op) (PHP7)  
 * All php code should not extend beyond a specific width(**80 chars soft limit and 100 chars hard limit**). and There is always a way to break the code in multiple line to increase the readability. Also word wrap feature should be discouraged, because they give us the illusion that code is broken into multiple lines but actually it is not. 



<hr>  

<div align="right">
<a href="../security-serious-business/README.md"><b>NEXT:</b> Security. Serious business...</a>
</div>
<div align="center">
<b><a href="../README.md">INDEX</a></b>
</div>
