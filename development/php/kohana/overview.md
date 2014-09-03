# Kohana

Notes from my attempt to quickly grok the [Kohana Framework](http://kohanaframework.org/).

## Conventions and Coding Style

### Class Names and File Location

Class names in Kohana follow a strict convention to facilitate autoloading.

Conventions:
- Class names should have uppercase first letters with underscores to separate words.
    - Underscores are significant as they reflect the file location in the filesystem.

- CamelCased class names should be used when it is undesirable to create a new directory level.
- All class file names and directory names must match the case of the class as per [PSR-0](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md).
- All classes should be in the ``classes`` directory.


#### Examples

- Controller_Template - ``classes/Controller/Template.php``
- Model_User - ``classes/Model/User.php``
- Database - ``classes/Database.php``


## Model View Controller

### Controllers

- sit between the view and model
- called by ``Request::execute()`` function based on the Route that the URL matched


#### Creating a Controller

To function, a controller must:

- reside in ``classes/Controller`` (or a sub-directory)
- filename must match the classname exactly, e.g. ``Articles.php``
- The class name must map to the filename (with / replaced with _) and each word is capitalized
- Must have the Controller class as a (grand)parent


#### $this->request

- Every controller has this property.
- the ``Request`` object that called the controller


#### $this->response

- Content to return for the request
- Can be used to set status and headers


#### Actions

- created by defining public function on controller with an ``action_`` prefix
- every action should set ``$this->response->body($view)`` to the view file to be sent to the browser

##### Basic Example

```php
public function action_hello()
{
    $this->response->body(View::factory('hello/world')); // This will load views/hello/world.php
}
```


#### Parameters

- accessed by calling ``$this->request->param('name')`` where name is the name defined in the route

##### Example

```php

// Assuming Route::set('example','<controller>(/<action>(/<id>(/<new>)))');
 
public function action_foobar()
{
    $id = $this->request->param('id');
    $new = $this->request->param('new');

    ...
```


#### before() and after()

- execute code before and/or after an action is executed
- could be used to check to see if a user is logged in or not

    
### Models

- manages the behavior and data of the application domain

#### Simple Model

```php

class Model_Post extends Model
{
    public function do_stuff()
    {
        // This is where you do domain logic...
    }
}
```

### Views

- contain display information for your application
- should be "dumb"

#### Creating View Files

- stored in ``views`` directory
- Valid examples include:
    - APPPATH/views/home.php
    - APPPATH/views/pages/about.php
    - APPPATH/views/products/details.php
    - MODPATH/error/views/errors/404.php
    - MODPATH/common/views/template.php

#### Loading Views

- typically loaded in a controller using the ``View::factory`` method
- usually assigned to ``Request::$response`` or to another view

```php
public function action_about()
{
    $this->response->body(View::factory('pages/about'));
}
```

When a view is assigned to a response body like above, it will be rendered when necessary. It can also be rendered by calling the ``View::render`` method or by type casting it to a ``string``.

#### Variables

- set and bind methods are available on a view
    - bind works in a by reference fashion
- variables can also be assigned directly to the view object (``$view->foo = 'bar'``)
