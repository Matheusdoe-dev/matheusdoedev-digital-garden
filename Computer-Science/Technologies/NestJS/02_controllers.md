# Controllers

- Controllers are responsible for handling incoming **requests** and returning **responses** to the client.

![Controllers](https://docs.nestjs.com/assets/Controllers_1.png)

- A controller's purpose is to receive specific requests for the application. The **routing** mechanism controls which controller receives which requests. Frequently, each controller has more than one route, and different routes can perform different actions.

- In order to create a basic controller, we use classes and **decorators**. Decorators associate classes with required metadata and enable Nest to create a routing map (tie requests to the corresponding controllers).

## Routing

- The `@Controller()` decorator is required to define a basic controller.

- Using a path prefix in a `@Controller()` decorator allows us to easily group a set of related routes, and minize repetitive code.

```ts
import { Controller, Get } from "@nestjs/common";

@Controller("cats")
export class CatsController {
  @Get()
  findAll(): string {
    return "This action returns all cats";
  }
}
```

- The `@Get()` HTTP request method decorator before the `findAll()` method tells Nest to create a handle for a specific endpoint for HTTP requests. The endpoint corresponds to the HTTP request method (GET in this case) and the route path.

- What is the route path? The route path for a handler is determined by concatenating the (optional) prefix declared for the controller, and any path specified in the method's decorator, Nest will map `GET /cats` requests to this handler. As mentioned, the path includes both the optional controller path prefix and any path string declared in the request method decorator.

|                        |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ---------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Standard (recommended) | Using this built-in method, when a request handler returns a JavaScript object or array, it will automatically be serialized to JSON. When it returns a JavaScript primitie, however, Nest will send just the value without attempting to serialize it. This makes response handling simple: just return the value, and Nest takes care of the rest. Furthermore, the response's **status code** is always 200 by default, except for POST requests which use 201. We can easily change this behavior by adding the `@HttpCode(...)` decorator at a handle-level. |
| Library-specific       | We can use the library-specific (e.g., Express) **response object**, which can be injected using the `@Res()` decorator in the method handler signature (e.g., `findAll(@Res() response)`). With this approach, you have the ability to use the native response handling methods exposed by that object. `response.status(200).send`.                                                                                                                                                                                                                             |

- WARNING - Nest detects when the handler is using either `@Rest()` or `@Next()`, indicating you have chose the library-specific option. If both approaches are used at the same time, the Standard approach is **automatically disabled** for this single route and will no longer work as expected. To use both approaches at the same time (for example, by injecting the response object to only set cookies/headers but still leave the rest to the framework), you must set the `passthrough` option to `true` in the `@Rest({ passthrough: true })` decorator.

## Request Object

- Handlers often need access to the client **request** details. Nest provides access to the **request object** of the underlying platform (Express by default). We can access the request object by instructing Nest to inject it by adding the `@Req()` decorator to the handler's signature.

```ts
import { Controller, Get, Req } from "@nestjs/comoon";
import { Request } from "express";

@Controller("cats")
export class CatsController {
  @Get()
  findAll(@Req() request: Request): string {
    return "This action returns all cats";
  }
}
```

- HINT - In order to take advantage of express typings (as in the request: Request parameter example above), install @types/express package.

- The request object represents the HTTP request and has properties for the request query string, parameters, HTTP headers, and body. We can use dedicated decorators instead, such as `@Body()` or `@Query()`, which are available out of the box.

## Resources

- Nest provides decorators for all of the standard HTTP methods: `Get()`, `@Post()`, `@Put()`, `@Delete()`, `@Patch()`, `@Options()`, and `@Head()`. In addition, `@All()` defines an endpoint that handles all of them.

```ts
import { Controller, Get, Post } from "@nestjs/commom";

@Controller("cats")
export class CatsController {
  @Post()
  create(): string {
    return "This action adds a new cat";
  }

  @Get()
  findAll(): string {
    return "This action return all cats";
  }
}
```

## Route wildcards

- Pattern based routes are supported as well. The asterisk is used as a wildcard, and will match any combination of characters.

```ts
@Get('ab*cd')
findAll() {
	return 'This route uses a wildcard';
}
```

## Status code

- The response **status code** is always **200** by default, except for POST requests which are **201**. We can change this behavior by adding the `@HttpCode(...)` decorator at a handler level.

```ts
@Post()
@HttpCode(204)
create() {
	return 'This action adds a new cat';
}
```

- HINT - Import `HttpCode` form the `@nestjs/common` package.

- Ofter, your status code isn't static but depends on various factors. In that case, you can use a library-specific **response** (inject using `@Res()`) object (or, in case of an error, throw an exception).

## Headers

- To specify a custom response header, you can either use a `@Header()` decorator or a library-specific response object (and call res.header() directly).

```ts
@Post()
@Header('Cache-Control', 'none')
create() {
	return 'This action adds a new cat';
}
```

- HINT - Import `Header` from the `@nestjs/common` package.

## Redirection

- To redirect a response to a specific URL, you can either use a `@Redirect()` decorator or a library-specific response object (and call `res.redirect()` directly).

- `@Redirect()` takes two arguments, `url` and `statusCode`, both are optional. The default value of `statusCode` is `302` (`Found`) if omitted.

```ts
@Get()
@Redirect('https://nestjs.com', 301)
```

- Sometimes you may want to determine the HTTP status code or the redirect URL dynamically. Do this by returning and object from the route handler method with the shape:

```ts
{
	"url": string,
	"statusCode": number,
}
```

- Returned values will override any arguments passed to the `@Redirect()` decorator.

```ts
@Get('docs')
@Redirect('https://docs.nestjs.com', 302)
getDocs(@Query('version') version) {
	if (version && version === '5') {
		return { url: 'https://docs.nestjs.com/v5/' };
	}
}
```

## References

- [Nest.js docs, Controllers](https://docs.nestjs.com/controllers)
