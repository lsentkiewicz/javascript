# nodejs
Standards and best practices for nodejs applications.


## Table of Contents

1. [Application structure](#application-structure)
1. [Configuration](#configuration)
1. [Npm scripts](#npm-scripts)
1. [Production and development modes](#production-and-development-modes)
1. [Error handling](#error-handling)
1. [Logging](#logging)
1. [Security](#security)



## Application structure
Recommended application structure using expressjs.  
We follow [SOA](https://en.wikipedia.org/wiki/Service-oriented_architecture) design style.
```
project
|   .eslintrc
|   .gitignore
│   README.md 
|   package.json
│
└───config # configuration files for environments
│   │   custom-environment-variables.json
│   │   default.json
│   │   production.json
│   │   test.json
│   
└───src
│   └───controllers # expressjs controllers
│       │   UserController.js
│       │   AuthController.js
|
│   └───models      # database models (usually mongoose or sequalizejs)
│       │   User.js
|
│   └───services    # services perform main business logic
│       │   UserService.js
│       │   AuthService.js
|
│   app.js    # Main entry point
│   worker.js # An example background worker
```

## Configuration
- Use [node-config](https://www.npmjs.com/package/config).  
- Use `.json` extension by default. If values are dynamic/javascript based then use `.js` extension.
- Use UPPERCASE naming. Grouping variables in nested objects is allow.
- Do not put every constant to the configuration file. Some values are never changed, and it's better to keep them in a seperate file e.g. `Const.js`.
- Usually, there should 4 files:
  - `default.json` contains default values for all environments. It should define settings for localhost deployment.
  - `production.json` will override `default.js`. Loaded when `NODE_ENV` is equal to `production`. Do not duplicate settings from `default.json`. Define only differences for example: disable verbose logging.
  - `test.json` similar to `production.json`, but loaded when `NODE_ENV` is equal to `TEST`. Usually, you should use a different database url.
  - `custom-environment-variables.json` it will override all previous settings based on env variables. Read more [here](https://github.com/lorenwest/node-config/wiki/Environment-Variables#custom-environment-variables).

Example configuration files for Heroku application with MongoLab.

```
// custom-environment-variables.json
{
    "PORT": "PORT",
    "MONGODB_URL": "MONGOLAB_URI"
}
```

```
// default.json
{
    "LOG_LEVEL": "debug",
    "PORT": 3500,
    "MONGODB_URL": "mongodb://localhost:27017/my_app"
}
```

```
// production.json
{
    "LOG_LEVEL": "info"
}
```

```
// test.json
{
    "MONGODB_URL": "mongodb://localhost:27017/my_app_test"
}
```

## Npm scripts
- Use npm scripts to start application and run tests.
- Do not use gulp, grunt because they are usually not needed.
- Npm scripts should always set the `NODE_ENV` variable. Use [better-npm-run](https://www.npmjs.com/package/better-npm-run) or [cross-env](https://www.npmjs.com/package/cross-env)
- Recommended scripts:
  - `start` start the application in production mode
  - `dev` start the application in development mode (in watch mode)
  - `lint` run eslint check
  - `lint:fix` run eslint check and autofix problems
  - `spec` run unit tests
  - `spec:watch` run unit tests in watch mode
  - `coverage` create coverage report
  - `test` run lint, unit tests and check coverage

## Production and development modes
Both modes are always required.  
Production mode:
- `NODE_ENV` must be set to `production`
- Only errors must be logged to the console. Do not output any debug information because log files will grow really fast.
- Do not return stack traces in the API response.

Development mode:
- `NODE_ENV` must be set to `development`
- Watch mode must be implemented. The application should auto reload on any file change. Use [nodemon](https://www.npmjs.com/package/nodemon).
- Allowed to log debug information.
- Allowed to return stack traces in the API response.

## Error handling
- Starter packs use service wrappers for proper error handling.
- Do not ignore errors in empty `try ... catch`. Log an error or add a proper inline comment in your code.

## Logging
- It's recommended to use [node-bunyan](https://github.com/trentm/node-bunyan) because it logs by default all information from an error instance (message, stack trace, extra properties).
- By default, do not configure logger to output data to log files. It's handled automatically when deploying to Heroku or when using monitors like [forever](https://www.npmjs.com/package/forever) or [pm2](https://www.npmjs.com/package/pm2).
- Do not log senstive information (also in development mode): passwords, secrets, access tokens, credit cards. 

## Security
- Use [pbkdf2](https://nodejs.org/api/crypto.html#crypto_crypto_pbkdf2_password_salt_iterations_keylen_digest_callback) algorithm for password hashing.
  - Generate a random salt for every user with min. 16 bytes.  
    ```
    require('crypto').randomBytes(16).toString('hex')
    ```
  - The number of iteration must be set to min. `100000`, keylen to `512` and digest to `sha512`.
    It will take ~1s to generate a hash.
  - Always use an async version.
  - In development/test modes you can set a lower value for iterations to speed up development or testing.
  - Why not bcrypt module?
    - [bcryptjs](https://www.npmjs.com/package/bcryptjs) is a pure JS module, and it blocks the whole nodejs thread. It's easy to DDOS application.
    - [bcrypt](https://www.npmjs.com/package/bcrypt) contains extra depedencies
    - `crypto` is a native nodejs module
