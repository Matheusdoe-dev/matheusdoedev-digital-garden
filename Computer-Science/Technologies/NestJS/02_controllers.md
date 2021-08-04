# Controllers

- Controllers are responsible for handling incoming **requests** and returning **responses** to the client.

![Controllers](https://docs.nestjs.com/assets/Controllers_1.png)

- A controller's purpose is to receive specific requests for the application. The **routing** mechanism controls which controller receives which requests. Frequently, each controller has more than one route, and different routes can perform different actions.

- In order to create a basic controller, we use classes and **decorators**. Decorators associate classes with required metadata and enable Nest to create a routing map (tie requests to the corresponding controllers).

## References

- [Nest.js docs, Controllers](https://docs.nestjs.com/controllers)
