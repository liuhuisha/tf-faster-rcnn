# tf-faster-rcnn for liver detection

**Acknowledge:** This reporitory was cloned from an earlier version of endernewton's tf-faster-rcnn([link](https://github.com/endernewton/tf-faster-rcnn)). For convenience I will change a lot of parts to apply my liver detection task.

## Still updating ...

## Change log

#### 2018-4-3

* Modify `./experiment/scripts/train_faster_rcnn.sh & test_faster_rcnn.sh`
  * add liver_ql case
  * `ANCHORS` [4,8,16]
  * `TRAIN_IMDB` two datasets (2016+2017)
  * `STEPSIZE` 70000
  * `ITERS` 100000

* Use `Leaky ReLU` in vgg16 and rpn

* Add image normalization: [-1, 1]  

#### 2018-4-4
* Remove 'only keep anchors inside the image'

* STEP_SIZE = [30000, 70000]  

* RPN_PRE_NMS_TOP_N, RPN_POST_NMS_TOP_N
* STEP_SIZE = [100000] ITERS = 150000

#### 2018-4-5
* vgg16.yml, RPN_BATCHSIZE = 512

* Add focal loss and decrease batch size for training  
  I have checked the signal/noise and found that the number of positive anchors is in the range [1, 20], maybe some more while the negative samples are about 10 thousand. So I have to restrict the batch size to 100(found in `./experiment/cfgs/vgg16.yml`).  
  Up to now, I have gotten a mean IoU of **0.480** with test instruction:
  ```bash
  bash experiments/scripts/test_faster_rcnn.sh 5 liver_ql vgg16 3 0.02
  ```

#### 2018-4-7
* Add abdominal mask as anchor filter. 

* Modify to two thresholds in test routine, `THRES_PRE_NMS` and `THRESH_POST_NMS`  
  Up to now, I have gotten a mean IoU of **0.575** with test instuction:
  ```bash
  bash experiments/scripts/test_faster_rcnn.sh 5 liver_ql vgg16 3 0. 0.015
  ```

#### 2018-4-8
* Disable pre-trained model (Keep)
  I got a mean IoU of **0.676** with test instruction:
  ```bash
  bash experiments/scripts/test_faster_rcnn.sh 7 liver_ql vgg16 3 0.01 0.
  ```

* rpn_loss_box * 5 (Remove)
 Don't have much improvement from last version.

#### 2018-4-9
* Add 3 convolution layers to classification subnet and regression subnet of rpn (Keep)
  I got a mean IoU of **0.689** with test instruction:
  ```bash
  bash experiments/scripts/test_faster_rcnn.sh 7 liver_ql vgg16 3 0. 0.02
  ```

#### 2018-4-10
* rpn_loss_box * 2 & step size [300000] (Keep)
  I got a mean IoU of **0.705** with test instruction:
  ```bash
  bash experiments/scripts/test_faster_rcnn.sh 7 liver_ql vgg16 3 0. 0.01
  ```

#### 2018-4-11
* Add group normalization to backbone net (Not train, neet tensorflow>=1.5)

* Use resnet-101 backbone (have some bugs)
* Add anchor batch size to 1000 (not test)

#### 2018-4-14
* batch norm, weight decay coef = 1e-0 (batch norm is too bad, remove)
* batch norm, weight decay coef = 1e-1 (batch norm is too bad, remove)
* (2)mask area lower threshold = 2000 (**0.703**, Keep(2) )

#### 2018-4-15
* step size [500000] (Remove)

#### 2018-4-18
* set width = 250, level = 55, anchors[4,8,12,16,24], ratios[0.5,0.7,1,1.5,2], cls_weight=50, bbox_weight=50 (training - output)
  I have found a bug in my test routine, that I have used mask image to generate abdomen mask :(  
  I got a mean IoU of **0.741** with test instruction:
  ```bash
  bash experiments/scripts/test_faster_rcnn.sh 7 liver_ql vgg16 3 0. 0.6
  ```

#### 2018-4-21
* Use expanded dataset, cls_weight=1, bbox_weight=1, step size = [160000], max_iter = 225000 (keep)  
  mean IoU: **0.737**  
  AP: 0.844 (>=0.5)  
* Use resnet-50 (origin)  
  mean IoU: **0.756**  
  AP: 0.868 (>=0.5)  

#### 2018-4-24
* Use resnet-50, stepsize=100000, max_iter=200000 (batch_norm)  
  mean IoU: **0.779**  
  AP: 0.899 (>=0.5)  
  Note: when using batch norm, batch mean and variance is used in both train and test modes. Moving mean and variance will harm test accurancy seriously.
* instance norm (inst_norm) (keep)  
  mean IoU: **0.779**  
  AP: 0.895 (>=0.5)  

#### 2018-4-25
* area_low >= 2000 stepsize=140000  
  mean IoU: **0.794**  
  AP: 0.900 (>=0.5)  

#### 2018-4-26
* Use resnet-101, stepsize=120000(Remove)

#### 2018-4-29
* Use resnet-50, layer norm, stepsize=100000  
  mean IoU: **0.696**  
  AP: 0.784 (>=0.5)  
* Use resnet-50, batch norm, relu  
  mean IoU: #**0.782**  
  AP: 0.876  
* Use resnet-50, instance norm, relu  
  mean IoU: #**0.782**  
  AP: 0.881  

#### 2018-4-30
* Use resnet-50, batch norm, leaky_relu  
  mean IoU: #**0.777**  
  AP: 0.852  
* Use resnet-50, insta norm, leaky_relu  
  mean IoU: #**0.779**  
  AP: 0.872  

#### 2018-5-2
* Use resnet-50, insta norm, relu, three slices as input  
  mean IoU: **0.727**  
  AP: 0.754  
* Use resnet-50, insta norm, relu, batch size = 200(keep)  
  mean IoU: **0.747**  
  AP: 0.887  
  max_iters = 300000(keep)  
  mean IoU: **0.737**  
  AP: 0.895  

#### 2018-5-3
* momentum=0.9  
  mean IoU: **0.714**  
  AP: 0.803  

#### 2018-5-7
* AdamOptimizer, stepsize=150000  
  mean IoU: #**0.781**  
  AP: 0.882  
* stepsize=[150000,250000], max_iters=350000(keep)  
  mean IoU: #**0.792**  
  AP: 0.906  
* batch norm
  mean IoU: #**0.774**
  AP: 0.880


#### 2018-5-01
Some problems:
* Case P021, false positive
* Case P024, slice 10/38 wrong label
* Case P002, slice 31/33 different window width/level will give show different edge
* Case P020, slice 43/49 mistake cardiac for liver

#### 2018-5-8
* Case R077