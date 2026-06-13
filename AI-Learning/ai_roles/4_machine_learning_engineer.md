# Machine Learning Engineer (Applied AI)

## Introduction
A Machine Learning (ML) Engineer is the practitioner who actually *trains* the AI. They take powerful, open-source "foundation models" (like YOLO for vision, or Llama for text) and "fine-tune" them on company-specific data so the model can solve a highly specific business problem. They bridge the gap between theoretical AI research and practical software engineering.

## Syllabus (Learning Path)
1.  **Programming:** Advanced Python, Numpy, Pandas.
2.  **Deep Learning Frameworks:** PyTorch, TensorFlow, Keras.
3.  **Machine Learning Math:** Calculus (Gradients/Derivatives), Linear Algebra (Matrices), Probability.
4.  **Computer Vision (CV):** OpenCV, CNNs, Object Detection (YOLO), Image Segmentation.
5.  **Natural Language Processing (NLP):** HuggingFace Transformers, Tokenization, LoRA (Low-Rank Adaptation).
6.  **Model Optimization:** Quantization (reducing model size), Pruning.

## Roles and Responsibilities
*   Curate and format specialized training datasets.
*   Write training loops and fine-tune Neural Networks.
*   Experiment with different model architectures and hyperparameters (learning rates, batch sizes).
*   Evaluate model accuracy using metrics like F1-Score, mAP, or BLEU.

## Real-World Example

### Problem Statement
An EdTech company uses mobile phones to scan OMR (Optical Mark Recognition) bubbles. The existing mathematical algorithm fails when the paper is curled or photographed at an angle. They need an AI that can visually locate the specific `MCQ_GRID` on a piece of paper, regardless of folds or shadows.

### Solution Approach
Take a pre-trained, lightweight Computer Vision model (YOLOv8 Nano) and fine-tune it specifically on photos of the company's proprietary OMR sheets.

### The Steps
1.  **Data Generation:** Write a Python script using the `Albumentations` library to take a blank digital OMR template and generate 10,000 synthetic variations (adding fake shadows, blurring, and perspective warps).
2.  **Formatting:** Format the bounding box coordinates of the grid into YOLO format (`class x_center y_center width height`).
3.  **Training:** Write a PyTorch training loop to load `yolov8n.pt`. Feed the 10,000 images through the model for 50 "Epochs" using a GPU.
4.  **Monitoring:** Watch the "Loss Curve" in tools like TensorBoard. If the loss plateaus too early, adjust the Learning Rate.
5.  **Exporting:** Export the final trained weights (`best.pt`) and use Quantization to compress the file from 20MB to 6MB so it runs instantly on a mobile phone or cheap CPU server.

### Tech Stack
*   **Language:** Python
*   **Framework:** PyTorch, Ultralytics (YOLO)
*   **Data Augmentation:** OpenCV, Albumentations
*   **Hardware:** Nvidia GPU (for training)

### Algorithm / Architecture
**Convolutional Neural Networks (CNNs) & Gradient Descent:** The model uses CNNs to extract visual features (edges, corners). Gradient Descent is the mathematical algorithm used during training to calculate the "error" of the model's prediction and slowly update its internal weights to improve accuracy on the next try.
