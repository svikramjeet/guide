# Raw PHP coding rules

It's understandable that most programmers follow some set of specific coding rules, which he/she is used to, but to keep the whole team in good mental health, some things need to be unified. And if no one have done that before, then brace yourselved, coding rules are coming.

This section was going to be long and verbose, but to be honest, [PSR](http://www.php-fig.org) documents are your friends here, so let's begin:

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

### Namespaces and autoloading


Again, [PSR-4](http://www.php-fig.org/psr/psr-4/) covers it properly. Please read it.

<div align="right">
<a href="../php-tips-and-good-practices/README.md"><b>NEXT:</b> PHP tips and good practices</a>
</div>
<div align="center">
<b><a href="../README.md">INDEX</a></b>
</div>
