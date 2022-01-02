# VISOLO
December 2021

### Overall Impressions

The task of video instance segmentation (VIS) is to generate spatio-temporal pixel masks for objects. VISOLO aims to address online VIS, where predictions need to be made on-the-fly as new frames are received. 

The core of this method is the similarity between features in the current frame and those in the previous frames. This similarity is used to weight the previous features and aggregate them into the current frame. The intuition is also straightforward: history features with high similarities might be related to the current instance and therefore should contribute more information. 

The concept of using similarity to weight features is similar to transformer, but it's only done once and there is no differentiation between query, key and value. I'd expect transformer to perform better given that it's configured properly. 

### Key Ideas
- **Memory matching module**: compute the similarity between feature vectors of the current frame and those of the history frames.
- **Temporal aggregation module**: The feature maps of the previous frames are aggregated through a weighted summation, where the weights are computed as softmax of the similarity. 
- **Score reweighting module**: This part is not clearly explained in the paper. A weight map with the same spatial dimension as the feature map is calculated by taking the *maximum* similarity of each pixel with *all* pixels in a previous frame. This weight map is used to scale the output score. The intuition behind this module might be: pixels with high similarity with pixels in the previous frame may be more trustworthy.
- **Instance tracking**: the similarity can be used to track object instances, i.e., the current instanced are connected to the previous instances with the maximum similarity. 