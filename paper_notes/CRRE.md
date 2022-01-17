# [Convolutional Recurrent Network for Road Boundary Extraction](https://arxiv.org/pdf/2012.12160.pdf) \[CVPR 2019\]

Jan 2022

*TL;DR* - Use CNN on local patches of BEV features to iteratively produce road boundary vertices

### Overall Impression

This is an extension of the same authors’ prior work, HRANet. The main differences are: 1) Starting points are predicted using landmark detection setting, 2) polylines are predicted using CNN instead of LSTM, 3) the input to the polyline detection phase has physical meaning.

### Key Ideas
- Input data: LIDAR/camera data projected to the BEV space image with 5 channels: RGB and gradients of height.
- Backbone: encoder-decode network with three output heads:
  - Detection map: distance transform to road boundaries
  - End points: probability map of the location of the end points
  - Direction map: normal direction of the road boundaries
- Convolutional snake (cSnake)
  - Input: detection map and direction map.
  - Starting from each end point, iteratively apply CNN on local patch around the previous detected vertex to produce a polyline.
  - The iteration stops when the produce vertex is outside of the image.
  - The average detection score of polyline vertices is calculated for each predicted polyline. The score is used to: 1) filter false positives, 2) merge duplicated polylines (the one with the higher score is kept).
- Loss: 
  - Visual features (three heads): regression loss
  - Convolutional snake: Chamfer loss

<p align="center"> <img src="../resources/images/crre_system.jpg" width="1024" /> </p>