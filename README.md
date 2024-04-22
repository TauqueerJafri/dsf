Building a recommender using Tensorflow/Keras.

First we built a recommender model in Google Colab. Then we deployed the model into production.

## A note about the data
This repo contains four data files which will be used for training a recommender model. They contain product ids and fake user ids.

## Serving a recommender using Tensorflow serving

##### Environment setup
- docker engine installed and running to run a serve
    General installation instructions are on the [Docker site](https://docs.docker.com/get-docker/), but some quick links here:
    [Docker for macOS](https://docs.docker.com/docker-for-mac/install/)
    [Docker for Windows](https://docs.docker.com/docker-for-windows/install/)
- http client installed to run a client 
    [Curl for mac](https://curl.haxx.se/dlwiz/?type=source&os=Mac+OS+X)  
    
### Running tensorflow serving in local Docker container

CMD 1

````
cd {recommender-model-folder}
````

CMD 2 

````
docker pull tensorflow/serving
````

CMD 3

````
_**Windows**_

docker run -d -p 8501:8501 -v "$PWD/:/models/recommender" -e MODEL_NAME=recommender tensorflow/serving

````

```

_**Mac**_

docker run -d -p 8501:8501 --mount type=bind,source=${PWD}/,target='/models/recommender' -e MODEL_NAME=recommender tensorflow/serving

````

CMD 4

````

_**Windows**_

$rec_request = @"
{
"signature_name" : "call_item_item",
"inputs" : {
"item": [123123]
}
}
"@


$rec_response = Invoke-RestMethod -Uri "http://localhost:8501/v1/models/recommender:predict" -Method Post -Body $rec_request -ContentType "application/json"
$rec_response | convertto-json

````

````
_**Mac**_
curl --header "Content-Type: application/json" --request POST --data '{"signature_name":"call_item_item","inputs": {"item": [123123] } }' http://localhost:8501/v1/models/recommender:predict
````

CMD 5 

````
_**Windows**_

$output = Invoke-RestMethod http://localhost:8501/v1/models/recommender/metadata
$output | convertto-json
````

````
_**Mac**_

curl http://localhost:8501/v1/models/recommender/metadata

````
