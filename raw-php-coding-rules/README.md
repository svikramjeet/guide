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


### Miscellaneous

 * **do not** use [error-supressing `@` operator](http://php.net/manual/en/language.operators.errorcontrol.php#language.operators.errorcontrol).
 * **do not** push (to the repo) code which contains semi-debug functions in it, like `print_r()` and `var_dump()` or `/test/` routes and similar things
 * if possible, rather use `'single quotes'` than `"double quotes"`, PHP will not have to parse them seeking variables used inside.
 * it's good to know, how `array_map()` and `array_walk()` work, but remember that `foreach()` is usually faster
 * when facing the necessity of multi-conditional expression, use `switch()` instead of multiple `if()` instructions. Remeber of `break` - it **may or may not** be necessary at the end of the `case`
 * in less complex conditional return and/or assignment statements, remember that you can use operators like:
   * [Ternary operator](http://php.net/manual/en/language.operators.comparison.php#language.operators.comparison.ternary) (since PHP7 you can ommit the second expression!)
   * [Null coalescing operator](http://php.net/manual/en/language.operators.comparison.php#language.operators.comparison.coalesce) (PHP7)
   * [Spaceship operator](http://php.net/manual/en/migration70.new-features.php#migration70.new-features.spaceship-op) (PHP7)  



<hr>  

<div align="right">
<a href="../security-serious-business/README.md"><b>NEXT:</b> Security. Serious business...</a>
</div>
<div align="center">
<b><a href="../README.md">INDEX</a></b>
</div>
