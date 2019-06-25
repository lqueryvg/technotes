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

List of Regions with names: https://docs.aws.amazon.com/general/latest/gr/rande.html

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

    export AWS_DEFAULT_REGION=us-east-1
    aws acm list-certificates
    aws acm get-certificate --certificate-arn blah        # show cert text
    aws acm describe-certificate --certificate-arn blah   # show meta data (e.g. DNS validation records)

## IAM

### CARPE

- Condition: when it can happen
- Action: what can be done
- Resource: which resource can happen to
- Principal: who can do it
- Effect: deny / allow

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

## ssm

```bash
aws ssm get-parameters-by-path --recursive --path  /blah/deblah # outputs json

aws ssm get-parameters-by-path --recursive --path  /blah --query  # list param names \
    'Parameters[*].[Name]' --output text


aws ssm get-parameters-by-path --recursive --path  /blah --query \ # output "name value" pairs as space separated text
    'Parameters[*].[Name, Value]' --output text

aws ssm get-parameters --names /blah/api_key  \
  --with-decryption                           \ # decrypt it
  --query 'Parameters[0].Value'               \ # get value only
  --output text                               \ # no quotes

aws ssm put-parameter --name /blah/de/blah \   # put multiline value from string
  --value file://~/Downloads/domain.key
```

## cloudfront

```
aws cloudfront create-invalidation --distribution-id $distid --paths /index.html 
aws cloudfront list-invalidations --distribution-id $distid
aws cloudfront get-invalidation --distribution-id $distid --id {blah}
distribution_id=$(aws cloudfront list-distributions \
  --query "DistributionList.Items[?Origins.Items[0].Id==\`${origin_id}\`] | [0].Id" \
  --output text)
```

## S3
If you want website bucket to redirect to another bucket, you have
to use the WEBSITE endpoint, not a REST endpoint (unfortunately the
CloudFront web UI offers a REST endpoint by default).
```
REST ${bucket_name}.s3.amazonaws.com
REST ${bucket_name}.s3.${region}.amazonaws.com
WEBSITE ${bucket_name}.s3-website.${region}.amazonaws.com
```
