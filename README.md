# AWS LAMBDA LOCALSTACK EXAMPLE

## Synopsis

The project is an AWS Lambda Function using Localstack.
It simple logs the input, context, function name, and function version.

## Motivation

I wanted to do a real Lambda Function running in my local machine without having an AWS account.

## Requirements

- You need Docker.
- You need to pull the <b>localstack</b> image. I did this using the docker-compose.yml:

***

<pre><code>

version: "3.8"

services:
  localstack:
    container_name: "${LOCALSTACK_DOCKER_NAME-rac_localstack_container}"
    image: localstack/localstack
    network_mode: bridge
    ports:
      - "53:53"
      - "53:53/udp"
      - "443:443"
      - "4566:4566"
      - "4571:4571"
      - "${PORT_WEB_UI-8080}:${PORT_WEB_UI-8080}"
    environment:
      - SERVICES=${SERVICES- }
      #- DEBUG=${DEBUG- } Esto no envia nada de logs a la consola
      - DEBUG=1
      - DATA_DIR=${DATA_DIR- }
      - PORT_WEB_UI=${PORT_WEB_UI- }
      - LAMBDA_EXECUTOR=${LAMBDA_EXECUTOR- }
      - LOCALSTACK_API_KEY=${LOCALSTACK_API_KEY- }
      - KINESIS_ERROR_PROBABILITY=${KINESIS_ERROR_PROBABILITY- }
      - DOCKER_HOST=unix:///var/run/docker.sock
      - HOST_TMP_FOLDER=${TMPDIR}
    volumes:
      - "${TMPDIR:-/tmp/localstack}:/tmp/localstack"
      - "/var/run/docker.sock:/var/run/docker.sock"
      - "./.localstack:/RAC/docker/localstack"

</code></pre>

***

- Use the following command to pull and run the Docker container with localstack

<pre><code>
     docker compose up
</code></pre>

- You need to download the AWS Command Line Interface, AWSCLI64.msi (for Windows for example), to execute the commands. [AWS Command Line Interface](https://aws.amazon.com/cli/)
  
- You need to be sure to have two files within <b>C:\Users\RAC\.aws\</b> (in Windows):
  - config
  - credentials 
  
- Within <b>config</b> file:
<pre><code>
    [default]
    output = json
    region = us-east-1
    aws_access_key_id = rac
    aws_secret_access_key = rac
</code></pre>

- Within <b>credentials</b> file:
<pre><code>
    [default]
    aws_access_key_id = rac
    aws_secret_access_key = rac
</code></pre>

- You can change the name <b>rac</b> for <b>test</b> instead or the name you want in both <b>config</b> and <b>credentials</b> files.

## Create the Lambda Function within LocalStack
aws lambda create-function --endpoint-url http://localhost:4566 --function-name awslambdalocalstack --runtime java8 --handler rafael.alcocer.caldera.aws.lambda.handler.AwsLambdaHandler --region us-east-1 --zip-file fileb://aws-lambda-localstack-example-0.0.1-SNAPSHOT.jar --role arn:aws:iam::12345:role/ignoreme

## To retrieve a list of Lambda functions
aws lambda list-functions --endpoint-url http://localhost:4566

## To Delete a Lambda Function
aws lambda delete-function --function-name awslambdalocalstack --endpoint-url http://localhost:4566

## License

All work is under Apache 2.0 license