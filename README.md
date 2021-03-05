# How to improve or debug the utilization of your compute resources

This documentation gives tips on how to optimize or debug the utilization of your training scripts.

## My GPU has zero utilization

![No GPU](./no-gpu.png)

If your GPU is not being used, it can likely be a error in your training script. Try one of the following tips:

- Make sure the framework you are using supports training on GPUs.
- Cross check the version of the framework you are using with the CUDA version on the training image. Make sure the two combinations are compatible with each other.
- Make sure you explicitly indicate in your code to use all the available GPU devices.
- Check the logs to see if there was a specific reason it was not using the GPU. 
- If none of the above options work, file a support ticket.

## The GPU never reaches full utilization

![Low Utilization](./low-util.png)

If your GPU never reaches full utilization, this means that you are not feeding the GPU the maximum amount of data that it can handle. Try one of the following tips:

- Increase your batch size so more data is processed through the GPU on each cycle. If you do this and the accuracy goes down, try increasing the learning rate. 
- If you are training on multiple GPUs, try to decrease the number of GPUs that you are using.
- Scale down to a smaller GPU in order to save costs. Here is the list of GPU-enabled VM families in order of speed:
    1. NC-series (NVIDIA Tesla K80)
    2. NV-series (NVIDIA Tesla M60)
    3. ND-series (NVIDIA Tesla P40)
    3. NCv2-series (NVIDIA Tesla P100)
    4. NCv3-series (NVIDIA Tesla V100)
    5. NDv2-series (NVIDIA Tesla V100 with NVLINK connections)
    5. NCasT4_v3-series (NVIDIA Tesla T4)
- If none of the above options work, you might not need a GPU. Try to use a CPU instead.

## The maximum GPU utilization is high, but there are periods where utilization decreases or goes to zero

![Low Utilization](./dips.png)

If your GPU has periods where it is not busy, this means the GPU is waiting for data in between batch or epoch cycles. Try one of the following tips:

- If your data is stored remotely, ensure you are prefetching your data and caching it locally. 
- Try to parallelize as much of your data fetching and data transformation steps as possible.
- Try loading in your data only after it has been preprocessed. In otherwords, preprocess your data independently using a non GPU-enabled VM.

## Some of the GPUs have higher utilization than other GPUs in a distributed run

![Low Some Nodes](./low-some-nodes.png)

If some nodes have much higher utilization than other nodes, this means there is likely a bottle neck in your network communication. Try one of the follow tips:

- Use the VM size with the maximum number of GPUs. For example, if you are training on NC-series, use a few Standard_NC24 nodes (which have 4 GPUs) rather than a lot of Standard_NC6 nodes (which have only 1 GPU).
- If you have quota, try using fewer GPUs that are faster and/or connected using NVLINK.
- Decrease the number nodes or try a different distributed strategy.