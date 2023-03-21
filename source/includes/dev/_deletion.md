# User Deletion

A User Deletion (or simply 'deletion') is a record of a user's deletion process. This record is created when a user
deletion is requested, and tracks the process of deleting all of that user's data from the RW API. Note that a deletion,
by itself, has no functional implication - creating a deletion will not delete a user account or any of its data, for
example.
It works solely as a tracking mechanism for the user's deletion process.

Each deletion model record has an overall `status` field, that can be either `pending` or `done` - the former indicates
that the deletion process was not fully completed automatically (there are still user resources left to be deleted); the
latter indicates that the deletion process has completed, and no user data remains in the RW API databases.

The exception to this is the deletion record itself, which will remain after the user data deletion process is
completed. However, the deletion record does not contain any user personal information, nor does it contain any
information uploaded by the user, and as such outside of the scope of deleting all user data from the platform.

Each deletion model record also has a series of boolean typed fields, each of which referring to a type of resource the
RW API stores and that can uploaded by a user (dataset, widget, layer, etc). These fields are meant to track which of
these resources have been successfully deleted.

User deletions come with a basic CRUD set of endpoints, all of which are only available to users with the `ADMIN` role,
and you will get a `403` HTTP error
if you try to access them without having the necessary permissions.

## Getting all deletions

```shell
curl -X GET https://api.resourcewatch.org/v1/deletion \
-H "Authorization: Bearer <your-token>"
```

> Example response:

```json

{
  "data": [
    {
      "type": "deletions",
      "id": "62bd87bab67dc765baf45597",
      "attributes": {
        "userId": "62bd87bab67dc765baf45596",
        "requestorUserId": "62bd87bab67dc765baf45596",
        "status": "pending",
        "datasetsDeleted": false,
        "layersDeleted": false,
        "widgetsDeleted": false,
        "userAccountDeleted": false,
        "userDataDeleted": false,
        "collectionsDeleted": false,
        "favouritesDeleted": false,
        "vocabulariesDeleted": false,
        "areasDeleted": false,
        "storiesDeleted": false,
        "subscriptionsDeleted": false,
        "dashboardsDeleted": false,
        "profilesDeleted": false,
        "topicsDeleted": false,
        "createdAt": "2022-06-30T11:23:38.002Z",
        "updatedAt": "2022-06-30T11:23:38.002Z"
      }
    }
  ],
  "links": {
    "self": "https://api.resourcewatch.org/v1/deletion?page[number]=1&page[size]=10",
    "first": "https://api.resourcewatch.org/v1/deletion?page[number]=1&page[size]=10",
    "last": "https://api.resourcewatch.org/v1/deletion?page[number]=1&page[size]=10",
    "prev": "https://api.resourcewatch.org/v1/deletion?page[number]=1&page[size]=10",
    "next": "https://api.resourcewatch.org/v1/deletion?page[number]=1&page[size]=10"
  },
  "meta": {
    "total-pages": 1,
    "total-items": 1,
    "size": 10
  }
}
```

This endpoint allows you to list all existing deletion records.

### Pagination

> Example request to load page 2 using 25 results per page

```shell
curl -X GET https://api.resourcewatch.org/v1/deletion?page[number]=2&page[size]=25
```

This endpoint adheres to the conventions defined in
the [Pagination guidelines for the RW API](/doc-api/concepts.html#pagination), so we recommend reading that section for
more
details on how paginate your deletion list.

### Filters

> Filtering deletions

```shell
curl -X GET https://api.resourcewatch.org/v1/deletion?status=done
```

The deletion list provides filtering based on these 3 fields:

| Filter          | Description                                              | Type   | Expected values     |
|-----------------|----------------------------------------------------------|--------|---------------------|
| userId          | Filter by the id of the user account to be deleted.      | String | any valid user id   |
| requestorUserId | Filter by the id of the user who requested the deletion. | String | any valid user id   |
| status          | Filter by the status of the deletion process.            | String | `done` or `pending` |

### Errors for getting all deletions

| Error code | Error message     | Description                                               |
|------------|-------------------|-----------------------------------------------------------|
| 401        | Not authenticated | You need to be logged in to be able to get all deletions. |
| 403        | Not authorized    | You need to have the `ADMIN` role                         |

## Getting a deletion by id

> Request a deletion by id:

```shell
curl -X GET https://api.resourcewatch.org/v1/deletion/<deletion-id> \
-H "Authorization: Bearer <your-token>"
```

> Example response:

```json
{
  "data": {
    "type": "deletions",
    "id": "62bd8d49cae089972ce81039",
    "attributes": {
      "userId": "62bd8d49cae089972ce81038",
      "requestorUserId": "62bd8d49cae089972ce81038",
      "status": "pending",
      "datasetsDeleted": false,
      "layersDeleted": false,
      "widgetsDeleted": false,
      "userAccountDeleted": false,
      "userDataDeleted": false,
      "collectionsDeleted": false,
      "favouritesDeleted": false,
      "vocabulariesDeleted": false,
      "areasDeleted": false,
      "storiesDeleted": false,
      "subscriptionsDeleted": false,
      "dashboardsDeleted": false,
      "profilesDeleted": false,
      "topicsDeleted": false,
      "createdAt": "2022-06-30T11:47:21.305Z",
      "updatedAt": "2022-06-30T11:47:21.305Z"
    }
  }
}
```

This endpoints allows you to retrieve the details of a single deletion record from its id.

### Errors for getting a deletion by id

| Error code | Error message      | Description                                            |
|------------|--------------------|--------------------------------------------------------|
| 401        | Not authenticated  | You need to be logged in to be able to get a deletion. |
| 403        | Not authorized     | You need to have the `ADMIN` role                      |
| 404        | Deletion not found | There is no deletion with the provided id              |

## Creating a deletion

> Create a deletion for the current user

```shell
curl -X POST "https://api.resourcewatch.org/v1/deletion" \
-H "Authorization: Bearer <your-token>"
```

> Response:

```json
{
  "data": {
    "type": "deletions",
    "id": "62bd8d49cae089972ce81039",
    "attributes": {
      "userId": "62bd8d49cae089972ce81038",
      "requestorUserId": "62bd8d49cae089972ce81038",
      "status": "pending",
      "datasetsDeleted": false,
      "layersDeleted": false,
      "widgetsDeleted": false,
      "userAccountDeleted": false,
      "userDataDeleted": false,
      "collectionsDeleted": false,
      "favouritesDeleted": false,
      "vocabulariesDeleted": false,
      "areasDeleted": false,
      "storiesDeleted": false,
      "subscriptionsDeleted": false,
      "dashboardsDeleted": false,
      "profilesDeleted": false,
      "topicsDeleted": false,
      "createdAt": "2022-06-30T11:47:21.305Z",
      "updatedAt": "2022-06-30T11:47:21.305Z"
    }
  }
}
```

> Create a deletion for a specific user with custom data

```shell
curl -X POST "https://api.resourcewatch.org/v1/deletion" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer <your-token>" \
-d  \
'{
    "userId": "bd8a36df-2e52-4b2d-b7be-a48bdcd7c769",
    "userAccountDeleted": true
}'
```

Use this endpoint to create a deletion record for the user identified in the token. Note that this does not delete any
user account or data - you should use the [delete user endpoint](/doc-api/reference.html#deleting-a-user) to achieve
that.

You can optionally specify the `userId` or any of the boolean type fields values in the body of your request.
The `requestorUserId`
is automatically set based on the user token passed as a request header. The `status` field is set to `pending` by
default.

### Errors for creating a deletion

| Error code | Error message                         | Description                                               |
|------------|---------------------------------------|-----------------------------------------------------------|
| 400        | Deletion already exists for this user | There is already a deletion record for this userId.       |
| 401        | Not authenticated                     | You need to be logged in to be able to create a deletion. |
| 403        | Not authorized                        | You need to have the `ADMIN` role                         |

## Updating a deletion

> Updating a deletion

```shell
curl -X PATCH "https://api.resourcewatch.org/v1/deletion/<deletion-id>" \
-H "Authorization: Bearer <your-token>" \
-H "Content-Type: application/json" -d \
'{
  "status":"done"
}'
```

> Response:

```json
{
  "data": {
    "type": "deletions",
    "id": "62bd8d49cae089972ce81039",
    "attributes": {
      "userId": "62bd8d49cae089972ce81038",
      "requestorUserId": "62bd8d49cae089972ce81038",
      "status": "done",
      "datasetsDeleted": false,
      "layersDeleted": false,
      "widgetsDeleted": false,
      "userAccountDeleted": false,
      "userDataDeleted": false,
      "collectionsDeleted": false,
      "favouritesDeleted": false,
      "vocabulariesDeleted": false,
      "areasDeleted": false,
      "storiesDeleted": false,
      "subscriptionsDeleted": false,
      "dashboardsDeleted": false,
      "profilesDeleted": false,
      "topicsDeleted": false,
      "createdAt": "2022-06-30T11:47:21.305Z",
      "updatedAt": "2022-06-30T12:16:45.381Z"
    }
  }
}
```

Use this endpoint to update an existing deletion. Besides the several boolean type fields, you can also update
the `status`
field to either `done` or `pending`.

### Errors for updating a deletion

| Error code | Error message      | Description                                               |
|------------|--------------------|-----------------------------------------------------------|
| 401        | Not authenticated  | You need to be logged in to be able to update a deletion. |
| 403        | Not authorized     | You need to have the `ADMIN` role                         |
| 404        | Deletion not found | There is no deletion with the provided id                 |

## Delete a deletion

> Deleting a deletion

```shell
curl -X DELETE "https://api.resourcewatch.org/v1/deletion/<deletion-id>" \
-H "Authorization: Bearer <your-token>"
```

> Response:

```json
{
  "data": {
    "type": "deletions",
    "id": "62bd8d49cae089972ce81039",
    "attributes": {
      "userId": "62bd8d49cae089972ce81038",
      "requestorUserId": "62bd8d49cae089972ce81038",
      "status": "done",
      "datasetsDeleted": false,
      "layersDeleted": false,
      "widgetsDeleted": false,
      "userAccountDeleted": false,
      "userDataDeleted": false,
      "collectionsDeleted": false,
      "favouritesDeleted": false,
      "vocabulariesDeleted": false,
      "areasDeleted": false,
      "storiesDeleted": false,
      "subscriptionsDeleted": false,
      "dashboardsDeleted": false,
      "profilesDeleted": false,
      "topicsDeleted": false,
      "createdAt": "2022-06-30T11:47:21.305Z",
      "updatedAt": "2022-06-30T12:16:45.381Z"
    }
  }
}
```

Use this endpoint to delete an existing deletion.

### Errors for deleting a deletion

| Error code | Error message      | Description                                               |
|------------|--------------------|-----------------------------------------------------------|
| 401        | Not authenticated  | You need to be logged in to be able to delete a deletion. |
| 403        | Not authorized     | You need to have the `ADMIN` role                         |
| 404        | Deletion not found | There is no deletion with the provided id                 |

