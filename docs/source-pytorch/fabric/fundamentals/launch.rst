:orphan:

###########################
Launch distributed training
###########################

To run your code distributed across many devices and/or across many machines, you need to do two things:

1. Configure Fabric with the number of devices and number of machines you want to use
2. Launch your code in multiple processes


----------


*******************
Launch with the CLI
*******************

The most convienent way to do all of the above is to run your Python script directly with the built-in command line interface (CLI):

.. code-block:: bash

    lightning run model path/to/your/script.py

This is essentially the same as running ``python path/to/your/script.py`` but it also lets you configure:

- ``--accelerator``: The accelerator to use
- ``--devices``: The number of devices to use (per machine)
- ``--num_nodes``: The number of machines (nodes) to use
- ``--precision``: Which type of precision to use
- ``--strategy``: The strategy (communication layer between processes)


.. code-block:: bash

    lightning run model --help

    Usage: lightning run model [OPTIONS] SCRIPT [SCRIPT_ARGS]...

      Run a Lightning Fabric script.

      SCRIPT is the path to the Python script with the code to run. The script
      must contain a Fabric object.

      SCRIPT_ARGS are the remaining arguments that you can pass to the script
      itself and are expected to be parsed there.

    Options:
      --accelerator [cpu|gpu|cuda|mps|tpu]
                                      The hardware accelerator to run on.
      --strategy [ddp|dp|deepspeed]   Strategy for how to run across multiple
                                      devices.
      --devices TEXT                  Number of devices to run on (``int``), which
                                      devices to run on (``list`` or ``str``), or
                                      ``'auto'``. The value applies per node.
      --num-nodes, --num_nodes INTEGER
                                      Number of machines (nodes) for distributed
                                      execution.
      --node-rank, --node_rank INTEGER
                                      The index of the machine (node) this command
                                      gets started on. Must be a number in the
                                      range 0, ..., num_nodes - 1.
      --main-address, --main_address TEXT
                                      The hostname or IP address of the main
                                      machine (usually the one with node_rank =
                                      0).
      --main-port, --main_port INTEGER
                                      The main port to connect to the main
                                      machine.
      --precision [64|32|16|bf16]     Double precision (``64``), full precision
                                      (``32``), half precision (``16``) or
                                      bfloat16 precision (``'bf16'``)
      --help                          Show this message and exit.



Here is how you run DDP with 8 GPUs and `torch.bfloat16 <https://pytorch.org/docs/1.10.0/generated/torch.Tensor.bfloat16.html>`_ precision:

.. code-block:: bash

    lightning run model ./path/to/train.py \
        --strategy=ddp \
        --devices=8 \
        --accelerator=cuda \
        --precision="bf16"

Or `DeepSpeed Zero3 <https://www.deepspeed.ai/2021/03/07/zero3-offload.html>`_ with mixed precision:

.. code-block:: bash

     lightning run model ./path/to/train.py \
        --strategy=deepspeed \
        --devices=8 \
        --accelerator=cuda \
        --precision=16

:class:`~lightning_fabric.fabric.Fabric` can also figure it out automatically for you!

.. code-block:: bash

    lightning run model ./path/to/train.py \
        --devices=auto \
        --accelerator=auto \
        --precision=16


----------


*******************
Programmatic Launch
*******************

It is also possible to launch the processses directly from within the Python script programmatically.
This is useful for debugging or when you want to build your own CLI around Fabric.

.. code-block:: python

    # train.py
    ...

    # Configure accelerator, devices, num_nodes, etc.
    fabric = Fabric(devices=4, ...)

    # This launches itself into multiple processes
    fabric.launch()


In the command line, you run this like any other Python script:

.. code-block:: bash

    python train.py


----------


************************
Launch inside a Notebook
************************

It is also possible to use Fabric in a Jupyter notebook (including Google Colab, Kaggle, etc.) and launch multiple processes there.
You can learn more about it :ref:`here <Fabric in Notebooks>`.
