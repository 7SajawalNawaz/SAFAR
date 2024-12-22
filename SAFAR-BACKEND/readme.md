# API Documentation: `POST /users/register`

## Endpoint
**POST** `/users/register`

---

## Description
This endpoint registers a new user into the system. It validates the request payload, hashes the user's password, and stores the user data in the database.

---

## Request Body
The request body must be in JSON format with the following fields:

| Field              | Type   | Required | Description                                  |
|--------------------|--------|----------|----------------------------------------------|
| `fullname`         | Object | Yes      | An object containing the user's full name.   |
| `fullname.firstname` | String | Yes      | The user's first name.                       |
| `fullname.lastname`  | String | Yes      | The user's last name.                        |
| `email`            | String | Yes      | The user's email address. Must be unique.    |
| `password`         | String | Yes      | The user's password. Must be at least 6 characters long. |

### Example Request Body
```json
{
  "fullname": {
    "firstname": "John",
    "lastname": "Doe"
  },
  "email": "johndoe@example.com",
  "password": "password123"
}
```

---

## Response
### Success (Status Code: `200`)
Returns a JSON object containing the authentication token and the newly created user's details.

#### Example Response
```json
{
  "token": "<JWT_TOKEN>",
  "user": {
    "fullname": {
      "firstname": "John",
      "lastname": "Doe"
    },
    "email": "johndoe@example.com",
    "_id": "64e76b7fc6f1f4a1b9e8e123"
  }
}
```

### Validation Error (Status Code: `400`)
Returns validation errors if any required fields are missing or invalid.

#### Example Response
```json
{
  "errors": [
    {
      "msg": "Invalid Email",
      "param": "email",
      "location": "body"
    },
    {
      "msg": "First name must be 3 characters long",
      "param": "fullname.firstname",
      "location": "body"
    },
    {
      "msg": "Password must be 6 characters long",
      "param": "password",
      "location": "body"
    }
  ]
}
```

### Duplicate Email Error (Status Code: `400`)
Returns an error if the email address already exists in the database.

#### Example Response
```json
{
  "error": "Email already exists."
}
```

---

## Middleware Validation
This endpoint uses `express-validator` to validate the request body:
- `body('email').isEmail().withMessage('Invalid Email')`
- `body('fullname.firstname').isLength({min: 3}).withMessage('First name must be 3 characters long')`
- `body('password').isLength({min: 6}).withMessage('Password must be 6 characters long')`

---

## Notes
1. Passwords are hashed using `bcrypt` before being saved to the database.
2. A JWT token is generated upon successful registration using a secret key defined in the environment variables.

---

## Environment Variables
Ensure the following environment variables are defined:
- `JWT_SECRET`: The secret key used for signing JWT tokens.

---

## Related Files
- **Controller**: `userController.js`
- **Service**: `userService.js`
- **Model**: `userModel.js`
- **Route**: `userRoute.js`
