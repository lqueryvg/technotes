# CLI

## Configure

    pip install awscli --upgrade --user
    export PATH=$PATH:~/.local/bin

    aws configure   # sets "default" profile config

    ~/.aws/
        config
            [profile-name]
            region = eu-central-1
            output = json

        credentials    # add stanzas for different accounts
            [profile-name]
            aws_access_key_id = blah
            aws_secret_access_key = blah

    # Select profile per command
    aws --profile {profile-name} ....

## Misc

    aws ec2 describe-regions --output table
    aws ec2 describe-availability-zones --region {region}

    aws lambda list-functions

    aws s3api create-bucket --bucket fred

    aws s3 ls aws s3api get-bucket-website --bucket {bucket}
    aws s3 ls s3://{bucket}/dir1/dir2/ --recursive
    aws s3 ls {bucket}/dir1/dir2/ --recursive
    aws s3api get-bucket-policy --bucket {bucket}
    aws s3 cp s3://{bucket}/file.txt -

    curl --upload-file {file} '{presigned-url}'   # can only generate through api
    curl '{presigned-url}'     # get object contents


    aws iam list-policies         # list customer managed policies

    aws iam list-attached-user-policies     # list inline policies for user
    aws iam list-user-policies              # list managed policies for user
    aws iam get-user-policy                 # get inline policies for user

    aws sts get-caller-identity

## IAM

### CARPE
- Condition: when it can happen
- Action:    what can be done
- Resource:  which resource can happen to
- Principal: who can do it
- Effect:    deny / allow

- principal (aka user)
- policy
  - grants permissions (aka authorisations)
  - types:
    - managed
      - AWS managed
      - customer managed
    - inline
      - embedded into user, group or role
  - types:
    - identity-based; attach to user or group
      - properties
        - effect; allow / deny
        - action; actions which can be performed
        - resource: on which action can be performed
          - arn:aws:SERVICE:REGION:ACCOUNT:THING
    - resource-based
      - attached to resource
        - what actions a principal can perform on a resource
        - conditions
        - inline policies (NOT managed)
      - cannot attach a resource-based policy to a user
    - trust
      - attached to a role
      - defines what principals can assume the role
- role
  - must have the following policies
    - trust policy; who can assume the role
    - permissions; what they can do with that role

## Useful links

- https://iam.cloudonaut.io
