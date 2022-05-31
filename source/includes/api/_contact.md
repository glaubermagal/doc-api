# Contact

The following contact endpoints are available


## Contact us

Sends a contact form including a topic, email address, and a message. 


### Example

Contact form sent using the type `General question or feedback`, the email address `example@example.com` and the message `This is a text`:

```shell
curl -X POST https://api.resourcewatch.org/v1/contact-us \
-H "Content-Type: application/json" -d \
    '{
        "topic": "General question or feedback",
        "email": "example.example@vizzuality.com",
        "text": "This is a test"
    }'
```

## Requesting a webinar

> To create a new webinar request, you need to provide at least the following details:

```shell
curl -X POST "https://api.resourcewatch.org/v1/form/request-webinar" \
-H "Content-Type: application/json" \
-d  \
'{
    "name": "Example name",
    "email": "example@email.com",
    "description": "Example description"
}'
```

> Successful response: 204 No Content

In order to request a new webinar, you should make a POST request to the `form/request-webinar` endpoint, providing at least a `name` and an `email` in the request body. You can also optionally provide a `description`.

Webinar requests are pushed into a Google Spreadsheet (using the Google Sheets API). Staging webinar requests are located [here](https://docs.google.com/spreadsheets/d/1JsXX7aE_XlJm-WWhs6wM5IW0UfLi-K9OmOx0mkIb0uA/edit?usp=sharing), and production webinar requests are located [here](https://docs.google.com/spreadsheets/d/1zqiimFua1Lnm9KM4ki_njCaMuRhaPBif30zbvxIZWa4/edit?usp=sharing).

In case of success, this endpoint
