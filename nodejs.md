# nodejs
Standards and best practcies for nodejs applications.


## Table of Contents

1. [Sample](#Sample)



## Application structure
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
│   └───services    # services perform main business logic
│       │   UserController.js
│       │   AuthController.js
│   app.js    # Main entry point
│   worker.js # An example background worker
```
