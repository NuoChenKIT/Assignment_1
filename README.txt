BMW_Assignment_1/
│
├── data/                   # Stores raw and processed data
│   └── .gitkeep
├── models/                 # Stores the fine-tuned model
│   └── .gitkeep
├── src/                    # Source code
│   ├── __init__.py
│   ├── scrape.py           # Step 1: Data Collection
│   ├── preprocess.py       # Step 1: Cleaning & Splitting
│   ├── train.py            # Step 2: Fine-tuning
│   └── evaluate.py         # Step 3: Evaluation & Inference
├── requirements.txt        # Dependencies
└── README.md               # Step 4: Documentation



# BMW Assignment 1

This project implements an end-to-end pipeline to fine-tune a small Language Model (GPT-2) on recent BMW Group press releases. The goal is to adapt a general-purpose model to the specific vocabulary and tone of BMW's corporate communications.


## How to Run

1.  **Install Dependencies:**
    ```bash
    pip install -r requirements.txt
    ```

2.  **Data Collection:**
    Scrapes the latest articles from the BMW PressClub.
    ```bash
    python src/scrape.py
    ```

3.  **Preprocessing:**
    Cleans the data and creates Training (90%) and Validation (10%) splits in JSON format.
    ```bash
    python src/preprocess.py
    ```

4.  **Training:**
    Fine-tunes GPT-2 on the processed data. Logs loss and eval metrics to the console.
    ```bash
    python src/train.py
    ```

5.  **Evaluation:**
    Calculates Perplexity and generates sample text based on BMW-themed prompts.
    ```bash
    python src/evaluate.py
    ```


### 1. Data Collection
I used `BeautifulSoup` over heavier tools like Selenium because the BMW press site is relatively static for text content. This keeps the pipeline lightweight. I chose to concatenate the "Title" and "Body" of the articles to ensure the model learns the relationship between headlines and content.

### 2. Model Selection
**Choice: GPT-2 (Small)**
*   **Reasoning:** While older than Llama 3 or Mistral, GPT-2 is perfectly sufficient for a proof-of-concept. It runs efficiently on consumer hardware (even CPUs) and requires minimal memory. Using a larger model would require quantization (QLoRA), adding complexity that distracts from the architectural logic of the pipeline.

### 3. Training Strategy
I utilized the Hugging Face `Trainer` API.
*   **Why:** It abstracts away the boilerplate of training loops (backward pass, optimizer step, learning rate scheduling) while adhering to industry best practices like gradient accumulation and automatic checkpointing.
*   **Configuration:** I used a low learning rate (`5e-5`) and a small batch size to prevent overfitting on this very small dataset.

### 4. Evaluation Metric
**Metric: Perplexity**
*   For Causal Language Models, Accuracy is not a useful metric (as there is no single "correct" next word). Perplexity measures how well the model predicts the sample data. A lower perplexity indicates the model has adapted to the domain language.

## Results Summary
*   **Training Loss:** showed a downward trend, indicating the model was successfully learning the dataset structure.
*   **Generation:** The model successfully mimics the corporate tone of BMW (using terms like "sustainability", "premium", "mobility"), though logical coherence is limited by the small model size and dataset.