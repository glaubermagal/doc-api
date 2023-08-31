# User data

The following endpoints expose the RW API's functionality regarding user data management. These endpoints will allow you
to store and recover user-specific data related to the RW API, like application-specific preferences, that are not
already covered by other endpoints.

These endpoints assume you already have a user account and are logged in using it - for more details on that,
see [this section](#user-management).

## What is user data?

The RW API endpoints allow users to upload their own customized data and share it with the world - it's its core
mission. However, at times, you may want to store data that is specific to your usage of the RW API, and while not
secret, it only makes sense to your specific user needs. Some examples of this may be a widget or layer personalization,
an application' settings, or additional details about your user profile.

The User data service allows you to store data specific to your usage of the RW API, in an easy and convenient way.
Besides a few core, common fields, your data is indexed by [application](/doc-api/concepts.html#applications), allowing
different RW API ecosystem apps and tools to share this common data storage, while ensuring simplicity for RW API
developers. To ensure this, make sure to properly store you application's data in the correct form -
inside `applicationData`, indexed by your application's name.

**Important:** User data, as defined managed by these endpoints, is not the same as user account, as managed by the 
[User management](#user-management) endpoints. A user account is what you use to login and authenticate. User data is an 
optional data storage feature, made available to anyone with an user account. Creating/updating/deleting a user account 
does not automatically create/update/delete user data. 

**Warning:** The data stored through these endpoints is individual to your user account, but it's not treated and
confidential or sensitive. You should not store any sort of data that you would not like to see potentially disclosed,
like passwords, sensitive and/or personal information, etc.

**Deprecation notice:** These docs cover the `/v2` user data endpoints, which are currently maintained. A `/v1` set of
endpoints also exists, but its deprecated, and should not be used. If your application is using the `/v1` endpoints, you
should upgrade at your earliest convenience. Functionality and data wise, `/v2` is a superset of `/v1`.

## Getting user data

> Get user data for the current user

```shell
curl -X GET "https://api.resourcewatch.org/v2/user" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>"
```

> Get user data for a given user id

```shell
curl -X GET "https://api.resourcewatch.org/v2/user/5bfd237767b3176dd63f2eb7" \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>"
```

> Response:

```json
{
  "data": {
    "type": "user",
    "id": "5bfd237767b3176dd63f2eb7",
    "attributes": {
      "fullName": "John Doe",
      "email": "john.doe@wri.org",
      "createdAt": "2017-04-25T14:15:58.469Z",
      "applicationData": {
        "gfw": {
          "sector": "Private sector",
          "state": "",
          "city": "",
          "howDoYouUse": [
            ""
          ],
          "primaryResponsibilities": [
            ""
          ],
          "signUpForTesting": false,
          "language": "en",
          "profileComplete": true,
          "interests": [],
          "signUpToNewsletter": false,
          "topics": []
        }
      }
    }
  }
}
```

There are two endpoints that allow you to retrieve user data:

- If no user id is provided, the service will return the data for the current user.
- If a user id is provided, the provided authentication token must either match the requested user, or it must have
  the `ADMIN` [role](/doc-api/concepts.html#user-roles).

**Errors**

| Error code | Error message      | Description                                              |
|------------|--------------------|----------------------------------------------------------|
| 401        | Not authenticated. | You are not logged in.                                   |
| 403        | Forbidden.         | You do not have permission to access the requested data. |
| 404        | User not found     | The requested user id does not have stored user data.    |

## Creating user data

> Create user data for the current user

```shell
curl -X POST https://api.resourcewatch.org/v2/user \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"  -d \
'{
  "fullName": "John Doe",
  "firstName": "John",
  "lastName": "Doe",
  "email": "john.doe@wri.org",
  "applicationData": {
    "gfw": {
      "someKey": "someValue"
    }
  }
}'
```

```json
{
  "data": {
    "type": "user",
    "id": "5bfd237767b3176dd63f2eb7",
    "attributes": {
      "fullName": "John Doe",
      "firstName": "John",
      "lastName": "Doe",
      "email": "john.doe@wri.org",
      "createdAt": "2017-04-25T14:15:58.469Z",
      "applicationData": {
        "gfw": {
          "sector": "Private sector",
          "state": "",
          "city": "",
          "howDoYouUse": [
            ""
          ],
          "primaryResponsibilities": [
            ""
          ],
          "signUpForTesting": false,
          "language": "en",
          "profileComplete": true,
          "interests": [],
          "signUpToNewsletter": false,
          "topics": [],
          "someKey": "someValue"
        }
      }
    }
  }
}
```

This endpoint allows you to store user data for a user that did not previously have user data. Each user (by id) can only have a single user data record, and it will be shared and visible in other RW API applications that rely on these endpoints, so it's important to follow a few guidelines to help maintain coherence:
- User data root fields (around name and email address) are meant to be shared between multiple apps, so only modify those if the change is intentional by the user.
- All application-specific data must be stored in the `applicationData` object, and in it, indexed by your [application](/doc-api/concepts.html#applications) name. The included example shows how the `gfw` application could store its data. This is not enforced, but disregarding this principle **may lead to your data being deleted** without prior notice.
- The `gfw` application has special logic and fields, as seen in the included example. However, other application keys will not have this, and no manipulation will occur on your data stored. What you send is what gets stored, no more, no less.

**Errors**

| Error code | Error message       | Description                                                                 |
|------------|---------------------|-----------------------------------------------------------------------------|
| 400        | Duplicated user.    | This user already has data associated with it.                              |
| 400        | Unsupported sector. | The provided `applicationData.gfw.sector` value is not formatted correctly. |
| 401        | Not authenticated.  | You are not logged in.                                                      |
| 403        | Forbidden.          | You do not have permission to access the requested data.                    |

## Updating user data

> Update user data for the given user id

```shell
curl -X PATCH https://api.resourcewatch.org/v2/user/5bfd237767b3176dd63f2eb7 \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json"  -d \
'{
  "fullName": "John Doe",
  "firstName": "John",
  "lastName": "Doe",
  "email": "john.doe@wri.org",
  "applicationData": {
    "gfw": {
      "someKey": "someOtherValue"
    }
  }
}'
```

```json
{
  "data": {
    "type": "user",
    "id": "5bfd237767b3176dd63f2eb7",
    "attributes": {
      "fullName": "John Doe",
      "firstName": "John",
      "lastName": "Doe",
      "email": "john.doe@wri.org",
      "createdAt": "2017-04-25T14:15:58.469Z",
      "applicationData": {
        "gfw": {
          "sector": "Private sector",
          "state": "",
          "city": "",
          "howDoYouUse": [
            ""
          ],
          "primaryResponsibilities": [
            ""
          ],
          "signUpForTesting": false,
          "language": "en",
          "profileComplete": true,
          "interests": [],
          "signUpToNewsletter": false,
          "topics": [],
          "someKey": "someOtherValue"
        }
      }
    }
  }
}
```

This endpoint allows you to update existing user data for a user. Similar to the user data creation process, there are a few things to keep in mind when updating user data, to help maintain coherence:
- User data root fields (around name and email address) are meant to be shared between multiple apps, so only modify those if the change is intentional by the user.
- All application-specific data must be stored in the `applicationData` object, and in it, indexed by your [application](/doc-api/concepts.html#applications) name. The included example shows how the `gfw` application could store its data. This is not enforced, but disregarding this principle **may lead to your data being deleted** without prior notice.
- The `gfw` application has special logic and fields, as seen in the included example. However, other application keys will not have this, and no manipulation will occur on your data stored. What you send is what gets stored, no more, no less.
- If updating a key within the `applicationData` object, all its content will be replace with the values you provide. Keys not provided are kept as-is.

You can only update user data for the user provided in the authentication token.

**Errors**

| Error code | Error message       | Description                                                                 |
|------------|---------------------|-----------------------------------------------------------------------------|
| 400        | Unsupported sector. | The provided `applicationData.gfw.sector` value is not formatted correctly. |
| 401        | Not authenticated.  | You are not logged in.                                                      |
| 403        | Forbidden.          | You do not have permission to access the requested data.                    |
| 404        | User not found      | The requested user id does not have stored user data.                       |

## Deleting user data

> Delete user data for the given user id

```shell
curl -X DELETE https://api.resourcewatch.org/v2/user/5bfd237767b3176dd63f2eb7 \
-H "Authorization: Bearer <your-token>" \
-H "x-api-key: <your-api-key>"
```

```json
{
  "data": {
    "type": "user",
    "id": "5bfd237767b3176dd63f2eb7",
    "attributes": {
      "fullName": "John Doe",
      "firstName": "John",
      "lastName": "Doe",
      "email": "john.doe@wri.org",
      "createdAt": "2017-04-25T14:15:58.469Z",
      "applicationData": {
        "gfw": {
          "sector": "Private sector",
          "state": "",
          "city": "",
          "howDoYouUse": [
            ""
          ],
          "primaryResponsibilities": [
            ""
          ],
          "signUpForTesting": false,
          "language": "en",
          "profileComplete": true,
          "interests": [],
          "signUpToNewsletter": false,
          "topics": [],
          "someKey": "someOtherValue"
        }
      }
    }
  }
}
```

This endpoint allows you to delete user data. Keep in mind that this will delete all user data for all [applications](/doc-api/concepts.html#applications), and not just yours. If you want to delete user data for your application, use the [update user data](#updating-user-data) endpoint instead. Fully deleting user data will typically only be used when deleting the actual user account.

You can only delete user data for the user provided in the authentication token.

**Errors**

| Error code | Error message       | Description                                                                 |
|------------|---------------------|-----------------------------------------------------------------------------|
| 401        | Not authenticated.  | You are not logged in.                                                      |
| 403        | Forbidden.          | You do not have permission to access the requested data.                    |
| 404        | User not found      | The requested user id does not have stored user data.                       |

