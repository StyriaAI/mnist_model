# MNIST model

This repository contains all the resources and instructions needed to set up a Tensorflow Model Server serving MNIST classifier model.

# 1. Model

MNIST classifier model can be used to classify images of handwritten digits into their numeric values. Model is available as a resource in this repository under `resources/mnist/1`. This path is structured in a way expected by Tensorflow Model Server, having the following parts: `resources/<model_name>/<model_version>`.

# 2. Tensorflow Model Server

Tensorflow Model Server is available pre-built in an official docker image `tensorflow/serving` available on docker hub: https://hub.docker.com/r/tensorflow/serving/

For the purpose of this demo we are using tensorflow serving version 1.8.

## Running with command line arguments

Position into the root of this repo and run the following command:

```
docker run tensorflow/serving:1.8.0 -p 8500:8500 --model_base_path=resources/mnist --model_name=mnist
```

Model server is now available on port 8500.

## Running with environment variables

Position into the root of this repo and run the following command:

```
docker run -e MODEL_BASE_PATH=resources/mnist -e MODEL_NAME=mnist -p 8500:8500 tensorflow/serving:1.8.0
```

## Integration with AWS S3

Loading models for the local filesystem is a great way to test and develop, but it is somewhat unpractical in production. For that purpose, Tensorflow Model Server can be integrated with AWS S3 in such a way that models are kept in an S3 bucket. In order to use S3, we need to set additional environment variables:

| env variable name | description | 
|-------------------|-------------|
| `S3_REGION`       | Region of the S3 bucket, e.g. `eu-west-1` |
| `AWS_ACCESS_KEY_ID` | AWS access key id to use for S3 authentication |
| `AWS_SECRET_ACCESS_KEY` | AWS secret access key to use for S3 authentication |
| `MODEL_BASE_PATH` | S3 prefix to check for models in form `s3://<bucket_name>/<prefix>` |
| `MODEL_NAME` | Model name |

NOTE: When S3 integration is enabled, you might see a lot of wierd log messages described in the following github issue: https://github.com/tensorflow/serving/issues/789. Feel free to ignore those messages.

Once you have S3 bucket containing the model set up, you can run the model server with the following command:
```
docker run -e AWS_SECRET_ACCESS_KEY=<secret_access_key> -e AWS_ACCESS_KEY_ID=<access_key_id> -e S3_REGION=<aws_region> -e MODEL_BASE_PATH=s3://<bucket_name>/<prefix> -e MODEL_NAME=mnist -p 8500:8500 tensorflow/serving:1.8.0
```
