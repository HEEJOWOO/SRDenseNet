# SRDenseNet : https://openaccess.thecvf.com/content_ICCV_2017/papers/Tong_Image_Super-Resolution_Using_ICCV_2017_paper.pdf

#### <I studied while referring to various sites, but it is not enough. Thanks anytime for feedback.>
### <heejo5@naver.com>

Image Super-Resolution Using Dense Skip Connections
---------------------------------------------------
* skip connection을 한번 사용한 DRCN 같은 경우 SR의 퍼포먼스를 매우 잘 살렸지만, 한 번이 아닌 깊은 네트워크에서 적절한 skip connection을 사용한다면 잠재적으로 더 SR의 성능을 올릴 수 있다는 것을 연구
* DenseNet을 기반한 super resolution을 제안을 하였고 SRDenseNet은 Dense Skip Connection을 이용하여 다른 level의 특징을 합쳤으며, SISR 복원 성능을 더 올림
* 보간과 다른 방법인 LR공간에서 convolution을 통해 처리한 후에 마지막에 upscaling factor를 학습하여 HR를 만드는 Decovolution을 사용하였고 또한 4개의 benchmark datasets에 대해 state of the art 를 달성하였으며, 시각적인 측면과 빠른 실행속도를 가짐

SRDenseNet Network
------------------
![image](https://user-images.githubusercontent.com/61686244/94680149-3d954780-035c-11eb-941a-cc8460ecc75c.png)
* (a), (b), (c)
  * (a)는 HR img를 복원하기위해 인풋으로 High-level 특징 맵만을 사용하는 네트워크
  * (b)는 HR img를 복원하기위해 인풋으로 low-level 특징과 high-level특징 맵을 사용한 구조
  * (c)는 HR img를 복원하기위해 인풋으로 skip connection을 통해 low level특징 뿐만 아니라 각각의 dese block 아웃풋도 인풋으로 들어가는 구조
* The convolution layer for learning low-level feature
* The blocks of DenseNet for learning high-level feature
* The deconvolution layers for learning upscaling filters
* The reconstruction layer for generating the HR output

Dense Block
-----------
![image](https://user-images.githubusercontent.com/61686244/94680545-de840280-035c-11eb-82e7-d08764f040a3.png)
* Low level의 특징을 학습하기 위해 입력된 LR img를 convolution으로 구한 뒤에 high level 특징을 학습하기 위해 DenseNet Block들이 나오게됨
* ResNet과는 다르게 DenseNet 은 합치는 것 이 아닌 연결되어 있는 구조로 이루어져있음
* 이렇게 연결돼 있는 Short Paths는 깊은 네트워크에서 정보의 흐름 뿐만 아니라 gradient vanishing문제를 해결 하였으며 추가적으로 DenseNet은 앞에있는 특징 맵을 재사용함으로써 파라미터의 수를 줄여 메모리와, 계산적인 측면에서 효율을 얻었음
* SRDenseNet은 하나의 Denseblock에 8개의 convolution을 가지고 있으며 각각의 convolution은 growth rate이라고 불리는 k개의 특징 맵을 만듦
* 넓어지는 것을 막기위해 growth rate을 16개로 정했으며 따라서 하나의 dense block은 8*16=128개의 특징 맵을 가지고있음

Deconvolution
-------------
![image](https://user-images.githubusercontent.com/61686244/94680918-84d00800-035d-11eb-979b-97094fc505aa.png)

Bottleneck and Reconstruction
-----------------------------
![image](https://user-images.githubusercontent.com/61686244/94681020-acbf6b80-035d-11eb-94a0-dbc7820636dc.png)
* 최종적으로 논문에서 제안을 한 네트워크 구조 c를 다시 보게 되면 Low level 특징 맵을 위해 convolution을 지나고, high level feature특징 맵을 위한 dense blocks들을 지난 뒤에 Bottleneck구조가 따르게됨
* Bottleneck을 사용하게 된 이유는 네트워크내에 모든 특징 맵들이 연결돼 있어 deconvolution에 많은 입력이 들어가게 된다고함
* 모델의 컴팩트함과 계산적인 효율을 유지하기위해 deconvolution에 들어가는 특징 맵의 수가 줄어야할 필요성이 있어 1x1 커널을 사용하여 input의 특징 맵을 줄였다고함
* boottleneck layer로 인해 deconvolution layer에 들어가기전에 특징맵의 수를 줄여 모델의 컴팩트함과 계산적인 효율성을 향상 시킬 수 있음
* bottleneck을 통과하게 되면 256개의 특징맵이 남는데 이것을 deconvolution 레이어에서 LR공간에서 HR공간으로 옮기고, 마지막으로 Reconstruction layer로 HR을 복원

SRDenseNet_H, SRDenseNet_HL, SRDenseNet_ALL
-------------------------------------------
![image](https://user-images.githubusercontent.com/61686244/94681203-03c54080-035e-11eb-8d22-c8a47f1f29c6.png)
![image](https://user-images.githubusercontent.com/61686244/94681279-20617880-035e-11eb-9496-c1be86a90dde.png)
* Training set으로 IamgeNet의 5만개의 이미지에서 랜덤하게 선택을 하여 사용하였다고 하고, test set으로는 benchmark인 set5, set14, B100, Urban100 4개의 데이터 셋을 사용하였다고함
* SRDenseNet_H 구조는 HR img를 복원하기위해 인풋으로 High-level 특징 맵만을 사용하는 네트워크이고, SRDenseNet_HL는 HR img를 복원하기위해 인풋으로 low-level 특징과 high-level특징 맵만을 사용한 구조이고 SRDenseNet_All는 HR img를 복원하기위해 인풋으로 skip connection을 통해 low level특징 뿐만 아니라 각각의 dese block 아웃풋도 인풋으로 들어가는 구조
* Skip connection을 추가하여 low level 특징 맵과 high level 특징 모두를 사용한게 더 좋은 결과를 내었으며, 다음으로는 모든 특징 맵들을 연결한 구조가 가장 좋은 성능을 만들었음

Comparison of SR
----------------
![image](https://user-images.githubusercontent.com/61686244/94681501-89e18700-035e-11eb-9fa6-ecfb3d7929fb.png)
![image](https://user-images.githubusercontent.com/61686244/94681576-ac73a000-035e-11eb-842d-195a36508b2b.png)
![image](https://user-images.githubusercontent.com/61686244/94681614-bac1bc00-035e-11eb-85ae-58b85bc14950.png)

Conclusions
-----------
* SRDenseNet : 69 weight layers, 68 activation layers
* 깊이에 비례한 receptive field, LR img 많은 정보 사용 가능
* 많은 activation layers 덕분에 LR img를 HR img에 매핑할 수 있음
* DenseNet 구조를 활용하여 Gradient vanishing 문제 해결 












