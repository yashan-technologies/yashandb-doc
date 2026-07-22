Compared to Standalone Deployment systems, distributed SQL computation incurs additional communication overhead between nodes. To improve performance and reduce the impact of data transmission across networks, YashanDB has implemented the following optimizations:

1. An increase in the PX operator, which is responsible for data exchange across networks. PX has the following types:

    Based on the data interaction nodes:
    - I2I: One node sends data to another node.

    - I2N: One node sends data to multiple nodes.

    - N2I: Multiple nodes send data to one node.

    - N2N: Multiple nodes send data to multiple nodes.

    Based on data direction:
    - PX send type: random, hash, broadcast

    - PX receive type: random, sort

2. To fully utilize the computing resources of each node, the YashanDB optimizer attempts to push down computations to the compute node (two-phase computation). The operators that can perform two-phase computations include:

    - Two-phase GROUP BY

    - Two-phase aggregation
