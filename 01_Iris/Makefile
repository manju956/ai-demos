train:
	python train.py

prepare:
	mkdir -p model_repository/iris/1
	cp model.onnx model_repository/iris/1
	cp config.pbtxt model_repository/iris

run: train prepare
	podman run --rm -p8000:8000 -p8001:8001 -p8002:8002 -v ~/model_repository:/models na.artifactory.swg-devops.com/sys-linux-power-team-ftp3distro-docker-images-docker-local/tritonserver:latest tritonserver --model-repository=/models

clean:
	rm -rf model_repository
	rm -rf model.onnx