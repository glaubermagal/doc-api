# Layer

## What is a layer?

By now, you are probably already familiar with [datasets](concepts.html#dataset) and [querying](concepts.html#query)
them (if you are not, now is a good time to get up to speed on those). Many of the datasets you'll find on the RW API -
and perhaps the datasets you are uploading too - contain georeferenced information. If that's the case, then you may
want to render your data as a web map layer, and the RW API's *layer* endpoints can help you with that.

As we've seen in the [layer concept docs](concepts.html#layer), a RW API layer may store data in different formats,
depending on the needs of its author. This is done using the several open format fields a layer has. To keep this
documentation easy to understand, we'll spit our approach to layers into two sections:

- We'll first discuss the details of the endpoints that allow you
  to [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) layers, without focusing on the actual data
  contained in it.
- In an future version of these docs, we'll dive deeper into some of the most common structures used to store data in
  the layer's open format fields.

After viewing the documentation below, consider looking at
the [webmap tutorial](tutorials.html#mapbox-webmap-quickstart) for a step-by-step guide to rendering an actual layer on
a web application.

## Getting all layers

> Getting a list of layers

```shell
curl -X GET "https://api.resourcewatch.org/v1/layer" \
-H "x-api-key: <your-api-key>"
```

> Example response:

```json
{
  "data": [
    {
      "id": "e5c3e7c5-19ae-4ca0-a461-71f1f67aa553",
      "type": "layer",
      "attributes": {
        "slug": "total-co2-emissions-by-year",
        "userId": "5858f37140621f11066fb2f7",
        "application": [
          "rw"
        ],
        "name": "Total CO2 emissions by year",
        "default": false,
        "dataset": "11de2bc1-368b-42ed-a207-aaff8ece752b",
        "env": "production",
        "provider": "cartodb",
        "iso": [],
        "description": null,
        "layerConfig": {
          "account": "rw",
          "body": {
            "maxzoom": 18,
            "minzoom": 3,
            "layers": [
              {
                "type": "mapnik",
                "options": {
                  "sql": "SELECT * cait_2_0_country_ghg_emissions_filtered",
                  "cartocss": "",
                  "cartocss_version": "2.3.0"
                }
              }
            ]
          }
        },
        "legendConfig": {
          "marks": {
            "type": "rect",
            "from": {
              "data": "table"
            }
          }
        },
        "applicationConfig": {},
        "staticImageConfig": {}
      }
    }
  ],
  "links": {
    "self": "https://api.resourcewatch.org/v1/layer?page[number]=1&page[size]=10",
    "first": "https://api.resourcewatch.org/v1/layer?page[number]=1&page[size]=10",
    "last": "https://api.resourcewatch.org/v1/layer?page[number]=634&page[size]=10",
    "prev": "https://api.resourcewatch.org/v1/layer?page[number]=1&page[size]=10",
    "next": "https://api.resourcewatch.org/v1/layer?page[number]=2&page[size]=10"
  },
  "meta": {
    "total-pages": 63,
    "total-items": 628,
    "size": 10
  }
}
```

This endpoint allows you to list existing layers and their properties. The result is a paginated list of 10 layers,
followed by metadata on total number of layers and pages, as well as useful pagination links. By default, only layers
with `env` value `production` are displayed. In the sections below, we’ll explore how you can customize this endpoint
call to match your needs.

### Getting all layers for a dataset

> Return the layers associated with a dataset

```shell
curl -X GET "https://api.resourcewatch.org/v1/dataset/<dataset-id>/layer" \
-H "x-api-key: <your-api-key>"
```

When handling layers, it's common to want to limit results to those layers associated with a given dataset. Besides
the [filters](reference.html#filters92) covered below, there's an additional convenience endpoint to get the layers
associated with a dataset, as shown in this example.

### Getting all layers for multiple datasets

> Return all layers associated with two datasets

```shell
curl -X POST "https://api.resourcewatch.org/layer/find-by-ids" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H 'Content-Type: application/json' \
-d '{
	"ids": ["<dataset 1 id>", "<dataset 2 id>"]
}'
```

```json
{
  "data": [
    {
      "id": "0dc39924-5736-4898-bda4-49cdc2f3b208",
      "type": "layer",
      "attributes": {
        "name": "NOAA NEXt-Generation RADar (NEXRAD) Products (Locations)",
        "slug": "noaa-next-generation-radar-nexrad-products-locations",
        "dataset": "0b9e546c-f42a-4b26-bad3-7d606f58961c",
        "description": "",
        "application": [
          "prep"
        ],
        "iso": [
          "USA"
        ],
        "provider": "arcgis",
        "userId": "legacy",
        "default": true,
        "protected": false,
        "published": false,
        "env": "production",
        "layerConfig": {
          "body": {
            "useCors": false,
            "layers": [
              0
            ],
            "url": "https://gis.ncdc.noaa.gov/arcgis/rest/services/cdo/nexrad/MapServer"
          },
          "type": "dynamicMapLayer"
        },
        "legendConfig": {
          "type": "basic",
          "items": [
            {
              "name": "NEXRAD",
              "color": "#FF0000",
              "icon": "http://gis.ncdc.noaa.gov/arcgis/rest/services/cdo/nexrad/MapServer/0/images/47768c7d812818af98d8da03a04d4fe4"
            }
          ]
        },
        "interactionConfig": {},
        "applicationConfig": {},
        "staticImageConfig": {},
        "createdAt": "2016-09-07T14:50:22.578Z",
        "updatedAt": "2017-12-18T12:08:25.703Z"
      }
    },
    {
      "id": "0b021478-f4d3-4a1e-ae88-ab97f0085bc9",
      "type": "layer",
      "attributes": {
        "name": "Projected change in average precipitation (in)",
        "slug": "projected-change-in-average-precipitation-between-u-s",
        "dataset": "d443beca-d199-4872-9d7d-d82c45e43151",
        "description": "Projected change in average precipitation for the A2 emissions scenario.",
        "application": [
          "prep"
        ],
        "iso": [
          "USA"
        ],
        "provider": "arcgis",
        "userId": "legacy",
        "default": true,
        "protected": false,
        "published": false,
        "env": "production",
        "layerConfig": {
          "body": {
            "use-cors": false,
            "layers": [
              12
            ],
            "url": "https://gis-gfw.wri.org/arcgis/rest/services/prep/nca_figures/MapServer"
          },
          "zoom": 4,
          "center": {
            "lat": 39.6395375643667,
            "lng": -99.84375
          },
          "type": "dynamicMapLayer",
          "bbox": [
            -125.61,
            24.73,
            -66.81,
            49.07
          ]
        },
        "legendConfig": {
          "type": "choropleth",
          "items": [
            {
              "value": "< -90",
              "color": "#8C5107"
            },
            {
              "value": "-90 - -60",
              "color": "#BF822B"
            }
          ]
        },
        "interactionConfig": {},
        "applicationConfig": {},
        "staticImageConfig": {},
        "createdAt": "2016-09-15T13:47:39.829Z",
        "updatedAt": "2018-03-02T20:54:17.260Z"
      }
    }
  ]
}
```

This endpoint allows authenticated users to load all layers belonging to multiple datasets in a single request.

> Return all layers associated with two datasets, that are associated with either `rw` or `prep` applications

```shell
curl -X POST "https://api.resourcewatch.org/layer/find-by-ids" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H 'Content-Type: application/json' \
-d '{
	"ids": ["<dataset 1 id>", "<dataset 2 id>"],
    "app" "rw,prep"
}'
```

> Return all layers associated with two datasets, that are associated with both `rw` and `prep` applications
> simultaneously

```shell
curl -X POST "https://api.resourcewatch.org/layer/find-by-ids" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H 'Content-Type: application/json' \
-d '{
	"ids": ["<dataset 1 id>", "<dataset 2 id>"],
    "app" "rw@prep"
}'
```

Besides the required `ids` array, your request body may optionally include a `app` string value if you'd like to filter
the returned layers by their `application`:

- Use a single value, like `rw`, if you want to show only layers that have `rw` as one of their applications.
- Use a comma separated list, like `rw,prep`, if you want to show only layers that have `rw` or `prep` as one of their
  applications.
- Use a @ separated list, like `rw@prep`, if you want to show only layers that have both `rw` and `prep` as their
  applications.
- Note that the the filters do not need to match on the full application list. For example, the filters `rw,prep`
  and `rw@prep` will both match a layer with the application list `["rw", "prep", "gfw"]`.

Please note that, unlike [getting all layers](reference.html#getting-all-layers)
or [getting all layers for a dataset](reference.html#getting-all-layers-for-a-dataset), this endpoint does not come with
paginated results, nor does it support [pagination](#pagination91), [filtering](#filters92) or [sorting](#sorting93)
or [including related entities](reference.html#include-entities-associated-with-the-layers) described in their
respective sections.

### Pagination

> Example request to load page 2 using 25 results per page

```shell
curl -X GET "https://api.resourcewatch.org/v1/layer?page[number]=2&page[size]=25" \
-H "x-api-key: <your-api-key>"
```

The Layers service adheres to the conventions defined in
the [Pagination guidelines for the RW API](concepts.html#pagination), so we recommend reading that section for more
details on how paginate your layers list.

### Filters

> Return the layers filtered by those whose name contains emissions

```shell
curl -X GET "https://api.resourcewatch.org/v1/layer?name=emissions" \
-H "x-api-key: <your-api-key>"
```

> Return the layers filtered by dataset

```shell
curl -X GET "https://api.resourcewatch.org/v1/layer?dataset=11de2bc1-368b-42ed-a207-aaff8ece752b" \
-H "x-api-key: <your-api-key>"
```

```shell
curl -X GET "https://api.resourcewatch.org/v1/dataset/11de2bc1-368b-42ed-a207-aaff8ece752b/layer" \
-H "x-api-key: <your-api-key>"


> Filter layers by published status

```shell
curl -X GET "https://api.resourcewatch.org/v1/layer?published=false" \
-H "x-api-key: <your-api-key>"
```

> Filter layers by environment

```shell
curl -X GET "https://api.resourcewatch.org/v1/layer?env=staging" \
-H "x-api-key: <your-api-key>"
```

> Return the layers filtered by those whose applications contain rw

```shell
curl -X GET "https://api.resourcewatch.org/v1/layer?app=rw" \
-H "x-api-key: <your-api-key>"
```

The layer list endpoint provides a wide range of filters that you can use to tailor your layer listing. Filtering layers
adheres to the conventions defined in the [Filter guidelines for the RW API](concepts.html#filtering), so we strongly
recommend reading that section before proceeding. In addition to these conventions, you will be able to use the great
majority of the layer fields you'll find on the [layer reference](reference.html#layer-reference) section, with the
following exceptions:

- `id`
- `userName`
- `userRole`: filtering by the role of the owning user can be done using the `user.role` query argument. If the
  requesting user does not have the ADMIN role, this filter is ignored. **Please keep in mind that, due to the
  limitations of the [underlying endpoint used to find user ids by role](developer.html#user-management), the
  performance of the request while using this filter might be degraded.**
- Filtering by fields of type `Object` is not supported.

Additionally, you can use the following filters:

- `collection`: filters by a [collection](reference.html#collections) id. Requires being authenticated.
- `favourite`: if defined, only the layers set by the user as [favorites](reference.html#favorites) will be returned.
  Requires being authenticated.

### Sorting

> Sorting layers

```shell
curl -X GET "https://api.resourcewatch.org/v1/layer?sort=name" \
-H "x-api-key: <your-api-key>"
```

> Sorting layers by multiple criteria

```shell
curl -X GET "https://api.resourcewatch.org/v1/layer?sort=name,slug" \
-H "x-api-key: <your-api-key>"
```

> Explicit order of sorting

```shell
curl -X GET "https://api.resourcewatch.org/v1/layer?sort=-name,+slug" \
-H "x-api-key: <your-api-key>"
```

> Sorting layers by the role of the user who owns the layer

```shell
curl -X GET "https://api.resourcewatch.org/v1/layer?sort=user.role" \
-H "x-api-key: <your-api-key>"
```

The Layer service currently supports sorting using the `sort` query parameter. Sorting layer adheres to the conventions
defined in the [Sorting guidelines for the RW API](concepts.html#sorting), so we strongly recommend reading that section
before proceeding. Additionally, you can check out the [Layer reference](reference.html#layer-reference) section for a
detailed description of the fields you can use when sorting. In addition to all layer model fields, you can sort the
returned results by the name (using `user.name`) or role (using `user.role`) of the user owner of the layer. Keep in
mind that sorting by user data is restricted to ADMIN users.

**Please also keep in mind that, due to the limitations of
the [underlying endpoint used to find user ids by name or role](developer.html#user-management), the performance of the
request while using this sort might be degraded.**

### Include entities associated with the layers

When fetching layers, you can request additional entities to be loaded. The following entities are available:

#### Vocabulary

> Loads vocabulary associated with each layer:

```shell
curl -X GET "https://api.resourcewatch.org/v1/layer?includes=vocabulary" \
-H "x-api-key: <your-api-key>"
```

> Example response:

```json
{
  "data": [
    {
      "id": "e5c3e7c5-19ae-4ca0-a461-71f1f67aa553",
      "type": "layer",
      "attributes": {
        "slug": "total-co2-emissions-by-year",
        "userId": "5858f37140621f11066fb2f7",
        "application": [
          "rw"
        ],
        "name": "Total CO2 emissions by year",
        "default": false,
        "dataset": "11de2bc1-368b-42ed-a207-aaff8ece752b",
        "env": "production",
        "provider": "cartodb",
        "iso": [],
        "description": null,
        "layerConfig": {
          "account": "rw",
          "body": {
            "maxzoom": 18,
            "minzoom": 3,
            "layers": [
              {
                "type": "mapnik",
                "options": {
                  "sql": "SELECT * cait_2_0_country_ghg_emissions_filtered",
                  "cartocss": "",
                  "cartocss_version": "2.3.0"
                }
              }
            ]
          }
        },
        "legendConfig": {
          "marks": {
            "type": "rect",
            "from": {
              "data": "table"
            }
          }
        },
        "applicationConfig": {},
        "staticImageConfig": {},
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

Loads all vocabulary entities associated with each layer. Internally this uses
the [`dataset/<dataset_id>/layer/<layer_id>/vocabulary`](reference.html#getting-all-vocabularies-and-tags-for-a-resource)
endpoint, and thus it's affected by its behavior - particularly, only vocabularies associated with the `rw` application
will be listed. There's currently no way to modify this behavior.

#### User

Loads the name and email address of the author of the layer. If you request this issue as an authenticated user
with `ADMIN` role, you will additionally get the author's role.

If the data is not available (for example, the user has since been deleted), no `user` property will be added to the
layer object.

**Please keep in mind that, due to the limitations of
the [underlying endpoint used to find users by ids](developer.html#finding-users-by-ids), the performance of the request
while including user information might be degraded.**

```shell
curl -X GET "https://api.resourcewatch.org/v1/layer?includes=user" \
-H "x-api-key: <your-api-key>"
```

> Example response:

```json
{
  "data": [
    {
      "id": "e5c3e7c5-19ae-4ca0-a461-71f1f67aa553",
      "type": "layer",
      "attributes": {
        "slug": "total-co2-emissions-by-year",
        "userId": "5858f37140621f11066fb2f7",
        "application": [
          "rw"
        ],
        "name": "Total CO2 emissions by year",
        "default": false,
        "dataset": "11de2bc1-368b-42ed-a207-aaff8ece752b",
        "env": "production",
        "provider": "cartodb",
        "iso": [],
        "description": null,
        "layerConfig": {
          "account": "rw",
          "body": {
            "maxzoom": 18,
            "minzoom": 3,
            "layers": [
              {
                "type": "mapnik",
                "options": {
                  "sql": "SELECT * cait_2_0_country_ghg_emissions_filtered",
                  "cartocss": "",
                  "cartocss_version": "2.3.0"
                }
              }
            ]
          }
        },
        "legendConfig": {
          "marks": {
            "type": "rect",
            "from": {
              "data": "table"
            }
          }
        },
        "applicationConfig": {},
        "staticImageConfig": {},
        "user": {
          "name": "John Doe",
          "email": "john.doe@vizzuality.com"
        }
      }
    }
  ]
}
```

#### Requesting multiple additional entities

You can request multiple related entities in a single request using commas to separate multiple keywords

```shell
curl -X GET "https://api.resourcewatch.org/v1/layer?includes=user,vocabulary" \
-H "x-api-key: <your-api-key>"
```

## Getting a layer by id or slug

> Getting a layer by id:

```shell
curl -X GET "https://api.resourcewatch.org/v1/dataset/<dataset-id>/layer/<layer_id>" \
-H "x-api-key: <your-api-key>"
curl -X GET "https://api.resourcewatch.org/v1/layer/<layer_id>" \
-H "x-api-key: <your-api-key>"
```

> Getting a layer by slug:

```shell
curl -X GET "https://api.resourcewatch.org/v1/dataset/<dataset-id>/layer/<layer_slug>" \
-H "x-api-key: <your-api-key>"
curl -X GET "https://api.resourcewatch.org/v1/layer/<layer_slug>" \
-H "x-api-key: <your-api-key>"
```

> Example response:

```json
{
  "data": {
    "id": "e5c3e7c5-19ae-4ca0-a461-71f1f67aa553",
    "type": "layer",
    "attributes": {
      "slug": "total-co2-emissions-by-year",
      "userId": "5858f37140621f11066fb2f7",
      "application": [
        "rw"
      ],
      "name": "Total CO2 emissions by year",
      "default": false,
      "dataset": "11de2bc1-368b-42ed-a207-aaff8ece752b",
      "env": "production",
      "provider": "cartodb",
      "iso": [],
      "description": null,
      "layerConfig": {
        "account": "rw",
        "body": {
          "maxzoom": 18,
          "minzoom": 3,
          "layers": [
            {
              "type": "mapnik",
              "options": {
                "sql": "SELECT * cait_2_0_country_ghg_emissions_filtered",
                "cartocss": "",
                "cartocss_version": "2.3.0"
              }
            }
          ]
        }
      },
      "legendConfig": {
        "marks": {
          "type": "rect",
          "from": {
            "data": "table"
          }
        }
      },
      "applicationConfig": {},
      "staticImageConfig": {}
    }
  },
  "meta": {
    "status": "saved",
    "published": true,
    "updatedAt": "2017-01-23T16:51:42.571Z",
    "createdAt": "2017-01-23T16:51:42.571Z"
  }
}
```

If you know the id or the `slug` of a layer, then you can access it directly. Both id and `slug` are case-sensitive.

### Include entities associated with the layer

You can load related `user` and `vocabulary` data in the same request.
See [this section](reference.html#include-entities-associated-with-the-layers) for more details.

## Creating a layer

> To create a layer, you need to provide at least the following details:

```shell
curl -X POST "https://api.resourcewatch.org/v1/dataset/<dataset-id>/layer" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-d  \
'{
    "application": [
      "rw"
    ],
    "name": "Water stress",
    "description": "water stress"
}'
```

> Response:

```json
{
  "data": {
    "id": "bd8a36df-2e52-4b2d-b7be-a48bdcd7c769",
    "type": "layer",
    "attributes": {
      "name": "Water stress",
      "slug": "Water-stress_7",
      "dataset": "7fa6ec77-5ab0-43f4-9a4c-a3d19bed1e90",
      "description": "water stress",
      "application": [
        "rw"
      ],
      "iso": [],
      "userId": "5dbadb06df2dc74d2ad054fb",
      "default": false,
      "protected": false,
      "published": true,
      "env": "production",
      "layerConfig": {},
      "legendConfig": {},
      "interactionConfig": {},
      "applicationConfig": {},
      "staticImageConfig": {},
      "createdAt": "2020-06-04T14:28:24.575Z",
      "updatedAt": "2020-06-04T14:28:24.575Z"
    }
  }
}
```

In this section we'll guide you through the process of creating a layer in the RW API. Layer creation is available to
all registered API users, and will allow you to store your own layer visualisation settings on the API, for reusing and
sharing.

Before creating a layer, there are a few things you must know and do:

- In order to be able to create a layer, you need to be [authenticated](reference.html#authentication).
- Depending on your user account's role, you may have permission to create a layer but not delete it afterwards - read
  more about this in [the RW API role-based access control guidelines](concepts.html#role-based-access-control).
- The layers you create on the RW API will be publicly visible and available to other users.

Creating a layer is done using a POST request and passing the relevant data as body fields. The supported body fields
are as defined on the [layer reference](reference.html#layer-reference) section, but the minimum field list you must
specify for all layers is:

- name
- description
- application

There's also a dependency on a dataset id, as it is required to build the POST URL. As noted on
the [layer concept](concepts.html#layer) documentation, a layer is meant to hold the rendering details of a layer, but
not the actual data - that should be part of the dataset. While this is not enforced - it's up to your rendering tool to
load the data, and it can do it from a RW API dataset or from anywhere else - it's common and best practice to have the
data for a layer be sourced from the dataset that's associated with it.

When a layer is created, a [vocabulary tag](reference.html#vocabularies-and-tags) for it is automatically created,
associated with the dataset tag.

The layer service was built to be very flexible, and not be restricted to specific layer rendering libraries or tools.
This gives you the freedom to use virtually any rendering technology you want, but it also means you'll have to make
additional decisions on how to structure your data into the different open format fields provided by a RW API layer. In
a future release of these docs, we'll show you some examples of how existing applications use different rendering tools
with the layer endpoints, to give you an idea on how you can structure your own data, and also as a way to help you get
started creating your first layers for your own custom applications. Until then,
the [tutorials section of the documentation](tutorials.html) shows an example of how existing raster tile layers may be
structured and how the information in the layer response can be utilized by a third-party visualization library.

#### Layer thumbnails

> Preview of the thumbnail of a layer

```shell
curl -X GET "https://resourcewatch.org/embed/layer/<layer_id>" \
-H "x-api-key: <your-api-key>"
```

When a layer is created or updated, the RW API will automatically try to generate a thumbnail preview of it. This is
done using the renderer that's part of the [Resource Watch](https://resourcewatch.org) website, and will only produce a
thumbnail if your layer uses the same approach as the RW website for declaring its layers. Searching for layers
belonging to the `rw` application will give you an idea of how this is achieved.

The thumbnail process is asynchronous, meaning it may take up to one minute for your newly created or updated layer to
have a valid and/or up to date `thumbnailUrl` value. If the thumbnail generation process fails (for example, if you
decide to use a different rendering tool and data structure), the thumbnail generation will fail silently, but will not
affect the actual layer creation or update process.

#### Errors for creating a layer

| Error code | Error message                                                                                  | Description                                                                                                            |
|------------|------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 400        | `<field>`: `<field>` can not be empty                                                          | Your are missing a required field value.                                                                               |
| 400        | - `<field>`: must be a `<restriction>`                                                         | The value provided for the mentioned field does not match the requirements.                                            |
| 401        | Unauthorized                                                                                   | You are not authenticated.                                                                                             |
| 403        | Forbidden                                                                                      | You are trying to create a layer with one or more `application` values that are not associated with your user account. |
| 404        | Dataset not found                                                                              | The provided dataset id does not exist.                                                                                |
| 404        | Error: StatusCodeError: 404 - {\"errors\":[{\"status\":404,\"detail\":\"Dataset not found\"}]} | The provided dataset exists but is not present on the [graph](reference.html#graph).                                   |

## Updating a layer

> Example PATCH request that updates a layer's name:

```shell
curl -X PATCH "https://api.resourcewatch.org/v1/dataset/<dataset-id>/layer/<layer_id>" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json" -d \
'{
  "name":"foo"
}'
```

The update layer endpoint allows you to modify the details of an existing layer. As noted on
the [layer concept](concepts.html#layer) documentation, the layer object stores the details of how layer is meant to be
rendered, but does not contain the actual data. As such, if you are looking to update the data that's being displayed on
your map, this is probably not the endpoint you're looking for - you may want
to [update your dataset](reference.html#updating-a-dataset) instead. Use this endpoint if you want to modify things like
legend details, color schemes, etc - this will depend on your rendering implementation.

Unless specified otherwise in their description, all the fields present in
the [layer reference](reference.html#layer-reference) section can be updated using this endpoint. When passing new
values for Object type fields, the new value will fully overwrite the previous one. It’s up to you, as an API user, to
build any merging logic into your application.

To perform this operation, the following conditions must be met:

- the user must be logged in and belong to the same application as the layer
- the user must comply with [the RW API role-based access control guidelines](concepts.html#role-based-access-control).

#### Layer thumbnails

When a layer is updated, a new thumbnail is generated. Refer to [this section](reference.html#layer-thumbnails) for
details on this process.

#### Errors for updating a layer

| Error code | Error message                          | Description                                                                                                                          |
|------------|----------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| 400        | - `<field>`: must be a `<restriction>` | The value provided for the mentioned field does not match the requirements.                                                          |
| 401        | Unauthorized                           | You need to be logged in to be able to update a layer.                                                                               |
| 403        | Forbidden                              | You need to either have the `ADMIN` role, or have role `MANAGER` and be the layer's owner (through the `userId` field of the layer). |
| 403        | Forbidden                              | You are trying to update a layer with one or more `application` values that are not associated with your user account.               |
| 404        | Layer with id <id> doesn't exist       | A layer with the provided id does not exist.                                                                                         |
| 404        | Dataset not found                      | A dataset with the provided id does not exist.                                                                                       |

## Deleting a layer

> Example DELETE request that deletes a layer:

```shell
curl -X DELETE "https://api.resourcewatch.org/v1/dataset/<dataset-id>/layer/<layer_id>" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"
```

> Response:

```json
{
  "data": {
    "id": "bd8a36df-2e52-4b2d-b7be-a48bdcd7c769",
    "type": "layer",
    "attributes": {
      "name": "Water stress",
      "slug": "Water-stress_7",
      "dataset": "7fa6ec77-5ab0-43f4-9a4c-a3d19bed1e90",
      "description": "water stress",
      "application": [
        "rw"
      ],
      "iso": [],
      "userId": "5dbadb06df2dc74d2ad054fb",
      "default": false,
      "protected": false,
      "published": true,
      "env": "production",
      "layerConfig": {},
      "legendConfig": {},
      "interactionConfig": {},
      "applicationConfig": {},
      "staticImageConfig": {},
      "createdAt": "2020-06-04T14:28:24.575Z",
      "updatedAt": "2020-06-04T14:28:24.575Z"
    }
  }
}
```

Use this endpoint if you'd like to delete a layer from the RW API. As a layer object does not store the actual data
being displayed, this will only delete the layer settings, but the actual data will continue to be available at its
source.

Besides deleting the layer itself, this endpoint also deletes graph vocabularies and metadata related to the layer
itself. These delete operations are issued after the layer itself is deleted. The process is not atomic, and the output
of the API request is based solely on the result of the deletion of the layer itself. For example, is the metadata
service is temporarily unavailable when you issue your delete layer request, the layer itself will be deleted, but the
associated metadata will continue to exist. The response will not reflect the failure to delete metadata in any way.

In order to delete a layer, the following conditions must be met:

- the layer's `protected` property must be set to `false`.
- the user must be logged in and belong to the same application as the layer.
- the user must comply with [the RW API role-based access control guidelines](concepts.html#role-based-access-control).

#### Errors for deleting a layer

| Error code | Error message                    | Description                                                                                                                         |
|------------|----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| 400        | Layer is protected               | You are attempting to delete a layer that has `protected` set to prevent deletion.                                                  |
| 401        | Unauthorized                     | You need to be logged in to be able to delete a layer.                                                                              |
| 403        | Forbidden                        | You need to either have the `ADMIN` role, or have role `MANAGER` and be the layer's owner (through the `userId` field of the layer) |
| 404        | Dataset not found                | A dataset with the provided id does not exist.                                                                                      |
| 404        | Layer with id <id> doesn't exist | A layer with the provided id does not exist.                                                                                        |

## Deleting layers by user id

> Example request for deleting layers by user id

```shell
curl -X DELETE "https://api.resourcewatch.org/v1/layer/by-user/<user_id>" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"
```

> Response:

```json
{
  "deletedLayers": [
    {
      "id": "bd8a36df-2e52-4b2d-b7be-a48bdcd7c769",
      "type": "layer",
      "attributes": {
        "name": "Water stress",
        "slug": "Water-stress_7",
        "dataset": "7fa6ec77-5ab0-43f4-9a4c-a3d19bed1e90",
        "description": "water stress",
        "application": [
          "rw"
        ],
        "iso": [],
        "userId": "5dbadb06df2dc74d2ad054fb",
        "default": false,
        "protected": false,
        "published": true,
        "env": "production",
        "layerConfig": {},
        "legendConfig": {},
        "interactionConfig": {},
        "applicationConfig": {},
        "staticImageConfig": {},
        "createdAt": "2020-06-04T14:28:24.575Z",
        "updatedAt": "2020-06-04T14:28:24.575Z"
      }
    }
  ],
  "protectedLayers": [
    {
      "id": "1a8a36df-2e52-4b2d-b7be-a48bdcd7c769",
      "type": "layer",
      "attributes": {
        "name": "Fire impact",
        "slug": "fire_impact_7",
        "dataset": "1ae6ec77-5ab0-43f4-9a4c-a3d19bed1e90",
        "description": "fire impact",
        "application": [
          "rw"
        ],
        "iso": [],
        "userId": "5dbadb06df2dc74d2ad054fb",
        "default": false,
        "protected": true,
        "published": true,
        "env": "production",
        "layerConfig": {},
        "legendConfig": {},
        "interactionConfig": {},
        "applicationConfig": {},
        "staticImageConfig": {},
        "createdAt": "2020-06-04T14:28:24.575Z",
        "updatedAt": "2020-06-04T14:28:24.575Z"
      }
    }
  ]
}
```

Use this endpoint if you'd like to delete all layers from associated with a user. As a layer object does not store the
actual data being displayed, this will only delete the layer settings, but the actual data will continue to be available
at its source.

Besides deleting the layers themselves, this endpoint also deletes graph vocabularies and metadata related to the
layers.
These delete operations are issued after each layer itself is deleted. The process is not atomic, and the output of the
API request is based solely on the result of the deletion of the layer themselves. For example, is the metadata service
is
temporarily unavailable when you issue your delete layers request, the layers themselves will be deleted, but the
associated
metadata will continue to exist. The response will not reflect the failure to delete metadata in any way.

Any microservice or user with ADMIN role can use this endpoint. Regular users can use this endpoint to delete the layers
they own.

The response includes two lists of layers:

- `deletedLayers`: an unpaginated list of all datasets that were layers as part of this operation.
- `protectedLayers`: an unpaginated list of all layers associated with the provided user, that have `protected` status
  set to `true`,
  and thus were not deleted.

#### Errors for deleting layers by user id

| Error code | Error message | Description                                                                                                          |
|------------|---------------|----------------------------------------------------------------------------------------------------------------------|
| 401        | Unauthorized  | You need to be logged in to be able to delete layers.                                                                |
| 403        | Forbidden     | You are trying to delete the layers of an user that is not the same logged user, not an ADMIN user or a microservice |

## Layer reference

This section gives you a complete view at the properties that are maintained as part of layer. When interacting with a
layer (on get, on create, etc) you will find most of these properties available to you, although they may be organized
in a slightly different structure (ie: on get, everything but the `id` is nested inside an `attributes` object).

You can find more details in
the [source code](https://github.com/resource-watch/layer/blob/master/app/src/models/layer.model.js).

| Field name        | Type    | Required            | Default value  | Description                                                                                                                                             |
|-------------------|---------|---------------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| id                | String  | Yes (autogenerated) |                | Unique Id of the layer. Auto generated on creation. Cannot be modified by users.                                                                        |
| name              | String  | Yes                 |                | Name of the layer.                                                                                                                                      |
| dataset           | String  | Yes                 |                | Id of the dataset to which the layer corresponds. Set on layer creation, cannot be modified.                                                            |
| slug              | String  | Yes (autogenerated) |                | Slug of the layer. Auto generated on creation. Cannot be modified by users.                                                                             |
| description       | String  | No                  |                | User defined description of the layer.                                                                                                                  |
| application       | Array   | Yes                 |                | Applications associated with this layer. Read more about this field [here](concepts.html#applications).                                                 |
| iso               | Array   | No                  |                | List of ISO3 codes of the countries that relate to the layer. If empty (or contains a single element: 'global') then the layer is a global layer.       |
| provider          | String  | No                  |                | Layer provider. It typically identifies the source service for the data displayed in the layer.                                                         |
| type              | String  | No                  |                | Layer type.                                                                                                                                             |
| userId            | String  | Yes (autopopulated) |                | Id of the user who created the layer. Set automatically on creation. Cannot be modified by users.                                                       |
| default           | Boolean | No                  | false          | If the layer should be used as the dataset's default layer.                                                                                             |
| protected         | Boolean | Yes                 | false          | If the layer is protected. A protected layer cannot be deleted.                                                                                         |
| published         | Boolean | Yes                 | true           | If the layer is published or not.                                                                                                                       |
| env               | String  | Yes                 | production     | Environment to which the layer belongs. Read more about this field in the [Environments concept section](concepts.html#environments).                   |
| applicationConfig | Object  | No                  |                | Schema-less object meant to host application-specific data or behavior configuration.                                                                   |
| layerConfig       | Object  | No                  |                | Schema-less object meant to define layer specific data, like source of data, styling and animation settings.                                            |
| legendConfig      | Object  | No                  |                | Schema-less object meant to define how a layer legend should be represented visually.                                                                   |
| staticImageConfig | Object  | No                  |                |                                                                                                                                                         |
| interactionConfig | Object  | No                  |                | Schema-less object meant to define interactive layer element behavior, ie.: how tooltips behave on click.                                               |
| userName          | String  | No (autogenerated)  | null           | Name of the user who created the layer. This value is used only internally, and is never directly exposed through the API. Cannot be modified by users. |
| userRole          | String  | No (autogenerated)  | null           | Role of the user who created the layer. This value is used only internally, and is never directly exposed through the API. Cannot be modified by users. |
| createdAt         | Date    | No (autogenerated)  | <current date> | Automatically maintained date of when the layer was created. Cannot be modified by users.                                                               |
| updatedAt         | Date    | No (autogenerated)  | <current date> | Automatically maintained date of when the layer was last updated. Cannot be modified by users.                                                          |
