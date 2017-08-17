# Getting Started

A quick start guide to writing Patrol rules.

## Prerequisites

* Node and npm installed
* An AWS account
* awscli configured
* config and template S3 buckets, see [S3 bucket section of the lambda-cfn README](https://github.com/mapbox/lambda-cfn#s3-buckets)

## What's your rule?

What condition do you want to monitor or problem do you want to solve? What service do you want to monitor? AWS? GitHub? Slack? You'll need to pick an existing Patrol project or propose a new one.

* Does your rule involve monitoring AWS? Check out [/patrol-rules-aws](https://github.com/mapbox/patrol-rules-aws)
* Does your rule monitor GitHub? Check out [/patrol-rules-github](https://github.com/mapbox/patrol-rules-github)
* None of the above services? [Create a new issue in this repo](https://github.com/mapbox/patrol/issues/new) and we'll discuss your idea.

## Setup

First globally install [lambda-cfn](https://github.com/mapbox/lambda-cfn). We'll be using lambda-cfn to create new Patrol rule functions and deploy them to AWS.

`npm install -g @mapbox/lambda-cfn`

Then clone and set up the Patrol repo that you'll be working on. In this guide we'll be using [patrol-rules-aws](https://github.com/mapbox/patrol-rules-aws) in our examples.

```
git clone git@github.com:mapbox/patrol-rules-aws.git
cd patrol-rules-aws
npm install
```

## Writing your new rule

Each Patrol rule function lives in its own sub-directory. To create our new function, run `lambda-cfn init <function directory name>`.

This will create a new subdirectory with a `function.js` and `function.template.js` file. See the [lambda-cfn](https://github.com/mapbox/lambda-cfn) README for more information on how to use these files.

## Writing tests

Don't forget to write tests for your rule! Tests live in the `tests` directory of each Patrol project, with one test file per Patrol rule function.

## Upload your code to S3

Before you can deploy the CloudFormation stack for your rule function you'll need to zip up and upload the code to S3. If you don't have your own build system you can use the `upload.sh` script in lambda-cfn to do this, [see the section on uploading your code in the lambda-cfn README](https://github.com/mapbox/lambda-cfn#uploading-your-code-to-s3).

## Deploying your rule to AWS

To deploy your rule function to AWS, run the following command from the rule subdirectory:

`lambda-cfn create <environment name>`

See the [lambda-cfn documentation](https://github.com/mapbox/lambda-cfn#creating-the-cloudformation-stack) for more information on `lambda-cfn create` and other deployment commands.

## Submitting a PR

If you'd like to have your rule featured in a Patrol project, please submit a PR. In order for PRs to be merged and approved they must have passing tests and must deploy to AWS successfully. 