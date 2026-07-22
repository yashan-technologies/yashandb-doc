In Distributed Cluster Deployment, users can scale cluster nodes as needed in a running environment, including two scenarios: CN node scale-out and DN node scale-out.

[CN Node Scaling](./CN Node Scaling) and [DN Node Scaling](./DN Node Scaling) will introduce the operation guidance for these two scenarios separately. Please note that these two operations can be combined, which means you can scale CN and DN nodes simultaneously with one command.

>**Note**:
>
>The scaling method in Distributed Cluster is at the server level, which means you cannot scale on all servers that have joined the cluster.There are two scenarios for it: adds same type nodes (for example, adding a CN on the server that has a CN); or adds diffrent type nodes  (for example, adding a DN on the server that has a CN but not DN).