# Favorites

The users can save their own favorites resources of the API.

<aside class="notice">
Remember — All favorite endpoints need to be authenticated.
</aside>


| Field        |     Description      |                          Type |
|--------------|:--------------------:|------------------------------:|
| id           |         Name         |                          Text |
| resourceId   |  Id of the resource  |                          Text |
| resourceType |   Type of resource   | Text (dataset, layer, widget) |
| userId       | Id of the owner user |                          Text |
| createdAt    |    Creation date     |                          Date |

## Create Favorite

To create a favorite, you need to define all next fields in the request body. The required fields that compose a favorite are:

| Field        |    Description     |                          Type |
|--------------|:------------------:|------------------------------:|
| resourceId   | Id of the resource |                          Text |
| resourceType |  Type of resource  | Text (dataset, layer, widget) |

> To create a favorite, you have to do a POST with the following body:


```shell
curl -X POST https://api.resourcewatch.org/v1/favourite \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"  -d \
 '{
   "resourceType":"<resourceType>",
   "resourceId": "<resourceId>"
  }'
```

## Get favorites

This endpoint returns the favorites of the logged user.

> To get all favorite of the logged user, you have to do a GET request:


```shell
curl -X GET https://api.resourcewatch.org/v1/favourite \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>"
```

You can also retrieve all data about the resources by including the query parameter "include=true" in the request.

```shell
curl -X GET https://api.resourcewatch.org/v1/favourite?include=true \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>"
```

## Get favorite by id

This endpoint returns the favorite with id of the param. If the favorite belongs to other user or not exist, the endpoint returns 400.

> To get the favorite by id, you have to do a GET request:


```shell
curl -X GET https://api.resourcewatch.org/v1/favourite/:id \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>"
```

## Delete favorite

This endpoint deletes the favorite with id of the param. If the favorite belongs to other user or not exist, the endpoint returns 400.

> To delete the favorite by id, you have to do a DELETE request:


```shell
curl -X DELETE https://api.resourcewatch.org/v1/favourite/:id \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>"
```

## Delete a favourite by user id

> To delete the favourite by user id, you have to do a DELETE request:

```shell
curl -X DELETE https://api.resourcewatch.org/v1/favourite/by-user/:userId \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>"
```


> In case of success, the deleted favourites are returned in the response body:

```json
{
  "data": [
    {
      "id": "5e6f6eb9bb494e001ab7e413",
      "type": "favourite",
      "attributes": {
        "userId": "57ac9f9e2930906323e573a2",
        "resourceType": "dataset",
        "resourceId": "1e56170c1fca55001ad51779",
        "createdAt": "2020-03-16T12:19:05.125Z",
        "application": "rw"
      }
    }
  ]
}
```

This endpoint deletes the favourites owned by the user with id `userId`. Any ADMIN user can use this endpoint to delete data for any user, while users with USER or MANAGER roles can only delete their own data.


#### Errors for deleting a favourites by user id

| Error code | Error message                      | Description                                                                           |
|------------|------------------------------------|---------------------------------------------------------------------------------------|
| 401        | Unauthorized                       | You need to be logged in to be able to delete favourites.                             |
| 403        | Forbidden                          | You need to either have the `ADMIN` role, or call this endpoint with your own user id |

