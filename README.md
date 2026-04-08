# Log Classification System — Hybrid AI Pipeline

## 📌 Overview

This project implements a **hybrid log classification system** that combines **rule-based methods, embedding-based machine learning, and LLM-based reasoning** in a cascaded pipeline.

The system is designed to:
- Efficiently classify structured and unstructured logs  
- Handle unseen or ambiguous log patterns  
- Balance **latency, cost, and accuracy** in a production-style setup<br>


## 🧠 Architecture

### Cascaded Inference Pipeline
Log Message<br>
↓<br>
Regex Classification (Rule-Based)<br>
↓ (if no match)<br>
Embedding + Classifier (BERT-based MiniLM)<br>
↓ (if low confidence / special source)<br>
LLM Classification (LLaMA 3)<br>



## ⚙️ Core Components

### 1. Rule-Based Classification (Regex)

- Uses predefined regex patterns for structured logs  
- Handles common categories such as:
  - User actions  
  - System notifications  
- Acts as the **fastest layer** in the pipeline  

**Characteristics:**
- Deterministic  
- No training required  
- High precision for known patterns<br>


### 2. BERT-Based Embedding + Classification

#### Model  
- all-MiniLM-L6-v2 (Sentence Transformers)  
- A **distilled BERT-family model (SBERT architecture)**  

#### Approach  
- Convert log messages into **dense semantic embeddings**  
- Use a **supervised classifier** (loaded via `joblib`) on top of embeddings  
log → embedding (MiniLM) → classifier → label

#### Why this approach  
- Captures semantic meaning of logs  
- Handles variations in log format  
- More efficient than full BERT fine-tuning  <br>

### Confidence Thresholding

- Uses prediction probabilities to filter low-confidence outputs  

python
if max(probabilities) < 0.5:
    return "Unclassified"<br>

### 3. LLM-Based Classification (Fallback Layer)
#### Model
- LLaMA 3 (via Groq API)
####Method
- Prompt-based classification
- Structured output enforced using tags

#### Role
Handles:
- Ambiguous logs
- Rare/unseen patterns
- Inconsistent legacy logs<br>



### 4. Source-Aware Routing
if source == "LegacyCRM":
    use LLM
else:
    use Regex → ML → fallback
   
- Legacy systems → directly handled by LLM
- Modern systems → processed through efficient pipeline<br>

#### 🔍 Unsupervised Learning (DBSCAN)
- Algorithm
  - DBSCAN (Density-Based Clustering)
- Usage
  - Applied during data exploration phase
- Purpose
  - Identify clusters of similar logs
  - Detect outliers (noise points)
- Impact
  - Improved understanding of log distribution
  - Assisted in labeling strategy
  - Helped identify edge cases handled by LLM<br>



## 🛠️ Tech Stack
#### AI / ML
Sentence Transformers (MiniLM - BERT-based)
Scikit-learn
DBSCAN (clustering)
#### LLM
LLaMA 3 (Groq API)
Prompt Engineering
Regex-based output parsing
#### Backend
FastAPI
Pandas
Python
#### Utilities
Joblib (model loading)
dotenv (environment variables)<br>

## 📊 Key Features
- Hybrid pipeline (Rule-based + ML + LLM)
- BERT-based semantic embeddings for log understanding
- Confidence-based classification filtering
- Outlier-aware design using DBSCAN insights
- Source-aware intelligent routing
- Batch processing via API<br>

## 📈 Future Improvements
- Fine-tuning transformer models on log-specific data
- Active learning for continuous model updates
- LLM response caching
- Real-time log streaming support
- Model monitoring and evaluation metrics<br>
