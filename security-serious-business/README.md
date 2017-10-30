
# Security. Serious business...

This part is not meant to make you and expert hacker or penterster. It is supposed to show you the basic concept of securing web applications if you are not familiar with it.  

### The Basics


Although most of modern PHP frameworks are quite well secured against popular attack vectors by default, you are not excused from knowing at least some of the abbreviations. Make sure you know at least the basic ideas of the following attacks:
 * [XSS (Cross-Site Scripting)](https://www.owasp.org/index.php/Cross-site_Scripting_(XSS))
 * [SQL Injection](https://www.owasp.org/index.php/SQL_Injection) / [Blind SQL Injection](https://www.owasp.org/index.php/Blind_SQL_Injection)
 * [CSRF (Cross-Site Request Forgery)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF))
 * [Code Injection](https://www.owasp.org/index.php/Code_Injection)

If you want to know more, feel free to dive into [OWASP's knowledge database](https://www.owasp.org).

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>


### User input validating

This part alone could take tons of text, but we should cover here basics only, so just a mention: malicious user input may cause any of problems mentioned above, as well as many more, that's why it's important to sanitize it. 

Function arguments should be validated and invalid arguments should be handled by using default values (if possible) or throwing `\InvalidArgumentException()` with proper message.

**NOTE**:

 * **data coming directly from user should always be validated**. This applies to every piece of data from user side, including form data, URL/request parameters and everything else. Seriously, everything. Even if you save and/or use client's `User-Agent` header, validate it first.
 * if you write internal function, which may be used by anyone else (or even you a couple of weeks later, when you may not remember everything perfectly), it should also contain input checking routines
 * **client-side data validation is worth nothing** in terms of security. Always assume the user is a highly skilled hacker and tampers all requests

**Don't reinvent the wheel**, use existing mechanisms for avoiding XSS and similar. Remember, simple `addslashes()` **will not work**.  

And **always parametrize queries. Always.**

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>




### `Exec()`


Just don't use it, if you don't need to.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

### Throwing the trash out


Many times you will be tempted to use `/test/` route to test `testAction()` of `TestController.php`, or something like that. That's perfectly normal, you can do that. But please, **don't commit it to code repo**. And if you do (accidentially of course), seriously, **remove it before deployment to production**.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

<div align="right">
<a href="../laravel-specific-tips-tricks-and-rules/README.md"><b>NEXT:</b> Laravel-specific tips, tricks and rules</a>
</div>
<div align="center">
<b><a href="../README.md">INDEX</a></b>
</div>
<div align="left">
<a href="../raw-php-coding-rules/README.md"><b>PREVIOUS:</b> Raw PHP coding rules</a>
</div>