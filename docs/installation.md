# Installation

In development:
- Read about the [serverless workflow](serverless_workflow.md) to understand why there is no local backend




## First Setup
Go through this once when you install the project, you shouldn't need to do that again.

- **Set up your backend dev environment**

    Follow the [serverless workflow](serverless_workflow.md#how-to-implement)

- **Deploy the backend to your personal stack on AWS**
    ```bash
    cd backend
    yarn install
    yarn deploy --aws-profile <PROFILE_NAME>
    ```

- **Create your Cognito User**

    Register a user:
    ```bash
    aws cognito-idp sign-up --region eu-west-2 --client-id <YOUR_APP_CLIENT_ID> --username <YOUR_USERNAME> --password <YOUR_PASSWORD>
    ```

    Confirm the user registration:
    ```bash
    aws cognito-idp admin-confirm-sign-up --region eu-west-2 --user-pool-id <YOUR_USER_POOL_ID> --username <YOUR_USERNAME>
    ```

- **Get JWT token for your Cognito User**

    Authenticate with user:
    ```
    aws cognito-idp  initiate-auth --auth-flow USER_PASSWORD_AUTH --region eu-west-2 --client-id  <CLIENT_ID> --auth-parameters  USERNAME="<EMAIL>",PASSWORD="<PASSWORD>"
    ```

- **Call with the Authorization token**

    CURL API with Authorization header
    ```
    curl -X GET <URL> \
    -H 'Authorization: Bearer <ID_TOKEN>'
    ```

## Start the app

What you need to do to (re)start the project:


