
<div align="center">   

# RideFlux AI Challenge : Object Detection
</div>

Microsoft의 [Dynamic Head](git@github.com:microsoft/DynamicHead.git)에서 Fork한 저장소.

Object Detection에 쓰일 모델을 학습하고 Inference한다. 

------


### Model Zoo

모델을 학습하는데 필요한 Pretrained Model. 해당 모델을 다운로드 받고 `pretrained/` 폴더 밑에 저장하면 된다.

| Config                                                                       |           Model         |   Backbone  | Scheduler | COCO mAP |  Weight                                                                                |       
|------------------------------------------------------------------------------|-------------------------|-------------|-----------|----------|----------------------------------------------------------------------------------------|                                                                                              
|  [cfg](configs/dyhead_swint_atss_fpn_2x_ms.yaml)                             |    ATSS + DyHead        |   Swin-Tiny | 2x + ms   | 49.8     |  [weight](https://xiyang.blob.core.windows.net/public/dyhead_swint_atss_fpn_2x_ms.pth) |    

------

### Usage
**Dependencies:**

[Detectron2](https://detectron2.readthedocs.io/en/latest/tutorials/install.html), [timm](https://rwightman.github.io/pytorch-image-models/)

Detectron2 저장소에서 Docker 설정을 하는 것을 매우 강하게 추천한다. 단, 이 경우 이러한 에러가 발생할 수 있다.
> AttributeError: module 'distutils' has no attribute 'version'

이 문제는 [여기](https://github.com/pytorch/pytorch/issues/69894#issuecomment-993805355)를 참고하여, Docker Container 내부 파일 `/usr/local/lib/python3.8/dist-packages/torch/utils/tensorboard/__init__.py `을 조금 변경하여 바꿔주면 된다. 


**Installation:**

```
python -m pip install -e DynamicHead
```

**Train:**

GPU 4개로 학습할 경우,

```
python train_net.py --config configs/dyhead_swint_atss_fpn_2x_ms.yaml --num-gpus 4
```

- 메모리 문제 등의 경우로 인해 Batch Size를 조정해야 할 경우, `configs/dyhead_swint_atss_fpn_2x_ms.yaml`에서 배치 크기를 조정하자
- `train_net.py`에서 호출할 파일 디렉토리 등을 설정한다. 
- 현재 `config`에 반영되어 있지만, 기존의 코드로 DyHead로는 돌아가지 않는다. yaml 파일에서 `  WEIGHT_DECAY_BIAS`을 설정해야 한다.
```
def setup(args):
    """
    Create configs and perform basic setups.
    """

    # Set Train and Val data directories

    return cfg
```

- 학습된 Weight 파일들은 `output/`에 저장된다.
- 필요한 경우 `extra/config.py`의 `cfg.MODEL.ATSS.NUM_CLASSES`에서 클래스 개수를 조정한다.
- 필요한 경우 `dyhead/config.py`의 `cfg.MODEL.ROI_HEADS.NUM_CLASSEES`에서 클래스 개수를 조정한다.

**Test:**

마찬가지로 Inference할 경우,
```
python train_net.py --config configs/dyhead_swint_atss_fpn_2x_ms.yaml --num-gpus 4 --eval-only MODEL.WEIGHTS $WEIGHT
```

- Inference 결과는 `output/inference`에 저장된다. 
- 학습된 모델들 inference는 `command_list.sh`를 실행하여 전체 Evaluation 실행


------

### DynamicHead Citation

```BibTeX
@InProceedings{Dai_2021_CVPR,
    author    = {Dai, Xiyang and Chen, Yinpeng and Xiao, Bin and Chen, Dongdong and Liu, Mengchen and Yuan, Lu and Zhang, Lei},
    title     = {Dynamic Head: Unifying Object Detection Heads With Attentions},
    booktitle = {Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)},
    month     = {June},
    year      = {2021},
    pages     = {7373-7382}
}
```


