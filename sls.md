
# Serverless

    export AWS_PROFILE=blah
    export AWS_SDK_LOAD_CONFIG=1

    yarn exec -- sls ...


    deploy list functions --stage prod
    logs -f fred  --stage prod
    invoke -f fred -log --stage prod

    deploy                          Deploy a Serverless service
    deploy function                 Deploy a single function from the service
    info                            Display information about the service
    invoke                          Invoke a deployed function
    invoke local                    Invoke function locally
    logs                            Output the logs of a deployed function
    help
    deploy list                     List deployed version of your Serverless Service
    deploy list functions           List all the deployed functions and their versions
    metrics                         Show metrics for a specific function
    remove                          Remove Serverless service and all resources
    package                         Packages a Serverless service
    print                           Print your compiled and resolved config file
    config                          Configure Serverless
    config credentials              Configures a new provider profile for the Serverless Framework
    generate-event -t aws:sqs -b '{"foo": "bar"}'
      # useful types are s3, sns, sqs, dynamo
      # body is only applicable to some types
      # not all types are supported

    sls invoke --function helloWorld
    yarn exec -- sls invoke  -f {function} \
      -d '{ "Records": [ { "s3": { "object": { "key": "some/key" },
                                   "bucket": { "name": "some-bucket" } } } ] }'

    rollback                        Rollback the Serverless service to a specific deployment
    rollback function               Rollback the function to the previous version
    create                          # Create new Serverless service
    test                            Run HTTP tests
