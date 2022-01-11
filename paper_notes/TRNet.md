# [Topology Preserving Local Road Network Estimation from Single Onboard Camera Image](https://arxiv.org/pdf/2112.10155v1.pdf)

Jan 2022

### Overall Impression

This paper is a follow up work of STSU (see my previous [notes](STSU.md)). The main expansion is the addition of a topology preserving loss. Another difference from STSU is that in this paper, the authors recognize that Using Hungarian matching is not ideal since it does not consider the fragmentation of the estimated curves.

### Key Ideas

- Notations:
  - $G(V,E)$ road network graph
  - $T(C, I)$ topological structure, where $C$ is the curves, $I_c \in I$ is the intersections.
  - Minimal cycle: cycles formed by curves where no curve intersects the enclosed area.
  - Minimal cover: set union of the curves that form the minimal cycle.
- Lemma:
  1. A minimal closed polycurve (minimal cycle) MC is uniquely identified by its minimal cover $B$.
  2. The global intersection order of the two structures are same if and only if the sets of minimal cycles are same.
  3. **The global intersection order can be uniquely described by the set of minimal covers $B$.**
     This is the most important lemma, which translates the topology preserving problem into a minimal cover detection problem, which can be modeled by classifiers. 
- Network Architecture:
  - Outputs: $V_c$ of size $N\times D$ which is a $D$ dimensional embedding for all $N$ curve candidates and $V_m$ of size $M \times E$, which is a $E$ dimensional embedding for all $M$ minimal cover candidates.
  - $V_c$ is processed in a similar way as STSU
  - $V_m$ is processed to produce three parts for every minimal cover candiates
    1. $Z_M^q$ A probablity of belonging to this minical cover is predicted for each curve.
    2. $Z_M^p$ A probability that this minical cover exists.
    3. $Z_M^r$ The center of this minical cover.
  - Network options: 1) transformer, 2) polygon-RNN.
- Training:
  - Min Matching:
    - Each candidate curve is matched to its closest GT curve.
    - For all the matched estimated curves whose corresponding GT curve is present in a minimal cover, set the membership target of $Z_M^q$ to 1.
    - Run Hungarian matching to find the pairs used for loss calculations.
  - Loss
    - $L_1$ loss for spline control points and minimal cycle centers.
    - BCE lsos for centerline and minimal cycle probabilities.
    - BCE loss for membership of minimal covers.