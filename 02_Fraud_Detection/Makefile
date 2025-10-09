WORKING_DIR := $(shell pwd)
MODEL_DIR := ${WORKING_DIR}/model_repository/fraud/1
PYTHON_VERSION := 3.12
train:
	python${PYTHON_VERSION} train.py

prepare:
	mkdir -p ${MODEL_DIR}
	cp model.onnx ${MODEL_DIR}
run:
	podman run --rm -itd -p8000:8000 -p8001:8001 -p8002:8002 -v ${WORKING_DIR}/model_repository:/models:Z quay.io/powercloud/tritonserver:latest tritonserver --model-repository=/models

generate-config: prepare
	podman run -itd --rm --name triton -p8000:8000 -p8001:8001 -p8002:8002 -v ${WORKING_DIR}/model_repository:/models:Z \
                quay.io/powercloud/tritonserver:latest tritonserver --model-repository=/models --strict-model-config=false
	@echo "Waiting for Triton Server to become ready..."
	sleep 5
	@echo "Downloading config.pbtxt file from triton.."
	# download config.pbtxt file
	curl http://localhost:8000/v2/models/fraud/config | jq '.' | tee config.json
	# stop triton server
	podman stop triton
	# Convert model config json file to protobuf file
	bash json_to_proto.sh
	cp config.pbtxt ${WORKING_DIR}/model_repository/fraud/
	# Cleanup unwanted downlaoded files
	rm -f ${WORKING_DIR}/model_config.proto config.json model_config_pb2.py

clean:
	rm -rf model_repository
	rm -rf model.onnx config.pbtxt
