# pay-publicauth
Payments Public API Authentication Service

## Integration tests

To run the integration tests, the `DOCKER_HOST` and `DOCKER_CERT_PATH` environment variables must be set up correctly. On OS X the environment can be set up with:

```
    eval $(boot2docker shellinit)
    eval $(docker-machine env <virtual-machine-name>)

```

The command to run the integration tests is:

```
    mvn test
```

## API

| Path                          | Supported Methods | Description                        |
| ----------------------------- | ----------------- | ---------------------------------- |
|[```/v1/api/auth```](#get-v1apiauth)              | GET    |  Look up the account id for a token.            |
|[```/v1/frontend/auth```](#post-v1frontendauth)             | POST   |  Generates a new dev token for a given account. |
|[```/v1/frontend/auth```](#put-v1frontendauth)             | PUT   |  Updates the description of an existing dev token. |
|[```/v1/frontend/auth/{account_id}```](#get-v1frontendauthaccount_id)             | GET   |  Retrieves all generated and not revoked tokens for this account. |
|[```/v1/frontend/auth/{account_id}/revoke```](#post-v1frontendauthaccount_idrevoke) | POST  |  Disables all dev tokens currently enabled for this account.  |


### GET /v1/api/auth

This endpoint looks up the account id for a token.

#### Request example

```
GET /v1/auth
Accept: application/json
Authorization: Bearer BEARER_TOKEN

```

#### Response example

```
200 OK
Content-Type: application/json

{
    "account_id": "ACCOUNT_ID"
}
```

Or if the token does not exist or has been revoked:

```
401 UNAUTHORIZED
```

##### Response field description

| Field                    | always present | Description                         |
| ------------------------ |:--------------:| ----------------------------------- |
| `account_id`             | X              | The account Id for the bearer token |

-----------------------------------------------------------------------------------------------------------

### POST /v1/frontend/auth

Generate and return a new token for the given gateway account id.

#### Request example

```
Content-Type: application/json
{
    "account_id": "GATEWAY_ACCOUNT_1",
    "description": "Token description"
}

```

##### Request body description

| Field                    | required | Description                                   |
| ------------------------ |:--------:| --------------------------------------------- |
| `account_id`             | X        | Gateway account to associate the new token to |
| `description`            | X        | Description of the new token                  |


#### Successful response example

```
200 OK
Content-Type: application/json

{
    "token": "GENERATED_TOKEN"
}
```

##### Successful response field description

| Field                  | Description                               |
| ---------------------- | ----------------------------------------- |
| `token`                | The newly generated token                 |

#### Unsuccessful response example

```
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
    "message": "Missing fields: [account_id]"
}
```
##### Unsuccessful response field description

| Field              | Description                     |
| ------------------ | ------------------------------- |
| `message`          | The error message               |

-----------------------------------------------------------------------------------------------------------

### PUT /v1/frontend/auth

Updates the description of an existing dev token.

#### Request example

```
Content-Type: application/json
{
    "token_link": "550e8400-e29b-41d4-a716-446655440000",
    "description": "New token description"
}

```

##### Request body description

| Field                    | required | Description                                                |
| ------------------------ |:--------:| ---------------------------------------------------------- |
| `token_link`             | X        | Token link as return by [GET /v1/frontend/auth/{account_id}](#get-v1frontendauthaccount_id) |
| `description`            | X        | New description of the existing token                      |


#### Successful response example

```
200 OK
Content-Type: application/json

{
    "token_link": "550e8400-e29b-41d4-a716-446655440000",
    "description": "New token description"
}
```

##### Successful response field description

| Field                  | Description                               |
| ---------------------- | ----------------------------------------- |
| `token_link`           | Token link of the updated resource        |
| `description`          | New description of the existing token     |

#### Unsuccessful response example

```
HTTP/1.1 404 Not Found
Content-Type: application/json

{
    "message": "Could not update token description"
}
```
##### Unsuccessful response field description

| Field              | Description                     |
| ------------------ | ------------------------------- |
| `message`          | The error message               |


-----------------------------------------------------------------------------------------------------------

### GET /v1/frontend/auth/{account_id}

Retrieves all generated and not revoked tokens for this account.

#### Request example

```
GET /v1/frontend/auth/15
Accept: application/json
```

#### Response example

```
200 OK
Content-Type: application/json

{
    "tokens": [
                {"token_link": "550e8400-e29b-41d4-a716-446655440000", "description":"token 1 description"},
                {"token_link": "550e8400-e29b-41d4-a716-446655441234", "description":"token 2 description"}
              ]
}
```

-----------------------------------------------------------------------------------------------------------

### POST /v1/frontend/auth/{account_id}/revoke

#### Request example

```
POST WITH EMPTY BODY
{
}
```


#### Response example

```
200 OK

```