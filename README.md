# Focal Loss for Dense Object Detection

R-CNN은 정확도를 높이기 위해 2단계 접근 방식을 사용합니다.

1번째 단계에서 개체들을 검출하고 개체 영역을 자릅니다. (Localization)

2번째 단계에서 CNN을 활용하여 개체에 대한 분류를 진행합니다. (Classification)

이러한 2단계 접근 방식은 속도에 저하를 유발하기 때문에 둘을 동시에 실행하는 1단계 접근 방식이 제안되었습니다. (YOLO, SSD)

하지만 1단계 접근 방식은 2단계 접근 방식보다 빠른 대신에 낮은 성능을 보였습니다.

Focal Loss for Dense Object Detection에서는 이러한 이유를 조사하고 Dense Detector에서 

극단적인 Foreground-Background 클래스 불균형 문제에 의해 발생한다는 것을 깨달았습니다.

이 문제를 해결하기 위해 Cross Entropy Loss Function을 수정한 Focal Loss 를 제안합니다.

## Foreground-Background Class imbalance

Foreground class는 물체가 Bounding Box 안에 있는 경우를 말합니다.

Background class는 물체가 Bounding Box 안에 없는 경우를 말합니다.

Foreground-Background Class imbalance는 이미지 내에 많은 수의 Background class와 적은 수의 Foreground class가 있어서

제대로 학습하지 못하는 문제입니다.

## Cross Entropy Loss

이 Loss Function은 동적으로 스케일링된 Cross Entropy Loss입니다. 

Cross Entropy Loss를 수식으로 나타내면 다음과 같습니다.

![eq1](https://latex.codecogs.com/gif.latex?%5Ctextup%7BCE%7D%28p_t%29%20%3D%20-%5Ctextup%7Blog%7D%28p_t%29)

여기서 ![pt](https://latex.codecogs.com/gif.latex?p_t)는 예측한 클래스가 맞을 확률입니다.

Loss가 낮을 수록 분류가 잘된 것이고, 높을 수록 잘 안된 것입니다.

그러나 CE Loss의 문제는 쉬운 샘플이 많이 들어오고 어려운 샘플이 적게 들어올 경우 발생합니다.

쉬운 샘플에 대부분의 학습이 되고, 어려운 샘플을 구분하지 못하는 상태가 되며 만약 쉬운 샘플이 Negative class일 경우에는

학습이 아예 되지 않는 상태가 됩니다.

이러한 점을 고치기 위해 ![alpha](https://latex.codecogs.com/gif.latex?\alpha)를 이용하여 Positive/Negative Class의

균형을 맞춰주는 가중치 도입법을 사용했습니다. 하지만, 이 방법은 쉬운/어려운 샘플을 구분하지 못합니다.

## Focal Loss

Focal Loss는 쉬운 샘플의 가중치를 낮추고 Hard Negative에 초점을 맞추어서 학습을 시킵니다.

이를 위해 조정 가능한 파라미터인 ![gamma](https://latex.codecogs.com/gif.latex?\gamma)를 사용합니다.

![gamma](https://latex.codecogs.com/gif.latex?\gamma)는 0보다 크거나 같으며 5보다 작거나 같습니다.

Cross Entropy Loss에 다음과 같이 추가합니다.

![fl](https://latex.codecogs.com/gif.latex?%5Ctextup%7BFL%7D%28p_t%29%3D-%281-p_t%29%5E%7B%5Cgamma%7Dlog%28p_t%29)

여기서 Focal Loss는 두가지 속성을 갖습니다.

(1) 샘플이 잘못 분류되어 pt가 작으면 계수가 1에 가까워지고 Loss에 대한 영향이 낮아집니다. 

pt가 1에 가까워지면 계수는 0이 되고 쉬운 샘플이 Loss에서 갖는 가중치가 낮아집니다.

(2) 매개변수 ![gamma](https://latex.codecogs.com/gif.latex?\gamma)는 쉬운 예제의 가중치가 미치는 영향을 조절합니다.

![gamma](https://latex.codecogs.com/gif.latex?\gamma)가 0인 경우 Cross Entropy Loss와 동일해집니다.

# RetinaNet

![img1](https://github.com/kjo26619/Object_Detection_RetinaNet/blob/main/RetinaNet.PNG)
( 출처 : https://arxiv.org/pdf/1708.02002.pdf )

RetinaNet은 백본 네트워크와 두 개의 네트워크로 구성됩니다. 

이는 1단계 접근 방식과 같으며 여기서 Loss를 Focal Loss를 사용합니다.

Focal Loss를 사용하여 다른 1단계 접근 방식보다 더 좋은 AP를 보였습니다.
