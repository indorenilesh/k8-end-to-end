# Setup K8 cluster

## I am using AWS cloud and ubuntu ami for servers.

### Create basic networking like vpc, subnet, internet gateway, nat gateway, route tables, security groups.

Use below cloudformation template file to create above resources

    cf-vpc-3pub-3pri.yml

### Create 3 ec2 instances and use ubuntu ami.

Use below cloudformation template file to create 3 ec2 instances.

    cf-3-ec2.yml
