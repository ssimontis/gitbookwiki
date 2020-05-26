---
description: Core Web Framework
---

# Giraffe

### HttpHandler

* Building block of Giraffe framework

```text
type HttpFuncResult = Task<HttpContext option>
type HttpFunc = HttpContext -> HttpFuncResult
type HttpHandler = HttpFunc -> HttpContext -> HttpFuncResult
```

* _HttpHandler_ is context, transformation function, and a context result wrapped in a Task and option
* Receives and returns a ASP.NET Core _HttpContext_ object, giving full control of incoming _HttpRequest_ and outgoing _HttpResponse_
* Each handler can process an incoming request and then pass further down peipeline by invoking the next _HttpFunc_ in the pipeline or short circuiting the execution by returning an option of _Some HttpContext_
  * _None_ can be returned if handler does not want to process an incoming request a t all
  * Surrounding handler might pick up incoming request or Giraffe middleware will defer the request to the next _RequestDelegate_ in the pipeline

### Giraffe pipeline vs Core pipeline

* ASP.NET Core pipeline is defined by nested middleware while Giraffe pipeline is defined by functions
* Giraffe pipeline is plugged into wider ASP.NET Core pipeline through _GiraffeMiddleware_
* If the Giraffe pipeline does not process an incoming request \(result was _None_ instead of _Some HttpContext_\), then the AS{.NET Core middleware can process the request or allow another framework to handle it
* Web application constructed via functional composition of handlers which can take advantage of existing .NET Core middleware

### ASP.NET Core Middleware

* Software assembled into an app pipeline to handle requests and responses
* Each component chooses whether to pass request to next pipeline component
  * Has a chance to perform work before and after the next component in the pipeline
* Request delegates are used to build the request pipeline
  * Configured with **Run**, **Map**, and **Use** extension methods
  * Can be specified in-line as an anonymous method \(in-line middleware\) or it can be defined in a reusable class
* When a middleware short-circuits, it is called _terminal middleware_ since further components are prevented from processing the request

#### IApplicationBuilder

* Exception-handling delegates should be called early in pipeline to catch exceptions later on in pipeline
* Simplest application possible uses a single request delegate that calls an anonymous function in response to every HTTP request
* **Use** chains multiple request delegates together
  * Passed context and a _next_ parameter which is used to pass the request to the next pipeline component
  * Failure to call _next_ parameter short-circuits the request
  * Actions are performed both before and after the next delegate
* Never call `next.Invoke` after the response has been sent to the client
  * Changes to _HttpResponse_ after response has been sent throw an exception
  * Protocol violations occur when content length is violated
  * Body format of message may be corrupted
  * **HasStarted** indicates if headers have been sent or if body has been written to
* **Run** delegates do not receive a _next_ parameter
  * First _Run_ delegate is always terminal
  * Used by convention to represent end of pipeline by some middleware components

#### Middleware Order

* ExceptionHandler
  * Use Developer Exception Page outsider of Production for more details
  * Can also use DB error page for development
  * In production, _ExceptionHandler_ route is defined instead
* HSTS
* HttpsRedirection
* Static Files
* Cookie Policy
* Routing
* Request localization
* CORS
* Authentication
* Authorization
* Session
* Custom middlewares
* Endpoint
* Custom middlewares
* Authorization
* Authentication
* ...
* Response

#### Endpoint

* Executes filter pipeline for ASP.NET Core application type
* MVC application
  * Resource filters
  * Model binding
  * Model validation
  * Action filters and User code
  * Exception filters
  * Result filters
* Razor Pages Filter pipeline
  * Resource filters
  * Model binding
  * Model validation
  * Page filters
  * User code
* Order that components are added within _Startup.Configure_ method determines order in which middleware components are invoked on requests and the reverse order for the response
  * Certain pieces of middleware must be placed in precise location or else security and functionality of application will not be possible

#### Branching Pipeline

* **Map** extensions are used as convention for branching
  * Pipeline diverts based upon matches of given request path
  * Branch is only executed if incoming URL matches request path defined for extension method
  * Nesting of mapping calls allows for multiple segments to be processed
* **MapWhen** branches pipeline based on result of a predicate of type _Func&lt;HttpContext, bool&gt;_
  * If predicate is true, control is transferred to a new function with an _IApplicationBuilder_ parameter
* **UseWhen** branches request pipeline based on result of a given predicate 
  * Branch is rejoined to main pipeline if it does not short-circuit or contain a terminal middleware

#### Real World Middleware

* Encapsulated in a class and exposed with an extension methods
* Class constructor receives a _RequestDelegate_ for the **next** function
  * Middleware defined in `Task InvokeAsync(HttpContext context)`
    * Can also use name Invoke as long as still async
    * Additional paramet

