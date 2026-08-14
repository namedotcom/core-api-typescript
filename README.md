# name.com Core API TypeScript Library

[![fern shield](https://img.shields.io/badge/%F0%9F%8C%BF-Built%20with%20Fern-brightgreen)](https://buildwithfern.com?utm_source=github&utm_medium=github&utm_campaign=readme&utm_source=https%3A%2F%2Fgithub.com%2Fnamedotcom%2Fcore-api-typescript)
[![npm shield](https://img.shields.io/npm/v/@namecom/core-api)](https://www.npmjs.com/package/@namecom/core-api)

Official SDK for the name.com Core API.

List endpoints are paginated: pass the `page` query parameter to page through
results, and read the response `links` header for next/previous page links.

Write endpoints that accept an `X-Idempotency-Key` header are safe to retry —
reusing the same key returns the original result instead of repeating the
operation. Keys are valid for 12 hours.

See https://docs.name.com for full guides and the API reference.


## Table of Contents

- [Documentation](#documentation)
- [Installation](#installation)
- [Reference](#reference)
- [Usage](#usage)
- [Environments](#environments)
- [Request and Response Types](#request-and-response-types)
- [Exception Handling](#exception-handling)
- [Advanced](#advanced)
  - [Subpackage Exports](#subpackage-exports)
  - [Additional Headers](#additional-headers)
  - [Additional Query String Parameters](#additional-query-string-parameters)
  - [Retries](#retries)
  - [Timeouts](#timeouts)
  - [Aborting Requests](#aborting-requests)
  - [Access Raw Response Data](#access-raw-response-data)
  - [Logging](#logging)
  - [Custom Fetch](#custom-fetch)
  - [Runtime Compatibility](#runtime-compatibility)
- [Contributing](#contributing)

## Documentation

API reference documentation is available [here](https://docs.name.com).

## Installation

```sh
npm i -s @namecom/core-api
```

## Reference

A full reference for this library is available [here](https://github.com/namedotcom/core-api-typescript/blob/HEAD/./reference.md).

## Usage

Instantiate and use the client with the following:

```typescript
import { NamecomClient } from "@namecom/core-api";

const client = new NamecomClient({ username: "YOUR_USERNAME", password: "YOUR_PASSWORD" });
await client.hello();
```

```typescript
import { NamecomClient } from "@namecom/core-api";

const client = new NamecomClient({ username: "YOUR_USERNAME", password: "YOUR_PASSWORD" });
await client.accountInfo.checkAccountBalance();
```

```typescript
import { NamecomClient } from "@namecom/core-api";

const client = new NamecomClient({ username: "YOUR_USERNAME", password: "YOUR_PASSWORD" });
await client.domains.search({
    keyword: "mydomain"
});
```

```typescript
import { NamecomClient } from "@namecom/core-api";

const client = new NamecomClient({ username: "YOUR_USERNAME", password: "YOUR_PASSWORD" });
await client.domains.listDomains();
```

```typescript
import { NamecomClient } from "@namecom/core-api";

const client = new NamecomClient({ username: "YOUR_USERNAME", password: "YOUR_PASSWORD" });
await client.domains.getDomain({
    domainName: "example.com"
});
```

```typescript
import { NamecomClient } from "@namecom/core-api";

const client = new NamecomClient({ username: "YOUR_USERNAME", password: "YOUR_PASSWORD" });
await client.dns.listRecords({
    domainName: "domainName"
});
```

```typescript
import { NamecomClient } from "@namecom/core-api";

const client = new NamecomClient({ username: "YOUR_USERNAME", password: "YOUR_PASSWORD" });
await client.dns.createRecord({
    domainName: "domainName",
    answer: "answer",
    host: "host",
    type: "A"
});
```

## Environments

This SDK allows you to configure different environments for API requests.

```typescript
import { NamecomClient, NamecomEnvironment } from "@namecom/core-api";

const client = new NamecomClient({
    environment: NamecomEnvironment.Sandbox,
});
```

## Request and Response Types

The SDK exports all request and response types as TypeScript interfaces. Simply import them with the
following namespace:

```typescript
import { Namecom } from "@namecom/core-api";

const request: Namecom.CreateAccountRequest = {
    ...
};
```

## Exception Handling

When the API returns a non-success status code (4xx or 5xx response), a subclass of the following error
will be thrown.

```typescript
import { NamecomError } from "@namecom/core-api";

try {
    await client.hello(...);
} catch (err) {
    if (err instanceof NamecomError) {
        console.log(err.statusCode);
        console.log(err.message);
        console.log(err.body);
        console.log(err.rawResponse);
    }
}
```

## Advanced

### Subpackage Exports

This SDK supports direct imports of subpackage clients, which allows JavaScript bundlers to tree-shake and include only the imported subpackage code. This results in much smaller bundle sizes.

```typescript
import { AccountInfoClient } from '@namecom/core-api/accountInfo';

const client = new AccountInfoClient({...});
```

### Additional Headers

If you would like to send additional headers as part of the request, use the `headers` request option.

```typescript
import { NamecomClient } from "@namecom/core-api";

const client = new NamecomClient({
    ...
    headers: {
        'X-Custom-Header': 'custom value'
    }
});

const response = await client.hello(..., {
    headers: {
        'X-Custom-Header': 'custom value'
    }
});
```

### Additional Query String Parameters

If you would like to send additional query string parameters as part of the request, use the `queryParams` request option.

```typescript
const response = await client.hello(..., {
    queryParams: {
        'customQueryParamKey': 'custom query param value'
    }
});
```

### Retries

The SDK is instrumented with automatic retries with exponential backoff. A request will be retried as long
as the request is deemed retryable and the number of retry attempts has not grown larger than the configured
retry limit (default: 2).

Which status codes are retried depends on the `retryStatusCodes` generator configuration:

**`legacy`** (current default): retries on
- [408](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/408) (Timeout)
- [429](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/429) (Too Many Requests)
- [5XX](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status#server_error_responses) (All server errors, including 500)

**`recommended`**: retries on
- [408](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/408) (Timeout)
- [429](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/429) (Too Many Requests)
- [502](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/502) (Bad Gateway)
- [503](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/503) (Service Unavailable)
- [504](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/504) (Gateway Timeout)

Use the `maxRetries` request option to configure this behavior.

```typescript
const response = await client.hello(..., {
    maxRetries: 0 // override maxRetries at the request level
});
```

### Timeouts

The SDK defaults to a 60 second timeout. Use the `timeoutInSeconds` option to configure this behavior.

```typescript
const response = await client.hello(..., {
    timeoutInSeconds: 30 // override timeout to 30s
});
```

### Aborting Requests

The SDK allows users to abort requests at any point by passing in an abort signal.

```typescript
const controller = new AbortController();
const response = await client.hello(..., {
    abortSignal: controller.signal
});
controller.abort(); // aborts the request
```

### Access Raw Response Data

The SDK provides access to raw response data, including headers, through the `.withRawResponse()` method.
The `.withRawResponse()` method returns a promise that results to an object with a `data` and a `rawResponse` property.

```typescript
const { data, rawResponse } = await client.hello(...).withRawResponse();

console.log(data);
console.log(rawResponse.headers['X-My-Header']);
```

### Logging

The SDK supports logging. You can configure the logger by passing in a `logging` object to the client options.

```typescript
import { NamecomClient, logging } from "@namecom/core-api";

const client = new NamecomClient({
    ...
    logging: {
        level: logging.LogLevel.Debug, // defaults to logging.LogLevel.Info
        logger: new logging.ConsoleLogger(), // defaults to ConsoleLogger
        silent: false, // defaults to true, set to false to enable logging
    }
});
```
The `logging` object can have the following properties:
- `level`: The log level to use. Defaults to `logging.LogLevel.Info`.
- `logger`: The logger to use. Defaults to a `logging.ConsoleLogger`.
- `silent`: Whether to silence the logger. Defaults to `true`.

The `level` property can be one of the following values:
- `logging.LogLevel.Debug`
- `logging.LogLevel.Info`
- `logging.LogLevel.Warn`
- `logging.LogLevel.Error`

To provide a custom logger, you can pass in an object that implements the `logging.ILogger` interface.

<details>
<summary>Custom logger examples</summary>

Here's an example using the popular `winston` logging library.
```ts
import winston from 'winston';

const winstonLogger = winston.createLogger({...});

const logger: logging.ILogger = {
    debug: (msg, ...args) => winstonLogger.debug(msg, ...args),
    info: (msg, ...args) => winstonLogger.info(msg, ...args),
    warn: (msg, ...args) => winstonLogger.warn(msg, ...args),
    error: (msg, ...args) => winstonLogger.error(msg, ...args),
};
```

Here's an example using the popular `pino` logging library.

```ts
import pino from 'pino';

const pinoLogger = pino({...});

const logger: logging.ILogger = {
  debug: (msg, ...args) => pinoLogger.debug(args, msg),
  info: (msg, ...args) => pinoLogger.info(args, msg),
  warn: (msg, ...args) => pinoLogger.warn(args, msg),
  error: (msg, ...args) => pinoLogger.error(args, msg),
};
```
</details>


### Custom Fetch

The SDK provides a low-level `fetch` method for making custom HTTP requests while still
benefiting from SDK-level configuration like authentication, retries, timeouts, and logging.
This is useful for calling API endpoints not yet supported in the SDK.

```typescript
const response = await client.fetch("/v1/custom/endpoint", {
    method: "GET",
}, {
    timeoutInSeconds: 30,
    maxRetries: 3,
    headers: {
        "X-Custom-Header": "custom-value",
    },
});

const data = await response.json();
```

### Runtime Compatibility


The SDK works in the following runtimes:



- Node.js 18+
- Vercel
- Cloudflare Workers
- Deno v1.25+
- Bun 1.0+
- React Native


## Contributing

While we value open-source contributions to this SDK, this library is generated programmatically.
Additions made directly to this library would have to be moved over to our generation code,
otherwise they would be overwritten upon the next generated release. Feel free to open a PR as
a proof of concept, but know that we will not be able to merge it as-is. We suggest opening
an issue first to discuss with us!

On the other hand, contributions to the README are always very welcome!
