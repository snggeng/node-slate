# Introduction

Welcome to the HypotheticalMeals API documentation. This is the API used by the official HypotheticalMeals web interface, so everything the web UI is able to do can also be accomplished via the API.

You can view code examples in the dark area to the right; switch the programming language of the examples with the tabs in the top right.

If anything is missing or seems incorrect, please check the GitHub issues for existing known issues or create a new issue.

This example API documentation page was created with [Slate](https://github.com/lord/slate) using the [NodeJS Fork](https://github.com/sdelements/node-slate).

# Authentication

> To authorize on /api routes, use this code:


```bash
# With shell, you can just pass the correct header with each request
curl -X GET -H "Content-type: application/json"
-H "Authorization: Bearer <ACCESS_TOKEN>"
https://vcm-2849.vm.duke.edu/api/ingredients
```

```javascript
const headers = new Headers()
headers.authorization = "Bearer <ACCESS_TOKEN>"

let ingredients =
fetch('https://vcm-2849.vm.duke.edu/api/ingredients', headers)
.then(response => response.json())
```

> Make sure to replace `Bearer <ACCESS_TOKEN>` with your JWT Token.

The HypotheticalMeals API is JSON based. There are two main API routes: `/public` and `/api`. In order to make an authenticated call to the API at any `/api/*` endpoint, you must include your access token with the call. OAuth2 uses a BEARER token that is passed along in an Authorization header.

<aside class="notice">
You must replace <code>Bearer <ACCESS_TOKEN></code> with your personal JWT Token.
</aside>

# Public
> /public routes do not need authorization:

The `/public` route is primarily used to `sign in` and `sign out` users. As a user you will need to `sign in` in order to gain access to your unique `JWT Token` which authenticates you for the session.

## Sign In
```bash
# With shell, you can just enter the right data and make a POST request
curl -d '{"username":"user", "password":"some password"}'
-H "Content-type: application/json"
-X POST https://vcm-2849.vm.duke.edu/public/signin
```

```javascript
// Authentication
const signIn = async (user) => {
  // Get data from db
  const response = await fetch('https://vcm-2849.vm.duke.edu/public/signin', {
    method: 'post',
    headers: {
      'Accept': 'application/json',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(user)
  })
  // Receive user token
  const user_token = await response.json()
  if (response.status !== 200) throw user_token

  return user_token
}

// Sign In
// throws error if username and password do not match
// returns user token if username and password match
let user = signIn({"username": "user", "password": "some password"})
```

> The above command returns JSON structured like this:

```json
{
    "success": true,
    "token": "JWT ey..."
}
```

> Make sure to replace `<username> and <password> fields` with your your user credentials.

This endpoint takes an object with keys "username" and "password" and validates if that user exists. If both keys match, then a unique `JWT token` corresponding to the user is generated and returned. On the client-side, you may use this `JWT token` to get user credentials

### HTTP Request

`POST https://vcm-2849.vm.duke.edu/public/signin`

### Query Parameters
### Token
Parameter | Default | Unique | Required | Description
--------- | ------- | ------ | -------- | ---------- |
success | nil | false | false | Returns query success. Either `true` or `false`.
token | nil | false | true | Unique `JWT Token` generated using user credentials that can be used to authenticated a user that has successfully signed in.

### User
Parameter | Default | Unique | Required | Description
--------- | ------- | ------ | -------- | ---------- |
username | nil | true | true | Unique name of the user that is case-insensitive.
password | nil | false | true | A salted hash of the user's original password
role | nil | false | false | The permission level of the user. There are three possible enums: `admin`, `manager`, `user`

<aside class="success">
Remember — You will need the `JWT token` to gain access to information at the <code>/api</code> route.
</aside>

<aside class="notice">
You must replace <code>username</code> and <code>password</code> with your personal user credentials.
</aside>

## Sign Out
```bash
# With shell, you can just make a GET request
curl -X GET -H "Content-type: application/json"
https://vcm-2849.vm.duke.edu/public/signout
```

```javascript
// Call the sign out endpoint using fetch
let response =
fetch('https://vcm-2849.vm.duke.edu/public/signout')
.then(response => response.json())
```
> The above command returns JSON structured like this:

```json
{
    "success": true,
    "message": "User has been successfully logged out"
}
```

This endpoint signs the current user out. On the client-side, if you are persisting the `JWT Token` using `local storage` or `session storage`, you should remove the token after a user signs out.

### HTTP Request

`POST https://vcm-2849.vm.duke.edu/public/signout`

### Query Parameters
### Response
Parameter | Default | Unique | Required | Description
--------- | ------- | ------ | -------- | ---------- |
success | nil | false | false | Returns query success. Either `true` or `false`.
message | nil | false | true | Returns a message saying that user has been logged out

# Ingredients

## Get All Ingredients

```bash
curl -X GET -H "Content-type: application/json"
-H "Authorization: Bearer <ACCESS_TOKEN>"
https://vcm-2849.vm.duke.edu/api/ingredients
```

```javascript
const headers = new Headers()
headers.authorization = "Bearer <ACCESS_TOKEN>"

let ingredients =
fetch('https://vcm-2849.vm.duke.edu/api/ingredients', headers)
.then(response => response.json())
```

> The above command returns JSON structured like this:

```json
[
  {
      "_id": "5a78faf0aafae6cc18aed2e4",
      "created_at": "2018-02-06T00:46:40.556Z",
      "updated_at": "2018-02-06T00:46:40.556Z",
      "temp_state": "frozen",
      "package": "pail",
      "name": "corn",
      "__v": 0
  },
  {
      "_id": "5a78fc24babe6bce9bb637b9",
      "created_at": "2018-02-06T00:51:48.780Z",
      "updated_at": "2018-02-06T00:51:48.780Z",
      "temp_state": "refrigerated",
      "package": "pail",
      "name": "carrot",
      "__v": 0
  }
]
```

This endpoint retrieves all ingredients.

### HTTP Request

`GET https://vcm-2849.vm.duke.edu/api/ingredients`

### Query Parameters

Parameter | Default | Unique | Required | Description
--------- | ------- | ------ | -------- | ---------- |
name | nil | true | true | Unique name of ingredient that is case-insensitive.
temp_state | nil | false | true | Temperature state of the ingredient. Only accepts three types of `enums`: `refrigerated`, `frozen`, `room_temperature`
package | nil | false | true | Package type of the ingredient. Only accepts six types of `enums`: `sack`, `pail`, `drum`, `supersack`, `truckload`, `railcar`

<aside class="success">
Remember — You need to be authorized in order to gain access to information at the <code>/api</code> route.
</aside>

## Get a Specific Ingredient

```bash
curl -X GET -H "Content-type: application/json"
-H "Authorization: Bearer <ACCESS_TOKEN>"
https://vcm-2849.vm.duke.edu/api/ingredients/5a78fc24babe6bce9bb637b9
```

```javascript
const headers = new Headers()
headers.authorization = "Bearer <ACCESS_TOKEN>"

let ingredients =
fetch('https://vcm-2849.vm.duke.edu/api/ingredients/5a78fc24babe6bce9bb637b9', headers)
.then(response => response.json())
```

> The above command returns JSON structured like this:

```json
{
    "_id": "5a78fc24babe6bce9bb637b9",
    "created_at": "2018-02-06T00:51:48.780Z",
    "updated_at": "2018-02-06T00:51:48.780Z",
    "temp_state": "refrigerated",
    "package": "pail",
    "name": "carrot",
    "__v": 0
}
```

This endpoint retrieves a specific ingredient.

<aside class="warning">Make sure you replace <code>5a78fc24babe6bce9bb637b9</code> with the relevant <code>_id</code> of the ingredient that you wish to retrieve.</aside>

### HTTP Request

`GET https://vcm-2849.vm.duke.edu/api/ingredients/<_id>`

### URL Parameters

Parameter | Default | Unique | Required | Description
--------- | ------- | ------ | -------- | ---------- |
name | nil | true | true | Unique name of ingredient that is case-insensitive.
temp_state | nil | false | true | Temperature state of the ingredient. Only accepts three types of `enums`: `refrigerated`, `frozen`, `room_temperature`
package | nil | false | true | Package type of the ingredient. Only accepts six types of `enums`: `sack`, `pail`, `drum`, `supersack`, `truckload`, `railcar`
