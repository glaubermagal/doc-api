# Widget

## What is a widget?

In a nutshell, a RW API widget is a toolset to help you render your data in a visually more appealing way.
The [widget concept documentation](concepts.html#widget) will give you a more detailed description of this, and we
encourage you to read it before proceeding. We also recommend you take a look at our section
about [Vega](reference.html#widget-configuration-using-vega-grammar) if you plan on reusing existing widgets or upload
widgets that are reusable by other users. The RW API does not require you to use Vega, but we highly recommend that you
do, as it's the technology used by many of the widgets you'll find on the RW API.

## Getting all widgets

> Getting a list of widgets

```shell
curl -X GET "https://api.resourcewatch.org/v1/widget" \
-H "x-api-key: <your-api-key>"
```

> Example response:

```json
{
  "data": [
    {
      "id": "51851e22-1eda-4bf5-bbcc-cde3f9a3a943",
      "type": "widget",
      "attributes": {
        "name": "Example Widget",
        "dataset": "be76f130-ed4e-4972-827d-aef8e0dc6b18",
        "slug": "example-widget",
        "userId": "5820ad9469a0287982f4cd18",
        "description": "",
        "source": null,
        "sourceUrl": null,
        "authors": null,
        "application": [
          "rw"
        ],
        "verified": false,
        "default": false,
        "protected": false,
        "defaultEditableWidget": false,
        "published": true,
        "freeze": false,
        "env": "production",
        "queryUrl": null,
        "widgetConfig": "{}",
        "template": false,
        "layerId": null,
        "createdAt": "2017-02-08T15:30:34.505Z",
        "updatedAt": "2017-02-08T15:30:34.505Z"
      }
    },
    {
      ...
    }
  ],
  "links": {
    "first": "https://api.resourcewatch.org/v1/widget?page%5Bnumber%5D=1",
    "prev": "https://api.resourcewatch.org/v1/widget?page%5Bnumber%5D=1",
    "next": "https://api.resourcewatch.org/v1/widget?page%5Bnumber%5D=2&page%5Bsize%5D=10",
    "last": "https://api.resourcewatch.org/v1/widget?page%5Bnumber%5D=64&page%5Bsize%5D=10",
    "self": "https://api.resourcewatch.org/v1/widget?page%5Bnumber%5D=1&page%5Bsize%5D=10"
  },
  "meta": {
    "total-pages": 38,
    "total-items": 372,
    "size": 10
  }
}
```

This endpoint allows you to list existing widgets and their properties. The result is a paginated list of 10 widgets,
followed by metadata on total number of widgets and pages, as well as useful pagination links. By default, only widgets
with `env` value `production` are displayed. In the sections below, we’ll explore how you can customize this endpoint
call to match your needs.

### Getting all widgets for a dataset

> Return the widgets associated with a dataset

```shell
curl -X GET "https://api.resourcewatch.org/v1/dataset/<dataset-id>/widget" \
-H "x-api-key: <your-api-key>"
```

> Example response:

```json
{
  "data": [
    {
      "id": "73f00267-fe34-42aa-a611-13b102f38d75",
      "type": "widget",
      "attributes": {
        "name": "Precipitation Change in Puget Sound",
        "dataset": "06c44f9a-aae7-401e-874c-de13b7764959",
        "slug": "precipitation-change",
        "userId": "58333dcfd9f39b189ca44c75",
        "description": "NOAA nCLIMDIV Precipitation: Historical Precipitation",
        "source": null,
        "sourceUrl": null,
        "authors": null,
        "application": [
          "prep"
        ],
        "verified": false,
        "default": true,
        "protected": false,
        "defaultEditableWidget": false,
        "published": true,
        "freeze": false,
        "env": "production",
        "queryUrl": "query/06c44f9a-aae7-401e-874c-de13b7764959?sql=select annual as x, year as y from index_06c44f9aaae7401e874cde13b7764959%20order%20by%20year%20asc",
        "widgetConfig": {
          ...
        },
        "template": false,
        "layerId": null,
        "createdAt": "2016-08-03T16:17:06.863Z",
        "updatedAt": "2017-03-21T16:07:57.631Z"
      }
    },
    {
      ...
    }
  ],
  "links": {
    "self": "https://api.resourcewatch.org/v1/dataset/06c44f9a-aae7-401e-874c-de13b7764959/widget?page[number]=1&page[size]=10",
    "first": "https://api.resourcewatch.org/v1/dataset/06c44f9a-aae7-401e-874c-de13b7764959/widget?page[number]=1&page[size]=10",
    "last": "https://api.resourcewatch.org/v1/dataset/06c44f9a-aae7-401e-874c-de13b7764959/widget?page[number]=2&page[size]=10",
    "prev": "https://api.resourcewatch.org/v1/dataset/06c44f9a-aae7-401e-874c-de13b7764959/widget?page[number]=1&page[size]=10",
    "next": "https://api.resourcewatch.org/v1/dataset/06c44f9a-aae7-401e-874c-de13b7764959/widget?page[number]=2&page[size]=10"
  },
  "meta": {
    "total-pages": 2,
    "total-items": 18,
    "size": 10
  }
}
```

When handling widgets, it's common to want to limit results to those widgets associated with a given dataset. Besides
the [filters](#filters130) covered below, there's an additional convenience endpoint to get the widgets associated with
a dataset, as shown in this example.

### Getting all widgets for multiple datasets

> Return all widgets associated with two datasets

```shell
curl -X POST "https://api.resourcewatch.org/widget/find-by-ids" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H 'Content-Type: application/json' \
-d '{
	"ids": ["<dataset 1 id>", "<dataset 2 id>"]
}'
```

> Example response:

```json
{
  "data": [
    {
      "id": "73f00267-fe34-42aa-a611-13b102f38d75",
      "type": "widget",
      "attributes": {
        "name": "Precipitation Change in Puget Sound",
        "dataset": "06c44f9a-aae7-401e-874c-de13b7764959",
        "slug": "precipitation-change",
        "userId": "58333dcfd9f39b189ca44c75",
        "description": "NOAA nCLIMDIV Precipitation: Historical Precipitation",
        "source": null,
        "sourceUrl": null,
        "authors": null,
        "application": [
          "prep"
        ],
        "verified": false,
        "default": true,
        "protected": false,
        "defaultEditableWidget": false,
        "published": true,
        "freeze": false,
        "env": "production",
        "queryUrl": "query/06c44f9a-aae7-401e-874c-de13b7764959?sql=select annual as x, year as y from index_06c44f9aaae7401e874cde13b7764959%20order%20by%20year%20asc",
        "widgetConfig": {
          ...
        }
      }
    },
    {
      ...
    }
  ]
}
```

This endpoint allows users to load all widgets belonging to multiple datasets in a single request.

> Return all widgets associated with two datasets, that are associated with either `rw` or `prep` applications

```shell
curl -X POST "https://api.resourcewatch.org/widget/find-by-ids" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H 'Content-Type: application/json' \
-d '{
	"ids": ["<dataset 1 id>", "<dataset 2 id>"],
    "app": "rw,prep"
}'
```

> Return all widgets associated with two datasets, that are associated with both `rw` and `prep` applications
> simultaneously

```shell
curl -X POST "https://api.resourcewatch.org/widget/find-by-ids" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H 'Content-Type: application/json' \
-d '{
	"ids": ["<dataset 1 id>", "<dataset 2 id>"],
    "app": "rw@prep"
}'
```

Besides the required `ids` array, your request body may optionally include a `app` string value if you'd like to filter
the returned widgets by their `application`:

- Use a single value, like `rw`, if you want to show only widgets that have `rw` as one of their applications.
- Use a comma separated list, like `rw,prep`, if you want to show only widgets that have `rw` or `prep` as one of their
  applications.
- Note that the the filters do not need to match on the full application list. For example, the filters `rw,prep`
  and `rw@prep` will both match a widget with the application list `["rw", "prep", "gfw"]`.

Please note that, unlike [getting all widgets](reference.html#getting-all-widgets)
or [getting all widgets for a dataset](reference.html#getting-all-widgets-for-a-dataset), this endpoint does not come
with paginated results, nor does it support [pagination](reference.html#pagination129)
, [filtering](reference.html#filters130) or [sorting](reference.html#sorting131)
or [including related entities](reference.html#include-entities-associated-with-the-widgets) described in their
respective sections.

### Pagination

> Example request to load page 2 using 25 results per page

```shell
curl -X GET "https://api.resourcewatch.org/v1/widget?page[number]=2&page[size]=25" \
-H "x-api-key: <your-api-key>"
```

The Widgets service adheres to the conventions defined in
the [Pagination guidelines for the RW API](concepts.html#pagination), so we recommend reading that section for more
details on how paginate your widgets list.

### Filters

> Return the widgets filtered by those whose name contains emissions

```shell
curl -X GET "https://api.resourcewatch.org/v1/widget?name=emissions" \
-H "x-api-key: <your-api-key>"
```

> Return the widgets filtered by dataset

```shell
curl -X GET "https://api.resourcewatch.org/v1/widget?dataset=11de2bc1-368b-42ed-a207-aaff8ece752b" \
-H "x-api-key: <your-api-key>"
curl -X GET "https://api.resourcewatch.org/v1/dataset/11de2bc1-368b-42ed-a207-aaff8ece752b/widget" \
-H "x-api-key: <your-api-key>"
```

> Filter widgets by default value

```shell
curl -X GET "https://api.resourcewatch.org/v1/widget?default=false" \
-H "x-api-key: <your-api-key>"
```

> Filter widgets by environment

```shell
curl -X GET "https://api.resourcewatch.org/v1/widget?env=staging" \
-H "x-api-key: <your-api-key>"
```

> Return the widgets filtered by those whose applications contain both `rw` and `prep` applications simultaneously

```shell
curl -X GET "https://api.resourcewatch.org/v1/widget?app=rw@prep" \
-H "x-api-key: <your-api-key>"
```

The widget list endpoint provides a wide range of filters that you can use to tailor your widget listing. Filtering
widgets adheres to the conventions defined in the [Filter guidelines for the RW API](concepts.html#filtering), so we
strongly recommend reading that section before proceeding. In addition to these conventions, you'll be able to use the
great majority of the widget fields you'll find on the [widget reference](reference.html#widget-reference), with the
following exceptions:

- `id`
- `userName`
- `userRole`: filtering by the role of the owning user can be done using the `user.role` query argument. **Please keep
  in mind that, due to the limitations of
  the [underlying endpoint used to find user ids by role](developer.html#user-management), the performance of the
  request while using this filter might be degraded.**

Additionally, you can use the following filters:

- `collection`: filters by a [collection](reference.html#collections) id. Requires being authenticated.
- `favourite`: if any value is defined for this query param, only the widgets set by the user
  as [favorites](reference.html#favorites) will be returned. Requires being authenticated.

### Sorting

> Sorting widgets

```shell
curl -X GET "https://api.resourcewatch.org/v1/widget?sort=name" \
-H "x-api-key: <your-api-key>"
```

> Sorting widgets by multiple criteria

```shell
curl -X GET "https://api.resourcewatch.org/v1/widget?sort=name,slug" \
-H "x-api-key: <your-api-key>"
```

> Explicit order of sorting

```shell
curl -X GET "https://api.resourcewatch.org/v1/widget?sort=-name,+slug" \
-H "x-api-key: <your-api-key>"
```

> Sorting widgets by the role of the user who owns the widget

```shell
curl -X GET "https://api.resourcewatch.org/v1/widget?sort=user.role" \
-H "x-api-key: <your-api-key>"
```

The Widget service currently supports sorting using the `sort` query parameter. Sorting widgets adheres to the
conventions defined in the [Sorting guidelines for the RW API](concepts.html#sorting), so we strongly recommend reading
that section before proceeding. Additionally, you can check out the [Widget reference](reference.html#widget-reference)
section for a detailed description of the fields you can use when sorting. In addition to all widget model fields, you
can sort the returned results by the name (using `user.name`) or role (using `user.role`) of the user owner of the
widget. Keep in mind that sorting by user data is restricted to ADMIN users.

**Please also keep in mind that, due to the limitations of
the [underlying endpoint used to find user ids by name or role](developer.html#user-management), the performance of the
request while using this sort might be degraded.**

### Include entities associated with the widgets

When loading widget data, you can optionally pass an `includes` query argument to load additional data.

#### Include Vocabulary

Loads related [vocabularies](reference.html#vocabularies-and-tags). If none are found, an empty array is returned.

```shell
curl -X GET "https://api.resourcewatch.org/v1/widget?includes=vocabulary" \
-H "x-api-key: <your-api-key>"
```

> Example response:

```json
{
  "data": [
    {
      "id": "51851e22-1eda-4bf5-bbcc-cde3f9a3a943",
      "type": "widget",
      "attributes": {
        "name": "Example widget",
        "dataset": "be76f130-ed4e-4972-827d-aef8e0dc6b18",
        "slug": "example-widget",
        "userId": "5820ad9469a0287982f4cd18",
        "description": null,
        "source": null,
        "sourceUrl": null,
        "authors": null,
        "application": [
          "rw"
        ],
        "verified": false,
        "default": false,
        "protected": false,
        "defaultEditableWidget": false,
        "published": true,
        "freeze": false,
        "env": "production",
        "queryUrl": null,
        "widgetConfig": "{}",
        "template": false,
        "layerId": null,
        "createdAt": "2017-02-08T15:30:34.505Z",
        "updatedAt": "2017-02-08T15:30:34.505Z",
        "vocabulary": [
          {
            "id": "resourcewatch",
            "type": "vocabulary",
            "attributes": {
              "tags": [
                "inuncoast",
                "rp0002",
                "historical",
                "nosub"
              ],
              "name": "resourcewatch",
              "application": "rw"
            }
          }
        ]
      }
    }
  ]
}
```

#### Include User

```shell
curl -X GET "https://api.resourcewatch.org/v1/widget?includes=user" \
-H "x-api-key: <your-api-key>"
```

> Example response:

```json
{
  "data": [
    {
      "id": "51851e22-1eda-4bf5-bbcc-cde3f9a3a943",
      "type": "widget",
      "attributes": {
        "name": "Example widget",
        "dataset": "be76f130-ed4e-4972-827d-aef8e0dc6b18",
        "slug": "example-widget",
        "userId": "5820ad9469a0287982f4cd18",
        "description": "",
        "source": null,
        "sourceUrl": null,
        "authors": null,
        "application": [
          "rw"
        ],
        "verified": false,
        "default": false,
        "protected": false,
        "defaultEditableWidget": false,
        "published": true,
        "freeze": false,
        "env": "production",
        "queryUrl": null,
        "widgetConfig": "{}",
        "template": false,
        "layerId": null,
        "createdAt": "2017-02-08T15:30:34.505Z",
        "updatedAt": "2017-02-08T15:30:34.505Z",
        "user": {
          "name": "John Sample",
          "email": "john.sample@vizzuality.com"
        }
      }
    }
  ]
}
```

Loads the name and email address of the author of the widget. If the user issuing the request has role `ADMIN`, the
response will also display the role of the widget's author. If the data is not available (for example, the user has
since been deleted), no `user` property will be added to the widget object.

**Please keep in mind that, due to the limitations of
the [underlying endpoint used to find users by ids](developer.html#finding-users-by-ids), the performance of the request
while including user information might be degraded.**

#### Include Metadata

```shell
curl -X GET "https://api.resourcewatch.org/v1/widget?includes=metadata" \
-H "x-api-key: <your-api-key>"
```

> Example response:

```json
{
  "data": [
    {
      "id": "51851e22-1eda-4bf5-bbcc-cde3f9a3a943",
      "type": "widget",
      "attributes": {
        "name": "Example widget",
        "dataset": "be76f130-ed4e-4972-827d-aef8e0dc6b18",
        "slug": "example-widget",
        "userId": "5820ad9469a0287982f4cd18",
        "description": "",
        "source": null,
        "sourceUrl": null,
        "authors": null,
        "application": [
          "rw"
        ],
        "verified": false,
        "default": false,
        "protected": false,
        "defaultEditableWidget": false,
        "published": true,
        "freeze": false,
        "env": "production",
        "queryUrl": null,
        "widgetConfig": "{}",
        "template": false,
        "layerId": null,
        "createdAt": "2017-02-08T15:30:34.505Z",
        "updatedAt": "2017-02-08T15:30:34.505Z",
        "metadata": [
          {
            "id": "5aeb1c74a096b50010f3843f",
            "type": "metadata",
            "attributes": {
              "dataset": "86777822-d995-49cd-b9c3-d4ea4f82c0a3",
              "application": "rw",
              "resource": {
                "id": "51851e22-1eda-4bf5-bbcc-cde3f9a3a943",
                "type": "widget"
              },
              "language": "en",
              "info": {
                "caption": "t",
                "widgetLinks": []
              },
              "createdAt": "2018-05-03T14:28:04.482Z",
              "updatedAt": "2018-06-07T11:30:40.054Z",
              "status": "published"
            }
          }
        ]
      }
    }
  ]
}
```

Loads the metadata available for the widget. If none are found, an empty array is returned.

#### Requesting multiple additional entities

```shell
curl -X GET "https://api.resourcewatch.org/v1/widget?includes=metadata,user,vocabulary" \
-H "x-api-key: <your-api-key>"
```

You can request multiple related entities in a single request using commas to separate multiple keywords

## Getting a widget by id

> Getting a widget by id:

```shell
curl -X GET "https://api.resourcewatch.org/v1/widget/<widget_id>" \
-H "x-api-key: <your-api-key>"
curl -X GET "https://api.resourcewatch.org/v1/dataset/<dataset-id>/widget/<widget_id>" \
-H "x-api-key: <your-api-key>"
```

> Example response:

```json
{
  "data": {
    "id": "51851e22-1eda-4bf5-bbcc-cde3f9a3a943",
    "type": "widget",
    "attributes": {
      "name": "Example widget",
      "dataset": "be76f130-ed4e-4972-827d-aef8e0dc6b18",
      "slug": "example-widget",
      "userId": "5820ad9469a0287982f4cd18",
      "description": "",
      "source": null,
      "sourceUrl": null,
      "authors": null,
      "application": [
        "rw"
      ],
      "verified": false,
      "default": false,
      "protected": false,
      "defaultEditableWidget": false,
      "published": true,
      "freeze": false,
      "env": "production",
      "queryUrl": null,
      "widgetConfig": "{}",
      "template": false,
      "layerId": null,
      "createdAt": "2017-02-08T15:30:34.505Z",
      "updatedAt": "2017-02-08T15:30:34.505Z"
    }
  }
}
```

If you know the `id` of a widget, then you can access it directly. Ids are case-sensitive.

### Overwrite the query url of a widget response

> Getting a widget by id with a custom query url value:

```shell
curl -X GET "https://api.resourcewatch.org/v1/widget/<widget_id>?queryUrl=/v1/query?sql=Select%20*%20from%20data" \
-H "x-api-key: <your-api-key>"
```

> Example response:

```json
{
  "data": {
    "id": "51851e22-1eda-4bf5-bbcc-cde3f9a3a943",
    "type": "widget",
    "attributes": {
      "name": "Example widget",
      "dataset": "be76f130-ed4e-4972-827d-aef8e0dc6b18",
      "slug": "example-widget",
      "userId": "5820ad9469a0287982f4cd18",
      "description": null,
      "source": null,
      "sourceUrl": null,
      "authors": null,
      "application": [
        "rw"
      ],
      "verified": false,
      "default": false,
      "protected": false,
      "defaultEditableWidget": false,
      "published": true,
      "freeze": false,
      "env": "production",
      "queryUrl": "/v1/query?sql=Select * from data",
      "widgetConfig": {
        "data": {
          "url": "/v1/query?sql=Select * from data"
        }
      },
      "template": false,
      "layerId": null,
      "createdAt": "2017-02-08T15:30:34.505Z",
      "updatedAt": "2017-02-08T15:30:34.505Z"
    }
  }
}
```

When getting a single widget, you can optionally provide a `queryUrl` query parameter. If provided, this parameter will
overwrite the following response values:

- `data.attributes.queryUrl`
- `data.attributes.widgetConfig.data.url`
- `data.attributes.widgetConfig.data[0].url`

This overwrite will only happen if a previous value already existed for the respective field. Also, keep in mind that
these changes will NOT be persisted to the database, and will only affect the current request's response.

### Customize the query url of a widget response

> Getting a widget by id with a custom query url parameters:

```shell
curl -X GET "https://api.resourcewatch.org/v1/widget/<widget_id>?geostore=ungeostore" \
-H "x-api-key: <your-api-key>"
```

> Example response:

```json
{
  "data": {
    "id": "51851e22-1eda-4bf5-bbcc-cde3f9a3a943",
    "type": "widget",
    "attributes": {
      "name": "Example widget",
      "dataset": "be76f130-ed4e-4972-827d-aef8e0dc6b18",
      "slug": "example-widget",
      "userId": "5820ad9469a0287982f4cd18",
      "description": null,
      "source": null,
      "sourceUrl": null,
      "authors": null,
      "application": [
        "rw"
      ],
      "verified": false,
      "default": false,
      "protected": false,
      "defaultEditableWidget": false,
      "published": true,
      "freeze": false,
      "env": "production",
      "queryUrl": "<previous queryUrl value>?geostore=ungeostore",
      "widgetConfig": {
        "data": {
          "url": "<previous widgetConfig.data.url value>?geostore=ungeostore"
        }
      },
      "template": false,
      "layerId": null,
      "createdAt": "2017-02-08T15:30:34.505Z",
      "updatedAt": "2017-02-08T15:30:34.505Z"
    }
  }
}
```

> Getting a widget by id with a custom query url value parameters:

```shell
curl -X GET "https://api.resourcewatch.org/v1/widget/<widget_id>?queryUrl=/v1/query?sql=Select%20*%20from%20data&geostore=ungeostore" \
-H "x-api-key: <your-api-key>"
```

> Example response:

```json
{
  "data": {
    "id": "51851e22-1eda-4bf5-bbcc-cde3f9a3a943",
    "type": "widget",
    "attributes": {
      "name": "Example widget",
      "dataset": "be76f130-ed4e-4972-827d-aef8e0dc6b18",
      "slug": "example-widget",
      "userId": "5820ad9469a0287982f4cd18",
      "description": null,
      "source": null,
      "sourceUrl": null,
      "authors": null,
      "application": [
        "rw"
      ],
      "verified": false,
      "default": false,
      "protected": false,
      "defaultEditableWidget": false,
      "published": true,
      "freeze": false,
      "env": "production",
      "queryUrl": "/v1/query?sql=Select * from data&geostore=ungeostore",
      "widgetConfig": {
        "data": {
          "url": "/v1/query?sql=Select * from data&geostore=ungeostore"
        }
      },
      "template": false,
      "layerId": null,
      "createdAt": "2017-02-08T15:30:34.505Z",
      "updatedAt": "2017-02-08T15:30:34.505Z"
    }
  }
}
```

When getting a single widget, you can optionally provide additional custom query parameter. These parameters will be
appended to the following response values:

- `data.attributes.queryUrl`
- `data.attributes.widgetConfig.data.url`
- `data.attributes.widgetConfig.data[0].url`

The widget service assumes these values are URLs, and will append your custom query parameters as such:

- If the value already has a `?` character present, your custom query parameters will be appended with `&` preceding
  them.
- If no `?` is present, the first custom query parameter will be appended with a `?`, and the following with a `&`before
  them.

This overwrite will only happen if a previous value already existed for the respective field. Also, keep in mind that
these changes will NOT be persisted to the database, and will only affect the current request's response. You can
combine these custom query parameters with
an [overwritten query url](reference.html#overwrite-the-query-url-of-a-widget-response), as seen on the included
example.

### Include entities associated with the widget

You can load related `user`, `vocabulary` and `metadata` data in the same request.
See [this section](reference.html#include-entities-associated-with-the-widgets) for more details.

## Creating a widget

> To create a widget, you need to provide at least the following details:

```shell
curl -X POST "https://api.resourcewatch.org/v1/dataset/<dataset-id>/widget" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"  -d \
 '{
    "application":[
      "gfw"
    ],
    "name":"Example Widget"
  }'


curl -X POST "https://api.resourcewatch.org/v1/widget" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"  -d \
 '{
    "application":[
      "gfw"
    ],
    "name":"Example Widget",
    "dataset": "7fa6ec77-5ab0-43f4-9a4c-a3d19bed1e90"    
  }'
```

> Response

```json
{
  "data": {
    "id": "7946bca1-6c2a-4329-a127-558ef9551eba",
    "type": "widget",
    "attributes": {
      "name": "Example Widget",
      "dataset": "7fa6ec77-5ab0-43f4-9a4c-a3d19bed1e90",
      "slug": "Example-Widget_2",
      "userId": "5dbadb0adf2dc74d2ad05dfb",
      "application": [
        "gfw"
      ],
      "verified": false,
      "default": false,
      "protected": false,
      "defaultEditableWidget": false,
      "published": true,
      "freeze": false,
      "env": "production",
      "template": false,
      "createdAt": "2020-06-11T14:13:19.677Z",
      "updatedAt": "2020-06-11T14:13:19.677Z"
    }
  }
}
```

In this section we'll guide you through the process of creating a widget in the RW API. Widget creation is available to
all registered API users, and will allow you to store your own widget visualisation settings on the API, for reusing and
sharing.

Before creating a widget, there are a few things you must know and do:

- In order to be able to create a widget, you need to be [authenticated](reference.html#authentication).
- Depending on your user account's role, you may have permission to create a widget but not delete it afterwards - read
  more about this in [the RW API role-based access control guidelines](concepts.html#role-based-access-control).
- The widgets you create on the RW API will be publicly visible and available to other users.

Creating a widget is done using a POST request and passing the relevant data as body files. The supported body fields
are as defined on the [widget reference](reference.html#widget-reference) section, but the minimum field list you must
specify for all widgets is:

- name
- application
- dataset - which can be specified either through the URL or the POST body.

The widget service was built to be very flexible, and not be restricted to specific widget rendering libraries or tools.
While we recommend using [Vega](reference.html#widget-configuration-using-vega-grammar), it's up to you to decide which
library to use, and how to save your custom data within a widget's `widgetConfig` field. While there is no hard limit on
this, widgets that rely on excessively large `widgetConfig` objects have been known to cause issues, so we recommend
being mindful of this.

#### Widget thumbnails

> Preview of the thumbnail of a widget

```shell
curl -X GET "https://resourcewatch.org/embed/widget/<widget_id>" \
-H "x-api-key: <your-api-key>"
```

When a widget is created or updated, the RW API will automatically try to generate a thumbnail preview of it. This is
done using the renderer that's part of the [Resource Watch](https://resourcewatch.org) website, and will only produce a
thumbnail if your widget uses the same approach as the RW website for declaring its widgets. Searching for widgets
belonging to the `rw` application will give you an idea of how this is achieved.

The thumbnail process is asynchronous, meaning it may take up to one minute for your newly created or updated widget to
have a valid and/or up to date `thumbnailUrl` value. If the thumbnail generation process fails (for example, if you
decide to use a different rendering tool and data structure), the thumbnail generation will fail silently, but will not
affect the actual widget creation or update process.

#### Errors for creating a widget

| Error code | Error message                          | Description                                                                                                             |
|------------|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------|
| 400        | `<field>`: `<field>` can not be empty  | Your are missing a required field value.                                                                                |
| 400        | - `<field>`: must be a `<restriction>` | The value provided for the mentioned field does not match the requirements.                                             |
| 401        | Unauthorized                           | You are not authenticated.                                                                                              |
| 403        | Forbidden                              | You are trying to create a widget with one or more `application` values that are not associated with your user account. |
| 404        | Dataset not found                      | The provided dataset id does not exist.                                                                                 |

## Updating a widget

> To update a widget, you have to do one of the following PATCH requests:

```shell
curl -X PATCH "https://api.resourcewatch.org/v1/dataset/<dataset-id>/widget/<widget_id_or_slug>" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"  -d \
 '{
    "name":"New Example Widget Name"
 }'


curl -X PATCH "https://api.resourcewatch.org/v1/widget/<widget_id_or_slug>" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"  -d \
 '{
    "name":"New Example Widget Name",
    "dataset":"<dataset-id>
 }'
```

> Response

```json
{
  "data": {
    "id": "7946bca1-6c2a-4329-a127-558ef9551eba",
    "type": "widget",
    "attributes": {
      "name": "New Example Widget Name",
      "dataset": "7fa6ec77-5ab0-43f4-9a4c-a3d19bed1e90",
      "slug": "Example-Widget_2",
      "userId": "5dbadb0adf2dc74d2ad05dfb",
      "application": [
        "gfw"
      ],
      "verified": false,
      "default": false,
      "protected": false,
      "defaultEditableWidget": false,
      "published": true,
      "freeze": false,
      "env": "production",
      "template": false,
      "createdAt": "2020-06-11T14:13:19.677Z",
      "updatedAt": "2020-06-11T14:13:19.677Z"
    }
  }
}
```

The update widget endpoint allows you to modify the details of an existing widget. As noted on
the [widget concept](concepts.html#widget) documentation, the widget object stores the details of how widget is meant to
be rendered, but may not contain the actual data. As such, if you are looking to update the data that's being displayed
on your widget, this is probably not the endpoint you're looking for - you may want
to [update your dataset](reference.html#updating-a-dataset) instead. Use this endpoint if you want to modify things like
legend details, color schemes, etc - this will depend on your rendering implementation.

Unless specified otherwise in their description, all the fields present in
the [widget reference](reference.html#widget-reference) section can be updated using this endpoint. When passing new
values for Object type fields, like `widgetConfig`, the new value will fully overwrite the previous one. It’s up to you,
as an API user, to build any merging logic into your application.

To perform this operation, the following conditions must be met:

- the user must be logged in and belong to the same application as the widget
- the user must match one of the following:
  - have role `ADMIN`
  - have role `MANAGER` or `USER` and be the widget's owner (through the `userId` field of the widget) and belong to all
    the applications associated with the widget.

#### Widget thumbnails

When a widget is updated, a new thumbnail is generated. Refer to [this section](reference.html#widget-thumbnails) for
details on this process.

#### Errors for updating a widget

| Error code | Error message                          | Description                                                                                                                                                                                                    |
|------------|----------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 400        | - `<field>`: must be a `<restriction>` | The value provided for the mentioned field does not match the requirements.                                                                                                                                    |
| 401        | Unauthorized                           | You need to be logged in to be able to update a widget.                                                                                                                                                        |
| 403        | Forbidden                              | You need to either have the `ADMIN` role, or have role `MANAGER` or `USER` and be the widget's owner (through the `userId` field of the widget) and belong to all the applications associated with the widget. |
| 403        | Forbidden                              | You are trying to update a widget with one or more `application` values that are not associated with your user account.                                                                                        |
| 404        | Widget with id <id> doesn't exist      | A widget with the provided id does not exist.                                                                                                                                                                  |
| 404        | Dataset not found                      | A dataset with the provided id does not exist.                                                                                                                                                                 |

## Cloning a widget

> To clone a widget, you should use one of the following POST requests:

```shell
curl -X POST "https://api.resourcewatch.org/v1/widget/<widget_id_or_slug>/clone" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"
```

```shell
curl -X POST "https://api.resourcewatch.org/v1/dataset/<dataset-id>/widget/<widget_id_or_slug>/clone" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"
```

> Response

```json
{
  "data": {
    "id": "7946bca1-6c2a-4329-a127-558ef9551eba",
    "type": "widget",
    "attributes": {
      "name": "Example Widget",
      "dataset": "7fa6ec77-5ab0-43f4-9a4c-a3d19bed1e90",
      "slug": "Example-Widget_2",
      "userId": "5dbadb0adf2dc74d2ad05dfb",
      "application": [
        "gfw"
      ],
      "verified": false,
      "default": false,
      "protected": false,
      "defaultEditableWidget": false,
      "published": true,
      "freeze": false,
      "env": "production",
      "template": false,
      "createdAt": "2020-06-11T14:13:19.677Z",
      "updatedAt": "2020-06-11T14:13:19.677Z"
    }
  }
}
```

This endpoint allows you to duplicate an existing widget. The newly created widget is identical to the old one, except:

- The `userId` will be the id of the user who issued the clone request
- `name` and `description` will be the same but can optionally be modified by the clone request (see the provided
  example)
- `createdAt` and `updatedAt` will have the current time.

To perform this operation, the following conditions must be met:

- the user must be logged in and belong to the same application as the widget
- the user must match one of the following:
  - have role `ADMIN` or `MANAGER`
  - have role `USER` and be the widget's owner (through the `userId` field of the widget) and belong to all the
    applications associated with the widget.

> Cloning a widget while optionally setting a new name or description:

```shell
curl -X POST "https://api.resourcewatch.org/v1/widget/<widget_id_or_slug>/clone" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"  -d \
 '{
   "name": "name for the cloned widget",
   "description": "description of the cloned widget"
}'
```

```shell
curl -X POST "https://api.resourcewatch.org/v1/dataset/<dataset-id>/widget/<widget_id_or_slug>/clone" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"  -d \
 '{
   "name": "name for the cloned widget",
   "description": "description of the cloned widget"
}'
```

> Response

```json
{
  "data": {
    "id": "7946bca1-6c2a-4329-a127-558ef9551eba",
    "type": "widget",
    "attributes": {
      "name": "name for the cloned widget",
      "description": "description of the cloned widget",
      "dataset": "7fa6ec77-5ab0-43f4-9a4c-a3d19bed1e90",
      "slug": "Example-Widget_2",
      "userId": "5dbadb0adf2dc74d2ad05dfb",
      "application": [
        "gfw"
      ],
      "verified": false,
      "default": false,
      "protected": false,
      "defaultEditableWidget": false,
      "published": true,
      "freeze": false,
      "env": "production",
      "template": false,
      "createdAt": "2020-06-11T14:13:19.677Z",
      "updatedAt": "2020-06-11T14:13:19.677Z"
    }
  }
}
```

#### Widget thumbnails

When a widget is cloned, a new thumbnail is generated. Refer to [this section](reference.html#widget-thumbnails) for
details on this process.

#### Errors for cloning a widget

| Error code | Error message                     | Description                                                                                                                                                                                                    |
|------------|-----------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 401        | Unauthorized                      | You need to be logged in to be able to clone a widget.                                                                                                                                                         |
| 403        | Forbidden                         | You need to either have the `ADMIN` or `MANAGER` role, or have role `USER` and be the widget's owner (through the `userId` field of the widget) and belong to all the applications associated with the widget. |
| 404        | Widget with id <id> doesn't exist | A widget with the provided id or slug does not exist.                                                                                                                                                          |

## Deleting a widget

> Example DELETE request that deletes a widget:

```shell
curl -X DELETE "https://api.resourcewatch.org/v1/dataset/<dataset-id>/widget/<widget_id>" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"


curl -X DELETE "https://api.resourcewatch.org/v1/widget/<widget_id>" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"
```

> Response

```json
{
  "data": {
    "id": "7946bca1-6c2a-4329-a127-558ef9551eba",
    "type": "widget",
    "attributes": {
      "name": "name for the cloned widget",
      "description": "description of the cloned widget",
      "dataset": "7fa6ec77-5ab0-43f4-9a4c-a3d19bed1e90",
      "slug": "Example-Widget_2",
      "userId": "5dbadb0adf2dc74d2ad05dfb",
      "application": [
        "gfw"
      ],
      "verified": false,
      "default": false,
      "protected": false,
      "defaultEditableWidget": false,
      "published": true,
      "freeze": false,
      "env": "production",
      "template": false,
      "createdAt": "2020-06-11T14:13:19.677Z",
      "updatedAt": "2020-06-11T14:13:19.677Z"
    }
  }
}
```

Use this endpoint if you'd like to delete a widget from the RW API. As a widget object may not store the actual data
being displayed, this may only delete the widget settings, but the actual data may continue to be available at its
source.

Besides deleting the widget itself, this endpoint also deletes graph vocabularies and metadata related to the widget
itself. These delete operations are issued after the widget itself is deleted. The process is not atomic, and the
response is based solely on the result of the deletion of the widget itself, not related resources. For example, is the
metadata service is temporarily unavailable when you issue your delete widget request, the widget itself will be
deleted, but the associated metadata will continue to exist. The response will not reflect the failure to delete
metadata in any way.

In order to delete a widget, the following conditions must be met:

- the widget's `protected` property must be set to `false`.
- the user must be logged in and belong to the same application as the widget.
- the user must comply with [the RW API role-based access control guidelines](concepts.html#role-based-access-control).

#### Errors for deleting a widget

| Error code | Error message                     | Description                                                                                                                           |
|------------|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|
| 400        | Widget is protected               | You are attempting to delete a widget that has `protected` set to prevent deletion.                                                   |
| 401        | Unauthorized                      | You need to be logged in to be able to delete a widget.                                                                               |
| 403        | Forbidden                         | You need to either have the `ADMIN` role, or have role `MANAGER` and be the widget's owner (through the `userId` field of the widget) |
| 404        | Dataset not found                 | A dataset with the provided id does not exist.                                                                                        |
| 404        | widget with id <id> doesn't exist | A widget with the provided id does not exist.                                                                                         |

## Deleting widgets by user id

> Example request that deletes widgets by user id

```shell
curl -X DELETE "https://api.resourcewatch.org/v1/dataset/<dataset-id>/widget/<widget_id>" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"


curl -X DELETE "https://api.resourcewatch.org/v1/widget/<widget_id>" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"
```

> Response

```json
{
  "deletedLayers": [
    {
      "id": "7946bca1-6c2a-4329-a127-558ef9551eba",
      "type": "widget",
      "attributes": {
        "name": "water widget",
        "description": "description of the water widget",
        "dataset": "7fa6ec77-5ab0-43f4-9a4c-a3d19bed1e90",
        "slug": "water_widget",
        "userId": "5dbadb0adf2dc74d2ad05dfb",
        "application": [
          "gfw"
        ],
        "verified": false,
        "default": false,
        "protected": false,
        "defaultEditableWidget": false,
        "published": true,
        "freeze": false,
        "env": "production",
        "template": false,
        "createdAt": "2020-06-11T14:13:19.677Z",
        "updatedAt": "2020-06-11T14:13:19.677Z"
      }
    }
  ],
  "protectedLayers": [
    {
      "id": "6a46bca1-6c2a-4329-a127-558ef9551eba",
      "type": "widget",
      "attributes": {
        "name": "fires widget",
        "description": "description of the fires widget",
        "dataset": "7fa6ec77-5ab0-43f4-9a4c-a3d19bed1e90",
        "slug": "fires_widget",
        "userId": "5dbadb0adf2dc74d2ad05dfb",
        "application": [
          "gfw"
        ],
        "verified": false,
        "default": false,
        "protected": true,
        "defaultEditableWidget": false,
        "published": true,
        "freeze": false,
        "env": "production",
        "template": false,
        "createdAt": "2020-06-11T14:13:19.677Z",
        "updatedAt": "2020-06-11T14:13:19.677Z"
      }
    }
  ]
}
```

Use this endpoint if you'd like to delete all widgets from associated with a user. As a widget object does not store the
actual data being displayed, this will only delete the widget settings, but the actual data will continue to be
available at its source.

Besides deleting the widgets themselves, this endpoint also deletes graph vocabularies and metadata related to the widgets.
These delete operations are issued after each widget itself is deleted. The process is not atomic, and the output of the
API request is based solely on the result of the deletion of the widget themselves. For example, is the metadata service
is temporarily unavailable when you issue your delete widgets request, the widgets themselves will be deleted, but the
associated metadata will continue to exist. The response will not reflect the failure to delete metadata in any way.

Any microservice or user with ADMIN role can use this endpoint. Regular users can use this endpoint to delete the
widgets they own.

The response includes two lists of widgets:

- `deletedWidgets`: an unpaginated list of all datasets that were widgets as part of this operation.
- `protectedWidgets`: an unpaginated list of all widgets associated with the provided user, that have `protected` status
  set to `true`,
  and thus were not deleted.

#### Errors for deleting widgets by user id

| Error code | Error message | Description                                                                                                           |
|------------|---------------|-----------------------------------------------------------------------------------------------------------------------|
| 401        | Unauthorized  | You need to be logged in to be able to delete widgets.                                                                |
| 403        | Forbidden     | You are trying to delete the widgets of an user that is not the same logged user, not an ADMIN user or a microservice |

## Widget reference

This section gives you a complete view at the properties that are maintained as part of widget. When interacting with a
widget (on get, on create, etc) you will find most of these properties available to you, although they may be organized
in a slightly different structure (ie: on get, everything but the `id` is nested inside an `attributes` object).

You can find more details in
the [source code](https://github.com/resource-watch/widget/blob/master/app/src/models/widget.model.js).

| Field name            | Type    | Required            | Default value  | Description                                                                                                                                                                                                                                                                                                 |
|-----------------------|---------|---------------------|----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| id                    | String  | Yes (autogenerated) |                | Unique Id of the widget. Auto generated on creation. Cannot be modified by users.                                                                                                                                                                                                                           |
| dataset               | String  | Yes                 |                | Id of the dataset to which the widget corresponds. Set on widget creation, cannot be modified.                                                                                                                                                                                                              |
| name                  | String  | Yes                 |                | Name of the widget.                                                                                                                                                                                                                                                                                         |
| slug                  | String  | Yes (autogenerated) |                | Slug of the widget. Auto generated on creation. Cannot be modified by users.                                                                                                                                                                                                                                |
| userId                | String  | Yes (autopopulated) |                | Id of the user who created the widget. Set automatically on creation. Cannot be modified by users.                                                                                                                                                                                                          |
| description           | String  | No                  |                | User defined description of the widget.                                                                                                                                                                                                                                                                     |
| source                | String  | No                  |                | Description of the source of the widget's data, as it's meant to be displayed to the end user.                                                                                                                                                                                                              |
| sourceUrl             | String  | No                  |                | URL of the source of the data, as it's meant to be displayed to the end user.                                                                                                                                                                                                                               |
| authors               | String  | No                  |                | Author or authors of the data displayed on the widget, as it's meant to be displayed to the end user.                                                                                                                                                                                                       |
| queryUrl              | String  | No                  |                | URL of the RW API query or external URL containing the data displayed on the widget                                                                                                                                                                                                                         |
| thumbnailUrl          | String  | No                  |                | URL of a example thumbnail of the rendered widget.                                                                                                                                                                                                                                                          |
| env                   | String  | Yes                 | production     | Environment to which the widget belongs. Read more about this field in the [Environments concept section](concepts.html#environments).                                                                                                                                                                      |
| widgetConfig          | Object  | No                  |                | Schema-less object meant to host widget behavior configuration.                                                                                                                                                                                                                                             |
| application           | Array   | Yes                 |                | Applications associated with this widget. Read more about this field [here](concepts.html#applications).                                                                                                                                                                                                    |
| layerId               | String  | No                  |                | Id of the layer to which the widget corresponds.                                                                                                                                                                                                                                                            |
| verified              | Boolean | Yes                 | false          |                                                                                                                                                                                                                                                                                                             |
| default               | Boolean | No                  | false          | If the widget should be used as the dataset's default widget.                                                                                                                                                                                                                                               |
| protected             | Boolean | Yes                 | false          | If the widget is protected. A protected widget cannot be deleted.                                                                                                                                                                                                                                           |
| defaultEditableWidget | Boolean | Yes                 | false          | Used by the RW frontend to determine if a widget is the default widget displayed on the Explore detail page for a given dataset.                                                                                                                                                                            |
| template              | Boolean | Yes                 | false          | If true, this widget is not necessarily meant to be rendered as-is, but rather as a template for other widgets to be generated from.                                                                                                                                                                        |
| published             | Boolean | Yes                 | true           | If the widget is published or not.                                                                                                                                                                                                                                                                          |
| freeze                | Boolean | Yes                 | false          | If true, the widget is meant to represent data from a specific snapshot taken at a given moment in time, as opposed to using a potentially mutable dataset. This data capturing functionality must be implemented by consuming applications, as this value has no logic associated with it on the API side. |
| userName              | String  | No (autogenerated)  | null           | Name of the user who created the widget. This value is used only internally, and is never directly exposed through the API. Cannot be modified by users.                                                                                                                                                    |
| userRole              | String  | No (autogenerated)  | null           | Role of the user who created the widget. This value is used only internally, and is never directly exposed through the API. Cannot be modified by users.                                                                                                                                                    |
| createdAt             | Date    | No (autogenerated)  | <current date> | Automatically maintained date of when the widget was created. Cannot be modified by users.                                                                                                                                                                                                                  |
| updatedAt             | Date    | No (autogenerated)  | <current date> | Automatically maintained date of when the widget was last updated. Cannot be modified by users.                                                                                                                                                                                                             |
