# Overview

- The `main.ts` includes an async function, which will **bootstrap** our application.

```ts
import { NestFactory } from "@nestjs/core";
import { AppModule } from "./app.module";

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(3000);
}
bootstrap();
```

- To create a Nest application instance, we use the core `NestFactory` class. `NestFactory` exposes a few static methods that allow creating an application instance. The `create()` method returns an application object, which fulfills the `INestApplication` interface. This object provides a set of methods which are described in the coming chapters.

- A project scaffolded with the Nest CLI creates an initial project structure that encourages developers to follow the convention of keeping each module in its own dedicated directory.

### Platform

- Nest aims to be a _platform-agnostic_ framework. Platform independence makes it possible to create reusable logical parts that developers can take advantage of across several different types of applications.

- There are two HTTP platforms supported out-of-the-box: **express** and **fastify**.

|                  |                                                                                                                          |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------ |
| platform-express | **Express** is a well-known minimalist web framework for node. The `@nestjs/platform-express` package is used by default |
| platform-fastify | **Fastify** is a high performance and low overhead framework highly focused on providing maximum efficiency and speed.   |

- Whichever platform is used, it exposes its own application interface. There are seen respectively as `NestExpressApplication` and `NestFastifyApplication`.

- When you pass a type to the `NestFactory.create()` method, the `app` object will have methods available exclusively for that specific platform. You don't need to specify a type unless you actually want to access the underlying platform API.

```ts
const app = await NestFactory.create<NestExpressApplication>(AppModule);
```

## Parts of Nest overview

|              |                                                             |
| ------------ | ----------------------------------------------------------- |
| Controllers  | Handles incoming requests                                   |
| Services     | Handles data access and business logic                      |
| Modules      | Groups together code                                        |
| Pipes        | Validates incoming data                                     |
| Filters      | Handles errors that occur during request handling           |
| Guards       | Handles authentication                                      |
| Interceptors | Adds extra logic to incoming requests or outgoing responses |
| Repositories | Handles data stored in a DB                                 |

## References

- [Nest.js docs, First Steps](https://docs.nestjs.com/first-steps)
- [Grider S, NestJS: The Complete Developer's Guide, Udemy, The Basics of Nest](https://www.udemy.com/share/104SA42@PW5jVF9cTFQGe0VCO2JOfT5uYA==/)
