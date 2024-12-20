# Areas

**Note: the documentation for these endpoints is not maintained and might not be up to date.**

The following sections describe endpoints API users can use to interact with version 1 of Areas of Interest.

## Get user areas

> Getting a list of areas for the current user

```shell
curl -X GET https://api.resourcewatch.org/v1/area
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>"
```

> Response:

```json
{
  "data": [
    {
      "type": "area",
      "id": "59ca3213d08a7d001054522b",
      "attributes": {
        "name": "Test area France",
        "application": "rw",
        "geostore": "8f77fe62cf15d5098ba0ee11c5126aa6",
        "userId": "58e22f662071c01c02f76a0f",
        "env": "production",
        "createdAt": "2017-09-26T10:55:15.990Z",
        "updatedAt": "2017-09-26T10:55:15.990Z",
        "image": "",
        "datasets": [

        ],
        "use": {

        },
        "iso": {

        }
      }
    },
    {
      "type": "area",
      "id": "59ca32ea3209db0014e9a7b7",
      "attributes": {
        "name": "Test custom area",
        "application": "rw",
        "geostore": "b12640deba9d3c5012c5359dd5572e2d",
        "userId": "58e22f662071c01c02f76a0f",
        "env": "production",
        "createdAt": "2017-09-26T10:58:50.226Z",
        "updatedAt": "2017-09-26T10:58:50.226Z",
        "image": "",
        "datasets": [

        ],
        "use": {

        },
        "iso": {

        }
      }
    }
  ]
}
```

Returns the list of areas created by the user provided.

### Filters

> Getting a list of areas for the current user and a given application

```shell
curl -X GET https://api.resourcewatch.org/v1/area?application=rw
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>"
```

| Parameter   |                                 Description                                 | Type |                               Values |
|-------------|:---------------------------------------------------------------------------:|-----:|-------------------------------------:|
| application | Application. Read more about this field [here](concepts.html#applications). | Text | Any Text, values separated by commas |
| env         | Environment. Read more about this field [here](concepts.html#environments). | Text |                                      |

### Pagination

> Example request to load page 2 using 25 results per page

```shell
curl -X GET https://api.resourcewatch.org/v1/area?page[number]=2&page[size]=25 \
-H "x-api-key: <your-api-key>"
```

The Areas service adheres to the conventions defined in the [Pagination guidelines for the RW API](concepts.html#pagination), so we recommend reading that section for more details on how paginate your areas list.

In the specific case of the Areas service, the default value for the `page[size]` query parameter is 1000 for backwards compatibility reasons, instead of 10. However (as recommended in the pagination guidelines), you should not rely on the default page size and always provide a value tailored to your needs.

### Sorting

> Sorting areas

```shell
curl -X GET https://api.resourcewatch.org/v1/area?sort=name \
-H "x-api-key: <your-api-key>"
```

> Sorting areas by multiple criteria

```shell
curl -X GET https://api.resourcewatch.org/v1/area?sort=name,createdAt \
-H "x-api-key: <your-api-key>"
```

> Sort by name descending, createdAt ascending

```shell
curl -X GET https://api.resourcewatch.org/v1/area?sort=-name,+createdAt \
-H "x-api-key: <your-api-key>"
```

The Areas service currently supports sorting using the `sort` query parameter. Sorting areas adheres to the conventions defined in the [Sorting guidelines for the RW API](concepts.html#sorting), so we strongly recommend reading that section before proceeding.

## Create area

Creates a new area

### Parameters

| Parameter   |                                 Description                                 | Type |                               Values | Required |
|-------------|:---------------------------------------------------------------------------:|-----:|-------------------------------------:|---------:|
| application | Application. Read more about this field [here](concepts.html#applications). | Text | Any Text, values separated by commas |      Yes |
| name        |                            Name of the new area                             | Text |                             Any Text |      Yes |
| geostore    |                                 Geostore ID                                 | Text |                             Any Text |      Yes |
| env         | Environment. Read more about this field [here](concepts.html#environments). | Text |                             Any Text |       No |

```shell
curl -X POST https://api.resourcewatch.org/v1/area \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"  -d \
 '{
   "name": <name>,
   "application": <application>,
   "geostore": <geostore id>
 }'
```

### Example

Create an area with name 'Portugal area' and Geostore ID '713899292fc118a915741728ef84a2a7' for the Resource Watch application

```shell
curl -X POST https://api.resourcewatch.org/v1/area?application=rw \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"  -d \
 '{
   "name": "Portugal area",
   "application": "rw",
   "geostore": "713899292fc118a915741728ef84a2a7"
 }'
```

```json
{
  "data": {
    "type": "area",
    "id": "5a0da028e6d876001080c259",
    "attributes": {
      "name": "Portugal area",
      "application": "rw",
      "geostore": "713899292fc118a915741728ef84a2a7",
      "userId": "58e22f662071c01c02f76a0f",              
      "env": "production",
      "createdAt": "2017-11-16T14:26:48.396Z",
      "updatedAt": "2017-11-16T14:26:48.396Z",
      "image": "",
      "datasets": [

      ],
      "use": {

      },
      "iso": {

      }
    }
  }
}
```

## Delete area

Deletes an area

```shell
curl -X DELETE https://api.resourcewatch.org/v1/area/<area-id> \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>"
```

### Example

```shell
curl -X DELETE https://api.resourcewatch.org/v1/area/59ca3213d08a7d001054522b \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>"
```

## Delete areas by user id

```shell
curl -X DELETE https://api.resourcewatch.org/v1/area/by-user/<user-id> \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>"
```

> Example response:

```json
{
  "data": [
    {
      "type": "area",
      "id": "59ca3213d08a7d001054522b",
      "attributes": {
        "name": "Test area France",
        "application": "rw",
        "geostore": "8f77fe62cf15d5098ba0ee11c5126aa6",
        "userId": "58e22f662071c01c02f76a0f",
        "env": "production",
        "createdAt": "2017-09-26T10:55:15.990Z",
        "updatedAt": "2017-09-26T10:55:15.990Z",
        "image": "",
        "datasets": [

        ],
        "use": {

        },
        "iso": {

        }
      }
    },
    {
      "type": "area",
      "id": "59ca32ea3209db0014e9a7b7",
      "attributes": {
        "name": "Test custom area",
        "application": "rw",
        "geostore": "b12640deba9d3c5012c5359dd5572e2d",
        "userId": "58e22f662071c01c02f76a0f",
        "env": "production",
        "createdAt": "2017-09-26T10:58:50.226Z",
        "updatedAt": "2017-09-26T10:58:50.226Z",
        "image": "",
        "datasets": [

        ],
        "use": {

        },
        "iso": {

        }
      }
    }
  ]
}
```

This endpoint deletes all areas for the provided `userId`. Any microservice or user with ADMIN role can use this endpoint. Regular users can use this endpoint to delete the areas they own.

### Errors for deleting areas by user id

| Error code | Error message (example) | Description                                                                                                          |
|------------|-------------------------|----------------------------------------------------------------------------------------------------------------------|
| 401        | `Unauthorized`          | No token was provided.                                                                                               |
| 403        | `Not authorized`        | You are trying to delete the areas of an user that is not the same logged user, not an ADMIN user or a microservice. |

## Get area

Gets all the information from an area

```shell
curl -X GET https://api.resourcewatch.org/v1/area/<area-id> \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>"
```

### Example

```shell
curl -X GET https://api.resourcewatch.org/v1/area/59ca32ea3209db0014e9a7b7 \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>"
```

```json
{
    "data": {
        "type": "area",
        "id": "59ca32ea3209db0014e9a7b7",
        "attributes": {
            "name": "Test custom area",
            "application": "rw",
            "geostore": "b12640deba9d3c5012c5359dd5572e2d",
            "userId": "58e22f662071c01c02f76a0f",
            "env": "production",
            "createdAt": "2017-09-26T10:58:50.226Z",
            "updatedAt": "2017-09-26T10:58:50.226Z",
            "image": "",
            "datasets": [],
            "use": {},
            "iso": {}
        }
    }
}
```
