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
    aws s3 cp file.txt s3://DOC-EXAMPLE-BUCKET --acl bucket-owner-full-control


    curl --upload-file {file} '{presigned-url}'   # can only generate through api
    curl '{presigned-url}'     # get object contents


    aws iam list-policies                   # list all policies
    aws iam list-policies --scope Local     # list customer managed policies
    aws iam get-policy --policy-arn {arn}                    # to get the version number
    aws iam get-policy-version --policy-arn {arn} --version-id v{n}  # the policy itself

    aws iam list-attached-user-policies     # list inline policies for user
    aws iam list-user-policies              # list managed policies for user
    aws iam get-user-policy                 # get inline policies for user

    aws iam get-role --role-name blah
    aws iam list-role-policies --role-name blah
    aws iam list-attached-role-policies --role-name blah   # managed policies attached to role
    aws iam get-role-policy --role-name {role} --policy-name {policy}   # inline policies


    aws sts get-caller-identity
    aws sts assume-role --role-arn "arn:aws:iam::123456789012:role/example-role" --role-session-name AWSCLI-Session

    export AWS_DEFAULT_REGION=us-east-1
    aws acm list-certificates
    aws acm get-certificate --certificate-arn blah        # show cert text
    aws acm describe-certificate --certificate-arn blah   # show meta data (e.g. DNS validation records)
    aws cloudwatch set-alarm-state --alarm-name {alarm-name} --state-value "OK" --state-reason "test"

    # list VPCs with Name (tag) and CIDR
    aws ec2 describe-vpcs --output table --query '
        Vpcs[].[Tags[?Key==`Name`]
        | [0].Value, CidrBlock, VpcId]
      '

    # subnets sort by VpcId
    aws ec2 describe-subnets --output table --query '
        Subnets[].[
          VpcId,
          AvailabilityZone,
          Tags[?Key==`Name`] | [0].Value,
          CidrBlock
        ] | sort_by(@, &[0])
      '

    # subnets sort by VpcId then AZ
    aws ec2 describe-subnets --output table --query '
        Subnets[].[
          join(`:`, [VpcId, AvailabilityZone]),
          Tags[?Key==`Name`] | [0].Value,
          CidrBlock
        ] | sort_by(@, &[0])
      '

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

aws ssm put-parameter --name /blah/de/blah \   # put multiline value from file
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

## ACM Feature / Bug

### Question

I'm facing the following error when trying to create an SSL certificate:

    * aws_acm_certificate.site_ssl_acm_certificate: Error requesting certificate:
    LimitExceededException: Error: you have reached your limit of 10 certificates
    in the last year.  status code: 400, request id: 91598338-b2c3-11e9-8144-231fc018067a

This is despite the fact that I only have 2 certificates:

    $ aws acm list-certificates --region=us-east-1
    {
        "CertificateSummaryList": [
            {
                "CertificateArn": "arn:aws:acm:us-east-1:123431574831:certificate/blahblah-9b02-4ef7-a474-31223e90bd51",
                "DomainName": "preview.dev.xyz.com "
            },
            {
                "CertificateArn": "arn:aws:acm:us-east-1:123431574831:certificate/blahblah-d138-4905-8728-e826de8c3936",
                "DomainName": "dev.xyz.com "
            }
        ]
    }

### Answer

> The reason you were unable to create new certificates is because of a low or
> non-existent 'containment score' in the us-east-1 region of the account 123431574831. What does this actually mean? Containment score is an anti-fraud
> mechanism and when you are using a new region that has no prior EC2 instances
> in it, you may run into this problem. You can avoid it by launching a small EC2
> instance in the specific region - it should give you a containment score within
> a few minutes, but it may take up to 4 hours on rare occasions. After that, you
> should be good to go and you are free to delete the instance.
>
> Our fraud team is definitely aware this creates a pain point for legitimate
> customers like you and they are actively looking into a better long-term
> solution. I had someone from the billing team look into your organization's
> accounts and give you a containment score in all regions through all accounts,
> meaning you should not run into this problem with your existing accounts. Newly
> created accounts, however, may have to do the EC2 strategy to get a containment
> score up and running.

## Lambda

```python
xyzhandler(event, context, callback)
  callback(error, response)  # call this from a sync handler (not async)
                             # set error to null to return a response
                             # response can be anything which can be stringified
```

## EventBridge

- rules
- event bus
- event bridge scheduler
  - can specify start and end time window for rate & cron events
  - many overlaps with eventbridge rules
  - one time events
    - schedule event at a particular time
    - give payload
  - flexible time windows
    - trigger events for multiple targets over a period of time
  - automatic retries
  - dead letter queues
- can call API directly
