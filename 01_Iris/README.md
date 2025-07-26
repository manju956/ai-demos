# Iris Classification with ONNX Runtime via Triton Inference Server

This guide explains how to run an Iris classification model using ONNX Runtime served via the Triton Inference Server. The Iris dataset is a classic dataset in machine learning, and this example demonstrates how to deploy a trained model for inference using modern serving infrastructure.

## Prerequisites

### Install the prerequirements:

```shell
pip install -r requirements.txt
```

### Train/Generate the model:

```
python train.py
```

> Note: This will generate a model by name model.onnx and save it in the current directory.

## Running the example

To run this example, you can use the following command:

```
make run
```

## Run the example via Openshift AI

### Prerequisite:

- Create the [storage connectivity](https://docs.redhat.com/en/documentation/red_hat_openshift_ai_self-managed/2.22/html/openshift_ai_tutorial_-_fraud_detection_example/setting-up-a-project-and-storage#creating-connections-to-storage) in the RHOAI project
- Make sure you Train and generate the model and copy to some s3 bucket

#### Flow I - UI
Create a Triton ServingRuntime template
```
oc apply -f deploy/template.yaml
```

Refer example [guide](https://docs.redhat.com/en/documentation/red_hat_openshift_ai_self-managed/2.22/html/openshift_ai_tutorial_-_fraud_detection_example/deploying-and-testing-a-model) to understand how to deploy a model.

#### Flow II - CLI
Deploy the serving runtime:
```
oc apply -f deploy/runtime.yaml
```


Deploy the InferenceService

```
oc apply -f deploy/isvc.yaml
```

