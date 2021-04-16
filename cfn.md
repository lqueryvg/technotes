# Commands
```
alias cfn="aws cloudformation"
cfn validate-template --template-body file://MyStack.json
cfn create-stack --template-body file://example-stack.json --stack-name example-stack
cfn describe-stack-events --stack-name example-stack
cfn describe-stack-resources --stack-name example-stack
cfn update-stack --template-body file://example-stack.json --stack-name example-stack \
  --parameters \   # optional
     ParameterKey=KeyPairName1,ParameterValue=SampleKeyPair1 \
     ParameterKey=KeyPairName2,ParameterValue=SampleKeyPair2
aws ec2 describe-tags --filter "resource-type=instance" --filter "resource-id=i-5689cc1d"
cfn-get-template  # get template from running instance
cfn delete-stack --stack-name example-stack
```

# Conditionals
```
!If [condition_name, value_if_true, value_if_false]
```
Use `AWS::NoValue` for null.
```
  Properties: 
    Size: 
      !If [CreateLargeSize, 100, 10]
    DBSnapshotIdentifier:
      !If
      - UseDBSnapshot
      - Ref: DBSnapshotName
      - Ref: AWS::NoValue
```

# Misc Functions
```
Fn::FindInMap   -> !FindInMap
```
Example:
```
Mappings:
  RegionMap:
    eu-west-1:
      AMI: ami-bff32ccc
    ap-southeast-1:
      AMI: ami-c9b572aa
...etc

    Properties:
      ImageId: !FindInMap [RegionMap, !Ref 'AWS::Region', AMI]
```

# UserData Example


```
---
AWSTemplateFormatVersion: '2010-09-09'
Parameters:
AMI:
  Type: String
  Default:
  - ami-bc5b48d0
InstanceType:
  Default:
  - t2.micro
Resources:
  MyInstance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: !Ref AMI
      InstanceType: !Ref InstanceType
      SubnetId: !Ref Subnet
      Tags:
      - Key: Name
        Value: MyInstance
      UserData:
        'Fn::Base64': !Sub |
          #!/bin/bash -ex
          echo hello world
```

# Outputs Example
```
Outputs:
  InstanceId:
    Value: !Ref MyInstance
  PublicIPAddress:
    Value:
      !GetAtt MyInstance.PublicIp
```

# Nested Stacks
The following example demonstrates:
- child stack
- passing parent parameter to child stack
- using output from child in parent output
```
Parameters:
  ParentKey1:
    Type: String
    Default: some text
Resources:
  myEC2Stack:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: https://s3-eu-west-1.amazonaws.com/my-container/myStack.yaml
      Parameters:
        ChildKey1: !Ref ParentKey1
Outputs:
  myOutput:
    Value: !Sub ${myEC2Stack.Outputs.InstanceId}   # assuming this is an output of sub stack
```


# cfn-init 

Add `AWS::CloudFormation::Init` to EC2 instance resource meta data to
install packages, files or start services.

```
Resources:
  EC2Instance:
    Type: AWS::EC2::Instance
    Metadata:
      AWS::CloudFormation::Init:
        config:
          packages:
            yum:
              httpd: []
          files:
            "/var/www/html/index.html":
              content: "<html><body>Hello World!</body></html>"
          services:
            sysvinit:
              httpd:
                enabled: "true"
                ensureRunning: "true"
```

# Cross-Stack Exports
- names must be unique with region
- can't create cross-stack references across regions
- can't delete a stack if another stack references one of its outputs.
- can't modify or remove an output value that is referenced by another stack.
- can't import into params section
- ImportValue function and output Export Name property can't use Ref or GetAtt
  that depend on a resource; use !Sub "${AWS::StackName}-blag" instead


