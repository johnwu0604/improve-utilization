# How to improve utilization of your compute resources

This documentation gives tips on how to optimize the utilization of your training scripts to either save costs or time.

## None of the GPUs ever reaches full utilization

This likely means that you are not feeding the GPU the maximum amount of data in which it can handle, try one of the following tips:

- Increase your batch size so more data is processed through the GPU on each cycle. If you do this and the accuracy goes down, try increasing the learning rate.
- If you are training on multiple GPUs, try to decrease the number of GPUs that you are using.
- Scale down to a smaller GPU in order to save costs. These are the list of GPU-enabled VM families in order of speed:
    1. NC-series (NVIDIA Tesla K80)
    2. NV-series (NVIDIA Tesla M60)
    3. ND-series (NVIDIA Tesla P40)
    3. NCv2-series (NVIDIA Tesla P100)
    4. NCv3-series (NVIDIA Tesla V100)
    5. NDv2-series (NVIDIA Tesla V100 with NVLINK connections)
    5. NCasT4_v3-series (NVIDIA Tesla T4)
- If none of the above options work, you might not need a GPU. Try to use a CPU instead.

## The GPU utilizations are high, but there are periodic anomalies where utilization decreases

This likely means that the GPU is waiting for data in between batch or epoch cycles. Try one of the following tips:

- If your data is stored remotely, ensure you are prefetching your data and caching it locally. 
- Try to parallelize as much of your data fetching and data transformation steps as possible.
- Try loading in your data only after it has been preprocessed. More specifically, preprocess your data independently using a non GPU-enabled VM.

## Some of nodes have higher utilization than other nodes

This likely means that the bottle nexk is in your network communication. Try one of the follow tips:

- Use the VM size with the maximum number of GPUs. For example, if you are training on NC-series, use a few Standard_NC24 nodes (which have 4 GPUs) rather than a lot of Standard_NC6 nodes (which have only 1 GPU).
- If you have quota, try using fewer GPUs that are faster and/or connected using NVLINK.
- Decrease the nodes or try different distributed strategy.