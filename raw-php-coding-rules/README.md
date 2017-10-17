# Raw PHP coding rules


It's understandable that most programmers follow some set of specific coding rules, which he/she is used to, but to keep the whole team in good mental health, some things need to be unified. And if no one have done that before, then brace yourselved, coding rules are coming.

This section was going to be long and verbose, but to be honest, [PSR](http://www.php-fig.org) documents are your friends here, so let's begin:

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

### Naming conventions


Basic rules are described in [PSR-1](http://www.php-fig.org/psr/psr-1/). Please read it. And be descriptive in naming. It's always better to see 

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
 * one-line blocks for control structures (`if()`, `elseif()`, `else`, `for()`, `foreach()` etc.) don't have to be enclosed in curly braces, as long as you put them in new line after control structure, with proper indentation, which you should do either way
 * control structures blocks are more readable, if opening brackets are put in new line, which means using [Allman style](https://en.wikipedia.org/wiki/Indent_style#Allman_style)

```php
if(!$request->isValid())
    return false;
    
if(!$request->method === 'POST')
{
    $postData = $request->post;
    foreach($postData as $dataCell)
        validate($dataCell);
        
    savePostData($postData);
}
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
