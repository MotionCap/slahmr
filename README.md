# Decoupling Human and Camera Motion from Videos in the Wild

Official PyTorch implementation of the paper Decoupling Human and Camera Motion from Videos in the Wild

[Project page](https://vye16.github.io/slahmr/) | [ArXiv](https://arxiv.org/abs/2302.12827)

<img src="./teaser.png">

##  [<img src="https://i.imgur.com/QCojoJk.png" width="40"> You can run SLAHMR in Google Colab](https://colab.research.google.com/drive/1knzxW3XuxiaBH6hcwx01cs6DfA4azv5E?usp=sharing)

## News

- [2023/07] We updated the code to support tracking from [4D Humans](https://shubham-goel.github.io/4dhumans/)! The original code remains in the `release` branch.
- [2023/02] Original release!

## Getting started
This code was tested on Ubuntu 22.04 LTS and requires a CUDA-capable GPU.

1. Clone repository and submodules
    ```
    git clone --recursive https://github.com/vye16/slahmr.git
    ```
    or initialize submodules if already cloned
    ```
    git submodule update --init --recursive
    ```

2. Set up conda environment. Run
    ```
    source install.sh
    ```

    <details>
        <summary>We also include the following steps for trouble-shooting.</summary>

    * Create environment
        ```
        conda env create -f env.yaml
        conda activate slahmr
        ```
        We use PyTorch 1.13.0 with CUDA 11.7. Please modify according to your setup; we've tested successfully for PyTorch 1.11 as well.
        We've also included `env_build.yaml` to speed up installation using already-solved dependencies, though it might not be compatible with your CUDA driver.

    * Install PHALP
        ```
        pip install phalp[all]@git+https://github.com/brjathu/PHALP.git
        ```

    * Install current source repo
        ```
        pip install -e .
        ```

    * Install ViTPose
        ```
        pip install -v -e third-party/ViTPose
        ```

    * Install DROID-SLAM (will take a while)
        ```
        cd third-party/DROID-SLAM
        python setup.py install
        ```
    </details>

3. Download models from [here](https://drive.google.com/file/d/1GXAd-45GzGYNENKgQxFQ4PHrBp8wDRlW/view?usp=sharing). Run
    ```
    ./download_models.sh
    ```
    or
    ```
    gdown https://drive.google.com/uc?id=1GXAd-45GzGYNENKgQxFQ4PHrBp8wDRlW
    unzip -q slahmr_dependencies.zip
    rm slahmr_dependencies.zip
    ```

    All models and checkpoints should have been unpacked in `_DATA`.


## Fitting to an RGB video:
For a custom video, you can edit the config file: `slahmr/confs/data/video.yaml`.
Then, from the `slahmr` directory, you can run:
```
python run_opt.py data=video run_opt=True run_vis=True
```

We use hydra to launch experiments, and all parameters can be found in `slahmr/confs/config.yaml`.
If you would like to update any aspect of logging or optimization tuning, update the relevant config files.

By default, we will log each run to `outputs/video-val/<DATE>/<VIDEO_NAME>`.
Each stage of optimization will produce a separate subdirectory, each of which will contain outputs saved throughout the optimization
and rendered videos of the final result for that stage of optimization.
The `motion_chunks` directory contains the outputs of the final stage of optimization,
`root_fit` and `smooth_fit` contain outputs of short, intermediate stages of optimization,
and `init` contains the initialized outputs before optimization.

We've provided a `run_vis.py` script for running visualization from logs after optimization.
From the `slahmr` directory, run
```
python run_vis.py --log_root <LOG_ROOT>
```
and it will visualize all log subdirectories in `<LOG_ROOT>`.
Each output npz file will contain the SMPL parameters for all optimized people, the camera intrinsics and extrinsics.
The `motion_chunks` output will contain additional predictions from the motion prior.
Please see `run_vis.py` for how to extract the people meshes from the output parameters.


## Export to FBX:
Export human pose to fbx, run
```
python export_fbx.py --input=/homeL/cong/HitLyn/slahmr/outputs/logs/video-val/2023-04-29/skating-all-shot-0-0--1/motion_chunks/last_iteration.npz --output=/homeL/cong/HitLyn/slahmr/outputs/logs/video-val/2023-04-29/skating-all-shot-0-0--1/motion_chunks/output.fbx --gender=male
```
Export camera pose to fbx, run

```
python export_camera.py
```


## BibTeX

If you use our code in your research, please cite the following paper:
```
@inproceedings{ye2023slahmr,
    title={Decoupling Human and Camera Motion from Videos in the Wild},
    author={Ye, Vickie and Pavlakos, Georgios and Malik, Jitendra and Kanazawa, Angjoo},
    booktitle={IEEE Conference on Computer Vision and Pattern Recognition (CVPR)},
    month={June},
    year={2023}
}
