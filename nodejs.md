# nodejs
Standards and best practices for nodejs applications.


## Table of Contents

1. [Application structure](#application-structure)



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
