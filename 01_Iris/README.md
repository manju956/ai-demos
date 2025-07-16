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
