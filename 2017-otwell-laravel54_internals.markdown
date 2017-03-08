Laravel 5.4 Internals Walkthrough
---------------------------------

_Taylor Otwell, Laracon Online 2017_

### Introduction

Taylor created Laravel while working at Userscape. Laravel is designed to be easy to use,
powerful, and solve a lot of common development problems.

We'll walk through how Laravel is put together at a fundamental level, so that we gain
a better understanding of how Laravel works, from the HTTP side and the artisan side.

### Kernels

Laravel has an HTTP kernel and a Console kernel. 
- app/Http/Kernel.php
- app/Console/Kernel.php

### HTTP Side of Laravel

**index.php**

Begins with autoload, which requires vendor/autoload.php (Composer convention) Then it
requires

**bootstrap/app.php**

...and this creates the 'instance' of Laravel. This is basically a dependency injection
container. (It was built this way because Taylor was originally going to use Silex.)
First the HTTP kernel and the Console kernel are bound into the container. Immediately
following is the Exception Handler. The app instance is then returned from the script.

Many people ask aboutusing the container outside Laravel. According to Taylor this is
easy.  :)  [Example] Pull in Illuminate/Container and create an instance of

- Illuminate/Container/Container, and you can pull things in (dd)

Next in index.php is

**Request Handling**

We grab an instance of the HTTP kernel next and call the handle() method, building a
request from the PHP global variables. The response is then sent out. Then
kernel->terminate is called to remove any _terminable_ middleware.

**The HTTP Kernel** (Foundation/Http/Kernel, not in app, which just has Middleware arrays)

The App and router instances are auto-injected into the kernel at construction time. The
handle method takes a request - not type-hinted because of possible PSR-7 adoption - and
then begins by allowing for form request spoofing (override). But then we run
sendRequestThroughRouter.

[Side note: $request / Request $request is available in the container, but use of that
functionality is not recommended - example used is when a class is bound into the
container as a singelton.]

At this point the framework still can't really _do_ anything. sendRequestThroughRouter
runs the bootstrap() process, which will only run once, and this brings in the
bootstrapper classes. Each of those does one thing (and these can be overridden). Examples:

- LoadEnvironmentVariables
- LoadConfiguration
- HandleExceptions
- RegisterFacades (class aliases)
- RegisterProviders: loops through all service providers in the config/app.php file,
  binding all the additional functionality into the Container.
    - Encryption provider highlighted
    - Queue provider highlighted
- BootProviders

[You can do almost anything (i.e. modules) from a service provider. But you wouldn't want
to try to access something in your provider's register() method from a different provider,
because it's not available yet - use the boot() method instead, because these are called
after all service providers are registered.]

After the kernel boostrap(), we are ready to handle the passed-in request. At this point
we pull in the Pipeline component - it can send objects through other objects. By default
the request is sent through the global middleware and, only after that, to the router. Any
of the middleware in the chain can reject the request and kick it back out (obvious
example: authentication).

[Side note: cannot access the current user in the constructor of a controller. In order
to know the middleware we need to run, we would have to _create_ the controller first.
If you need to get the user very early, for example, you can pass a closure to the
middleware in the controller constructor.]

The router then finds the URI, runs route-level middleware, etc. and returns the response.

The response is then sent to the browser.

### Console Side

This process is similar to the HTTP side - calling autoload and bootstrap to begin with.
Then we create the Console kernel instance, passing in arguments, creating an output
object, and returning a status code.

**Foundation/Console/Kernel**

The Console kernel calls a similar array of bootstrappers on setup (like the HTTP kernel).

The only difference is the SetRequestForConsole boostrapper, which binds a dummy HTTP
request into the container.

Once bootstrapped, in the handle() method the commands are loaded. (Things like Artisan
commands.) Difference from HTTP: there is no Middleware.

### Summary

This simple foundation provides a lot of power, particularly via the service providers.
Hopefully Laravel feels a little less like magic now than it did before. :)

### Questions

**Q. How does Taylor work on Laravel?**

A. He uses a Laravel application on his machine, and another repo that's just for the
framework. He symlinks framework changes into his current Laravel dev site while working
on changes (laracopy script). For packages, he uses a feature of Composer called
path repository. He defines a repository from the package directory and use the package
locally during development just like any other package from Composer.

**Q. Will Dusk be available outside Laravel?**

A. No current plans for that. He views these components as 'exclusive to Laravel', because
they rely on a lot of power that already exists in Laravel and would require a ton of
messy interfaces, etc. to be portable.

**Q. Horizon?**

A. Development is going well. :) Plans are to demo Horizon on stage in New York.

**Q. Laracon US?**

A. Sold out. Matt and Jeff are back, along with Evan and Adam. Two speakers still to be
announced. Mattias Hansen and wife will talk about a successful project. Don't forget
about Laracon EU!
