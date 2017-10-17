
# Laravel-specific tips, tricks and rules

This part is inspired by some of [Laracasts](https://laracasts.com/) videos, or - to be more specific - bad practices promoted by them and probably should be titled *"What Laracasts didn't tell you"*.  
Yes, Laracasts are quite popular and are a good source of "getting started" tutorials for Laravel. But not everything shown there is a good practice - it's more like a shortcut to achieve a particular goal just to show some features. The podcasts contain some ugly hacks and things which will make your work harder in a long term.  
I'll start with the most obvious...


### Care about database structure

Eloquent can do many things for us. But seems like it doesn't create foreign keys in the database, when you are declaring relations in Model files. Maybe it's because Model and Migration are totally separated (unlike Symfony).  
Long story short, always remember to **create foreign keys** for related objects.  
Creating **indexes** is also a good idea for columns which records will frequently be searched by:


```php
# database\migrations\*_create_*_table.php
Schema::create('posts', function (Blueprint $table) {
    $table->increments('id');
    $table->integer('author_id')->unsigned()->index();           // <---- INDEX
    $table->string('title')->index();                            // <---- INDEX
    $table->text('body');
    $table->timestamps();

    $table->foreign('author_id')->references('id')->on('users'); // <---- FOREIGN KEY
});
```

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>


### Named routes are your friends

Every time you write

```php
#web.php
Route::get('/posts/{post}', 'PostController@show');
```
```blade
{{--show.blade.php--}}
<a href="/posts/{{ $post->id }}">{{ $post->title }}</a>
```

...a kitten dies. Why is that? Let's imagine you have a lot of such links in your `blade` templates. And suddenly your clients says *"You know, we are changing company image and 'posts' suggests we are some blog... we  now want the user to see 'articles', because we are adult company"*.  
Now, what do you do? *You need to change that href in every template.* And of course it is not a big problem, you just need to use some "search and replace" feature, of course in all files, watching out not to change too much, because you probably have similar routes somewhere etc. etc...

What **you should** do is to name a route:

```php
#web.php
Route::get('/posts/{post}', 'PostController@show')->name('post_show'); // or 'post_show_by_id' if there's more
```
```blade
#show.blade.php
<a href="{{ route('post_show', ['post' => $post]) }}">{{ $post->title }}</a>
```

Remember about [hardcoded values](#no-hardcoded-values)? That's generally the same thing. Name your routes. Save kittens.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

### Static model functions are a no-no.

In a perfect world of MVC, Model classes should contain only getters and setters. Let's not go that far. Laravel already allows us to use a lot of static functions directly from Model class, but that doesn't mean we need to make Model files more messy. Let's introduce a simple rule: **if it doesn't call `$this`, it doesn't belong in Model file**.  
Of course that's oversimplified on purpose, but you get the point - if a function doesn't operate on Model's instance, it should not be in Model's code. In Model's code there should be only instance-related functions.  
So where? **Repository** is your friend. If you implement filters/helpers/anything that operates on a set of Models, put it in the Repository file.

Simple example:

```php
#app/Repositories/TagRepository.php

namespace App\Repositories;

use App\Tag;
use Illuminate\Support\Collection;

class TagRepository
{
    public static function purgeUnusedTags()
    {
        /** @var $unusedTagIds Collection */
        $unusedTagIds = Tag::doesntHave('posts')->get()->pluck('id')->toArray();
        if(count($unusedTagIds))
            Tag::destroy($unusedTagIds);
    }
}
```

Of course if for some reasons you need to instantiate the Repository and use non-static functions, there's no problem with that.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>


### `Use` instead of full names, non-string class names etc.

It may seem not crucial, but seeing Laracasts typing fully qualified class name in one file and then short name (with `use`) in another may cause epilepsy. So let's set this straight: **use short class names**.

```php
// BAD:
$post = \App\Post::find(1)
```
```php
// GOOD:
use App\Post;
// ...
$post = Post::find(1);
```

Yes, in this exaple it's one line of code more. It will repay you in keystrokes after you use the same class in a couple of actions.

Another thing is passing class name as function argument. String format (with namespace) makes no sense here and it's less readable, not to mention not every editor/IDE will parse it as class name:

```php
// BAD:
$factory->define('\App\User', function (Faker $faker) { // ...
```
```php
// GOOD:
use \App\User;
// ...
$factory->define(User::class, function (Faker $faker) { // ...
```

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

### Make your life easier with some packages

1. Don't be afraid to use [FormFacade and HtmlFacade](https://laravelcollective.com/docs/master/html), it will make Blade files more readable in most cases.

2. Become familiar with [Annotations](https://laravelcollective.com/docs/master/annotations), whih will save you a lot of time and keystrokes, while still keeping the code readable and consistent. 

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

<div align="right">
<a href="../symfony-specific-tips-tricks-and-rules/README.md"><b>NEXT:</b> Symfony-specific tips, tricks and rules</a>
</div>
<div align="center">
<b><a href="../README.md">INDEX</a></b>
</div>
<div align="left">
<a href="../security-serious-business/README.md"><b>PREVIOUS:</b> Security. Serious business...</a>
</div>