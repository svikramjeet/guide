
# Symfony-specific tips, tricks and rules


First, let's assume we're using Symfony 3 running on PHP7 and we can move on...


### Using `FrameworkExtraBundle` for routing and templates


This helps keeping consistent look and allows you to see everything in one place. Looking at the controller's action you'll know, what route it is using, what method it is handling and what template it is rendering:

```php
#BlogpostController.php
namespace uCreate\BlogBundle\Controller\Blogpost;

use Sensio\Bundle\FrameworkExtraBundle\Configuration\Method;
use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;
use Sensio\Bundle\FrameworkExtraBundle\Configuration\Template;

// ...

/**
 * @Route(path="/blogpost/create", name="blogpost_create")
 * @Template(template="BlogBundle:Blogpost:create.html.twig")
 * @Method("POST")
 */
public function createAction()
{
    // ...
    // no return Response() needed here
}

```

Of course you don't always need to use all of mentioned annotations, for example API methods probably won't use `@Template()` and most of web-specific actions won't need `@Method()`.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

### Names' consistency


The example above should shed a bit of light on this subject. You can see action name has on the action itself (`createAction`, not `createBlogpostAction`), because it's already inside Blogpost-specific controller. Just like TWIG file name is inside `Blogpost` directory etc. But routing name is and `object_action` string, which makes it easier to sort and find in all routes list. 

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

<div align="right">
<a href="../examples-of-poorly-written-code/README.md"><b>NEXT:</b> Examples of poorly written code</a>
</div>
<div align="center">
<b><a href="../README.md">INDEX</a></b>
</div>
<div align="left">
<a href="../laravel-specific-tips-tricks-and-rules/README.md"><b>PREVIOUS:</b> Laravel-specific tips, tricks and rules</a>
</div>