1) Create an IAM User
  Dont access AWS through the credentials of your account: use IAM instead

  a) Create an IAM user

  b) Add the user to an IAM group and assign the IAM user to an IAM group with admin permissions.

  Once this is complete, you can access AWS using a special URL and creds for the IAM user.
    http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_SettingUp.html

    Login: https://your_account_alias.signin.aws.amazon.com/console/

2) Configure the user-defined VPC (this is recommended over the default VPC)
    a) create a VPC security group that authorizes connections from the application/service to the AWS RDS instance

    b) The VPC must have AT LEAST 2 subnets, each in a separate AZ.

    c) Must specify a DB subnet group that defines which subnets in that VPC can be used by the DB instance.

  Currently here and at "Do you need failover support?" at http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_SettingUp.html
