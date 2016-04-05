## Developing a ruleset for Patrol
Rulesets are kept as separate repositories from the underlying Patrol infrastructure code. If developing a public rule set it is recommended to prefix the repository name with `patrol-rules-` to aid in discoverability.

#### Guidelines
1. Rule namespace is _currently_ global across all ruleset. See https://github.com/mapbox/lambda-cfn/issues/6
2. [Rule specifications](https://github.com/mapbox/lambda-cfn/blob/master/rule-spec.md)
2. A rule repository typically has the following structure:

	```
	patrol-rules-XXX
			-> package.json
			-> rules
					-> RULE-A.js
					-> ...
			-> test
					-> rules
							-> RULE-A.test.js
							-> ...
	```

3. Rulesets require `lambda-cfn` which also provides mock SNS queues for testing. A typical ruleset `package.json`:

	```javascript
	{
	  "name": "patrol-rules-aws",
	  "version": "0.0.1",
	  "description": "XXX rules for Patrol",
	  "main": "index.js",
	  "engines": {
		"node": "0.10.38"
	  },
	  "repository": {
		"type": "git",
		"url": "XXX"
	  },
	  "scripts": {
		"test": "NODE_ENV=test tape test/*.test.js test/**/*.test.js"
	  },
	  "dependencies": {
		"lambda-cfn": "0.0.5"
	  },
	  "devDependencies": {
		"aws-sdk": "^2.2.35",
		"jscs": "^1.11.3",
		"jshint": "^2.6.3",
		"tape": "^3.5.0"
	  },
	  "jscsConfig": {
		"preset": "airbnb",
		"requireCamelCaseOrUpperCaseIdentifiers": null
	  },
	  "jshintConfig": {
		"node": true,
		"globalstrict": false,
		"undef": true,
		"unused": true,
		"noarg": true
	  },
	  "author": "XXX",
	  "license": "XXX",
	  "bugs": {
		"url": "XXX"
	  },
	  "homepage": "XXX"
	}
	```



