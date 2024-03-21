
environment.yml이 충돌로 인해 진행 되지 않아 아래의 코드로 환경을 설정함.
```python
conda install numpy=1.21
conda install pytorch==1.10 cudatoolkit=11.3 -c pytorch
conda install -c conda-forge marching_cubes #version=0.3.1
conda install -c conda-forge tqdm #version=4.66.2
conda install matplotlib #version=3.8.0
pip install opencv-python #version=4.9.0.80
conda install imageio #version=2.33.1
pip install numba #version=0.59.0
pip install tensorboardX #version=2.6.2.2
conda install configargparse #version=1.4
conda install -c conda-forge scikit-image
pip install --upgrade PyMCubes
```

scripts와 config내의 경로를 설정.
다음과 같이 입력하면 경로내의 문서가 바로 변환되어 저장되니 신중히 엔터를 누르시길.
문서에는 <absolute-path-to-code>과 같은 형식으로 입력 되어있다. 이를 sed명령어를 이용하여 변환하는 작업

예시) 절대 경로( /githubCode/eventNeRF, /anaconda3/envs/eventnerf)일 때 다음과 같이 작성
```
sed -i 's/<absolute-path-to-code>/\/githubCode\/eventNeRF/gi' configs/**/*.txt scripts/*.sh
sed -i 's/<path-to-conda-env>/\/anaconda3\/envs\/eventnerf/gi' scripts/*.sh
```
데이터 다운 받고 models.zip은 logs/ 파일을 만들어서 안에 저장하기. 아래와 같은 코드로 인해.
```python
f = os.path.join(args.basedir, args.expname, 'train_images.json')
```

ddp_mesh_nerf.py의 line 7에 skimage 라이브러리를 사용하는데 버전때문인지 없는 함수가 코드에 작성되어 있다. 따라서 작성자가 주석으로 해둔 pyMcube의 mcubes를 임포트 한다.
```python
import mcubes
# from skimage.measure import marching_cubes as mcubes
```

----
# [EventNeRF](https://4dqv.mpi-inf.mpg.de/EventNeRF/)
[Viktor Rudnev](https://twitter.com/realr00tman), [Mohamed Elgharib](https://people.mpi-inf.mpg.de/~elgharib/), [Christian Theobalt](https://www.mpi-inf.mpg.de/~theobalt/), [Vladislav Golyanik](https://people.mpi-inf.mpg.de/~golyanik/)

![EventNeRF: Neural Radiance Fields from a Single Colour Event Camera](demo/EventNeRF.gif)

Based on [NeRF-OSR codebase](https://github.com/r00tman/NeRF-OSR), which is based on [NeRF++ codebase](https://github.com/Kai-46/nerfplusplus) and inherits the same training data preprocessing and format.

## Data

Download the datasets from [here](https://nextcloud.mpi-klsb.mpg.de/index.php/s/xDqwRHiWKeSRyes).

Untar the downloaded archive into `data/` sub-folder in the code directory.

See NeRF++ sections on [data](https://github.com/Kai-46/nerfplusplus#data) and [COLMAP](https://github.com/Kai-46/nerfplusplus#generate-camera-parameters-intrinsics-and-poses-with-colmap-sfm) on how to create adapt a new dataset for training. 

Please contact us if you need to adapt your own event stream as it might need updates to the code.

## Create environment
--Change "ipython=7.30.0=py39hf3d152e_0" to "ipython" in line 93 of the environment.yml because of ResolnPackageNotFound error. 
```
conda env create --file environment.yml
conda activate eventnerf
```

## Training and Testing

Use the scripts from `scripts/` subfolder for training and testing.
Please replace `<absolute-path-to-code>` and `<path-to-conda-env>` in the `.sh` scripts and the corresponding `.txt` config file
To do so automatically for all of the files, you can use `sed`:
```
sed 's/<absolute-path-to-code>/\/your\/path/' configs/**/*.txt scripts/*.sh
sed 's/<path-to-conda-env>/\/your\/path/' scripts/*.sh
```

## Models

 - `configs/nerf/*`, `configs/lego1/*` -- synthetic data,
 - `configs/nextgen/*`, `configs/nextnextgen/*` -- real data (from the revised paper),
 - `configs/ablation/*` -- ablation studies,
 - `configs/altbase.txt` -- constant window length baseline,
 - `configs/angle/*` -- camera angle error robustness ablation,
 - `configs/noise/*` -- noise events robustness ablation,
 - `configs/deff/*` -- data efficiency ablation (varying amount of data by varying the simulated event threshold),
 - `configs/e2vid/*` -- synthetic data e2vid baseline,
 - `configs/real/*` -- real data (from the old version of the paper)

## Mesh Extraction

To extract the mesh from a trained model, run

```
ddp_mesh_nerf.py --config nerf/chair.txt
```

Replace `nerf/chair.txt` with the path to your trained model config.


## Evaluation
Please find the guide on evaluation, color-correction, and computing the metrics in [`metric/README.md`](https://github.com/r00tman/EventNeRF/blob/main/metric/README.md).

## Citation

Please cite our work if you use the code.

```
@InProceedings{rudnev2023eventnerf,
      title={EventNeRF: Neural Radiance Fields from a Single Colour Event Camera},
      author={Viktor Rudnev and Mohamed Elgharib and Christian Theobalt and Vladislav Golyanik},
      booktitle={Computer Vision and Pattern Recognition (CVPR)},
      year={2023}
}
```

## License

This work is licensed under the Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License. To view a copy of this license, visit [http://creativecommons.org/licenses/by-nc-sa/4.0/](http://creativecommons.org/licenses/by-nc-sa/4.0/) or send a letter to Creative Commons, PO Box 1866, Mountain View, CA 94042, USA.

