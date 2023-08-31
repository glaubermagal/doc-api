# Contact

The following contact endpoints are available

## Contact us

> Example request with the minimum required data

```shell
curl -X POST https://api.resourcewatch.org/v1/form/contact-us \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json" -d \
    '{
        "email": "example.example@vizzuality.com",
        "message": "This is a test"
    }'
```

> Example request with complete data

```shell
curl -X POST https://api.resourcewatch.org/v1/form/contact-us \
-H "x-api-key: <your-api-key>" \
-H "Content-Type: application/json" -d \
    '{
        "email": "example.example@vizzuality.com",
        "message": "This is a test",
        "language": "es_MX",
        "tool": "fw",
        "topic": "data-related-inquiry"
    }'
```

Sends a contact form including an origin email address and a message. It can also optionally include:

- `language`: One of the following supported values: `en`, `es_MX`, `fr`, `id`, `pt_BR` or `zh`. Defaults to `en`.
- `topic`: One of the following supported values: `report-a-bug-or-error`, `provide-feedback`, `data-related-inquiry`
  or `general-inquiry`. Defaults to `general-inquiry`.
- `tool`: One of the following supported values: `gfw`, `gfw-pro`, `fw`, `blog`, `map-builder` or `not-applicable`.
  Defaults to `not-applicable`

The `language` value is used as a reference for future communication with the user. `topic` and `tool` are used to
internally better determine the communication path the request must follow in order to reach the desired person that
will handle it.

On success, a 200 HTTP code with no response body is returned.

## Requesting a webinar

> Example request for a webinar

```shell
curl -X POST "https://api.resourcewatch.org/v1/form/request-webinar" \
-H "Content-Type: application/json" \
-H "x-api-key: <your-api-key>" \
-d  \
'{
    "name": "Example name",
    "email": "example@email.com",
    "request": "Example request text"
}'
```

This endpoint allows users to request a webinar. Internally, it sends the user submitted information to a WRI staff
member, via email. The user must provide their name, email address, and a request text, all of which will be included
in the above mentioned email. A successful request returns an HTTP 204 response. 
