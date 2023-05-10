# Application

The following endpoints expose the RW API's functionality regarding application management.

## Applications vs applications

Naming things is said to be one of the most challenging tasks software developers face, and the team behind the RW API
is no different, which is why the RW API effectively has 2 different notions of `applications`:

- The "vanilla" [applications](concepts.html#applications) concept:
  - An `application` is identified solely by its name, present on a different resource (dataset, widget, etc)
  - Applications are not standalone resources
  - It's used mostly for filtering/grouping said resources
- The one presented in this section:
  - An `application` is a complex data structure, that has a name and several other attributes
  - There are multiple endpoints to manage `applications` themselves, and their associations with other types of
    resources
  - It's used to manage RW API usage by different parties.

This documentation section will focus on the second instance, while you can learn more about the first one in the link
above.

## What is an application?

In the context of the RW API, an application correspond to a unique software application which functionality relies on
the RW API. Some examples of this are the [Resource Watch](https://resourcewatch.org) or
the [Global Forest Watch](https://globalforestwatch.org) websites. If you want to develop your own application that
uses any of the RW API endpoints or data, you need to create an application.

Each application has, among other elements, an **API key**. Each API key is unique, and should be provided with each
HTTP request your client application makes to the RW API. This key is used to identify your application, and serves to
ensure fair usage of shared RW API resources, or allow you to request a higher request throttle limit, should you so
require.

Note that applications and API keys do not replace [user authentication](reference.html#authentication), and they are
meant to be used simultaneously, as they fundamentally serve two different purposes:

- Applications (through API keys) identify the client software application who is accessing the RW API
- User authentication (through JWT Tokens) identify the user who is accessing the RW API.

## Application ownership

Before diving into application management endpoints, there is another important point that you should keep in mind when
managing your applications - application ownership. Simply put, each application must have an owner, which must be
either a user or an organization. A [user](reference.html#user-management) identifies a single person using the RW API,
while an [organization](reference.html#organization) groups different users and grants them shared privileges over
applications. Each application must be owned by either a single user or a single organization, and only the owner of an
application can modify it.

Whenever you create/edit an application you must/can provide either a user id or an organization id to set it as the
application's owner. You cannot provide both simultaneously, and if you are editing an existing application, the newly
passed ownership value will overwrite the previous one.

## Getting all applications

> Getting a list of applications

```shell
curl -X GET https://api.resourcewatch.org/v1/application
```

> Response:

```json
{
  "data": [
    {
      "type": "applications",
      "id": "63be3da993ea69eb458fc466",
      "attributes": {
        "name": "Sample application",
        "organization": null,
        "user": {
          "id": "5bfd237767b3176dd63f2eb7",
          "name": "John Doe"
        },
        "apiKeyValue": "8a81e9de-517e-466f-a5d3-a1d4ccf0e290",
        "createdAt": "2023-01-11T04:40:09.455Z",
        "updatedAt": "2023-01-11T04:40:09.455Z"
      }
    }
  ],
  "links": {
    "self": "https://api.resourcewatch.org/v1/application?page[number]=1&page[size]=10",
    "first": "https://api.resourcewatch.org/v1/application?page[number]=1&page[size]=10",
    "last": "https://api.resourcewatch.org/v1/application?page[number]=99&page[size]=10",
    "prev": "https://api.resourcewatch.org/v1/application?page[number]=1&page[size]=10",
    "next": "https://api.resourcewatch.org/v1/application?page[number]=2&page[size]=10"
  },
  "meta": {
    "total-pages": 99,
    "total-items": 990,
    "size": 10
  }
}
```

This endpoint will allow you to get the list of the applications available in the API. By default, this endpoint will
give you a paginated list of 10 applications.

Depending on your [user role](/doc-api/concepts.html#user-roles), the behavior of this endpoint will vary:

- If you have the `ADMIN` or `MANAGER` roles, you will see all applications for all users.
- If you have the `USER` role, you will only see applications for which you are the owner.

For a detailed description of each field, check out the [Application reference](reference.html#application-reference)
section.

### Pagination

> Example request to load page 2 using 25 results per page

```shell
curl -X GET https://api.resourcewatch.org/v1/application?page[number]=2&page[size]=25
```

The application service adheres to the conventions defined in
the [Pagination guidelines for the RW API](concepts.html#pagination), so we recommend reading that section for more
details on how paginate your applications list.

### Errors for getting all applications

| Error code | Error message (example)                         | Description                                   |
|------------|-------------------------------------------------|-----------------------------------------------|
| 400        | `"page.size" must be less than or equal to 100` | You cannot request page sizes larger than 100 |
| 401        | `Not authenticated`                             | No authorization token was provided.          |

## Getting an application by id

> Getting an application by id:

```shell
curl -X GET "https://api.resourcewatch.org/v1/application/51943691-eebc-4cb4-bdfb-057ad4fc2145"
```

> Response:

```json
{
  "data": {
    "type": "applications",
    "id": "63be3da993ea69eb458fc4a6",
    "attributes": {
      "name": "Sample application",
      "organization": null,
      "user": {
        "id": "5bfd237767b3176dd63f2eb7",
        "name": "John Doe"
      },
      "apiKeyValue": "8a81e9de-517e-466f-a5d3-a1d4ccf0e290",
      "createdAt": "2023-01-11T04:40:09.455Z",
      "updatedAt": "2023-01-11T04:40:09.455Z"
    }
  }
}
```

If you know the id of an application, then you can access it directly. The id is case-sensitive.

To access the details of a given application using this endpoint, you need to meet one of the following conditions:

- Have the `ADMIN` or `MANAGER` [user role](/doc-api/concepts.html#user-roles).
- Own the application
- Be a member of the organization that owns the application.

### Errors for getting an application by id

| Error code | Error message (example) | Description                                                                                |
|------------|-------------------------|--------------------------------------------------------------------------------------------|
| 401        | `Not authenticated`     | No authorization token was provided.                                                       |
| 403        | `Not authorized`        | You don't have the necessary permissions to see the details for this application.          |
| 404        | `Application not found` | The application id provided is invalid or does not match any of the existing applications. |

## Creating an application

```shell
curl -X POST "https://api.resourcewatch.org/v1/application" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer <your-token>" \
-d  \
'{
    "name": "Sample application",
    "user": "5bfd237767b3176dd63f2eb7"
}'
```

> Response:

```json
{
  "data": {
    "type": "applications",
    "id": "63be3da993ea69eb458fc4a6",
    "attributes": {
      "name": "Sample application",
      "organization": null,
      "user": {
        "id": "5bfd237767b3176dd63f2eb7",
        "name": "John Doe"
      },
      "apiKeyValue": "8a81e9de-517e-466f-a5d3-a1d4ccf0e290",
      "createdAt": "2023-01-11T04:40:09.455Z",
      "updatedAt": "2023-01-11T04:40:09.455Z"
    }
  }
}
```

Creating an application should be one of your very first steps when using the RW API, as you will need it to make
subsequent requests for other resources, like datasets, widgets, etc. If you haven't already, you should review the
details of [what is an application](reference.html#what-is-an-application)
and [application ownership](reference.html#application-ownership) before proceeding.

Creating an application is done using a POST request and passing the relevant data as body fields. The supported body
fields are as defined on the [application reference](reference.html#application-reference) section, but the minimum
value you need to provide is the `name`.

Unless specified otherwise, an application owned by the user who created it. If you have the `ADMIN` user role, you can
specify the id of the user who will own the application through the `user` body field. You can alternatively use the
`organization` body field to specify the id of the organization that will own the application - you can only do so if
you have the `ADMIN` user role, or own that organization.

Any logged in user can create an application.

A successful application creation request will return a 200 HTTP code, and the application details as stored on the RW
API. Pay special attention to the `id`, as it will allow you
to [access your application](reference.html#getting-an-application-by-id) later.

#### Errors for creating an application

| Error code | Error message                                                              | Description                                                                                             |
|------------|----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| 400        | `<field>` is required                                                      | Your are missing a required field value.                                                                |
| 400        | `<field>` is not allowed                                                   | You have provided a body value that is not supported by the endpoint.                                   |
| 400        | "value" must contain at least one of [user, organization]                  | You must provided exactly one of either `user` or `organization`                                        |
| 401        | Not authenticated                                                          | You are not authenticated. Only authenticated users can create applications.                            |
| 403        | Not authorized                                                             | You are authenticated but do not have the necessary permissions to create this application.             |
| 403        | User can only create applications for themselves or organizations they own | You are trying to create an application for a user or organization to which you don't have permissions. |

## Updating an application

> Updating an application

```shell
curl -X PATCH "https://api.resourcewatch.org/v1/application/51943691-eebc-4cb4-bdfb-057ad4fc2145"
-H "Content-Type: application/json"  -d \
-H "Authorization: Bearer <your-token>" \
 '{
    "name":"new application name",
}'
```

> Example response

```json
{
  "data": {
    "type": "applications",
    "id": "63be3da993ea69eb458fc4a6",
    "attributes": {
      "name": "new application name",
      "organization": null,
      "user": {
        "id": "5bfd237767b3176dd63f2eb7",
        "name": "John Doe"
      },
      "apiKeyValue": "8a81e9de-517e-466f-a5d3-a1d4ccf0e290",
      "createdAt": "2023-01-11T04:40:09.455Z",
      "updatedAt": "2023-01-11T04:40:09.455Z"
    }
  }
}
```

> Modify an application to be owned by an organization

```shell
curl -X PATCH "https://api.resourcewatch.org/v1/application/51943691-eebc-4cb4-bdfb-057ad4fc2145"
-H "Content-Type: application/json"  -d \
-H "Authorization: Bearer <your-token>" \
 '{
    "organization":"6bfd237767b3176dd63f2eb7",
}'
```

> Example response

```json
{
  "data": {
    "type": "applications",
    "id": "63be3da993ea69eb458fc4a6",
    "attributes": {
      "name": "new application name",
      "user": null,
      "organization": {
        "id": "6bfd237767b3176dd63f2eb7",
        "name": "Organization name"
      },
      "apiKeyValue": "8a81e9de-517e-466f-a5d3-a1d4ccf0e290",
      "createdAt": "2023-01-11T04:40:09.455Z",
      "updatedAt": "2023-01-11T04:40:09.455Z"
    }
  }
}
```

> Generate a new API key

```shell
curl -X PATCH "https://api.resourcewatch.org/v1/application/51943691-eebc-4cb4-bdfb-057ad4fc2145"
-H "Content-Type: application/json"  -d \
-H "Authorization: Bearer <your-token>" \
 '{
    "regenApiKey":"true",
}'
```

> Example response

```json
{
  "data": {
    "type": "applications",
    "id": "63be3da993ea69eb458fc4a6",
    "attributes": {
      "name": "Sample application",
      "user": null,
      "organization": {
        "id": "6bfd237767b3176dd63f2eb7",
        "name": "Organization name"
      },
      "apiKeyValue": "9811e9de-517e-466f-a5d3-a1d4ccf0e017",
      "createdAt": "2023-01-11T04:40:09.455Z",
      "updatedAt": "2023-01-11T04:40:09.455Z"
    }
  }
}
```

This endpoint allows you to modify an existing application. You can modify the name, ownership (by passing the `id` of
either the `user` or `orgnaization`). You can also request for a new API key to be issued for your application - in
which case the previous API key will become invalid, and all requests using it may stop functioning.

To be able to modify an application, you must either have the `ADMIN` user role, be the owner of the application, or
belong to the organization that owns the application with the `ORG_ADMIN` role.

#### Errors for updating an application

| Error code | Error message                                                                     | Description                                                                                 |
|------------|-----------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| 400        | `<field>` is not allowed                                                          | You have provided a body value that is not supported by the endpoint.                       |
| 400        | "value" contains a conflict between optional exclusive peers [user, organization] | You can only provide one of ether `user` or `organization`.                                 |
| 401        | Not authenticated                                                                 | You are not authenticated. Only authenticated users can update applications.                |
| 403        | Not authorized                                                                    | You are authenticated but do not have the necessary permissions to update this application. |
| 404        | Application with id <id> doesn't exist                                            | An application with the provided id does not exist.                                         |

## Deleting an application

> Deleting an application

```shell
curl -X DELETE https://api.resourcewatch.org/v1/application/<application-id> \
-H "Authorization: Bearer <your-token>" \
-H "Content-Type: application/json"
```

> Response:

```json
{
  "data": {
    "type": "applications",
    "id": "63be3da993ea69eb458fc4a6",
    "attributes": {
      "name": "Sample application",
      "organization": null,
      "user": {
        "id": "5bfd237767b3176dd63f2eb7",
        "name": "John Doe"
      },
      "apiKeyValue": "8a81e9de-517e-466f-a5d3-a1d4ccf0e290",
      "createdAt": "2023-01-11T04:40:09.455Z",
      "updatedAt": "2023-01-11T04:40:09.455Z"
    }
  }
}
```

Use this endpoint if you wish to delete an application. Deleting an application will render its API key invalid, and all
requests made using it will return an error.

To be able to delete an application, you must either have the `ADMIN` user role, be the owner of the application, or
belong to the organization that owns the application with the `ORG_ADMIN` role.

#### Errors for deleting an application

| Error code | Error message                          | Description                                                                                 |
|------------|----------------------------------------|---------------------------------------------------------------------------------------------|
| 401        | Unauthorized                           | You need to be logged in to be able to delete an application.                               |
| 401        | Not authenticated                      | You are not authenticated. Only authenticated users can delete applications.                |
| 403        | Not authorized                         | You are authenticated but do not have the necessary permissions to delete this application. |
| 404        | Application with id <id> doesn't exist | An application with the provided id does not exist.                                         |

## Application reference

This section gives you a complete view at the properties that are maintained as part of application. When interacting
with an application (on get, on create, etc) you will find most of these properties available to you, although they may
be organized in a slightly different structure (ie: on get, everything but the `id` is nested inside an `attributes`
object).

You can find more details in
the [source code](https://github.com/resource-watch/authorization/tree/dev/src/models/application.model.ts).

| Field name   | Type   | Required                              | Default value  | Description                                                                                                                                                    |
|--------------|--------|---------------------------------------|----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| id           | String | Yes (autogenerated)                   |                | Unique Id of the application. Auto generated on creation. Cannot be modified by users.                                                                         |
| name         | String | Yes                                   |                | Name of the application.                                                                                                                                       |
| user         | String | Yes (either `user` or `organization`) |                | Id of the user who owns the application. Either this or `organization` must be set. Only the id is stored. If set, responses will include the id and the name. |
| organization | String | Yes (either `user` or `organization`) |                | Id of the organization who owns the application. Either this or `user` must be set. Only the id is stored. If set, responses will include the id and the name. |
| createdAt    | Date   | No (autogenerated)                    | <current date> | Automatically maintained date of when the application was created. Cannot be modified by users.                                                                |
| updatedAt    | Date   | No (autogenerated)                    | <current date> | Automatically maintained date of when the application was last updated. Cannot be modified by users.                                                           |
