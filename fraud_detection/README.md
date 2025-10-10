# Fraud detection Classification with ONNX Runtime via Triton Inference Server

This guide explains how to run an Fraud detection classification model using ONNX Runtime served via the Triton Inference Server. The Fraud detection dataset is a classic dataset in machine learning, and this example demonstrates how to deploy a trained model for inference using modern serving infrastructure.

## Train/Generate the model:
Build ONNX build_env container image from root directory of ai-demos repo.
```shell
cd ..
podman build . -t localhost/build_env
```

Run the container to train and generate the model using ONNX runtime. Execute the below command from root directory of ai-demos repo.
```shell
mkdir -p $(pwd)/model_repository
podman run --rm  --name fraud_detection -v $(pwd)/fraud_detection:/app:Z -v $(pwd)/Makefile:/app/Makefile:Z -v $(pwd)/model_repository:/app/model_repository:Z \
    --entrypoint="/bin/sh" localhost/build_env -c "cd /app && make train APP=fraud_detection"
```

> Note: This will persist the generated model file in the path `<current_dir>/../model_repository/fraud_detection/1/model.onnx`

Generate Model configuration file for fraud_detection application dynamically
```shell
make generate-config APP=fraud_detection
```

< Note: This will persist the generated model config file in the path `<current_dir>/../model_repository/fraud_detection/config.pbtxt`


## Running the triton server with fraud detection example

Use the model file generated in previus step to be served from triton server by mounting **model_repository** directory

```shell
make run
```

After successful execution of above commands, triton inference server will run inside container on HTTP port 8000

### Testing fraud detection example against Triton inference server
Check the models loaded on the inference server

```shell
curl -X POST  http://0.0.0.0:8000/v2/repository/index
```

You can expect below response as an output
```json
[{"name":"fraud","version":"1","state":"READY"}]
```

Inference the model with the fraudulent data
```shell
curl -X POST  http://0.0.0.0:8000/v2/models/fraud_detection/infer   -H "Content-Type: application/json"   -H "Accept: application/json" -d @sample-fraud.json
```

Sample output
```json
{
  "model_name":"fraud",
  "model_version":"1",
  "outputs":[
  {
    "name":"label",
    "datatype":"INT64",
    "shape":[1,1],
    "data":[1]
  },
  {
    "name":"probabilities",
    "datatype":"FP32",
    "shape":[1,2],
    "data":[4.172325134277344e-7,0.9999995827674866]
  }]
}
```

## Run the example via Openshift AI

### Prerequisite:

- Create the [storage connectivity](https://docs.redhat.com/en/documentation/red_hat_openshift_ai_self-managed/2.22/html/openshift_ai_tutorial_-_fraud_detection_example/setting-up-a-project-and-storage#creating-connections-to-storage) in the RHOAI project
- Make sure you Train and generate the model and copy to some s3 bucket

### Deploy the model:
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

### Inference the model:

Get the route from the `oc get route` command and use it to make inference requests.

In this example route is:

```
fraud-mkumatag.apps.abhinav-rabari-21.ibm.com
```

List the models:

```
curl -X POST -k https://fraud-mkumatag.apps.abhinav-rabari-21.ibm.com/v2/repository/index
```

Inference the model with the data
```
curl -X POST -k https://fraud-mkumatag.apps.abhinav-rabari-21.ibm.com/v2/models/fraud/infer   -H "Content-Type: application/json"   -H "Accept: application/json" -d @sample-fraud.json
```

Sample formatted output:
```json
{
  "model_name": "fraud",
  "model_version": "1",
  "outputs": [
    {
      "name": "label",
      "datatype": "INT64",
      "shape": [
        1,
        1
      ],
      "data": [
        1
      ]
    },
    {
      "name": "probabilities",
      "datatype": "FP32",
      "shape": [
        1,
        2
      ],
      "data": [
        0,
        1
      ]
    }
  ]
}
```
