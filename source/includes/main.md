# Introduction

Welcome to the HypotheticalMeals API documentation. This is the API used by the official HypotheticalMeals web interface, so everything the web UI is able to do can also be accomplished via the API.

You can view code examples in the dark area to the right; switch the programming language of the examples with the tabs in the top right.

If anything is missing or seems incorrect, please check the GitHub issues for existing known issues or create a new issue.

This example API documentation page was created with [Slate](https://github.com/lord/slate) using the [NodeJS Fork](https://github.com/sdelements/node-slate).

# Authentication

> To authorize, use this code:


```bash
# With shell, you can just pass the correct header with each request
curl -X GET -H "Content-type: application/json" -H "Authorization: Bearer <ACCESS_TOKEN>"
https://vcm-2849.vm.duke.edu/api/ingredients
```

```javascript
const headers = {
  "authorization": "Bearer <ACCESS_TOKEN>"
}

let ingredients = fetch('https://vcm-2849.vm.duke.edu/api/ingredients', headers).then(response => response.json())
```

> Make sure to replace `Bearer <ACCESS_TOKEN>` with your JWT Token.

The HypotheticalMeals API is JSON based. There are two main API routes: `/public` and `/api`. In order to make an authenticated call to the API at any `/api/*` endpoint, you must include your access token with the call. OAuth2 uses a BEARER token that is passed along in an Authorization header.

<aside class="notice">
You must replace <code>Bearer <ACCESS_TOKEN></code> with your personal JWT Token.
</aside>

# Ingredients

## Get All Ingredients

```bash
curl -X GET -H "Content-type: application/json" -H "Authorization: Bearer <ACCESS_TOKEN>"
https://vcm-2849.vm.duke.edu/api/ingredients
```

```javascript
const headers = {
  "authorization": "Bearer <ACCESS_TOKEN>"
}

let ingredients = fetch('https://vcm-2849.vm.duke.edu/api/ingredients', headers).then(response => response.json())
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
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
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

<!-- <aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside> -->

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve
