# REST API Standards

REST (Representational State Transfer) is a set of principles that define how web standards such as HTTP and URLs are used to create interoperable systems. RESTful APIs are designed to use HTTP requests to perform CRUD (Create, Read, Update, Delete) operations. Below are the standards and best practices for building a REST API, along with examples using Express.js.

## 1. **Use HTTP Methods Correctly**

- **GET**: Retrieve data from the server.
- **POST**: Create new data on the server.
- **PUT**: Update existing data.
- **PATCH**: Partially update existing data.
- **DELETE**: Delete data from the server.

### Example:

```javascript
const express = require('express');
const app = express();

app.use(express.json());

let users = [
    { id: 1, name: 'John Doe' },
    { id: 2, name: 'Jane Doe' }
];

// GET: Retrieve all users
app.get('/users', (req, res) => {
    res.json(users);
});

// POST: Create a new user
app.post('/users', (req, res) => {
    const newUser = {
        id: users.length + 1,
        name: req.body.name
    };
    users.push(newUser);
    res.status(201).json(newUser);
});

// PUT: Update a user
app.put('/users/:id', (req, res) => {
    const user = users.find(u => u.id === parseInt(req.params.id));
    if (!user) return res.status(404).send('User not found');
    
    user.name = req.body.name;
    res.json(user);
});

// DELETE: Remove a user
app.delete('/users/:id', (req, res) => {
    const user = users.find(u => u.id === parseInt(req.params.id));
    if (!user) return res.status(404).send('User not found');
    
    users = users.filter(u => u.id !== user.id);
    res.json(user);
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

## 2. **Use Meaningful and Consistent Resource Names**

- Use nouns (not verbs) for resource names.
- Use plural nouns for resources.
- Keep URLs simple and consistent.

### Example:

- `GET /users` instead of `GET /getAllUsers`
- `POST /users` instead of `POST /createUser`

## 3. **Stateless Operations**

   Each request from a client must contain all the information the server needs to fulfill that request. The server should not store anything about the client between requests.

### Example:

```javascript
app.get('/users/:id', (req, res) => {
    const user = users.find(u => u.id === parseInt(req.params.id));
    if (!user) return res.status(404).send('User not found');
    
    res.json(user);
});
```

## 4. **Use Appropriate HTTP Status Codes**

- `200 OK`: Successful GET, PUT, PATCH, or DELETE request.
- `201 Created`: Successful POST request.
- `204 No Content`: Successful request but no content to return.
- `400 Bad Request`: The request could not be understood or was missing required parameters.
- `404 Not Found`: Resource was not found.
- `500 Internal Server Error`: An error occurred on the server.

### Example:

```javascript
app.post('/users', (req, res) => {
    if (!req.body.name) {
        return res.status(400).send('Name is required');
    }
    
    const newUser = {
        id: users.length + 1,
        name: req.body.name
    };
    users.push(newUser);
    res.status(201).json(newUser);
});
```

## 5. **Version Your API**

   Use versioning in your API to ensure that changes do not break existing clients.

### Example:

```javascript
app.get('/api/v1/users', (req, res) => {
    res.json(users);
});
```

## 6. **Provide Pagination, Filtering, and Sorting**

   For endpoints that return a list of resources, provide query parameters for pagination, filtering, and sorting.

### Example:

```javascript
app.get('/users', (req, res) => {
    const page = parseInt(req.query.page) || 1;
    const limit = parseInt(req.query.limit) || 10;
    const startIndex = (page - 1) * limit;
    const endIndex = page * limit;

    const result = users.slice(startIndex, endIndex);
    res.json(result);
});
```

## 7. **Use JSON for Request and Response Bodies**

   JSON is the standard format for data exchange in REST APIs.

### Example:

```javascript
app.post('/users', (req, res) => {
    const newUser = {
        id: users.length + 1,
        name: req.body.name
    };
    users.push(newUser);
    res.status(201).json(newUser);
});
```

## 8. **Error Handling**

   Return meaningful error messages to the client, along with the appropriate HTTP status code.

### Example:

```javascript
app.get('/users/:id', (req, res) => {
    const user = users.find(u => u.id === parseInt(req.params.id));
    if (!user) return res.status(404).json({ error: 'User not found' });
    
    res.json(user);
});
```
