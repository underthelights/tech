# Computer Vision
## CNN Architecture



* AlexNet: ImageNet Classification with Deep Convolutional Neural Networks 
* ZFNet (DeconvNet): Visualizing and Understanding Convolutional Networks ([pdf](https://arxiv.org/pdf/1311.2901.pdf), [note](https://drive.google.com/open?id=1bzkoKVxLALaD6ZWQh5-vP9qOodMdIwi0), code)
* NIN: Network in Network
* VggNet: Very Deep Convolutional Networks for Large-Scale Image Recognition
* GoogLeNet: Going Deeper with Convolutions
* ResNet:
  - ResNet-v1: Deep Residual Learning for Image Recognition ([note](https://drive.google.com/open?id=1Ahws2bBE_YSjvNcxsF9tnwRCv3HfaVhr))
  - ResNet-v2: Identity Mappings in Deep Residual Networks
  - Wide Residual Networks ([note](https://drive.google.com/open?id=14eQSeymwXgS7JvBbAkOnudAm6MFnJify), code)
* InceptionNet:
  - Inception-v1: GoogLeNet
  - Inception-v2, v3: Rethinking the Inception Architecture for Computer Vision ([note](https://drive.google.com/open?id=1SVOpf9aElrAGCZHlX7NvYL8pbehXpw8i), code)
  - Inception-v4, Inception-ResNet and the Impact of Residual Connections on Learning 
* DenseNet:
* NASNet: Learning Transferable Architectures for Scalable Image Recognition ([note](https://drive.google.com/open?id=1o1SfbVIgEhRWGQG_mPpxKoCDyWtNoifJ), code)
* EfficientNet:([note](https://drive.google.com/open?id=1LtdSId0HTpM8_O4k4WFrzHz4ldPf7dTu))


### [Visualizing CNNs](./doc/visualizing_cnn.md)
* DeconvNet
* BP: Deep inside convolutional networks: Visualising image classification models and saliency maps ([note](https://drive.google.com/open?id=1IBP1uMr08hBp3bKjvyNnwFMu0S8ORGcs))
* Guided-BP (DeconvNet+BP): Striving for simplicity: The all convolutional net ([note](https://drive.google.com/open?id=1KUq5-h_xVmjd4FudGDeBUfPV9vBMHV68), code)
* Understanding Neural Networks Through Deep Visualization


### [Weakly Supervised Localization](./doc/cam.md)
* From Image-level to Pixel-level Labeling with Convolutional Networks (2015)
* GMP-CAM: Is object localization for free? - Weakly-supervised learning with convolutional neural networks (2015) ([note](https://drive.google.com/open?id=1Xpnhq0snjkPMsxKLpmhLOpoZgfFlL9H3), code)
* GAP-CAM: Learning Deep Features for Discriminative Localization (2016) ([note](https://drive.google.com/open?id=1lrkE07E3bnLscAnScwq0OIO3AaRHrqnb), code)
* c-MWP: Top-down Neural Attention by Excitation Backprop
* Grad-CAM: Visual Explanations from Deep Networks via Gradient-based Localization (2017) ([note](https://drive.google.com/open?id=10obbO7F2igia6gCcc9IqxATzOxdoPl7L), code)


### [Object Detection](./doc/detection.md)
* OverFeat - Integrated Recognition, Localization and Detection using Convolutional Networks ([note](https://drive.google.com/open?id=1O3j-ag0pPRbRjG4ovWmxnZIwhUJ0twvK), code)


### [Semantic Segmentation](./doc/semantic_segmentation.md)
![x](https://user-images.githubusercontent.com/40276516/77990083-598f4080-735b-11ea-81ee-0249acb7bebd.png)
* FCN_V1 (2014)에서 직접적인 영향을 받은 모델들:
  * FCN + max-pooling indices를 사용: SegNet V2 (2015) ([note](https://drive.google.com/open?id=1CDNkW-3LKVDjGAyPCgj8fOz78pMY0Pd7))
  * FCN 개선: Fully Convolutional Networks for Semantic Segmentation (FCN_V2, 2016) ([note](https://drive.google.com/open?id=1Kr2-ZdiqKmsgXP2ofaUZm_PT5UbbTyDN), [code](https://github.com/bt22dr/CarND-Semantic-Segmentation/blob/master/main.py))
  * FCN + atrous convolution과 CRF를 사용: DeepLap V2 (2016)
  * FCN + Dilated convolutions 사용: Multi-Scale Context Aggregation by Dilated Convolutions (2015)
  * FCN + Multi-scale: Predicting Depth, Surface Normals and Semantic Labels with a Common Multi-Scale Convolutional Architecture (2015)
  * FCN + global context 반영 위해 global pooling 사용: ParseNet (2015) 
  * FCN + 모든 레이어에서 skip 사용: U-Net (2015) ([note](https://drive.google.com/open?id=1Up8PiwA79J8R3ScjgYTmLzt8pYYa83EN))
* PSPNet (2016) ([note](https://drive.google.com/open?id=1xPu7Z-0jWepxb1av9fG2Py72Yz0enWym))
* DeepLabv3+ (2018) ([note](https://drive.google.com/open?id=1YFUdcwKzIrTzfmL6o94y01tDXsZ2n6vc))
* EncNet (2018)
* FastFCN (2019)
* Instance Segmentation
  * DeepMask
  * SharpMask
  * Mask R-CNN (2017) ([note](https://drive.google.com/open?id=1kFVOdctJTcWYkflfCM1Ys-J7Fo8COC6R))
* 3D / Point Cloud
  * PointNet (2017)
  * SGPN (2017)
* Weakly-supervised Segmentation

### [Style Transfer](./doc/style_transfer.md)
* ~~A Neural Algorithm of Artistic Style (2015)~~
* Image Style Transfer Using Convolutional Neural Networks (2016)
* Perceptual Losses for Real-Time Style Transfer and Super-Resolution (2016)
* Instance Normalization: 
  * Instance Normalization: The Missing Ingredient for Fast Stylization (2016)
  * Improved Texture Networks: Maximizing Quality and Diversity in Feed-forward Stylization and Texture Synthesis (2017)


### Siamese, Triplet Network
* Triplet Network
  * FaceNet: A Unified Embedding for Face Recognition and Clustering ([note](https://drive.google.com/open?id=1E9ZGncIvpJoPK5_mSq5J-Mn33r2_xAqj), code)
  * Learning Fine-grained Image Similarity with Deep Ranking ([note](https://drive.google.com/open?id=1BrjRlzB139v5nmCgdLruJGn1drmBb33m), code)
* Siamese Network


### Mobile
* Shufflenet: An extremely efficient convolutional neural network for mobile devices
* Mobilenets: Efficient convolutional neural networks for mobile vision applications


### [Etc.](./doc/etc/md)
* A guide to convolution arithmetic for deep learning ([note](https://drive.google.com/open?id=1zGGzI4qc49u5zV0jFSkzD8xDMY0OalN1))
