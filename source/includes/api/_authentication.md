# Authentication

## JWT tokens

Most RW API endpoints are public, but some actions require user authentication. Each endpoint's documentation will
specify whether authentication is required and under what conditions.

The RW API uses JSON Web Tokens to authenticate requests. Check out the [Quickstart Guide](quickstart.html) for
instructions on how to create an account and generate a token.

Authentication is performed using JWT tokens. For all authenticated requests, you must provide your JWT using the
header `Authorization: Bearer <your token>`. Please note that your JWT tokens act like passwords, so you should never
share or expose them publicly. Doing so may allow other users to impersonate your RW API user account, and may grant
them permissions over your datasets, applications, etc.

If you lose your JWT, or if it becomes invalidated (which happens whenever the name, email, or applications associated
with your account changes), you can always [log in via the browser](https://api.resourcewatch.org/auth/login) and
generate a new token
at [https://api.resourcewatch.org/auth/generate-token](https://api.resourcewatch.org/auth/generate-token).

## API Keys

Besides JWT tokens, the RW API also uses API keys to identify the [client application](reference.html#application)
issuing the request. This is done for moderation and analytical purposes, ensuring it's
available to everyone for fair use. Unlike the JWT tokens described above, API keys are not used to grant permissions 
over data. While it's not critical to keep them private, you should have a different API key for each
application you create that uses the RW API.

An API key is generated automatically once you register a [client application](reference.html#application), which you
can learn more about in the corresponding section of the reference docs.
