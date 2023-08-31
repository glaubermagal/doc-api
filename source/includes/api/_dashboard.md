# Dashboard

**Note: the documentation for these endpoints is not maintained and might not be up to date.**

## What is a dashboard

A dashboard contains the information to display a web page belonging to a user.

## Getting all dashboards



```shell
curl -X GET https://api.resourcewatch.org/v1/dashboard \
-H 'Authorization: Bearer <your-token>' \
-H "x-api-key: <your-api-key>"
```

> Example response:

```json
{
    "data": [
        {
            "id": "10",
            "type": "dashboards",
            "attributes": {
                "name": "Cities Dashboard",
                "slug": "cities-dashboard",
                "summary": "",
                "description": "",
                "content": "<p></p>\n<iframe width=\"500\" height=\"410\" src=\"/embed/widget/5ebeddda-8f3d-4e63-8a52-08e15c3e148c\" frameBorder=\"0\"></iframe>\n<p></p>\n<iframe width=\"500\" height=\"410\" src=\"/embed/widget/73c574b9-f9ab-4f77-87be-651ff8dac5fe\" frameBorder=\"0\"></iframe>\n<p>test1</p>\n",
                "published": true,
                "photo": {
                    "cover": "https://s3.amazonaws.com/image.jpg",
                    "thumb": "https://s3.amazonaws.com/image.jpg",
                    "original": "https://s3.amazonaws.com/image.jpg"
                },
                "user-id": "eb63867922e16e34ef3ce862",
                "private": true,
                "env": "production",
                "application":  ["rw"],
                "is-highlighted": false,
                "is-featured": false,
                "author-title": "",
                "author-image": {
                    "cover": "/author_images/cover/missing.png",
                    "thumb": "/author_images/thumb/missing.png",
                    "original": "/author_images/original/missing.png"
                }
            }
        }
    ],
    "links": {
        "self": "https://api.resourcewatch.org/v1/dashboard?page%5Bnumber%5D=1&page%5Bsize%5D=10",
        "first": "https://api.resourcewatch.org/v1/dashboard?page%5Bnumber%5D=1&page%5Bsize%5D=10",
        "prev": null,
        "next": "https://api.resourcewatch.org/v1/dashboard?page%5Bnumber%5D=2&page%5Bsize%5D=10",
        "last": "https://api.resourcewatch.org/v1/dashboard?page%5Bnumber%5D=14&page%5Bsize%5D=10"
    },
    "meta": {
        "total-pages": 14,
        "total-items": 140,
        "size": 10
    }
}
```

This endpoint will allow to get all dashboards belonging to a user.

### Filters

> Example request using query string filters:

```shell
curl -X GET https://api.resourcewatch.org/v1/dashboard?name=text&private=false \
-H "x-api-key: <your-api-key>"
```

> Deprecated filter syntax:

```shell
curl -X GET https://api.resourcewatch.org/v1/dashboard?filter[name]=text&filter[private]=false \
-H "x-api-key: <your-api-key>"
```

Available filters parameters:

| Field          | Description                                                                                                                                                                                                                   |                         Type |
|----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------:|
| name           | Filter dashboards by name (partial matches and case-insensitive supported).                                                                                                                                                   |                         Text |
| published      | Filter dashboards by publishing status (true, false).                                                                                                                                                                         |                      Boolean |
| private        | Filter dashboards by private status (true, false).                                                                                                                                                                            |                      Boolean |
| user           | Filter dashboards by author user id.                                                                                                                                                                                          |                         Text |
| user.role      | The role of the user who created the dashboard. If the requesting user does not have the ADMIN role, this filter is ignored.                                                                                                  | `ADMIN`, `MANAGER` or `USER` |
| application    | The application to which the dashboard belongs. Read more about this field [here](concepts.html#applications).                                                                                                                |          Text (single value) |
| env            | Environment to which the dashboard belongs. Multiple values can be combined using `,` as a separator. Does not support regexes. Read more about this field in the [Environments concept section](concepts.html#environments). |                         Text |
| is-highlighted | Filter dashboards by highlighted ones (true,false).                                                                                                                                                                           |                      Boolean |
| is-featured    | Filter dashboards by featured ones (true,false).                                                                                                                                                                              |                      Boolean |
| author-title   | Filter dashboards by the title of the author of the dashboard.                                                                                                                                                                |                         Text |

**Warning:** Please keep in mind that, due to the limitations of the [underlying endpoint used to find user ids by role](developer.html#user-management), the performance of the request while using `user.role` filter might be degraded.

**Deprecation notice:** The format *filter[filterName]=value* which was previously supported for some filters, is now deprecated, in favor of *filterName=value*.

### Pagination

> Example request to load page 2 using 25 results per page

```shell
curl -X GET https://api.resourcewatch.org/v1/dashboard?page[number]=2&page[size]=25 \
-H "x-api-key: <your-api-key>"
```

The Dashboards service adheres to the conventions defined in the [Pagination guidelines for the RW API](concepts.html#pagination), so we recommend reading that section for more details on how paginate your dashboards list.

### Sorting

> Sorting dashboards

```shell
curl -X GET https://api.resourcewatch.org/v1/dashboard?sort=name \
-H "x-api-key: <your-api-key>"
```

> Sorting dashboards by multiple criteria

```shell
curl -X GET https://api.resourcewatch.org/v1/dashboard?sort=name,slug \
-H "x-api-key: <your-api-key>"
```

> Explicit order of sorting

```shell
curl -X GET https://api.resourcewatch.org/v1/dashboard?sort=-name,+slug \
-H "x-api-key: <your-api-key>"
```

> Sorting dashboards by the role of the user who owns the dashboard

```shell
curl -X GET https://api.resourcewatch.org/v1/dashboard?sort=user.role \
-H "x-api-key: <your-api-key>"
```

The Dashboards service currently supports sorting using the `sort` query parameter. Sorting dashboards adheres to the conventions defined in the [Sorting guidelines for the RW API](concepts.html#sorting), so we strongly recommend reading that section before proceeding. In addition to all dashboard model fields, you can sort the returned results by the name (using `user.name`) or role (using `user.role`) of the user owner of the dashboard. Keep in mind that sorting by user data is restricted to ADMIN users.

**Please also keep in mind that, due to the limitations of the [underlying endpoint used to find user ids by name or role](developer.html#user-management), the performance of the request while using this sort might be degraded.**

### Include related entities

When loading dashboards, you can optionally pass an `includes` query argument to load additional data.

#### User

> Example request including the information for the user who owns the dashboard:

```shell
curl -X GET https://api.resourcewatch.org/v1/dashboard?includes=user \
-H "x-api-key: <your-api-key>"
```

> Example response:

```json
{
    "data": [
      {
        "id": "86",
        "type": "dashboards",
        "attributes": {
          "name": "Test dashboard three",
          "slug": "test-dashboard-three-cd4305e9-e3c8-456b-85a0-32eccb6100e6",
          "summary": "test dashboard three summary",
          "description": "test dashboard three description",
          "content": "test dashboard three description",
          "published": true,
          "photo": {
            "cover": "https://s3.amazonaws.com/image.jpg",
            "thumb": "https://s3.amazonaws.com/image.jpg",
            "original": "https://s3.amazonaws.com/image.jpg"
          },
          "user-id": "57ac9f9e29309063404573a2",
          "private": true,
          "env": "production",
          "application":  ["rw"],
          "is-highlighted": false,
          "is-featured": false,
          "author-title": "",
          "author-image": {
            "cover": "/author_images/cover/missing.png",
            "thumb": "/author_images/thumb/missing.png",
            "original": "/author_images/original/missing.png"
          },
          "user": {
            "name": "John Doe",
            "role": "ADMIN",
            "email": "john.doe@vizzuality.com"
          }
        }
      }
   ]
}
```

Loads the name and email address of the owner of the dashboard. If you request this issue as an authenticated user with ADMIN role, you will additionally get the owner's role.

If the data is not available (for example, the user has since been deleted), no `user` property will be added to the layer object.

**Please keep in mind that, due to the limitations of the [underlying endpoint used to find users by ids](developer.html#finding-users-by-ids), the performance of the request while including user information might be degraded.**

## Getting a dashboard by its ID

> How to get a dashboard by its ID:

```shell
curl -X GET https://api.resourcewatch.org/dashboard/24 \
-H "x-api-key: <your-api-key>"
```

> Response:

```json
{
  "data": {
    "id": "24",
    "type": "dashboards",
    "attributes": {
      "name": "Cities",
      "slug": "cities",
      "summary": "Traditional models of city development can lock us into congestion, sprawl, and inefficient resource use. However, compact, connected, and efficient growth can help ensure more competitive cities, and provide a better quality of life for citizens.  The decisions that national leaders, local officials, developers, and planners make today will determine how billions of urban cities will live over the next century. Already, half the global population resides in cities. That figure is set to increase to 70 percent by 2050.",
      "description": "",
      "content": "[]",
      "published": false,
      "photo": {
        "cover": "https://s3.amazonaws.com/wri-api-backups/resourcewatch/staging/dashboards/photos/000/000/024/cover/data?1523301918",
        "thumb": "https://s3.amazonaws.com/wri-api-backups/resourcewatch/staging/dashboards/photos/000/000/024/thumb/data?1523301918",
        "original": "https://s3.amazonaws.com/wri-api-backups/resourcewatch/staging/dashboards/photos/000/000/024/original/data?1523301918"
      },
      "user-id": "58f63c81bd32c60206ed6b12",
      "private": true,
      "env": "production",
      "user": null,
      "is-highlighted": false,
      "is-featured": false,
      "author-title": "",
      "author-image": {
        "cover": "/author_images/cover/missing.png",
        "thumb": "/author_images/thumb/missing.png",
        "original": "/author_images/original/missing.png"
      }
    }
  }
}
```

## Creating a dashboard

> Example request for creating a dashboard:

```shell
curl -X POST https://api.resourcewatch.org/v1/dashboard \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"  -d \
 '{
      "data": {
          "type": "dashboards",
          "attributes": {
              "name": "Cities",
              "summary": "Traditional models of city development can lock us into ...",
              "description": "",
              "content": "[{...}]",
              "published": false,
              "photo": {
                  "cover": "...",
                  "thumb": "...",
                  "original": "..."
              },
              "private": true,
              "env": "production",
              "application":  ["rw"],
              "is-highlighted": false,
              "is-featured": false,
              "author-title": "",
              "author-image": @file
          }
      }
  }'
```

> Example response:

```json
{
    "data": {
        "id": "243",
        "type": "dashboards",
        "attributes": {
            "name": "Cities",
            "slug": "cities-94bbc472-8970-4d9e-a3f2-d5422b1011e0",
            "summary": "Traditional models of city development can lock us into ...",
            "description": "",
            "content": "[{...}]",
            "published": false,
            "photo": {
                "cover": "...",
                "thumb": "...",
                "original": "..."
            },
            "user-id": "eb63867922e16e34ef3ce862",
            "private": true,
            "env": "production",
            "user": null,
            "application":  ["rw"],
            "is-highlighted": false,
            "is-featured": false,
            "author-title": "",
            "author-image": {
                "cover": "...",
                "thumb": "...",
                "original": "..."
            }
        }
    }
}
```

When creating a dashboard, the `application` field should be present and cannot contain any values that are not associated with the creating user's account. If an `application` value is not provided, `["rw"]` is used by default, and the process will fail if the user account does not belong to it. Additionally, keep in mind that this endpoint implements role-based access control in accordance with [the RW API role-based access control guidelines](concepts.html#role-based-access-control).

Supported fields:

| Name           | Description                                                                                                                               | Accepted values                   |
|----------------|-------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------|
| name           | Short name for the dashboard                                                                                                              | any valid text                    |
| summary        | Summary of the content of the dashboard                                                                                                   | any valid text                    |
| description    | Description of the dashboard                                                                                                              | any valid text                    |
| content        | Content of the dashboard, typically encoded as a JSON string                                                                              | any valid text                    |
| published      | If the dashboard is in a publishable state                                                                                                | boolean                           |
| photo          | Object containing a set of image urls associated with the dashboard                                                                       | object                            |
| private        | If the dashboard is private or publicly available.                                                                                        | boolean                           |
| production     | If the dashboard is available in the production environment.                                                                              | boolean                           |
| preproduction  | If the dashboard is available in the preproduction environment.                                                                           | boolean                           |
| staging        | If the dashboard is available in the staging environment.                                                                                 | boolean                           |
| application    | Application(s) to which the dashboard belongs. Defaults to `["rw"]`. Read more about this field [here](concepts.html#applications).       | array of strings                  |
| env            | Environment to which the dashboard belongs. Read more about this field in the [Environments concept section](concepts.html#environments). | string                            |
| is-highlighted | If this dashboard is highlighted (`true`/`false`). Defaults to `false`. Only accessible to users with `ADMIN` role.                       | boolean                           |
| is-featured    | If this dashboard is featured (`true`/`false`). Defaults to `false`. Can only be set by user with `ADMIN` role.                           | boolean                           |
| author-title   | The title of the author of the dashboard.                                                                                                 | any valid text                    |
| author-image   | File for the image of the author of the dashboard.                                                                                        | valid image file (jpg, jpeg, png) |

## Editing a dashboard

> Example request for updating a dashboard:

```shell
curl -X PATCH https://api.resourcewatch.org/v1/dashboard/<id of the dashboard> \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"  -d \
 '{
      "data": {
          "attributes": {
              "description": "Dashboard that uses cities"
          }
      }
  }'
```

> Example response:

```json
{
    "data": {
        "id": "243",
        "type": "dashboards",
        "attributes": {
            "name": "Cities",
            "slug": "cities-94bbc472-8970-4d9e-a3f2-d5422b1011e0",
            "summary": "Traditional models of city development can lock us ...",
            "description": "Dashboard that uses cities",
            "content": "[{...}]",
            "published": false,
            "photo": {
                "cover": "...",
                "thumb": "...",
                "original": "..."
            },
            "user-id": "eb63867922e16e34ef3ce862",
            "private": true,
            "env": "production",
            "user": null,
            "application": ["rw"],
            "is-highlighted": false,
            "is-featured": false,
            "author-title": "",
            "author-image": {
                "cover": "...",
                "thumb": "...",
                "original": "..."
            }
        }
    }
}
```

In order to perform this operation, the following conditions must be met:

- the user must comply with [the RW API role-based access control guidelines](concepts.html#role-based-access-control).
- the user must be logged in and belong to the same application as the dashboard

When updating the `application` field of a dashboard, a user cannot add values not associated with their user account.

## Delete dashboard

> Example request for deleting a dashboard:

```shell
curl -X DELETE https://api.resourcewatch.org/v1/dashboard/<id of the dashboard> \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"
```

In order to perform this operation, the following conditions must be met:

- the user must comply with [the RW API role-based access control guidelines](concepts.html#role-based-access-control).
- the user must be logged in and belong to the same application as the dashboard

## Clone dashboard

> Example request for cloning a dashboard:

```shell
curl -X POST https://api.resourcewatch.org/v1/dashboard/<id>/clone \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"  -d \
 '{
    "data": {
        "attributes": {
            "name": "Copy of Cities dashboard",
        }
    }
  }'
```

> Example response:

```json
{
    "data": {
        "id": "224",
        "type": "dashboards",
        "attributes": {
            "name": "Copy of Cities dashboard",
            "slug": "cities-a9cb2c87-f6b6-48cf-9b52-9e0de4fd8d6f",
            "summary": "Traditional models of city development can lock us into congestion, sprawl, and inefficient resource use. However, compact, ...",
            "description": "",
            "content": "[{\"id\":1511952250652,\"type\":\"widget\",\"content\":{\"widgetId\":\"b9186ce9-78ae-418b-a6d3-d521283ce485\",\"categories\":[]}},...}]",
            "published": false,
            "photo": {
                "cover": "/system/dashboards/photos/data?1523301918",
                "thumb": "/system/dashboards/photos/data?1523301918",
                "original": "/system/dashboards/photos/data?1523301918"
            },
            "user-id": "1111167922e16e34ef3ce872",
            "private": true,
            "env": "production",
            "application":  ["rw"],
            "is-highlighted": false,
            "is-featured": false,
            "author-title": "",
            "author-image": {
                "cover": "/author_images/cover/missing.png",
                "thumb": "/author_images/thumb/missing.png",
                "original": "/author_images/original/missing.png"
            }
        }
    }
}
```

Clones an existing dashboard using its ID. If the original dashboard contains widgets, they will be duplicated and the new ids will be used by the new dashboard. Data can be provided in the body of the request in order to overwrite the data of the original dashboard. In the example on the side, the `name` of the dashboard will be overwritten.

The following attributes can be overwritten by providing new values in the request body:

- `name`
- `description`
- `content`
- `published`
- `env`
- `summary`
- `photo`
- `private`
- `production`
- `preproduction`
- `staging`
- `author-title`
