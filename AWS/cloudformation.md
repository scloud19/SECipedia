CloudFormation Template
	Stack - A grouping of AWS resources that are created from the template
	Ex:
		"Resources" : {
		  ...    
		  "DBInstance" : {
		    "Type": "AWS::RDS::DBInstance",
		    "Properties": {
		      "DBName"            : { "Ref" : "DBName" },
		      "Engine"            : "MySQL",
		      "MasterUsername"    : { "Ref" : "DBUsername" },
		      "DBInstanceClass"   : { "Ref" : "DBClass" },
		      "DBSecurityGroups"  : [{ "Ref" : "DBSecurityGroup" }],
		      "AllocatedStorage"  : { "Ref" : "DBAllocatedStorage" },
		      "MasterUserPassword": { "Ref" : "DBPassword" }
		    }
		  },
		    
		  "DBSecurityGroup": {
		    "Type": "AWS::RDS::DBSecurityGroup",
		    "Properties": {
		      "DBSecurityGroupIngress": { "EC2SecurityGroupName": { "Ref": "WebServerSecurityGroup"} },
		      "GroupDescription"      : "Frontend Access"
		    }        
		  },
		  ...    
		},



	DBInstance is the resource declaration name, you can use this in other areas of the template

	The Parameters section is used to pass values into the template (think passwords, etc.)
		If the parameter section is expecting a certain value, you will be prompted for this in the "Specify Parameters" page when you are creating the stack.
	
	Mappings
		Use mappings to declare conditional values that are evaluated in a similar manner as a lookup table statement.
			Ex: Select the correct AMI for a given region

	Outputs
		After the stack has been created, output a given result and Outputs define custom values that are returned by the "aws cloudformation describe-stacks" command
			EX: URL of the given website, etc.
	
	Tags
		A Stack's metadata
			Key-value pairs that help identify the stacks.

When you delete a stack, all of its resources will be deleleted as well