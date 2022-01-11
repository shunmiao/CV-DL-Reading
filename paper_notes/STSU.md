# [Structured Bird’s-Eye-View Traffic Scene Understanding from Onboard Images](https://arxiv.org/abs/2110.01997) 

Jan 2022

### Overall Impression

The core idea of STSU is similar to DETR3 D, i.e., using trainable queries to retrieve object detection results. The main novelty lies in the formulation of road network as centerline objects. The definition of centerline is critical (i.e., if and how are the starting/ending points uniquely defined) because it can cause ambiguity in the model's prediction (e.g., should it produce one long centerline? or multiple shorter centerlines?). Unfortunately, I don't see clear definition of centerlines in this paper.

### Key Ideas

- Formulation:
  - *Centerlines*: The road netowrk is conceptually described by a directed graph $G=(V,E)$. In the implementation, the graph consists of a number of "centerlines" described by Bézier curve control points. The connectivity is described by an incidence matrix $I$. 
  - *objects*: Objects like viechels are described by BEV bounding boxes with 5 DoF. The formulation is the same as DETR3D.
  - A transformer-based neural network takes the camera image as input ans outputs the detection results of both centerlines and objects.
- Architecture:
  - A transformer-based architecture is employed. Two sets of queries for centerlines and objects are used to produce detection resutls, similar to DETR. 
  - Positional encoding are added to the input feature map, includingL 1) image domain positional encoding, and 2) BEV location positional encoding (convert image pixels to BEV location with a flat suface assumption. Logarithm is applied to make the encoding more evenly distributed).
  - Lane branch:
    - **Detection head**: tells if the centerline corresponding to the query exists.
    - **Control head**: outputs the Beizer curve control points.
    - **Association head**: produces *association feature vectors*, which are used in association classification.
    - **Association classifier**: takes two *association feature vectors* as input and outputs the probability of the input centerlines are connected. 
  - Object branch:
    - **Detection head**: tells if the object corresponding to the query exists.
    - **5-params head**: outputs the 5 DoF parameters of the object bounding box.
    - **Refinement net **: outputs segmentation of the object bounding box in the RoI
- Loss: Detection results (both centerlines and objects) are matched with the ground truth using Hungarian matching. Training loss is calculated on matched objected as $L_{CE}+\lambda L_1$, where the first term is classification loss and the second term is the loss of the parameters (Bézier control points or bounding boxes). 
- Ground truth: The definition of centerline instances is not clear. Need to look into the [code](https://github.com/ybarancan/STSU) for more details. 