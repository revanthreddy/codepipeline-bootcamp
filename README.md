# Codepipeline Workshop

This project contains source code and supporting files to do a workshop on Codepipeline
The application uses several AWS resources, including Lambda functions and an API Gateway API. These resources are defined in the `template.yml` file in this project. You can update the template to add AWS resources through the same deployment process that updates your application code.


## Prerequisites

* AWS CLI
* default profile has Admin access to the AWS account
* IDE (Like Pycharm, VS Code)

## Run the required setup stack

The setup stack creates necessary IAM roles used by Cloudformation, CodeBuild Project and roles, S3 buckets used by Codepipeline during pipeline transitions

Run the below command to create a setup stack at the project root level
```bash
$ sh run-setup.sh
```

Below resources are created
* ArtifactBucket 					AWS::S3::Bucket
* CloudformationLambdaTrustRole 	AWS::IAM::Role	
* CodeBuild						    AWS::CodeBuild::Project
* CodeBuildRole 					AWS::IAM::Role
* SourceBucket  					AWS::S3::Bucket

From your cloudformation console, look at the outputs section of workshop-setup and note down the 
SourceBucket and ArtifactBucket

## Start the Codepipeline setup

Now that all the resources are in place (created by the setup stack), lets use them to create a Codepipeline that will deploy a lambda and an API Gateway in front of it


### Configure the pipeline settings and choose Next.

* Pipeline name – workshop-pipeline
* Service role – New service role
* Artifact store – Custom location and fill in the 'Bucket' field with <ArtifactBucket> (created in setup stack)

### Source provider – S3

* Bucket  – <SourceBucket>
* S3 object key – app.zip
* Change detection options – Amazon CloudWatch Events

### Configure build project settings and choose Continue to CodePipeline

* Project Name - workshop-build (created as part of the stack)

### Configure deploy stage settings and choose Next

* Deploy Provider - AWS Cloudformation
* Action mode - Create or update a stack
* Stack name - app-stack
* Template section 
    - Artifact Name - BuildArtifact
    - File name - package.yml
* Role name - <workshop-setup-CloudformationLambdaTrustRole-XXXXXXXXX

### Create Pipeline

* Review all the changes and click "Create Pipeline"


## Upload code to the SourceBucket (copied from setup stack output)
```bash
sh upload-code-to-s3.sh <SourceBucket>
```

## Cleanup

To delete the sample application and the bucket that you created, use the AWS CLI.

```bash
sh delete-setup.sh
```

## Resources

See the [AWS SAM developer guide](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html) for an introduction to SAM specification, the SAM CLI, and serverless application concepts.

Next, you can use AWS Serverless Application Repository to deploy ready to use Apps that go beyond hello world samples and learn how authors developed their applications: [AWS Serverless Application Repository main page](https://aws.amazon.com/serverless/serverlessrepo/)