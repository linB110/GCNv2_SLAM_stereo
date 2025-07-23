# 🌀 GCNv2\_SLAM-Stereo

A modified version of [GCNv2\_SLAM](https://github.com/jiexiong2016/GCNv2_SLAM) and [ORB\_SLAM2](https://github.com/raulmur/ORB_SLAM2) with **stereo camera** support and enhanced setup instructions.
This project enables real-time stereo visual SLAM using the GCNv2 keypoint extractor.

---

## 🚀 Features

* ✅ **Stereo camera support** for EuRoC dataset
* ✅ Enhanced build and environment setup (C++ & Python)
* ✅ Compatible with [TUM](https://vision.in.tum.de/data/datasets/rgbd-dataset) / [EuRoC](https://projects.asl.ethz.ch/datasets/doku.php?id=kmavvisualinertialdatasets)
* ✅ Visual keypoint matching inspection tool
* ✅ `evo_ape` trajectory evaluation ready

---

## 👨‍💻 My Contributions

This project is a major enhancement of the original [GCNv2\_SLAM](https://github.com/jiexiong2016/GCNv2_SLAM), focusing on **stereo SLAM integration** and **EuRoC dataset support**, which were not supported in the original version.

**Key contributions:**

* 🔄 **Extended RGB-D pipeline to stereo camera input**

  * Implemented stereo image stream parsing and keypoint matching
  * **Supported the EuRoC MAV dataset**

* 💡 **CMake and libtorch integration**

  * Set up GCNv2 with libtorch (PyTorch C++ API)

* 📊 **Created visualization scripts**

  * Keypoint accuracy and feature matching overlay
  * Output for qualitative inspection

This work demonstrates practical integration of learned feature extractors into a real-time stereo SLAM system.

---

## 📚 Technical Summary

This project bridges deep learning and classic SLAM by integrating the **GCNv2 graph-based keypoint extractor** into a **stereo visual SLAM** pipeline.

**Key Technologies:**

* **Stereo SLAM:** 3D point estimation from synchronized stereo pairs
* **GCNv2:** A learned graph-convolutional keypoint descriptor model
* **SLAM backend:** Adapted from ORB-SLAM2 (BA, map, keyframes)
* **Evaluation:** Pose accuracy benchmarking using `evo_ape`

---

## 🧪 Engineering Challenges & Solutions

* ❗ **Libtorch (C++) integration was ABI-sensitive**

  * ✅ Recompiled PyTorch 1.9.1 with CUDA 10.2, ensured C++14 compatibility and ABI consistency

* ❗ **GCNv2 only supported RGB-D in original repo**

  * ✅ Re-implemented frame pipeline to support stereo camera geometry 

* ❗ **EuRoC dataset requires accurate timestamp sync**

  * ✅ Developed `associate.py` for stereo alignment, interpolated missing timestamps if needed
  * 
* ❗ **Minor revision in model for modern pytorch usage**

  * ✅ To ensure compatibility with modern PyTorch (≥1.10), I modified parts of the model's traced forward() function that relied on legacy or deprecated behavior

---

## 📃 Environment & Dependencies

Tested on:

* **Ubuntu 18.04 LTS**
* **GPU:** NVIDIA RTX 2060
* **CUDA:** 10.2
* **cuDNN:** Compatible with CUDA 10.2
* **PyTorch (C++):** 1.9.1

### 📦 Required Downloads

```bash
# libtorch
wget https://download.pytorch.org/libtorch/cu102/libtorch-cxx11-abi-shared-with-deps-1.9.1+cu102.zip
unzip libtorch-cxx11-abi-shared-with-deps-1.9.1+cu102.zip
```

Also clone and build:

* [ORB\_SLAM2](https://github.com/raulmur/ORB_SLAM2)

---

## 🔧 Build GCNv2\_SLAM

Use the provided `CMakeLists.txt`, then run:

```bash
./build.sh
```

---

## 🥪 Test Keypoint Extractor

Python environment setup:

```bash
conda create -n gcnv2_env python=3.8 -y
conda activate gcnv2_env
conda install pytorch==1.10.2 torchvision==0.11.3 cudatoolkit=10.2 -c pytorch
pip install opencv-python matplotlib
```

Run test:

```bash
python show_accuracy.py
```

This will generate `GCN_matching/` folder with visual keypoint match overlays.

---

## 🎮 Run SLAM

### 💡 Model

Use model file:

```
model/gcn2_320x240.pt
```

### 🔄 Create Association File

```bash
python associate.py
```

### 📹 Run on TUM (RGB-D)

```bash
cd ~/GCN2
GCN_PATH=/home/user/GCNv2_SLAM/GCN2/gcn2_320x240.pt ./rgbd_gcn \
    /home/user/GCNv2_SLAM/Vocabulary/GCNvoc.bin \
    /home/user/GCNv2_SLAM/GCN2/TUM3.yaml \
    /home/user/dataset/TUM/rgbd_dataset_freiburg1_xyz \
    /home/user/dataset/TUM/rgbd_dataset_freiburg1_xyz/association.txt
```

### 🌍 Run on EuRoC (Stereo)

```bash
cd ~/GCN2
GCN_PATH=/home/user/GCNv2_SLAM/GCN2/gcn2_320x240.pt \
    ./stereo_gcn /home/user/GCNv2_SLAM/Vocabulary/GCNvoc.bin \
    /home/user/ORB_SLAM2/Examples/Stereo/EuRoC.yaml \
    /home/user/dataset/EuRoC/MH_01/mav0/cam0/data \
    /home/user/dataset/EuRoC/MH_01/mav0/cam1/data \
    /home/user/ORB_SLAM2/Examples/Stereo/EuRoC_TimeStamps/MH01.txt
```

---

## 📊 Evaluation with `evo`

```bash
evo_ape tum \
    /home/user/dataset/TUM/rgbd_dataset_freiburg1_xyz/groundtruth.txt \
    /home/user/GCNv2_SLAM/GCN2/KeyFrameTrajectory.txt \
    --align --plot
```

---

## 📁 Suggested Folder Structure

```
GCNv2_SLAM-Stereo/
├── GCN2/
│   ├── gcn2_320x240.pt
│   ├── TUM3.yaml
│   ├── KeyFrameTrajectory.txt
├── Vocabulary/
│   └── GCNvoc.bin
├── dataset/
│   └── TUM/...
├── build.sh
├── associate.py
├── show_accuracy.py
├── CMakeLists.txt
└── ...
```

---

## 📊 Benchmark Results

✅ : fully tracked | ❌ : tracking lost

| Dataset / Sequence             | ORB (nlevel=8) | ORB (nlevel=1) | 320x240.pt | 640x480.pt | aug.pt     | tiny.pt    |
| ------------------------------ | -------------- | -------------- | ---------- | ---------- | ---------- | ---------- |
| **TUM RGB-D**                  |                |                |            |            |            |            |
| rgbd\_dataset\_freiburg1\_desk | 0.018781 ✅     | 0.014945 ✅     | 0.036593 ❌ | 0.132776 ✅ | 0.020748 ❌ | 0.222797 ✅ |
| rgbd\_dataset\_freiburg1\_xyz  | 0.012081 ✅     | 0.009779 ✅     | 0.080495 ✅ | 0.014587 ✅ | 0.088993 ✅ | 0.084459 ✅ |
| **EuRoC Stereo**               |                |                |            |            |            |            |
| MH\_01                         | 0.037540 ✅     | 0.040972 ✅     | 0.018083 ❌ | 0.175981 ✅ | 0.014021 ❌ | 0.038764 ❌ |
| MH\_05                         | 0.047538 ✅     | 0.188428 ✅     | 0.544578 ❌ | 0.892108 ❌ | 0.558102 ❌ | 0.052919 ❌ |

---

## 🙏 Acknowledgements

This project builds on:

* [ORB\_SLAM2](https://github.com/raulmur/ORB_SLAM2)
* [GCNv2\_SLAM](https://github.com/jiexiong2016/GCNv2_SLAM)

Stereo support and evaluation pipelines were added in this fork.

---

## 📌 What I Learned

* Deep integration of learned vision models in real-time SLAM systems
* Debugging stereo frame synchronization and calibration
* Understanding SLAM frontend-backend architecture
* Quantitative evaluation and reproducible research practices

---

Pull requests are welcome! Feel free to fork and build on this project.
