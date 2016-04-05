#### Getting started with Patrol
This guide assumes AWS credentials are exported as local environment variables `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`.

1. Clone repositories

	```bash
	git clone git@github.com:mapbox/patrol
	git clone git@github.com:mapbox/cfn-config
	git clone git@github.com:mapbox/streambot
	```

2. Install `cfn-config`

	```bash
	cd cfn-config
	npm install && npm link
	```

3. Create an S3 bucket for `cfn-config` to store template configurations (for example: `cfn-configs`)
4. Create `~/.cfnrc`

	```javascript
	{
		"bucket": "cfn-configs"
	}
	```

1. [Create a `streambot` stack](https://github.com/mapbox/streambot/blob/master/readme.md)
6. Include desired rulesets in Crownest `patrol/package.json`

	```javascript
	"dependencies": {
		"patrol-rules-aws": "0.0.1",
		"ADDITIONAL-RULESET": "VERSION",
		"lambda-cfn": "0.0.5"
	  },
	```

1. Install Patrol dependencies

	```bash
	cd patrol
	npm install
	```

6. Modify `cloudformation/patrol-template.js` to select rules to deploy

	```javascript
	var lambdaCfn = require('lambda-cfn');
	module.exports = lambdaCfn(
	  [
		'node_modules/patrol-rules-aws/rules/assumeRole.js',
		'node_modules/patrol-rules-aws/rules/blacklistedResources.js',
		'node_modules/patrol-rules-aws/rules/cloudfrontModifyDelete.js',
		'node_modules/patrol-rules-aws/rules/cloudTrail.js',
		'node_modules/patrol-rules-aws/rules/whitelistedIAMActions.js'
		'node_modules/ADDITIONAL-RULESET/rules/ADDITION-RULE.js'
	  ],
	  {
		"AWSTemplateFormatVersion": "2010-09-09",
		"Description": "patrol"
	  }
	);
	```

1. Run tests

	```bash
	npm test
	```

3. Zip up Patrol for deployment to AWS Lambda: http://docs.aws.amazon.com/lambda/latest/dg/nodejs-create-deployment-pkg.html.
4. Copy the zip up to S3. By default, the Patrol template asks for the parameters for an S3 bucket, a prefix, and a gitsha. The default location of the zip for the Patrol lambda is then: `BUCKET/PREFIX/GITSHA.zip`
2. Create the stack with `cfn-config`. This step will create everything but the CloudWatch Event Rules.

	```bash
	cfn-create -t cloudformation/patrol-template.js -n YOUR-STACK-NAME -r us-east-1
	```

3. Create the CloudWatch Event Rules

	```bash
	lambda-cfn-rules YOUR-STACK-NAME
	```