# SWIRIS: Stereo Vision Dataset for Instance Segmentation in Inland Water Reservoirs

SWIRIS is a publicly available stereoscopic vision dataset designed for instance segmentation in inland water reservoirs. The dataset focuses on challenging aquatic environments, including water surfaces, riparian vegetation, algae, and water–land transition zones, supporting applications such as autonomous navigation and cyanobacterial bloom monitoring.

---

## Main Contributions

- Construction of a novel **stereo vision dataset** captured with a **ZED 2 camera**
- 3011 high-resolution images (1080×640) extracted from video sequences
- Data collected in two representative Spanish reservoirs:
  - **Embalse del Guajaraz** (Toledo)
  - **Laguna del Campillo** (Community of Madrid)
- Manual and assisted annotation of five semantic classes:
  - Algae  
  - Water  
  - Water_Edge  
  - Reeds  
  - Background_Vegetation
- Baseline evaluation using **YOLO-seg**, **Mask R-CNN (Detectron2)** and **SAM2**
- Synthetic data generation using **Stable Diffusion** and **ControlNet**
- Deployment and benchmarking on **NVIDIA platforms**

---

## Dataset Access (Roboflow)

The SWIRIS dataset was annotated and managed using the **Roboflow platform**.  
All images, annotations, and dataset versions are hosted there.

**Dataset URL (Roboflow):**  
Baseline dataset: [SWIRIS-Dataset](https://app.roboflow.com/universidad-complutense-de-madrid-oqgje/swiris-dataset/8)

Available export formats:
- YOLOv8 Segmentation
- COCO Instance Segmentation
- Semantic masks (via conversion)

Dataset split:
- 70% training
- 20% validation
- 10% testing

> **Note:** The dataset itself is **not stored directly in this GitHub repository** to avoid duplication and large file storage issues. This repository focuses on **reproducibility, benchmarking, and training pipelines**.

---

## Synthetic Data Augmentation

To improve dataset robustness and generalization, synthetic images were generated from real samples using:

- **Stable Diffusion v1.5** (semantic-guided generation)
- **ControlNet (Canny)** for structure-preserving augmentation

Four experimental scenarios were evaluated:
- **S1:** Real data only    
  Roboflow: [Dataset version](https://app.roboflow.com/universidad-complutense-de-madrid-oqgje/swiris-dataset/8).
- **S2:** Real data + Stable Diffusion (+25%)   
  Training set augmented with photorealistic samples generated using Stable Diffusion.  
  Roboflow: [Dataset version](https://app.roboflow.com/universidad-complutense-de-madrid-oqgje/daserdetectron2/1).
- **S3:** Real + ControlNet (+25%)  
  Training set augmented with structure-preserving samples generated using ControlNet (Canny).  
  Roboflow: [Dataset version](https://app.roboflow.com/universidad-complutense-de-madrid-oqgje/swiris_controlnet/1). 
- **S4:** Real + Stable Diffusion + ControlNet (+50%)  
  Combined augmentation using both Stable Diffusion and ControlNet synthetic samples.  
  Drive: [Dataset version](https://drive.google.com/file/d/1-gBlRCbsXFhBdAJdJncYCSimnakpgQFH/view?usp=sharing).


Synthetic data is used **only in the training set** to ensure fair evaluation.

---

## Model Training and Reproducibility

### 1. YOLO-based Models (Ultralytics)

Models:
- YOLOv8n-seg
- YOLO11n-seg
- YOLO12n-seg
- YOLO26n-seg

Framework:
- https://github.com/ultralytics/ultralytics

Typical training configuration:
- Epochs: 200  
- Batch size: 16  
- Optimizer: SGD (momentum = 0.937)  
- Image size: 1080×640  

---

### 2. Mask R-CNN (Detectron2)

For Mask R-CNN training, **Detectron2** was used.

#### Setup

```bash
git clone https://github.com/facebookresearch/detectron2
cd detectron2
pip install -e .
```

### 3. SAM2 (Segment Anything Model v2)

For prompt-free instance segmentation, we fine-tuned SAM2 (Segment Anything Model v2) on the SWIRIS dataset using a multiclass training strategy. SAM2 is a prompt-based segmentation model; however, in this work it is adapted to operate without user prompts through supervised fine-tuning.

#### Setup

```bash
# Clone SAM2 repository
git clone https://github.com/facebookresearch/segment-anything-2 sam2
cd sam2
pip install -e .
cd ..

# Download pretrained weights
wget -O sam2/sam2_hiera_tiny.pt \
https://dl.fbaipublicfiles.com/segment_anything_2/072824/sam2_hiera_tiny.pt

```

## Example of quantitative comparison between models


| YOLOv8n-seg | YOLO11n-seg | YOLO12n-seg | YOLO26n-seg | Mask R-CNN | SAM2 |
|-------------|-------------|-------------|-------------|------------|------|
| ![](predict/yolov8base.jpg) | ![](predict/yolo11base.jpg) | ![](predict/yolo12base.jpg) | ![](predict/yolo26base.jpg) | ![](predict/D2base.jpg) | ![](predict/sam2base.png) |
| ![](predict/yolov8sd.jpg)   | ![](predict/yolo11sd.jpg)   | ![](predict/yolo12sd.jpg)   | ![](predict/yolo26sd.jpg)   | ![](predict/D2sd.jpg)   | ![](predict/sam2sd.png)   |
| ![](predict/yolov8cn.jpg)   | ![](predict/yolo11cn.jpg)   | ![](predict/yolo12cn.jpg)   | ![](predict/yolo26cn.jpg)   | ![](predict/D2cn.jpg)   | ![](predict/sam2cn.png)   |
| ![](predict/yolov8all.jpg)  | ![](predict/yolo11all.jpg)  | ![](predict/yolo12all.jpg)  | ![](predict/yolo26all.jpg)  | ![](predict/D2all.jpg)  | ![](predict/sam2all.png)  |


**Figure:** Row 1 corresponds to the models in scenario S1, row 2 to S2, row 3 to S3, and row 4 to S4.
To see more images with predictions, go to the directory [predict](https://github.com/Ljmn30/SWIRIS-Dataset/tree/main/predict).

## Publications

Title: "SWIRIS: Stereo Vision Dataset for Instance Segmentation in Inland Water Reservoirs".    
Journal:   

## Acknowledgements
This paper has been partially funded by the EU (FEDER), the Spanish MINECO under grants PID2021-126576NB-I00 and TED2021-130123B-I00 funded by MCIN/AEI/10.13039/501100011033 and by European Union "ERDF A way of making Europe" and the NextGenerationEU/PRT. J.L.M. thanks the National Secretariat of Science, Technology and Innovation (SENACYT) of Panama for financial support during the completion of his PhD.
