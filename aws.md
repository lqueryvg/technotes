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


    # Select profile
    aws --profile {profile-name} ....

## Misc
    aws lambda list-functions

    aws s3api create-bucket --bucket fred

    aws aws s3 ls aws s3api get-bucket-website --bucket {bucket}
    aws s3api get-bucket-policy --bucket {bucket}
    aws iam list-policies

