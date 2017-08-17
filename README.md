# Patrol

An AWS Lambda powered monitoring framework for security, compliance, and best practices across an organization. Patrol uses [lambda-cfn](https://github.com/mapbox/lambda-cfn/) to quickly generate and deploy AWS Lambda rule functions and notifications.

This is an issue and documentation only repo for managing the Patrol project and its related repositories. As of [v2.0.0 of lambda-cfn](https://github.com/mapbox/lambda-cfn/blob/master/CHANGELOG.md#200-2017-08-15) you no longer need to use a parent Patrol repository to deploy your Patrol rule functions.

## Getting started

See the [getting started guide](GETTING-STARTED.md) for a quick start tutorial on how to create and deploy Patrol rules.

## Architecture

Patrol consists of a set of Patrol rules or rule functions that monitor for insecure or non-compliant events in a third party service. Each rule is powered by its own AWS Lambda function, which is deployed as its own independent AWS CloudFormation stack using [lambda-cfn](https://github.com/mapbox/lambda-cfn/). Rule event sources can be CloudWatch event rules, scheduled rules, SNS subscriptions, and webhook events.

## Rule example

The [`cloudTrail`](https://github.com/mapbox/patrol-rules-aws/blob/master/cloudTrail/function.js) rule function in [patrol-rules-aws](https://github.com/mapbox/patrol-rules-aws) monitors for disallowed actions on CloudTrail trails. Disallowed actions such as `DeleteTrail` and `StopLogging` (types of CloudWatch events) are provided as parameters at deploy time using the function's [`function.template.js` CloudFormation template](https://github.com/mapbox/patrol-rules-aws/blob/master/cloudTrail/function.template.js#L6-L8).

When a disallowed event occurs this fires off the Lambda function, which sends a message to an SNS topic that's configured with an email address. This allows you to connect your Patrol rule function to a monitoring or alerting system such as PagerDuty.

## Patrol rule repositories

Related Patrol rules are grouped into the same shared GitHub repository. Rules are generally grouped by the third party service that they monitor. Their GitHub repositories follow the naming convention `patrol-rules-<service>`.

* [patrol-rules-aws](https://github.com/mapbox/patrol-rules-aws) for monitoring Amazon Web Services (AWS)
* [patrol-rules-github](https://github.com/mapbox/patrol-rules-github/) for monitoring GitHub

Have another third party service you'd to monitor that's not listed above? [Create a new issue](https://github.com/mapbox/patrol) with your idea or show us your new Patrol stack! 

## Deploying Patrol rules

Deploy Patrol rules to AWS using [lambda-cfn](https://github.com/mapbox/lambda-cfn). 

Each Patrol rule is its own independent CloudFormation stack - you'll need to deploy each rule separately.

## Contributing

This is an issue and documentation only repo - you're welcome to open a new issue. Also, check out the `CONTRIBUTING.md` for lambda-cfn, patrol-rules-aws, patrol-rules-github, and other related repositories.