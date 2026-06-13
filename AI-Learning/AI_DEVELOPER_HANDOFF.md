# AI Developer Handoff Document

Welcome! If you are reading this, you are taking over the AI pipeline for the KakshaPlus OMR Engine. We have transitioned the architecture from a purely Classical Computer Vision approach (OpenCV) to a hybrid approach utilizing Deep Learning (YOLOv8) for layout detection.

This document outlines the entire debugging journey we took to reach this point, and provides exact instructions on how to train and deploy the production YOLO model.

---

## Part 1: The Debugging Journey (What Happened Today)

Today, we faced a major issue where the OMR engine was failing to detect Questions 1 and 2, as well as the Roll Number grid, on a specific image uploaded from the Flutter app. 

Here is the step-by-step breakdown of how we fixed the pipeline:

1. **Threshold Tuning:** Initially, the system reported `Blank` for all questions. We discovered that the `bubble_darkness_threshold` in `config.json` was set to a rigid `250` (meaning only pure, absolute black pixels were counted). We lowered this to `180` to gracefully handle varying lighting conditions and pencil darkness. This immediately allowed the system to detect Q3-Q100 perfectly.
2. **The 50px Margin Bug:** We found that the `alignment_adapter.py` fallback logic (which triggers when corner registration marks are missing) was mapping the physical outer edges of the paper directly to `[0, 0]`. However, the JSON template coordinates expected a 50px margin around the grid. We updated the fallback logic to map the paper edges to `[50, 50]`, perfectly aligning the internal coordinates.
3. **Docker Compose Environment Fix:** We discovered that `docker-compose.yml` had hardcoded `environment:` variables (like `BUBBLE_DARKNESS_THRESHOLD=500`), which was silently overriding the `.env` configurations. We replaced these blocks with `env_file: .env` across all docker files to enforce a single source of truth.
4. **The Root Cause of Q1 & Q2 Failure:** Despite all fixes, Q1, Q2, and the Roll No grid were still failing on one specific image. We deduced this was a **perspective distortion caused by a curled paper edge**. Because the top-right registration mark was missing on the printed sheet, OpenCV fell back to tracing the physical white paper edge. Since the top-left edge of the paper was curled/folded backwards, OpenCV's perspective warp created a magnifying distortion isolated to the top-left corner, pushing the Q1 and Q2 bubbles outside of their invisible scanning boxes.
5. **The YOLOv8 Solution:** To make the system immune to curled paper edges and missing registration marks, we built a YOLOv8 integration. Instead of mathematically tracing the edges of the paper, YOLO simply draws a box around the MCQ Grid itself, allowing for a perfect crop regardless of the paper's physical shape.

*Note: We tested the YOLO pipeline using a Synthetic Data Generator, but because the model was trained on computer-generated drawings rather than real photos, it hallucinated on the real image. The YOLO hookup in `answer_extractor_service.py` is currently reverted pending training on real data.*

---

## Part 2: Instructions for the AI Developer

Your objective is to train a production-ready YOLOv8 model that can detect the `MCQ_GRID` in real-world mobile photos, and re-enable the YOLO integration in the main API.

### The "Domain Shift" Problem (Why 50 Epochs on Synthetic Data Failed)
You might wonder: *"We trained the model for 10 epochs on synthetic data. What if we just run it for 50 epochs? Will that fix it?"*
**Answer: No.** The issue is not the *number of epochs* (how long the AI studied), but the *type of data* it studied. Our `dataset_generator.py` created purely synthetic images (perfect black circles on pure white backgrounds). When the AI was suddenly shown a real photo from a mobile phone (grayish paper texture, shadows, wooden table background, lead reflections), it suffered from **"Domain Shift"**. Training for 50 epochs on synthetic data will only make it 5x better at reading MS Paint drawings, not real photos. This is why you **must** collect real data!

### 1. Data Collection & Annotation
The synthetic dataset generator (`src/modules/omr/yolo/dataset_generator.py`) is great for testing the pipeline, but **Deep Learning models need real-world variance**.

1. **Collect Data:** Gather 50 to 150 real photos of the filled OMR sheets. Ensure variance in lighting, shadows, camera angles, and backgrounds (e.g., wooden tables, dark sheets). Include photos where the paper is slightly folded or curled.
2. **Annotate Data:** Use a platform like [Roboflow](https://roboflow.com/) or [CVAT](https://cvat.ai/). Upload the images and draw tight bounding boxes around:
   - Class 0: `MCQ_GRID` (The entire box containing the 1-100 questions)
   - Class 1: `ROLL_NO_GRID` (Optional, but good for future proofing)
3. **Export:** Export the dataset in **YOLOv8 format**.

### 2. Training the Model (Using a Free Cloud GPU)
**Do not train on the CPU-only Hostinger VPS.** Deep learning requires a GPU. If you do not have a dedicated local GPU, you can train the YOLOv8 Nano model for free using **Google Colab** or **Kaggle Notebooks**.

#### Option A: Google Colab Instructions (Recommended)
1. Zip the dataset you exported from Roboflow (it should contain the `data.yaml` file and `train`/`val` folders). Name it `omr_dataset.zip`.
2. Open [colab.research.google.com](https://colab.research.google.com/) and create a "New Notebook".
3. In the top menu, go to **Runtime > Change runtime type** and select **T4 GPU**.
4. Click the "Folder" icon on the left sidebar and upload your `omr_dataset.zip` file.
5. Create a new code cell, paste the following training script, and click "Play" to run it:

```python
# 1. Install the required YOLO library
!pip install ultralytics -q

# 2. Unzip your dataset into the Colab environment
!unzip -q omr_dataset.zip -d /content/omr_dataset

# 3. Import YOLO and load the Nano model
from ultralytics import YOLO
model = YOLO('yolov8n.pt') 

# 4. Train the model for 50 epochs
# (Update the path to wherever your data.yaml file ended up after unzipping)
results = model.train(
    data='/content/omr_dataset/data.yaml', 
    epochs=50, 
    imgsz=640,
    project='omr_training',
    name='run1'
)

# 5. The trained model weights will be saved automatically.
print("Training Complete! You can download your best.pt file from the file explorer on the left.")
print("Path: /content/omr_training/run1/weights/best.pt")
```

6. Once training finishes, look in the file explorer on the left sidebar. Navigate to `/content/omr_training/run1/weights/`. 
7. Right-click `best.pt` and select **Download**.

### 3. Deployment
1. Rename your trained weights file to `best.pt`.
2. Upload `best.pt` to the UAT/Production server and place it precisely here:
   `/workspace/outputs/yolo_runs/omr_grid_detector/weights/best.pt` 
   *(You can use `docker cp` to push the file into the running container).*

### 4. Re-enabling the YOLO Pipeline
Once the real model is inside the container, you must re-hook the YOLO adapter into the main OMR engine.

Open `src/modules/omr/application/answer_extractor_service.py` and navigate to around Line 126 (`# STEP 3: Alignment`).

**Replace the existing classical alignment code:**
```python
        # STEP 3: Alignment (Corner Detection)
        aligned_img, align_status = align_image(img, thresh, t_config.get("target_width", 1240), t_config.get("target_height", 1754))
```

**With the hybrid YOLO -> Classical Fallback code:**
```python
        # STEP 3: Alignment (Deep Learning -> Fallback to Corner Detection)
        from src.modules.omr.infrastructure.yolo_alignment_adapter import align_image_yolo
        aligned_img, align_status = align_image_yolo(img, b_map, t_config)
        
        if aligned_img is None:
            logger.info("YOLO alignment skipped or failed. Falling back to classical 4-corner detection...")
            from src.modules.omr.infrastructure.alignment_adapter import align_image
            aligned_img, align_status = align_image(img, thresh, t_config.get("target_width", 1240), t_config.get("target_height", 1754))
```

### 5. Verification
Send a test image via the `/api/omr/process-single` endpoint. If the pipeline is working correctly, you will see the logs print:
`✅ YOLO successfully warped the image based on MCQ Grid detection!`

Congratulations, your OMR pipeline is now fully AI-powered and invincible!
