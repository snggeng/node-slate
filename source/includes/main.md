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
> /public routes do not need authorization

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

## Get Paginated Ingredients

```bash
curl -X GET -H "Content-type: application/json"
-H "Authorization: Bearer <ACCESS_TOKEN>"
https://vcm-2849.vm.duke.edu/api/ingredients/page/1/limit/4/q?temp_state=frozen&package=pail
```

```javascript
const headers = new Headers()
headers.authorization = "Bearer <ACCESS_TOKEN>"

let ingredients =
fetch('https://vcm-2849.vm.duke.edu/api/ingredients/page/1/limit/4/q?temp_state=frozen&package=pail', headers)
.then(response => response.json())
```

> The above command returns JSON structured like this:

```json
{
    "ingredients": [
        {
            "_id": "5a77ccae84982db466fdfb3b",
            "created_at": "2018-02-05T03:17:02.474Z",
            "updated_at": "2018-02-05T03:38:48.077Z",
            "temp_state": "frozen",
            "package": "pail",
            "name": "beef",
            "__v": 0
        },
        {
            "_id": "5a77d21513677cb64d67b74c",
            "created_at": "2018-02-05T03:40:05.008Z",
            "updated_at": "2018-02-05T03:49:11.880Z",
            "temp_state": "frozen",
            "package": "pail",
            "name": "lamb",
            "__v": 0
        },
        {
            "_id": "5a77f97154983dc974de3819",
            "created_at": "2018-02-05T06:28:01.105Z",
            "updated_at": "2018-02-05T06:28:01.105Z",
            "temp_state": "frozen",
            "package": "pail",
            "name": "fish",
            "__v": 0
        },
        {
            "_id": "5a77f971d5498cad97hk4u3820",
            "created_at": "2018-02-05T06:28:01.105Z",
            "updated_at": "2018-02-05T06:28:01.105Z",
            "temp_state": "frozen",
            "package": "pail",
            "name": "seabass",
            "__v": 0
        }
    ],
    "numPages": 4
}
```

This endpoint retrieves a paginated and queryable array of ingredients.

In the example on the right, `/page/1` refers to the first page and `/limit/4` refers to the number of ingredients per page. `page/<num>` refers to the offset based on the `limit`. For example, if our query were `/api/ingredients/page/2/limit/4/q` then it would return the 5th - 8th ingredients in the sorted list of ingredients.

The lines after `q?` refer to query parameters that we can use to add filters to the paginated results. For instance, the in the example on the right, `q?temp_state=frozen&package=pail` would only return ingredients that are frozen and stored in pails.

If there are less ingredients in the database than the query limit specified, all valid results will be returned regardless.

<aside class="warning">Make sure you replace <code>page/<num></code> and <code>limit/<num></code> with the relevant page and limit numbers.</aside>

### HTTP Request

`GET https://vcm-2849.vm.duke.edu/api/ingredients/page/<num>/limit/<num>/q?<query_string>`

### URL Parameters

Parameter | Default | Unique | Required | Description
--------- | ------- | ------ | -------- | ---------- |
ingredients | nil | false | false | An array of ingredients that match the `page`, `limit`, and `query_string` provided in the `URL`
numPages | nil | false | false | A number which is the limit of the original query

<aside class="success">
Remember — On your client-side, you will want to use this route to get paginated results in order to reduce throughput and make the load-time of your view independent of the number of documents in the database.
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

## Create an Ingredient

```bash
curl -d {"name": "pork", "package": "truckload", "temp_state": "refrigerated"}
-H "Content-type: application/json"
-H "Authorization: Bearer <ACCESS_TOKEN>"
-X POST
https://vcm-2849.vm.duke.edu/api/ingredients
```

```javascript
const headers = new Headers()
headers.authorization = "Bearer <ACCESS_TOKEN>"

let newIngredient = {
  "name": "pork",
  "package": "truckload",
  "temp_state": "refrigerated"
}

let createdIngredient =
fetch('https://vcm-2849.vm.duke.edu/api/ingredients', {
  method: 'POST',
  headers: {
    'Accept': 'application/json',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(newIngredient)
}, headers)
.then(response => response.json())

```

 > The above command returns JSON structured like this:

```json
{
    "_id": "5a78fc24babe6bce9bb432b10",
    "created_at": "2018-02-06T00:51:48.780Z",
    "updated_at": "2018-02-06T00:51:48.780Z",
    "temp_state": "refrigerated",
    "package": "truckload",
    "name": "pork",
    "__v": 0
}
```
> The `_id` field will always be unique to the newly created document

This endpoint creates a specific ingredient.

<aside class="warning">Make sure you do not create new ingredients with a <code>name</code> that already exist in the database. For instance, since we already have <code>name: "fish"</code> in our database, trying to create a new ingredient with the same <code>name</code> will result in an error and your request will not be successful.</aside>

### HTTP Request

`POST https://vcm-2849.vm.duke.edu/api/ingredients/<_id>`

### URL Parameters

Parameter | Default | Unique | Required | Description
--------- | ------- | ------ | -------- | ---------- |
name | nil | true | true | Unique name of ingredient that is case-insensitive.
temp_state | nil | false | true | Temperature state of the ingredient. Only accepts three types of `enums`: `refrigerated`, `frozen`, `room_temperature`
package | nil | false | true | Package type of the ingredient. Only accepts six types of `enums`: `sack`, `pail`, `drum`, `supersack`, `truckload`, `railcar`


## Update an Ingredient

```bash
curl -d {"name": "fish", "package": "drum"}
-H "Content-type: application/json"
-H "Authorization: Bearer <ACCESS_TOKEN>"
-X PUT
https://vcm-2849.vm.duke.edu/api/ingredients/5a78fc24babe6bce9bb637b9
```

```javascript
const headers = new Headers()
headers.authorization = "Bearer <ACCESS_TOKEN>"

let fieldsToUpdate = {
  "name": "fish",
  "package": "drum"
}

let updatedIngredient =
fetch('https://vcm-2849.vm.duke.edu/api/ingredients/5a78fc24babe6bce9bb637b9', {
  method: 'PUT',
  headers: {
    'Accept': 'application/json',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(fieldsToUpdate)
}, headers)
.then(response => response.json())

```

 > The above command returns JSON structured like this:

```json
{
    "_id": "5a78fc24babe6bce9bb637b9",
    "created_at": "2018-02-06T00:51:48.780Z",
    "updated_at": "2018-02-06T00:51:48.780Z",
    "temp_state": "refrigerated",
    "package": "drum",
    "name": "fish",
    "__v": 0
}
```
> Notice that the document with the same `_id` has different `package` and `name` fields

This endpoint updates a specific ingredient.

<aside class="warning">Make sure you replace <code>5a78fc24babe6bce9bb637b9</code> with the relevant <code>_id</code> of the ingredient that you wish to update.</aside>

### HTTP Request

`PUT https://vcm-2849.vm.duke.edu/api/ingredients/<_id>`

### URL Parameters

Parameter | Default | Unique | Required | Description
--------- | ------- | ------ | -------- | ---------- |
name | nil | true | true | Unique name of ingredient that is case-insensitive.
temp_state | nil | false | true | Temperature state of the ingredient. Only accepts three types of `enums`: `refrigerated`, `frozen`, `room_temperature`
package | nil | false | true | Package type of the ingredient. Only accepts six types of `enums`: `sack`, `pail`, `drum`, `supersack`, `truckload`, `railcar`

## Delete an Ingredient

```bash
-H "Content-type: application/json"
-H "Authorization: Bearer <ACCESS_TOKEN>"
-X DELETE
https://vcm-2849.vm.duke.edu/api/ingredients/5a78fc24babe6bce9bb637b9
```

```javascript
const headers = new Headers()
headers.authorization = "Bearer <ACCESS_TOKEN>"


let deletedIngredient =
fetch('https://vcm-2849.vm.duke.edu/api/ingredients/5a78fc24babe6bce9bb637b9', {
  method: 'DELETE',
  headers: headers
})
.then(response => response.json())

```

 > The above command returns JSON structured like this:

```json
{
    "_id": "5a78fc24babe6bce9bb637b9",
    "created_at": "2018-02-06T00:51:48.780Z",
    "updated_at": "2018-02-06T00:51:48.780Z",
    "temp_state": "refrigerated",
    "package": "drum",
    "name": "fish",
    "__v": 0
}
```
> The deleted document is returned on success. It has been removed from the database

This endpoint removes a specific ingredient.

<aside class="warning">Make sure you replace <code>5a78fc24babe6bce9bb637b9</code> with the relevant <code>_id</code> of the ingredient that you wish to update.</aside>

### HTTP Request

`DELETE https://vcm-2849.vm.duke.edu/api/ingredients/<_id>`

### URL Parameters

Parameter | Default | Unique | Required | Description
--------- | ------- | ------ | -------- | ---------- |
name | nil | true | true | Unique name of ingredient that is case-insensitive.
temp_state | nil | false | true | Temperature state of the ingredient. Only accepts three types of `enums`: `refrigerated`, `frozen`, `room_temperature`
package | nil | false | true | Package type of the ingredient. Only accepts six types of `enums`: `sack`, `pail`, `drum`, `supersack`, `truckload`, `railcar`
