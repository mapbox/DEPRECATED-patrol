## Crowsnest

A lightweight, general purpose, serverless monitoring framework for security, compliance, and best practice issues across an organization. It simplifies wiring arbitrary event streams to logic and notifications.

Crowsnest is a Node application deployed on AWS, leveraging the following AWS services:
- [CloudFormation](http://aws.amazon.com/documentation/cloudformation/)
- [IAM](http://aws.amazon.com/documentation/iam/)
- [Lambda](http://aws.amazon.com/documentation/lambda/)
- [CloudWatch Events](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/WhatIsCloudWatchEvents.html)
- [Simple Notification Service](http://aws.amazon.com/documentation/sns/)

Crowsnest can consume event streams from non-AWS sources and provides three types of rule triggers:
- CloudWatch Event Rules
- Scheduled rules
- SNS subscriptions

#### General Anatomy of a Crowsnest stack

[**Crowsnest**](https://github.com/mapbox/crowsnest)

Crowsnest itself ties together [`lambda-cfn`](https://github.com/mapbox/lambda-cfn) and repositories containing rulesets ([`crowsnest-rules-aws`](https://github.com/mapbox/crowsnest-rules-aws)). The `crowsnest` repository is an example only, please fork to maintain your own custom Crowsnest stack.

It requires [`cfn-config`](https://github.com/mapbox/cfn-config/) to deploy the javascript CloudFormation template.

Rulesets are added to the `crowsnest/package.json`, allowing rulesets to be independently version controlled in their own repositories.

Rules compiled into the Crowsnest lambda are defined in `crowsnest/cloudformation/crowsnest.template.js`.

[**lambda-cfn**](https://github.com/mapbox/lambda-cfn)

`lambda-cfn` is a reusable framework to generate Lambda functions. It does most of the heavy lifting, creating both the CloudFormation template and the CloudWatch Event Rules.

[**cfn-config**](https://github.com/mapbox/cfn-config/)

`cfn-config` provides a interactive commandline tool to create and update CloudFormation templates. It supports the Javascript CloudFormation templates necessary for Crowsnest.

[**crowsnest-rules-aws**](https://github.com/mapbox/crowsnest-rules-aws)

Public Crowsnest rules for monitoring AWS resources and API calls.

[**streambot**](https://github.com/mapbox/streambot)

`streambot` provides a wrapper function and a backend to load CloudFormation parameters into a lambda's environment.

#### Using Crowsnest

- [Getting started](GETTING-STARTED.md)
- [Developing Rulesets](DEVELOPING-RULESETS.md)
- [Hacking on Crowsnest](HACKING.md)


#### Crowsnest in action

The `disallowedResources.js` rule from `crowsnest-rules-aws` uses CloudTrail to generate an alert when a policy document grants access to specific resource ARNs.

```
   +------------------+
   | CloudTrail Event |
   |      Stream      |
   +--------+---------+
            |
            |     Crowsnest managed resources
+-------------------------------------------+
|           |                               |
|     +-----v-----------+                   |
|     | CloudWatch Rule |                   |
|     +--------+--------+                   |
|              |                            |
|              | Run event against          |     +-----------+
|              |                            |     | Streambot |
|      +-------v-------+                    |     +------^----+
|      | event pattern |                    |            |
|      +-------+-------+                    |            |
|              |                            |            |
|              | on match, trigger          |            |
|              |                            |            |
|      +-------v--------------------+       |            |
|      | Subscribed Lambda function <--------------------+
|      +-------------+--------------+       |      get parameters
|                    |                      |
|                    |                      |
|      +-------------v---------+            |
|      | process event payload |            |
|      +---+-------------------+            |
|          |                                |
|          |                                |
|          | emit message to SNS topic      |
|          |                                |
|          |                                |
|     +----v------+                         |
|     | SNS topic |                         |
|     +---------+-+                         |
|               |                           |
+-------------------------------------------+
                |
                |
            +---v-------+
            | PagerDuty |
            +-----------+


```

#### Rule walkthrough
[disallowedResources.js](https://github.com/mapbox/crowsnest-rules-aws/blob/master/rules/disallowedResources.js)

1. The rule requires a comma separated list of ARNs, specified during template deployment by the parameter `config.parameters.disallowedResourceArns`.
2. The `config.eventRule` object describes the CloudWatch Event Rule, the source for the rule (CloudTrail), and the rule filter pattern. See [CloudWatch Events and Event Patterns](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/CloudWatchEventsandEventPatterns.html) for more on the syntax of event patterns.
3. When the rule pattern is matched, CloudWatch Rules triggers the Crowsnest Lambda and passes the event to the `index.disallowedResources` handler.
4. The rule function defined in `module.rule.fn` and bound to the `index.disallowedResources` handler processes the event, and the function emits a message to an SNS topic when appropriate.
