# GraphQL Server for Express, Connect, HAPI and Koa

[![npm version](https://badge.fury.io/js/apollo-server.svg)](https://badge.fury.io/js/apollo-server)
[![Build Status](https://travis-ci.org/apollostack/apollo-server.svg?branch=master)](https://travis-ci.org/apollostack/apollo-server)
[![Coverage Status](https://coveralls.io/repos/github/apollostack/apollo-server/badge.svg?branch=master)](https://coveralls.io/github/apollostack/apollo-server?branch=master)
[![Get on Slack](https://img.shields.io/badge/slack-join-orange.svg)](http://www.apollostack.com/#slack)

Apollo Server is a community-maintained open-source GraphQL server. It works with all Node.js HTTP server frameworks: Express, Connect, HAPI and Koa.

## Principles

Apollo Server is built with the following principles in mind:

* **By the community, for the community**: Apollo Server's development is driven by the needs of developers
* **Simplicity**: by keeping things simple, Apollo Server is easier to use, easier to contribute to, and more secure
* **Performance**: Apollo Server is well-tested and production-ready - no modifications needed


Anyone is welcome to contribute to Apollo Server, just read [CONTRIBUTING.md](./CONTRIBUTING.md), take a look at the [roadmap](./ROADMAP.md) and make your first PR!


## Getting started

Apollo Server is super-easy to set up. Just npm-install apollo-server, write a GraphQL schema, and then use one of the following snippets to get started. For more info, read the [Apollo Server docs](http://docs.apollostack.com/apollo-server).

### TypeScript

If you want to build your GraphQL server using TypeScript, Apollo Server is the project for you.

```sh
npm install apollo-server
typings i -SG dt~express dt~express-serve-static-core dt~serve-static dt~mime dt~hapi dt~cookies dt~koa
```

For using the project in JavaScript, just run `npm install --save apollo-server` and you're good to go!

### Express

```js
import express from 'express';
import { apolloExpress } from 'apollo-server';

const myGraphQLSchema = // ... define or import your schema here!
const PORT = 3000;

var app = express();

app.use('/graphql', bodyParser.json(), apolloExpress({ schema: myGraphQLSchema }));

app.listen(PORT);
```

### Connect
```js
import connect from 'express';
import { apolloConnect } from 'apollo-server';

const PORT = 3000;

var app = connect();

app.use('/graphql', bodyParser.json(), apolloConnect({ schema: myGraphQLSchema }));

app.listen(PORT);
```

### HAPI
```js
import hapi from 'hapi';

const server = new hapi.Server();

const HOST = 'localhost';
const PORT = 3000;

server.connection({
    host: HOST,
    port: PORT,
});

server.register({
    register: new ApolloHAPI(),
    options: { schema: myGraphQLSchema },
    routes: { prefix: '/graphql' },
});
```
### Koa
```js
import koa from 'koa';
import koaRouter from 'koa-router';
import { apolloKoa } from 'apollo-server';

const app = new koa();
const router = new koaRouter();
const PORT = 3000;

app.use(koaBody());

router.post('/graphql', apolloKoa({ schema: myGraphQLSchema }));
app.use(router.routes());
app.use(router.allowedMethods());
app.listen(PORT);
```

## Options

Apollo Server can be configured with an options object with the the following fields:

* **schema**: the GraphQLSchema to be used
* **context**: the context value passed to resolvers during GraphQL execution
* **rootValue**: the value passed to the first resolve function
* **formatError**: a function to apply to every error before sending the response to clients
* **validationRules**: additional GraphQL validation rules to be applied to client-specified queries
* **formatParams**: a function applied for each query in a batch to format parameters before execution
* **formatResponse**: a function applied to each response after execution

All options except for `schema` are optional.

### Whitelisting

The `formatParams` function can be used in combination with the `OperationStore` to enable whitelisting.

```js
const store = new OperationStore(Schema);
store.put('query testquery{ testString }');
apolloOptions = {
    schema: Schema,
    formatParams(params) {
        params['query'] = store.get(params.operationName);
        return params;
    },
};
```

## Differences to express-graphql

Apollo Server and express-graphql are more or less the same thing (GraphQL middleware for Node.js), but there are a few key differences:

* express-graphql works with Express and Connect, Apollo Server supports Express, Connect, HAPI and Koa.
* express-graphql's main goal is to be a minimal reference implementation, whereas Apollo Server's goal is to be a complete production-ready GraphQL server.
* Compared to express-graphql, Apollo Server has a simpler interface and supports exactly one way of passing queries.
* Apollo Server separates serving GraphiQL (GraphQL UI) from responding to GraphQL requests.
* express-graphql contains code for parsing HTTP request bodies, Apollo Server leaves that to standard packages like body-parser.
* Includes an `OperationStore` to easily manage whitelisting
* Built with TypeScript

Despite express-graphql being a reference implementation, Apollo Server is actually easier to understand and more modular than express-graphql.

That said, Apollo Server is heavily inspired by express-graphql (it's the reference implementation after all). Rather than seeing the two as competing alternatives, we think that they both have separate roles in the GraphQL ecosystem: express-graphql is a reference implementation, and Apollo Server is a GraphQL server to be used in production and evolve quickly with the needs of the community. Over time, express-graphql can adopt those features of Apollo Server that have proven their worth and become established more widely.
