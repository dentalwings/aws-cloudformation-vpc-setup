[![Build Status](https://travis-ci.com/dentalwings/aws-cloudformation-vpc-setup.svg?branch=master)](https://travis-ci.com/dentalwings/aws-cloudformation-vpc-setup)

# AWS Cloudformation VPC Setup

These are Cloudformation templates to create an VPC Infrastructure with:
* private and public subnets
* optional bastion host
* IPv6 support
* exported to S3 to have it directly usable:<br/>[![Launch Stack](assets/launch-stack.png)](https://console.aws.amazon.com/cloudformation/home#/stacks/new?stackName=infrastructure&templateURL=https%3A%2F%2Fs3.ca-central-1.amazonaws.com%2Fdentalwings-cloudformation-templates%2Faws-cloudformation-vpc-setup%2Fcloudformation_az_creation.yml)

## Parameters

| Variable | Default | Description |
| --- |  --- | --- |
| `Tags` |  `''` | A comma seperated list of key=value pairs |
| `VpcCIDR` | `10.0.0.0/16` | IP range (CIDR notation) for this VPC |
| `CreateBastionHost` | `false` | Should we setup bastion hosts? |
| `BastionHostInstanceType` | `t2.micro` | InstanceType of Bastion Host |
| `BastionHostAllowedIPRange` | `t2.micro` | IP Range (CIDR notation) which is allowed connect from |
| `BastionHostKeyName` | `None` | Name of an existing EC2 KeyPair for SSH access |

## Cloudformation Stack Exports

| Variable | Description |
| --- | --- |
| `${AWS::StackName}-VPCID` | A reference to the created VPC |
| `AZ[1-6]PrivateSubnet` | AZ[1-6] Private Subnet |
| `AZ[1-6]PublicSubnet` | AZ[1-6] Public Subnet |
