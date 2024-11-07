### Set environment variables(On Every node): 
```
export NCCL_DEBUG=INFO
export NCCL_SOCKET_IFNAME=eth0  # Change according to your network interface
```

### Node Setup process:

***On both nodes, Node 1 and Node 2***
```
git clone https://github.com/pandyamrut/multi-node.git
cd ddp
```

### Configure Network
```
# On Node 2, ping Node 1 to verify connectivity
ping <node1_ip_address>

# Check if port is accessible
nc -zv <node1_ip_address> 29400
```

***NOTE:*** Once repository is cloned on the both the nodes please, connection test is done, enviornment variables are set. Use below commands to launch training. 


### Training Commands: 
NOTE: `--nnodes` represents number of nodes involved in training. `--node_rank=0` represents the rank of the node, rank 0 is the master node. `--nproc_per_node=1` represents number of GPU used in training from availble GPUs on a node. 
***NODE-1***: 

```
torchrun \
    --nproc_per_node=1 \
    --nnodes=2 \
    --node_rank=0 \
    --rdzv_id=687 \
    --rdzv_backend=static \    # --rdzv_backend=c10d fro some cases.
    --rdzv_endpoint=<MASTER_IP>:<MASTER_PORT> \
    main.py 50 10
```

***NODE-2***
```
torchrun \
    --nproc_per_node=1 \
    --nnodes=2 \
    --node_rank=1 \
    --rdzv_id=687 \
    --rdzv_backend=static \
    --rdzv_endpoint=<MASTER_IP>:<MASTER_PORT> \
    main.py 50 10
```

### Launch Order

1. First execute the command on Node 1 (Master)
2. Then execute the command on Node 2 (Worker)
3. Training will begin when both nodes are connected.


### Note
- Keep both terminals open during training
- Training parameters: 50 epochs with batch size of 10
- Both commands must use the same rdzv_id and rdzv_endpoint
- Most common problems during exectuion are related to network, ipv6/ipv4 addresses, Ethernet interface.
 
