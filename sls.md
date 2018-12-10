# Serverless

https://github.com/creationix/nvm

    nvm install lts/carbon
    nvm list

    export AWS_PROFILE=blah
    export AWS_SDK_LOAD_CONFIG=1
    sls deploy
    sls remove
    sls deploy list
    sls deploy list functions
    sls invoke --function helloWorld



    npx sls invoke  -f {function} \
      -d '{ "Records": [ { "s3": { "object": { "key": "some/key" },
                                   "bucket": { "name": "some-bucket" } } } ] }'
