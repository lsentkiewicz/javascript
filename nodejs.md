# nodejs
Standards and best practcies for nodejs applications.


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
│   │   default.js
│   │   production.js
│   │   test.js
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
│       │   UserController.js
│       │   AuthController.js
|
│   app.js    # Main entry point
│   worker.js # An example background worker
```
