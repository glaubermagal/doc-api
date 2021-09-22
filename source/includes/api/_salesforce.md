# Salesforce integration

This service allows easy communication of RW API user data to [Salesforce](www.salesforce.com) for convenient [CRM](https://en.wikipedia.org/wiki/Customer_relationship_management) purposes.
) 

## Searching for user details

> Searches for users by email address

```shell
curl -X GET https://api.resourcewatch.org/v1/salesforce/contact/search?email=user@email.com
```

> Response:

```json
{
  "data": [
    {
      "Id": "0123456789abcdef",
      "FirstName": "Test",
      "LastName": "User",
      "Email": "user@email.com",
      "Personal_Email__c": "user@email.com",
      "Work_Email__c": "user@email.com",
      "Alternate_Email__c": null
    }
  ]
}
```

Returns the details associated with users matching the provided search criteria. If more than one user matches the search criteria, only the first one (as sorted by Salesforce) is returned - this is likely to change in the future.

### Filters

Parameter    |               Description |    Type |
------------ | :-----------------------: | ------: |
email        | Email address of the user |    Text |

Currently, only filtering by `email` is supported. It is also required.

#### Errors for searching for user details

Error code     | Error message  | Description
-------------- | -------------- | --------------
400            | "email" is required | The required `email` query parameter is missing in the request.


## Logging user data update

```shell
curl -X POST https://api.resourcewatch.org/v1/salesforce/contact/log-action \
-H "Authorization: Bearer <your-token>" \
-H "Content-Type: application/json"  -d \
 '{
   "email": "user@email.com",
   "firstName": "John",
   "lastName": "Doe"
 }'
```

Logs a change to a user's details. The user to update is identified by its email address, which is a required body parameter. If more than one user account matches the provided email address, the first one (as sorted by Salesforce) is used.

Changes logged using this endpoint are not applied synchronously, so they may not be reflected immediately on data loaded through other endpoints.

A successful user data update logging returns an empty HTTP 201 response.



### Logging user data update parameters

Parameter |  Type | Required |
--------- |  ---: | -------: |
email     |  Text |      Yes |
accountName     |  Text |       No |
cityOfInterest     |  Text |       No |
collabSummary     |  Text |       No |
communityMemberType     |  Text |       No |
countryOfInterest     |  Text |       No |
gfwContactType     |  Text |       No |
firstName     |  Text |       No |
lastName     |  Text |       No |
partnerType     |  Text |       No |
preferredLanguage     |  Text |       No |
primaryRole     |  Text |       No |
sector     |  Text |       No |
sourceOfContactCreation     |  Text |       No |
stateDepartmentProvinceOfInterest     |  Text |       No |
title     |  Text |       No |
topicsOfInterest     |  Text |       No |


#### Errors for logging user data update

Error code     | Error message  | Description
-------------- | -------------- | --------------
400            | "email" is required | The required `email` body parameter is missing in the request.
400            | "\<field name\>" is an invalid field | The provided field name is not supported as a body parameter.
