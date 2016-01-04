

DE = Dev Exam

Need to revisit for exam: Active Directory Integration lecture for all courses

Need to revisit for my own learning
    Do this after learning about IAM
        http://docs.aws.amazon.com/es_es/AWSEC2/latest/UserGuide/iam-policies-for-amazon-ec2.html (and the nested documents)

        http://docs.aws.amazon.com/es_es/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html

IAM
    Synopsis
        Allows you to manage users and their level of access to the AWS Console

        Allows centralized control of an AWS account

        Integrates with existing activate directory accounts allowing single sign on (DE)

        Allows multifactor authentication
            When signing in, you provide 2 means of verifying yourself
                Ex: So in addition to supplying a username/pass, you have to supply something else

        Gives temporary access for users/devices and services where necessary
            Ex: Logging in to your application with Facebook, etc.

        You can set up your own password rotation policy


    Strategies
        Outside access
            Create 2 security groups.  1 for public access/1 for private.

        Don't give people the creds for the main AWS account.
          It is recommended that you don't even access the main AWS account.  In this case:
            Setup an an adminstrator group and add the user to the group.  Then, the user can access AWS using a special URL and the creds for the IAM user.

    Policy Structure

        {
          "Statement":[{
            "Effect":"effect",
            "Action":"action",
            "Resource":"arn",
            "Condition":{
              "condition":{
                "key":"value"
                }
              }
            }
          ]
        }

        Values in Key-Value pairs
            effect
                Values: Allow/Deny
                    Default
                        IAM users don't have permission to use resources and API actions.

                    An explicit deny overrides any allows
            resource
                The resource that's affected by the action.

                Need to utilize the Amazon Resource Name (arn)
                    E: ARN = Amazon Resource Name

                If the API action doesn't support ARNs, use * to specify that all resources can be affected by the action

            condition
                These are optional and can be used to control when your policy will be in effect.







    To initially set up everything in the main IAM console, you need to make sure that all of the security "checkmarks" are finished for the Security Status dialog

    Allows you to manage users and their level of access to the AWS Console
        Granular level of control: Can set access via User/Group/Roles

            User: End users (people)

            Group: A collection of users under one set of permissions
                Ex: We set up a Group Called developers and give them a policy name of "power user access", which is like a permissions template.

                Ex:  We can also say this set of users has access to this s3 bucket, and another has access to a different s3 bucket.
                

                    We then create a user and assign them the group "developers".  
            Roles: Similar to a group, but you can assign both users and AWS resources (like EC2).  This allows you to centralize your account management so you don't store credentials on EC2, etc.  (This is a best practice)
                Ex: Say you want to access an S3 bucket from Ec2.
                    You can either store the s3 creds locally on the EC2 (not recommended) and then connect to s3 via the API, etc.

                    Or, you can assign your EC2 instance a role that has a policy tha allows for connecting to S3.

                Policys
                    Include a Policy Document
                        A JSON document that sets up to the policy.



                The Role ARN is the role's unique name in aws

                Different types of Roles
                    Service Roles

                    Cross-Account Access (accessing via other AWS accounts)

                    Identity Provider Access (logging in with facebook,etc)
                        Also see Web Identity Federation

                Ex: For example we can give our EC2 instances an "Amazon EC2" role which allows EC2 Instances to call AWS services on our behalf.
                    Then for a given role, we can attach up to 2 policies.  We will attach "AmazonS3FullAccess" as a policy.
                Now, any Ec2 instance that we utilize we can assign it the aforementioned role that we created

                Exam: You can't change the IAM role on a running EC2 instance.  You also can't associate an IAM role with an already running instance.  You can only one IAM role with an ec2 instance.


                oles can't be assigned to an EC2 instance after that instance has been created. You cannot change them either.  Although, you can change the permissions of a role that has already been assigned to an Ec2 instance.

                Roles and their policy templates
                    You can also utilize the policy templates with Groups
                    Policy template examples
                        Admin: Full access to AWS services and resources

                        Power User Access: Admin but no user/group management

                        Read Only

                        There are others :D

                        You can only assign 2 policys to a role
                        * We don't have to use a template

    Don't sign in with the root account for main activity

    When you create a user, it will only give you the Access Key ID (username) and Secret Access Key (password) once
        This pair will allow for access to the AWS CLI, API calls to the AWS portal, and using SDKs, etc.

        If someone looses their key, you don't have to delete/recreate the user (although you can)
            Simply select the user in the IAM console, User Actions tab, Manage Access Keys.

            You can delete their existing key and then create a new one

    To give access to the AWS console, you create that in IAM as well
        Select the user, User Actions tab, Manage Password


    Scenario:
        Through AWS CLI, access your S3 bucket while authenticating through IAM.  In other words, don't store your key pair on EC2.
            Create an s3 role in IAM
            Launch an EC2 and select that role
            ssh into box with key/pair in EC2 setup

    When setting up an account for the first time
        In the IAM console you'll see various items that you need to complete.  The following is notes on those items
            Steps
                Activate MFA on your root account
                    Virtual MFA device
                        To use this, you must first install an AWS MFA-compatible application on the user's smartphone, PC, or other device.

                    Hardware MFA device

    Web Identity Federation
        Logging in via LinkedIn, amazon.com, etc.

        To see how to set it up, Google this string:
            Web Identity Federation with Mobile Apps

        Also useful to Google:
            "AWS Web Identity Federation Playground"
            This will help explain the "how it works" section below.

        How does this work?
            We verify our identity with Facebook

            Facebook gives us an access token

            We exchange that access token for a temporary security credentials on AWS by making a AssumeRoleWithWebIdentity request.

            We make the request and get
                Secret Access Key
                Access Key ID
                Session Token

            We'll have the permissions that were set up in the access policy 
                Ex: If you're given s3 list bucket privs, you can get an XML document back that lists the documents in the bucket


    Exam Tips
        SAML
            Secure Assertive Markup Language
                What is used if you're trying to authenticate with Active Directory from AWS

        You always Authenticate against the identity provider first (e.g. facebook/active directory) BEFORE you can access a given AWS resource.
            Once authenticated, you obtain your temporary security credentials.
                With these creds, you make an API call that includes "AssumeRoleWithWebIdentity"




         
