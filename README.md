# Morpheus Demo for Oracle CloudWorld

### Prerequisite:

1. X Window environment
1. Docker and Docker Compose
1. NVIDIA driver and container toolkit
   
### Setup Morpheus
```
sudo apt install git-lfs
git lfs install
git clone https://github.com/nv-morpheus/Morpheus.git

export MORPHEUS_ROOT=$(pwd)/Morpheus
cd $MORPHEUS_ROOT
git submodule update --init --recursive
scripts/fetch_data.py fetch all
```

### SID Visualization
```
export DOCKER_IMAGE_TAG="sid-viz"
./docker/build_container_dev.sh
cd ${MORPHEUS_ROOT}/examples/sid_visualization
sed -i "/runtime: nvidia/d" docker-compose.yml
xhost +
DOCKER_BUILDKIT=1 docker compose build 

docker compose up
```

should see the GUI window now

IMG

```
cd ${MORPHEUS_ROOT}/examples/sid_visualization
docker compose exec morpheus bash

# run in the morpheus container. 
BUILD_DIR=build-docker ./scripts/compile.sh
pip install -e .

python examples/sid_visualization/run.py \
  --debug --use_cpp=False --num_threads=1 \
  --triton_server_url=triton:8001 \
  --input_file=./examples/data/sid_visualization/group1-benign-2nodes.jsonlines \
  --input_file=./examples/data/sid_visualization/group2-benign-50nodes.jsonlines \
  --input_file=./examples/data/sid_visualization/group3-si-50nodes.jsonlines \
  --input_file=./examples/data/sid_visualization/group4-benign-49nodes.jsonlines
```
 IMG 2

 ### GNN Fraud Detection

```
docker run --rm -ti --gpus=all --net=host -v /var/run/docker.sock:/var/run/docker.sock nvcr.io/nvidia/morpheus/morpheus:23.07-runtime bash

# inside Docker container
mamba env update -n ${CONDA_DEFAULT_ENV} -f examples/gnn_fraud_detection_pipeline/requirements.yml
pip install --ignore-requires-python stellargraph==1.2.1

cd ${MORPHEUS_ROOT}/examples/gnn_fraud_detection_pipeline
python run.py
```


