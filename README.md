Facebook DarkForest Go Project
========

Build
------------
Dependencies: 

1. Install [torch7](http://torch.ch/docs/getting-started.html).
2. [Install CUDA / CuDNN](https://github.com/facebook/fb.resnet.torch/blob/master/INSTALL.md)
2. Install a few packages
```bash
luarocks install class
luarocks install image
luarocks install tds
luarocks install cudnn
```
 This program supports 1 to 4 GPUs.

Then just compile with the following command:

```bash
sh ./compile.sh
```

GCC 4.8+ is required. Depending on the location of your C++ compiler, please change the script accordingly. 
Tested in CentOS 6.5 and Ubuntu 14.04.

Usage
------------
Step 1: Download the models.  

Create `./models` directory and download trained [models](https://www.dropbox.com/sh/6nm8g8z163omb9f/AABQxJyV7EIdbHKd9rnPQGnha?dl=0).

Step 2: First run the GPU server   

```bash
cd ./local_evaluator     
sh cnn_evaluator.sh [num_gpu] [pipe file path]
```

* `num_gpu`         the number of GPUs (1-8) you have for the current machine. 
* `pipe file path`  The path that the pipe file is settled. Default is `/data/local/go`. If you have specific other path, then you need to specify the same when running `cnnPlayerMCTSV2.lua`

Example: `sh cnn_evaluator.sh 4 /data/local/go`

Step 3: Run the main program

```bash
cd ./cnnPlayerV2     
th cnnPlayerMCTSV2.lua [options]
```

See `cnnPlayerV2/cnnPlayerMCTSV2.lua` for a lot of options. For a simple first run (assuming you have 4 GPUs), you could use:

```bash
th cnnPlayerMCTSV2.lua --num_gpu [num_gpu] --time_limits 10
```   
or

```bash
th cnnPlayerMCTSV2.lua --use_formal_params --time_limits 10
```   

To load an existing game up to move 23:
```bash
th cnnPlayerMCTSV2.lua [other_options] --setup_board "/path/to/sgf 23"
```   

When you are in the interactive environment, type 

* `clear_board` to clear the board
* `genmove b`    to genmove the black move.
* `play w Q4`    to play a move at Q4 for specific color.
* `quit`         to quit.

A complete game may look like:

```bash
clear_board
[MCTS initialization ...]
place_free_handicap 3
genmove b 
[MCTS generates moves..e.g., it returns Q16]
play w D4
genmove b
[MCTS generates moves...]
quit
```

For more commands, please use command `list_commands`, check the details of [GTP protocol](http://senseis.xmp.net/?GTP) or take a look at the source code.

Award
--------------
* Stable **KGS 5d**. [link](http://www.gokgs.com/graphPage.jsp?user=darkfmcts3)
* 3rd place in KGS Go Tournament. [link](http://www.weddslist.com/kgs/past/119/index.html)
* 2nd place in UEC Computer Go Cup. [link](http://jsb.cs.uec.ac.jp/~igo/eng/result2.html)

Trouble Shooting 
----------------
**Q**: My program hanged on genmove/quit, what happened?  
**A**: Make sure you run the GPU server under ./local\_evaluator, the server remains active and the pipe file path matches between the server and the client.

If you have any questions or find any bugs, please **open a Github issue** by clicking *"Issues"* tab and then click *"New Issue"*.

Code Overview
-------------

The system consists of the following parts. 

* `./CNNPlayerV2`  
Lua (terminal) interface for Go.   

1. `CNNPlayerV3.lua`              Run Pure-DCNN player
2. `CNNPlayerMCTSV2.lua`          Run player with DCNN + MCTS

* `./board`   
Things about board and its evaluations. Board data structure and different playout policy.

* `./mctsv2`  
Implementation of Monte Carlo Tree Search

* `./local_evaluator`  
Simple GPU-based server. Communication with search threads via pipe. 

* `./utils`  
Simple utilities, e.g., read/write sgf files.

* `./test`  
Test utilities.

* `./train`  
Training code (will be released soon).

* `./models`  
All pre-trained models. Please download them [here](https://www.dropbox.com/sh/6nm8g8z163omb9f/AABQxJyV7EIdbHKd9rnPQGnha?dl=0) and save to the `./models` directory.

* `./sgfs`
Some exemplar sgf files.

License
----------
Please check the LICENSE file for the license of Facebook DarkForest Go engine. 

Acknowledgement
---------
Although DarkForest is standalone and does not depend on external libraries, some portions of the tactics and pattern code were inspired by Pachi [engine](https://github.com/pasky/pachi).

Reference
----------
If you use the pre-trained models or any engine, please reference the following paper:

```
Better Computer Go Player with Neural Network and Long-term Prediction, ICLR 2016  
Yuandong Tian, Yan Zhu

@article{tian2015better,
  title={Better Computer Go Player with Neural Network and Long-term Prediction},
  author={Tian, Yuandong and Zhu, Yan},
  journal={arXiv preprint arXiv:1511.06410},
  year={2015}
}


```

Here is the arxiv [link](http://arxiv.org/abs/1511.06410)


