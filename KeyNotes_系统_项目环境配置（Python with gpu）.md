> 本文档——更偏向**系统**
> 
> ==**"KeyNotes_理论_CUDA & （Python with gpu）环境中cpp扩展的编译"**==——更偏向**理论**
> 
> ==**"KeyNotes_实践_项目环境配置-编译cpp扩展"**==——更偏向**实践**

```
硬件 (RTX 5060Ti——Blackwell架构，CUDA GPU 计算能力12.0)
 ├─-> 运行要求-需要调用gpu的cpp扩展: 如果conda环境中的cpp扩展需要调用gpu，就需要编译 
 sm_120 的 SASS(`setup.py` 中的` extra_compile_args `在编译阶段指定 GPU 架构，如`-
 gencode=arch=compute_120,code=sm_120)
 └─-> 硬件约束(固有属性): 无论何时，pytorch ≥ 2.7.0；cuda ≥ 12.8

系统 (Ubuntu / WSL)
 ├─ NVIDIA Driver (Windows端)
 │    └─ 版本必须 >= CUDA版本，即限制最高cuda版本 (越新越好，向下兼容所有旧版)
 │
 ├─ System CUDA Toolkit (Linux端)
 │    ├─ 作用: 包含 NVCC, 仅用于"自编译 C++ 扩展"
 │    ├─ 硬件约束(固有属性): 必须 >= 12.8 (为了生成 sm_120 代码)
 │    └─ 配置: 可以多版本并存，通过 ~/.bashrc 的 PATH 或者conda环境变量配置决定由于版本生效
 │    /usr/local/cuda-12.8
 │    ├─ nvcc
 │    ├─ include
 │    └─ lib64
 │
 └─ 调用gpu的Conda 环境
      └─ PyTorch (Pip/Conda install)(必须)(自带 CUDA Runtime (libs))(整个conda环境运行
      都调用这个CUDA Runtime)
           ├─ 情况1: 无自编译cpp扩展
           │    ├─ ps:不依赖: System CUDA Toolkit (NVCC)
           │    ├─ 硬件约束(固有属性)-1.pytorch ≥ 2.7.0 with ≥ cu128(CUDA
           │     Runtime>=12.8);2.英伟达 Driver版本够新
           │    └─ 项目约束-(conda级)Pytorch-CUDA Runtime: 推荐什么版本的pytorch
           │
           └─ 情况2：有自编译 Extension (如 flash-attn, custom ops)
                ├─ 依赖: System CUDA Toolkit (NVCC)
                ├─ 硬件约束(固有属性)-1.pytorch ≥ 2.7.0 with ≥ cu128(CUDA
                Runtime>=12.8);2.英伟达 Driver版本够新
                ├─ 项目约束-(conda级)Pytorch-CUDA Runtime: 推荐什么版本的pytorch
                └─ 兼容性约束-(System级) CUDA Toolkit:1.CUDA Toolkit=CUDA Runtime(至
                少大版本一致);2.需要编译 sm_120 的 SASS(`setup.py`中的
                `extra_compile_args`在编译阶段指定GPU架构:如`-
                gencode=arch=compute_120,code=sm_120)
```


**PS:**
1. **(System级) CUDA Toolkit 的环境变量配置**
- System CUDA Toolkit 12.8 路径： `/usr/local/cuda-12.8`。
- 法一：（系统级）配置： `~/.bashrc`:
```bash
export PATH=/usr/local/cuda-12.8/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-12.8/lib64:$LD_LIBRARY_PATH
export CUDA_HOME=/usr/local/cuda-12.8
```
- 法二：（conda级）配置： `~/miniconda3/envs/phmr/etc/conda/activate.d/env_vars.sh` 以及 `~/miniconda3/envs/phmr/etc/conda/deactivate.d/env_vars.sh`

> 详细方法，参考==**"KeyNotes_项目环境配置-编译cpp扩展"

2. **install pytorch**
```bash
pip install torch --index-url https://download.pytorch.org/whl/cu128
```




