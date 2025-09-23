WORKING_DIR := $(shell pwd)
train:
        python train.py

prepare:
        mkdir -p ${WORKING_DIR}/model_repository/fraud/1
        cp model.onnx ${WORKING_DIR}/model_repository/fraud/1

run:
        podman run --rm -itd -p8000:8000 -p8001:8001 -p8002:8002 -v ${WORKING_DIR}/model_repository:/models:Z quay.io/powercloud/tritonserver:latest tritonserver --model-repository=/models

generate-config: prepare
        podman run -itd --rm --name triton -p8000:8000 -p8001:8001 -p8002:8002 -v ${WORKING_DIR}/model_repository:/models:Z \
                quay.io/powercloud/tritonserver:latest tritonserver --model-repository=/models --strict-model-config=false
        @echo "Waiting for Triton Server to become ready..."
        sleep 5
        @echo "Downloading config.pbtxt file from triton.."
        # download config.pbtxt file
        curl http://localhost:8000/v2/models/fraud/config | jq '.' | tee ${WORKING_DIR}/model_repository/fraud/config.pbtxt
        # stop triton server
        podman stop triton
clean:
        rm -rf model_repository
        rm -rf model.onnx
