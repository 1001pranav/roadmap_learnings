# CoDE Documentation

## SWAGGER 

### Install Swagger in node.js
```linux
npm install swagger-jsdoc swagger-ui-express --save-dev
```

### Swagger Configuration file

#### Configuration for express.js
create a file called  - `swagger.js`
```javascript
import swaggerJsdoc from 'swagger-jsdoc';
import swaggerUi from 'swagger-ui-express';

const options = {
  definition: {
    openapi: '3.0.0',
    info: {
      title: 'My Node.js API',
      version: '1.0.0',
      description: 'A sample API for demonstrating Swagger documentation',
    },
    servers: [
      {
        url: 'http://localhost:3000',
        description: 'Local development server',
      },
    ],
  },
  apis: ['./routes/*.js'], // Path to the API routes files
};

const swaggerSpec = swaggerJsdoc(options);

function swaggerDocs(app, port) {
  // Serve Swagger UI at /docs endpoint
  app.use('/docs', swaggerUi.serve, swaggerUi.setup(swaggerSpec));

  // Serve swagger.json at /docs.json endpoint
  app.get('/docs.json', (req, res) => {
    res.setHeader('Content-Type', 'application/json');
    res.send(swaggerSpec);
  });
}

export default swaggerDocs;
```
Inside app.js/index.js file  import `swaggerDocs(app, port)` function from above

```javascript
import express from 'express';
import swaggerDocs from './swagger';

const app = express();
const port = 3000;

// Your API routes go here

swaggerDocs(app, port);

app.listen(port, () => {
  console.log(`Server is running on port ${port}`);
});
```

### GET Method (Query Parameters)
```
@swagger
openapi: 3.0.0
info:
  title: Sample API
  version: 1.0.0
  description: API documentation for a sample application
servers:
  - url: http://localhost:3000
paths:
  /users:
    get:
      summary: Retrieve a list of users
      description: Get a list of users with optional filters.
      parameters:
        - name: age
          in: query
          required: false
          description: Filter users by age.
          schema:
            type: integer
            example: 25
        - name: name
          in: query
          required: false
          description: Filter users by name.
          schema:
            type: string
            example: "John"
      responses:
        '200':
          description: A list of users
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/User'
              examples:
                example1:
                  value:
                    - id: 1
                      name: "John Doe"
                      age: 30
                    - id: 2
                      name: "Jane Doe"
                      age: 25
```

### GET Method (Path)
```
@swagger
openapi: 3.0.0
info:
  title: Sample API
  version: 1.0.0
  description: API documentation for a sample application
servers:
  - url: http://localhost:3000
paths:
  /users/{userId}:
    get:
      summary: Retrieve a user by ID
      description: Get details of a user by their unique ID.
      parameters:
        - name: userId
          in: path
          required: true
          description: The ID of the user to retrieve.
          schema:
            type: integer
            example: 1
      responses:
        '200':
          description: User found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
              examples:
                example1:
                  value:
                    id: 1
                    name: "John Doe"
                    age: 30
        '404':
          description: User not found
```

### POST Method
```
@swagger
openapi: 3.0.0
info:
  title: Sample API
  version: 1.0.0
  description: API documentation for a sample application
servers:
  - url: http://localhost:3000
paths:
  /users:
    post:
          summary: Create a new user
          description: Add a new user to the system.
          requestBody:
            required: true
            content:
              application/json:
                schema:
                  $ref: '#/components/schemas/NewUser'
                examples:
                  example1:
                    value:
                      name: "Alice"
                      age: 28
          responses:
            '201':
              description: User created successfully
              content:
                application/json:
                  schema:
                    $ref: '#/components/schemas/User'
                  examples:
                    example1:
                      value:
                        id: 3
                        name: "Alice"
                        age: 28
```
### DELETE Method
```
@swagger
openapi: 3.0.0
info:
  title: Sample API
  version: 1.0.0
  description: API documentation for a sample application
servers:
  - url: http://localhost:3000
paths:
  /users/{userId}:
     put:
      summary: delete a user by ID
      description: delete an existing user.
      parameters:
        - name: userId
          in: path
          required: true
          description: The ID of the user to update.
          schema:
            type: integer
            example: 1
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/NewUser'
            examples:
              example1:
                value:
                  name: "John Smith"
                  age: 31
      responses:
        '200':
          description: User updated successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
              examples:
                example1:
                  value:
                    id: 1
                    name: "John Smith"
                    age: 31
        '404':
          description: User not found

```

### Components/schemas
```
components:
  schemas:
    User:
      type: object
      required:
        - id
        - name
      properties:
        id:
          type: integer
          example: 1
        name:
          type: string
          example: "John Doe"
        age:
          type: integer
          example: 30

    NewUser:
      type: object
      required:
        - name
      properties:
        name:
          type: string
          example: "Alice"
        age:
          type: integer
          example: 28
```



### Authorization

If we want to specify any securiy in API then we need to add following in under `options` in  `swagger.js` file
```Javascript
  security: [
        {
          bearerAuth: [], // Apply this security to all routes by default
        },
  ],
```
or if we want security for specific API's then we can add under any specific API's YML like this under path
```js
 *     security: 
 *       - bearerAuth: []
```

