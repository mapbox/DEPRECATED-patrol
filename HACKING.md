#### Hacking the Crowsnest stack

Because `crowsnest` includes both the rulesets and `lambda-cfn` in its `package.json`, developing new functionality could require several roundtrips through Github. 

Instead, leverage `npm link` to link in the development dependency. For example, if working on `lambda-cfn`, the workflow would look something like:
- Once finished with modifications to `lambda-cfn` link it to `crowsnest`

	```bash
	cd lambda-cfn  #modified version
	npm link
	cd ../crowsnest
	npm link lambda-cfn #links in the modified version
	npm install #installs the remaining dependencies but skips lambda-cfn
	```

- To remove the development version (nodejs v0.10.x)

	```bash
	npm r lambda-cfn -g
	```