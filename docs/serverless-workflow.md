# Serverless Workflow

## Introduction

With Serverless, a local backend environment is no more needed. Each developer could use
their own personal stack on the cloud provider.
It doesn't cost too much because each service pricing is on demand and only one developer uses their personal stack.

It's allow you to:

- Develop in an iso production environment.
- Invoke a function locally while triggering the rest of the stack online.

It's possible because:

- The deployment process is quick.
- The deployment scripts can be easily changed to deploy an other stack.
- Other AWS services are called via an API, so it does not matter if the lambda code executes locally or on a cloud 'container'

The main downsides are:

- You need an internet connection to develop.

If you can't or don't want to use online stacks to develop, there are ways to create local stacks:
[Serverless-offline](https://github.com/dherault/serverless-offline) and/or [AWS localstack](https://github.com/localstack/localstack).
Be careful it's way more complicated to correctly set up a local stack
than using an online stack and quite far from being iso production (not best practice).

## <span id="how-to-implement">How to implement?</span>

You need to be authenticated as a user with enough permissions to deploy your stack and invoke your lambdas.

The best practice is to have an IAM programmatic access only user with an attached policy for serverless required permissions.

- **Create an IAM user per developer**

  - Go on the AWS IAM console or ask to an admin.
  - Add new user (go to **IAM** > **Users** > **Add user**)
    - Select programmatic access only
    - Name it `{name}-{serverless}` (e.g. `ben-serverless`)
  - Attach a policy matching [policy.json](./policy.json)
  - Download the CSV credential file provided and send it to the developer.

  - Ensure you have the awscli library installed
    (https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)

* Save your credentials using `aws configure --profile {name}-{serverless}`
* Make sure you enable MFA for the user in Users > `{name}-{serverless}` > Security Credentials
* ... and then copy the MFA ARN `arn:aws:iam::XXXXXXXXXX:mfa/{name}-{serverless}`
* MFA is enabled so run [mfa.sh](./mfa.sh) `./mfa.sh <AWS_PROFILE> <MFA_ARN> <MFA_TOKEN_CODE>`

  - Validate this by running `aws s3 ls --profile mfa`
  - Note: profile is now `mfa` (for aws commands use `--profile` for sls commands run `--aws-profile`)

* **Use your profiles**

  [Serverless framework](https://serverless.com/framework/docs/providers/aws/) is used in this project.
  The `serverless` command is hidden behind the `yarn` scripts.

  By default `serverless` use your `default` AWS profile. But there are two ways to override it:

  - use the `yarn` script with `--aws-profile <PROFILE_NAME>`

    Example

    ```
    yarn deploy:function hello --profile ben-serverless
    ```

  - Set up environment variable `AWS_PROFILE=<PROFILE_NAME>`

    You can add `export AWS_PROFILE=<PROFILE_NAME>` to your `.bashrc`, `.zshrc` or equivalent depending on your shell to add your profile during your project

* **Deploy and use your personal stack**

  - **_#TODO the developer should be able to put the name of his personal stack
    in an uncommitted file. And the deploy script should throw an error if the .env
    is not set to avoid the deployment of an unwanted stack._**

  To deploy and invoke your functions, use :

  - Deploy: `yarn deploy`
  - Deploy a single function: `yarn deploy -f <function_name>`
  - Invoke Remote: `yarn sls invoke -f <function_name>`
  - Invoke Local: `yarn sls invoke local -f hello <function_name>`
  - Display logs: `sls logs -f hello <function_name> -t`
  - Remove the stack: `yarn sls remove`

**Further reading on credentials**

- [Docs from `serverless`](https://github.com/serverless/serverless/blob/master/docs/providers/aws/guide/credentials.md)
