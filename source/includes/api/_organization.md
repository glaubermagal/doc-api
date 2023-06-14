# Organization

The following endpoints expose the RW API's functionality regarding organization management.

## What is an organization?

In the context of the RW API, an organization is an resource meant to group
multiple [users](reference.html#user-management) and grant them certain permissions and/or ownership over a group of
[applications](reference.html#applications). An example of an RW API organization would be WRI itself, the owner of the
RW API - it's an entity that has multiple people (users) and that runs multiple software applications (GFW, RW, etc)
that rely on the services of the RW API.

## Organization ownership and user association

Each organization can have multiple users, yet not all users in the same organization have the same permissions to
operate over said organization's resources. To differentiate between these types of access, every user associated with
an organization does so through one of the following roles:

- Organization administrator, or `ORG_ADMIN`: users with this role have full access to the organization and its
  applications, and can perform any operation over them. Each organization must have one and only one user with this
  role at any given time.
- Organization member, or`ORG_MEMBER`: users with this role can access but not modify the organization details and
  applications. Each organization can have any number of these users.

A user can be associated with any number of organizations, and can have a different role for each of them.

## Getting all organizations

> Getting a list of organizations

```shell
curl -X GET https://api.resourcewatch.org/v1/organization
```

> Response:

```json
{
  "data": [
    {
      "type": "organizations",
      "id": "63da7d2357499d85f8436cd9",
      "attributes": {
        "name": "Test org 1",
        "applications": [
          {
            "id": "63be3da993ea69eb458fc466",
            "name": "Sample application"
          }
        ],
        "users": [
          {
            "id": "5bfd237767b3176dd63f2eb7",
            "name": "John Doe",
            "role": "ORG_ADMIN"
          }
        ],
        "createdAt": "2023-02-01T14:54:27.530Z",
        "updatedAt": "2023-02-01T14:54:27.530Z"
      }
    }
  ],
  "links": {
    "self": "https://api.resourcewatch.org/v1/organization?page[number]=1&page[size]=10",
    "first": "https://api.resourcewatch.org/v1/organization?page[number]=1&page[size]=10",
    "last": "https://api.resourcewatch.org/v1/organization?page[number]=99&page[size]=10",
    "prev": "https://api.resourcewatch.org/v1/organization?page[number]=1&page[size]=10",
    "next": "https://api.resourcewatch.org/v1/organization?page[number]=2&page[size]=10"
  },
  "meta": {
    "total-pages": 99,
    "total-items": 990,
    "size": 10
  }
}
```

This endpoint will allow you to get the list of the organizations available in the API. By default, this endpoint will
give you a paginated list of 10 organizations.

In order to use this endpoint, you need to be authenticated as an `ADMIN` or `MANAGER` user.

For a detailed description of each field, check out the [Organization reference](reference.html#organization-reference)
section.

### Pagination

> Example request to load page 2 using 25 results per page

```shell
curl -X GET https://api.resourcewatch.org/v1/organization?page[number]=2&page[size]=25
```

The organization service adheres to the conventions defined in
the [Pagination guidelines for the RW API](concepts.html#pagination), so we recommend reading that section for more
details on how paginate your organizations list.

## Getting an organization by id

> Getting an organization by id:

```shell
curl -X GET "https://api.resourcewatch.org/v1/organization/51943691-eebc-4cb4-bdfb-057ad4fc2145"
```

> Response:

```json
{
  "data": {
    "type": "organizations",
    "id": "63da7d2357499d85f8436cd9",
    "attributes": {
      "name": "Test org 1",
      "applications": [
        {
          "id": "63be3da993ea69eb458fc466",
          "name": "Sample application"
        }
      ],
      "users": [
        {
          "id": "5bfd237767b3176dd63f2eb7",
          "name": "John Doe",
          "role": "ORG_ADMIN"
        }
      ],
      "createdAt": "2023-02-01T14:54:27.530Z",
      "updatedAt": "2023-02-01T14:54:27.530Z"
    }
  }
}
```

If you know the id of an organization, then you can access it directly. The id is case-sensitive.

In order to use this endpoint, you must be logged in and either:

- have either `ADMIN` or `MANAGER` [user role](/doc-api/concepts.html#user-roles).
- be associated with the target organization, as either a member or as an admin -
  see [Organization ownership and user association](#organization-ownership-and-user-association) for more details.

### Errors for getting an organization by id

| Error code | Error message (example) | Description                                                                   |
|------------|-------------------------|-------------------------------------------------------------------------------|
| 401        | `Not authenticated`     | No authorization token was provided.                                          |
| 403        | `Not authorized`        | You do not have the necessary permissions to see this organization's details. |

## Creating an organization

```shell
curl -X POST "https://api.resourcewatch.org/v1/organization" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer <your-token>" \
-d  \
'{
    "name": "Test org 1",
    "user": [{
      "id": "5bfd237767b3176dd63f2eb7",
      "role": "ORG_ADMIN"
    }]
}'
```

> Response:

```json
{
  "data": {
    "type": "organizations",
    "id": "63be3da993ea69eb458fc4a6",
    "attributes": {
      "name": "Test org 1",
      "applications": [],
      "users": [
        {
          "id": "5bfd237767b3176dd63f2eb7",
          "name": "John Doe",
          "role": "ORG_ADMIN"
        }
      ],
      "createdAt": "2023-01-11T04:40:09.455Z",
      "updatedAt": "2023-01-11T04:40:09.455Z"
    }
  }
}
```

Creating an organization is currently reserved for RW API administrators (users with
the `ADMIN` [role](/doc-api/concepts.html#user-roles), so if you'd like to create your own,
please [contact us](https://resourcewatch.org/about/contact-us) letting us know about you and your
organization.

Creating an organization is done using a POST request and passing the relevant data as body files. The supported body
fields are as defined on the [organization reference](reference.html#organization-reference) section, but the minimum
field list you must specify for any organization is:

- `name`
- `users`, with a list of object, containing the user `id`, and its `role` in the organization. At least one user must
  be provided, and at least one user must have the role `ORG_ADMIN`.

A successful organization creation request will return a 200 HTTP code, and the organization details as stored on the RW
API. Pay special attention to the `id`, as it will allow you
to [access your organization](reference.html#getting-an-organization-by-id) later.

When creating an organization, you can also pass an optional array of applications ids, as `applications` in the request
body. These applications must already exist.

It's worth keeping in mind that applications can either belong to a single user or a single organization, so by creating
an organization with `applications` associated with it, you may be removing associations between the
provided `applications` and other users/organizations.

### Errors for creating an organization

| Error code | Error message                                    | Description                                                                                  |
|------------|--------------------------------------------------|----------------------------------------------------------------------------------------------|
| 400        | `<field>` is required                            | Your are missing a required field value.                                                     |
| 400        | `<field>` is not allowed                         | You have provided a body value that is not supported by the endpoint.                        |
| 400        | "users" must contain at least 1 items            | You must provide at least one user.                                                          |
| 400        | "users" must contain a user with role ORG_ADMIN' | You must provide at least a user with `ORG_ADMIN` role.                                      |
| 401        | Not authenticated                                | You are not authenticated. Only authenticated users can create organizations.                |
| 403        | Not authorized                                   | You are authenticated but do not have the necessary permissions to create this organization. |

## Updating an organization

> Updating an organization

```shell
curl -X PATCH "https://api.resourcewatch.org/v1/organization/51943691-eebc-4cb4-bdfb-057ad4fc2145"
-H "Content-Type: organization/json"  -d \
-H "Authorization: Bearer <your-token>" \
 '{
    "name":"new organization name",
}'
```

> Example response

```json
{
  "data": {
    "type": "organizations",
    "id": "63be3da993ea69eb458fc4a6",
    "attributes": {
      "name": "new organization name",
      "applications": [],
      "users": [
        {
          "id": "5bfd237767b3176dd63f2eb7",
          "name": "John Doe",
          "role": "ORG_ADMIN"
        }
      ],
      "createdAt": "2023-01-11T04:40:09.455Z",
      "updatedAt": "2023-01-11T04:40:09.455Z"
    }
  }
}
```

> Modify an organization and replacing its applications

```shell
curl -X PATCH "https://api.resourcewatch.org/v1/organization/51943691-eebc-4cb4-bdfb-057ad4fc2145"
-H "Content-Type: organization/json"  -d \
-H "Authorization: Bearer <your-token>" \
 '{
    "applications": ["6bfd237767b3176dd63f2eb7"],
}'
```

> Example response

```json
{
  "data": {
    "type": "organizations",
    "id": "63be3da993ea69eb458fc4a6",
    "attributes": {
      "name": "new organization name",
      "applications": [
        {
          "id": "6bfd237767b3176dd63f2eb7",
          "name": "application name"
        }
      ],
      "users": [
        {
          "id": "5bfd237767b3176dd63f2eb7",
          "name": "John Doe",
          "role": "ORG_ADMIN"
        }
      ],
      "createdAt": "2023-01-11T04:40:09.455Z",
      "updatedAt": "2023-01-11T04:40:09.455Z"
    }
  }
}
```

This endpoint allows you to modify an existing organization. When updating an organization, you can pass new values for
any of these optional parameters:

- `name`: Modifies the organization's name.
- `users`:  A list of objects, with `id` being the user's id, and `role` being said user's role in the organization -
  must be either `ORG_ADMIN` or `ORG_MEMBER`. One and only one `ORG_ADMIN` user must exist per organization.
- `applications`: A list of applications ids to be owned by this organization.

When modifying either `users` or `applications`, keep in mind that the list of associations you provide will overwrite
the previous one.

It's worth keeping in mind that applications can either belong to a single user or a single organization, so by updating
an organization with `applications` associated with it, you may be removing associations between the
provided `applications` and other users/organizations.

In order to use this endpoint, you must be logged in and either:

- have the `ADMIN` [user role](/doc-api/concepts.html#user-roles).
- be the organization admin -
  see [Organization ownership and user association](#organization-ownership-and-user-association) for more details.

Additionally, if you are using this endpoint to add existing applications to an organization, you must either

- have the `ADMIN` [user role](/doc-api/concepts.html#user-roles).
- be the owner of the application.

### Errors for updating an organization

| Error code | Error message                                          | Description                                                                                                                                                 |
|------------|--------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 400        | `<field>` is not allowed                               | You have provided a body value that is not supported by the endpoint.                                                                                       |
| 400        | "users" must contain at least 1 items                  | You must provide at least one user.                                                                                                                         |
| 400        | "users" must contain a user with role ORG_ADMIN        | You must provide at least a user with `ORG_ADMIN` role.                                                                                                     |
| 400        | "users" must contain single a user with role ORG_ADMIN | You must provide only one user with `ORG_ADMIN` role.                                                                                                       |
| 401        | Not authenticated                                      | You are not authenticated. Only authenticated users can update organizations.                                                                               |
| 403        | Not authorized                                         | You are authenticated but do not have the necessary permissions to update this organization, or to add one of the provided application to the organization. |
| 404        | Organization with id <id> doesn't exist                | An organization with the provided id does not exist.                                                                                                        |

## Deleting an organization

> Deleting an organization

```shell
curl -X DELETE https://api.resourcewatch.org/v1/organization/<organization-id> \
-H "Authorization: Bearer <your-token>" \
-H "Content-Type: organization/json"
```

> Response:

```json
{
  "data": {
    "type": "organizations",
    "id": "63da7d2357499d85f8436cd9",
    "attributes": {
      "name": "Test org 1",
      "applications": [
        {
          "id": "63be3da993ea69eb458fc466",
          "name": "Sample application"
        }
      ],
      "users": [
        {
          "id": "5bfd237767b3176dd63f2eb7",
          "name": "John Doe",
          "role": "ORG_ADMIN"
        }
      ],
      "createdAt": "2023-02-01T14:54:27.530Z",
      "updatedAt": "2023-02-01T14:54:27.530Z"
    }
  }
}
```

Use this endpoint if you wish to delete an organization. In order to use this endpoint, you must be logged in and have
the `ADMIN` [user role](/doc-api/concepts.html#user-roles).

Only organizations that do not have any applications associated with them can be deleted.

### Errors for deleting an organization

| Error code | Error message                                                | Description                                                                                                                                    |
|------------|--------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| 400        | Organizations with associated applications cannot be deleted | You are trying to delete an organization that still has applications associated with it. Either transfer ownership or delete the applications. |
| 401        | Not authenticated                                            | You are not authenticated. Only authenticated users can delete organizations.                                                                  |
| 403        | Not authorized                                               | You are authenticated but do not have the necessary permissions to delete this organization.                                                   |
| 404        | Organization with id <id> doesn't exist                      | An organization with the provided id does not exist.                                                                                           |

## Organization reference

This section gives you a complete view at the properties that are maintained as part of organization. When interacting
with an organization (on get, on create, etc) you will find most of these properties available to you, although they may
be organized in a slightly different structure (ie: on get, everything but the `id` is nested inside an `attributes`
object).

You can find more details in
the [source code](https://github.com/resource-watch/authorization/tree/dev/src/models/organization.model.ts).

| Field name   | Type         | Required            | Default value  | Description                                                                                                                                                                                                       |
|--------------|--------------|---------------------|----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| id           | String       | Yes (autogenerated) |                | Unique Id of the organization. Auto generated on creation. Cannot be modified by users.                                                                                                                           |
| name         | String       | Yes                 |                | Name of the organization.                                                                                                                                                                                         |
| users        | Object array | Yes                 |                | List of objects, each containing the `id` of an user, and the `role` they have in the organization - which can be either `ORG_ADMIN` or `ORG_MEMBER`. Every organization must have at least one `ORG_ADMIN` user. |
| applications | String array | No                  |                | List of applications owned by this organization                                                                                                                                                                   |
| createdAt    | Date         | No (autogenerated)  | <current date> | Automatically maintained date of when the organization was created. Cannot be modified by users.                                                                                                                  |
| updatedAt    | Date         | No (autogenerated)  | <current date> | Automatically maintained date of when the organization was last updated. Cannot be modified by users.                                                                                                             |
