# Table of Contents

* [Introduction](#introduction) (read it, please, it's not typical *blah blah blah*)
* [Raw PHP coding rules](raw-php-coding-rules/README.md#raw-php-coding-rules)
    * [Naming conventions](raw-php-coding-rules/README.md#naming-conventions)
    * [Brief or verbose, that is the question](raw-php-coding-rules/README.md#brief-or-verbose-that-is-the-question)
    * [Coding style](raw-php-coding-rules/README.md#coding-style)
    * [Namespaces and autoloading](raw-php-coding-rules/README.md#namespaces-and-autoloading)
* [PHP tips and good practices](php-tips-and-good-practices/README.md#php-tips-and-good-practices)
    * [Validating input data](php-tips-and-good-practices/README.md#validating-input-data)
    * [Declaring types and PHPDoc annotations](php-tips-and-good-practices/README.md#declaring-types-and-phpdoc-annotations)
    * [No hardcoded values!](php-tips-and-good-practices/README.md#no-hardcoded-values)
    * [No external assets!](php-tips-and-good-practices/README.md#no-external-assets)
* [Security. Serious business...](security-serious-business/README.md#security-serious-business)
    * [The Basics](security-serious-business/README.md#the-basics)
    * [User input validating](security-serious-business/README.md#user-input-validating)
    * [`Exec()`](security-serious-business/README.md#exec)
    * [Throwing the trash out](security-serious-business/README.md#throwing-the-trash-out)
* [Laravel-specific tips, tricks and rules](laravel-specific-tips-tricks-and-rules/README.md#laravel-specific-tips-tricks-and-rules)
    * [Care about database structure](laravel-specific-tips-tricks-and-rules/README.md#care-about-database-structure)
    * [Named routes are your friends](laravel-specific-tips-tricks-and-rules/README.md#named-routes-are-your-friends)
    * [Static model functions are a no-no](laravel-specific-tips-tricks-and-rules/README.md#static-model-functions-are-a-no-no)
    * [`Use` instead of full names](laravel-specific-tips-tricks-and-rules/README.md#use-instead-of-full-names)
    * [Make your life easier with some packages](laravel-specific-tips-tricks-and-rules/README.md#make-your-life-easier-with-some-packages)
 * [Symfony-specific tips, tricks and rules](symfony-specific-tips-tricks-and-rules/README.md#symfony-specific-tips-tricks-and-rules)
    * [Using `FrameworkExtraBundle` for routing and templates](symfony-specific-tips-tricks-and-rules/README.md#using-frameworkextrabundle-for-routing-and-templates)
    * [Names' consistency](symfony-specific-tips-tricks-and-rules/README.md#names-consistency)
 * [Examples of poorly written code](examples-of-poorly-written-code/README.md#examples-of-poorly-written-code)
 * [Summary](summary/README.md#summary)


# Introduction

This document was created to help you keep your code consistent and clean. It covers basic PHP coding rules as well as Symfony-specific coding standards, which you are encouraged to follow for the greater good of us all and for the glory of the Company. Please, feel encouraged, or else.

**First thing you need to read**, is [Clean Code concepts for PHP](https://github.com/ucreate-marcinw/clean-code-php). It's a lot of reading, but it is a really valuable set of instructions and rules.

**Second**, if you have some time - and you should find some for it - is [PHP the Right Way](http://www.phptherightway.com), which has everything you need to know and something more.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>
