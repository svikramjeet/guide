
# PHP tips and good practices

This section will contain some tips and common good practices.


### Validating input data


Before we proceed to security rules, what's worth mentioning is checking input. Function arguments should be validated and invalid arguments should be handled by using default values (if possible) or throwing `\InvalidArgumentException()` with proper message.

**NOTE**:

 * **data coming directly from user should always be validated**. This applies to every piece of data from user side, including form data, URL/request parameters and everything else. Seriously, everything. Even if you save/use client's `User-Agent` header, validate it first.
 * if you write internal function, which may be used by anyone else (or even you a couple of weeks later, when you may not remember everything perfectly), it should also contain input checking routines

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

<hr>

<div align="right">
<a href="../security-serious-business/README.md"><b>NEXT:</b> Security. Serious business...</a>
</div>
<div align="center">
<b><a href="../README.md">INDEX</a></b>
</div>
<div align="left">
<a href="../raw-php-coding-rules/README.md"><b>PREVIOUS:</b> Raw PHP coding rules</a>
</div>