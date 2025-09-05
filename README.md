## A. docker pull


## B. docker build
### 1. docker build
```
cd docker
docker build -t ketiodt-gdino .
docker run -it -d \
    --network=host \
    --gpus '"device=0"' \
    -e DISPLAY=$DISPLAY \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    --name ketiodt ketiodt-gdino /bin/bash
```

### 2. container 실행
```
docker start -ai odt-gdino
```

### 3. run example
```
cd ~/GroundingDINO
python3 demo/inference_on_a_image.py -c groundingdino/config/GroundingDINO_SwinT_OGC.py -p weights/groundingdino_swint_ogc.pth -i .asset/cats.png -o "output" -t "cat"
```