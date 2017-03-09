What is Container
--------------------

_Matt Stauffer, Laracon Online 2017_

Container is a glue for Laravel

* Routing
* Middleware
* Requests
* Constructor Injection

### Naming Containers

* The container
* Application
* IOC Container
* DI Container

```php
$logger = app(Illuminate\Log\Writer::class);
```

### Autowiring

Ability to instantiate a class without being given explicit instructions of how to...

Framework will use reflection to determine and provide its dependencies.

### Manual Binding
```php
app()->bind(MySelrvice::class, function(){
	return new MyService('qwyuiyt');
});

$service = app(MyService::class);
```

Can also do with singleton:

```php
app()->singleton(OnlyOne::class, function(){
	return new OnlyOne();
});

app()->instance(OnlyOne::class);
```

### Aliases
```php
app()->alias(MyClass::class, 'myClass');

app()->alias(
	PostmarkMailer::class,
	Mailer::class
);

```

### Service Providers

Central location to bind services for use by your application code, usually grouped by functionality.

#### Anatomy of a service provider

* `register`: binding into container
* `boot`: called after all registrations are complete
* `defer?`: parameter that allows to avoid running service provider if certain bindings are not requested.
* `provides`: if deferred, which bindings should be requested to run the service provider if deferred

### Facades

They are actually rather static proxies...

Examples:

```php
Route::get();
Log::info();
DB::statement();
```

Creating your facade:

1. Bind your class to container
2. Create Facade Class and point to a class name
3. Configure a facade alias

#### Real Time Facades:

Can prepend `Facades/` to the class name to call any class method"

```php
class Thing {
	public function do() 
	{
		//...
	}
}

Facades/Thing::do();
```
