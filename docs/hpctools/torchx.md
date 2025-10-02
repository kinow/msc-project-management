# Torchx

PyTorch TorchX was mentioned in the AI part of the HPC Tools. I think
there was a comment about it having issues to be used in the CESGA FT3,
so I added a mental note to try it if I had a chance, just to see what
was the error.

First, following the instructions of slide `AIHPC03 1.pdf`, one of the
last slides “Notebook pytorchDDP101” contains instructions for activating
the Python virtual environment and launching the notebook `pytorchDP101.ipynb`.

Executing the cells, you will have a `ddp.py` script that creates a single
model, an optimizer and run a few epochs to calculate the loss using
some sample data.

The notebook contains code to run this using `torchrun`.

```bash
$ !torchrun --nproc_per_node=2 ddp.py
[1] loss: 1.485
[1] loss: 1.404
[2] loss: 0.915
[2] loss: 0.820
[3] loss: 0.424
[3] loss: 0.419
[4] loss: 0.180
[4] loss: 0.176
[5] loss: 0.087
[5] loss: 0.092
[6] loss: 0.033
[6] loss: 0.035
[7] loss: 0.014
[7] loss: 0.013
[8] loss: 0.006
[8] loss: 0.006
[9] loss: 0.003
[9] loss: 0.002
[10] loss: 0.001
Finished Training
[10] loss: 0.001
Finished Training
[rank0]:[W1002 20:54:20.664574185 ProcessGroupNCCL.cpp:1538] Warning: WARNING: destroy_process_group() was not called before program exit, which can leak resources. For more info, please see https://pytorch.org/docs/stable/distributed.html#shutdown (function operator())
```

I added a few cells below the last cell of the notebook, and these commands
executed in those cells worked to launch the same with `torchx` and get a
somewhat similar output (although a lot more verbose):

```bash
!pip install torchx
...
...
...
!torchx run --scheduler local_cwd dist.ddp --cpu 2 --script ddp.py
torchx 2025-10-02 20:57:27 INFO     Tracker configurations: {}
torchx 2025-10-02 20:57:27 INFO     Log directory not set in scheduler cfg. Creating a temporary log dir that will be deleted on exit. To preserve log directory set the `log_dir` cfg option
torchx 2025-10-02 20:57:27 INFO     Log directory is: /scratch/1308307/torchx_d6e62rcl
torchx 2025-10-02 20:57:27 INFO     Waiting for the app to finish...
ddp/0 [0]:[1] loss: 2.046
ddp/0 [1]:[1] loss: 2.223
ddp/0 [0]:[2] loss: 1.091
ddp/0 [0]:[3] loss: 0.523
ddp/0 [0]:[4] loss: 0.224
ddp/0 [0]:[5] loss: 0.088
ddp/0 [0]:[6] loss: 0.038
ddp/0 [1]:[2] loss: 1.120
ddp/0 [1]:[3] loss: 0.518
ddp/0 [1]:[4] loss: 0.228
ddp/0 [1]:[5] loss: 0.093
ddp/0 [1]:[6] loss: 0.038
ddp/0 [0]:[7] loss: 0.016
ddp/0 [0]:[8] loss: 0.006
ddp/0 [0]:[9] loss: 0.003
ddp/0 [0]:[10] loss: 0.001
ddp/0 [0]:Finished Training
ddp/0 [1]:[7] loss: 0.016
ddp/0 [1]:[8] loss: 0.007
ddp/0 [1]:[9] loss: 0.003
ddp/0 [1]:[10] loss: 0.001
ddp/0 [1]:Finished Training
ddp/0 [0]:[rank0]:[W1002 20:57:32.418684829 ProcessGroupNCCL.cpp:1538] Warning: WARNING: destroy_process_group() was not called before program exit, which can leak resources. For more info, please see https://pytorch.org/docs/stable/distributed.html#shutdown (function operator())
torchx 2025-10-02 20:57:34 INFO     Job finished: SUCCEEDED
```

I am not sure yet how to parse the output of `torchx`, but at least it appears to work
OK on FT3, and the model + optimizer output converge towards a low value, 0.001.
