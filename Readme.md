[![Build Status](https://travis-ci.com/dentalwings/aws-cloudformation-vpc-setup.svg?branch=master)](https://travis-ci.com/dentalwings/aws-cloudformation-vpc-setup)

# AWS Cloudformation VPC Setup

These are Cloudformation templates to create an VPC Infrastructure with:
* private and public subnets
* optional bastion host
* IPv6 support
* exported to S3 to have it directly usable:<br/>[![Launch Stack](assets/launch-stack.png)](https://console.aws.amazon.com/cloudformation/home#/stacks/new?stackName=infrastructure&templateURL=https%3A%2F%2Fs3.ca-central-1.amazonaws.com%2Fdentalwings-cloudformation-templates%2Faws-cloudformation-vpc-setup%2Fcloudformation_vpc_creation.yml)

via CLI:

    aws cloudformation create-stack \
      --capabilities CAPABILITY_IAM \
      --stack-name infrastructure \
      --template-url https://s3.ca-central-1.amazonaws.com/dentalwings-cloudformation-templates/aws-cloudformation-vpc-setup/cloudformation_vpc_creation.yml

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
| `${AWS::StackName}-PrivateSubnets` | Comma seperated list of all created private subnets |
| `${AWS::StackName}-PublicSubnets` | Comma seperated list of all created public subnets |

### extending the VPC with more PublicSubnets

This template create subnets using the `Fn::CIDR` intrinsic function to get the CIDRs for the subnets, e.g.:

    PublicCIDR: !Select [11, !Cidr [!Ref VpcCIDR, 256, 8]]
    PrivateCIDR: !Select [21, !Cidr [!Ref VpcCIDR, 256, 8]]
    PublicIpv6CIDR: !Select [11, !Cidr [!Select [0, !GetAtt 'VPC.Ipv6CidrBlocks'], 256, 64]]
    PrivateIpv6CIDR: !Select [21, !Cidr [!Select [0, !GetAtt 'VPC.Ipv6CidrBlocks'], 256, 64]]

If you want to create additional subnets (e.g. for ElasticCacheSubnetGroups or RDSSubnetGroups) you can import the CIDRs with:

    Fn::ImportValue:
      !Sub "${NetworkStackName}-PublicCIDR"

to get an SubnetCIDR select a diferent index than 11-16 and 21-26:

    !Select
      - 100
      - !Cidr
          - Fn::ImportValue:
              !Sub "${NetworkStackName}-PublicIpv6CIDR"
          - 256
          - 64
