# The Taxonomy of AI: Types of Models & Networks

Once you understand the basic difference between Machine Learning (ML) and Deep Learning (DL), the next step is understanding the specific *types* of algorithms and networks within them. 

This guide breaks down the most important categories you need to know.

---

## 1. Types of Machine Learning (ML)

Machine Learning algorithms are generally categorized by *how* they learn from data.

### A. Supervised Learning
The computer is provided with data that is already labeled with the correct answers. It learns the relationship between the data and the answer.
*   **Classification:** Predicting a category. (e.g., *"Is this email Spam or Not Spam?"*, *"Will this student Pass or Fail?"*)
*   **Regression:** Predicting a continuous number. (e.g., *"Based on historical data, what will the school's electricity bill be next month?"*)

### B. Unsupervised Learning
The computer is given raw data with *no labels* or correct answers. Its job is to find hidden structures or patterns on its own.
*   **Clustering:** Grouping similar data points together. (e.g., An algorithm looks at all your student behavioral data and automatically groups students into 3 clusters without you telling it to. You then realize Cluster 1 is "Gifted Students" and Cluster 3 is "At-Risk Students".)

### C. Reinforcement Learning (RL)
The computer learns by trial and error in a simulated environment, receiving "rewards" for good actions and "punishments" for bad ones.
*   **Example:** Training an AI to play Chess or walk a robot dog. It tries millions of random moves, failing constantly, until it discovers the exact sequence that maximizes its reward score.

---

## 2. Types of Artificial Neural Networks (Deep Learning)

When dealing with complex unstructured data (images, audio, massive text), we use Deep Learning. Different types of data require different shapes of Neural Networks.

### A. Feedforward Neural Networks (FNN)
The simplest type of neural network. Information only moves in one direction: from input, through hidden layers, to output. 
*   **Best for:** Simple tabular data or basic classification tasks. Not used much for complex modern AI.

### B. Convolutional Neural Networks (CNN)
The undisputed king of **Computer Vision**. CNNs use mathematical "filters" to scan across an image to detect edges, shapes, and eventually complex objects.
*   **Best for:** Image processing, facial recognition, autonomous driving.
*   **Famous Example:** **YOLOv8** (You Only Look Once), which we use in Kaksha+ to instantly detect the MCQ Grid on a photographed OMR sheet.

### C. Recurrent Neural Networks (RNN & LSTM)
Networks designed to process sequential data where *order matters*. They have "memory" of previous inputs.
*   **Best for:** Time-series forecasting (stock market), heart rate monitoring, and older speech-to-text systems.
*   **Note:** RNNs used to be the standard for processing text, but they have largely been replaced by Transformers (see below) because RNNs are slow and "forget" early parts of long paragraphs.

---

## 3. Types of Generative AI & Advanced Architectures

Generative AI doesn't just analyze data; it creates *new* data.

### A. Transformers (The Engine of LLMs)
Invented by Google in 2017, the Transformer architecture revolutionized AI. Instead of reading a sentence word-by-word like an RNN, Transformers use an **"Attention Mechanism"** to look at every word in a paragraph simultaneously, understanding exactly how each word relates to the others.
*   **Best for:** Large Language Models (LLMs), translation, code generation.
*   **Famous Examples:** **GPT-4** (OpenAI), **Llama 3** (Meta), **Gemini** (Google).

### B. Diffusion Models
Instead of outputting text, Diffusion models generate highly detailed images. They work by taking an image of pure static TV noise and gradually "diffusing" (removing) the noise step-by-step until a perfectly clear image appears that matches your text prompt.
*   **Best for:** AI Image and Video Generation.
*   **Famous Examples:** **Midjourney**, **Stable Diffusion**, **DALL-E 3**.

### C. Mixture of Experts (MoE)
An advanced architecture used inside massive LLMs. Instead of a single giant "brain" trying to answer every question, an MoE model is composed of many smaller, specialized "expert" neural networks (e.g., one expert in math, one in coding, one in French). When you ask a question, a "Router" sends your prompt only to the relevant experts, drastically reducing server costs while maintaining high intelligence.
*   **Famous Example:** **GPT-4** and **Mixtral 8x7B** are widely known to be Mixture of Experts models.
