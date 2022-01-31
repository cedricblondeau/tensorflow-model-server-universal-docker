# tensorflow-model-server-universal-docker

> Simple tensorflow-model-server-universal docker image - that runs on Macbook M1 Pro.

## Motivation

As of Jan 2022, [TF serving official docker image won't run on M1 Apple machines](https://github.com/tensorflow/serving/issues/1948) even with the `--platform linux/amd64` option, likely because of emulation issues.

Using the `tensorflow-model-server-universal` package fixes this problem and allows emulation. As a tradeoff, it also [disables some optimizations](https://www.tensorflow.org/tfx/serving/setup#installing_using_apt). Not recommended in a production environment and only tested on a MacBook M1 Pro.

## Get started

```
# Download the TensorFlow Serving Docker image and repo
docker pull cedricbl/tf-serving-universal-amd64

git clone https://github.com/tensorflow/serving
# Location of demo models
TESTDATA="$(pwd)/serving/tensorflow_serving/servables/tensorflow/testdata"

# Start TensorFlow Serving container and open the REST API port
docker run -t --rm -p 8501:8501 \
    -v "$TESTDATA/saved_model_half_plus_two_cpu:/models/half_plus_two" \
    -e MODEL_NAME=half_plus_two \
    --platform linux/amd64 \
    cedricbl/tf-serving-universal-amd64 &

# Query the model using the predict API
curl -d '{"instances": [1.0, 2.0, 5.0]}' \
    -X POST http://localhost:8501/v1/models/half_plus_two:predict

# Returns => { "predictions": [2.5, 3.0, 4.5] }
```
