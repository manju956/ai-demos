WORKING_DIR := $(shell pwd)
train:
	python train.py

prepare:
	mkdir -p ${WORKING_DIR}/model_repository/fraud/1
	cp model.onnx ${WORKING_DIR}/model_repository/fraud/1
	cp config.pbtxt ${WORKING_DIR}/model_repository/fraud

run: train prepare
	podman run --rm -itd -p8000:8000 -p8001:8001 -p8002:8002 -v ${WORKING_DIR}/model_repository:/models:Z na.artifactory.swg-devops.com/sys-linux-power-team-ftp3distro-docker-images-docker-local/tritonserver:latest tritonserver --model-repository=/models

clean:
	rm -rf model_repository
	rm -rf model.onnx
