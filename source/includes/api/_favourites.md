# Favorites

The users can save their own favorites resources of the API.

<aside class="notice">
Remember — All favorite endpoints need to be authenticated.
</aside>


| Field             | Description                                                                     | Type
| ------------------|:-----------------------------------------:                                      | -----:
| id                | Name                                                                            | Text
| resourceId        | Id of the resource                                                              | Text
| resourceType      | Type of resource                                                                | Text (dataset, layer, widget)
| userId            | Id of the owner user                                                            | Text
| createdAt         | Creation date                                                                   | Date



## Create Favorite

To create a favorite, you need to define all next fields in the request body. The required fields that compose a favorite are:

| Field             | Description                                                                     | Type
| ------------------|:-----------------------------------------:                                      | -----:
| resourceId        | Id of the resource                                                              | Text
| resourceType      | Type of resource                                                                | Text (dataset, layer, widget)


> To create a favorite, you have to do a POST with the following body:


```shell
curl -X POST https://api.resourcewatch.org/v1/favourite \
-H "Authorization: Bearer <your-token>" \
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
-H "Authorization: Bearer <your-token>"
```

You can also retrieve all data about the resources by including the query parameter "include=true" in the request.

```shell
curl -X GET https://api.resourcewatch.org/v1/favourite?include=true \
-H "Authorization: Bearer <your-token>"
```

## Get favorite by id

This endpoint returns the favorite with id of the param. If the favorite belongs to other user or not exist, the endpoint returns 400.

> To get the favorite by id, you have to do a GET request:


```shell
curl -X GET https://api.resourcewatch.org/v1/favourite/:id \
-H "Authorization: Bearer <your-token>"
```

## Delete favorite

This endpoint deletes the favorite with id of the param. If the favorite belongs to other user or not exist, the endpoint returns 400.

> To delete the favorite by id, you have to do a DELETE request:


```shell
curl -X DELETE https://api.resourcewatch.org/v1/favourite/:id \
-H "Authorization: Bearer <your-token>"
```

## Delete a favourite by user id

> To delete the favourite by user id, you have to do a DELETE request:

```shell
curl -X DELETE https://api.resourcewatch.org/v1/favourite/by-user/:userId \
-H "Authorization: Bearer <your-token>"
```

This endpoint deletes the favourites where the userId on the param is found as its ownerId. Any user with ADMIN role can use this endpoint. Regular users can use this endpoint to delete the resources themselves own. Not being authenticated will return a 401, not being an ADMIN or not being logged as the user that owns the resources will return a 403.

