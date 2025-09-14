# SyllabusRAG 📘

SyllabusRAG helps you ask questions about a university syllabus easily.  
It reads a syllabus file, builds a knowledge base, and uses an AI model to answer questions.  

## How It Works ⚡

- The syllabus data is stored in a file called `syllabus.json`.  
- Each course is read and turned into a simple text summary.  
- These summaries are saved in `knowledge_base.txt`.  
- The summaries are converted into vector embeddings using **sentence-transformers**.  
- The vectors are stored in a **FAISS index** for fast similarity search.  
- A small AI model (`google/gemma-7b-it`) is loaded from Hugging Face.  
- When you ask a question:  
  - If you include a course code, the system retrieves that course directly.  
  - If not, it finds the closest course by comparing your question with the vector index.  
- The AI generates an answer based only on the matched course information.  
- If nothing relevant is found, it clearly responds that it cannot answer.  

## Folder Structure 📂
```text
.
├── data
│   └── syllabus.json
├── main.ipynb
├── requirements.txt
└── training_data
    ├── knowledge_base.txt
    └── faiss_index
        ├── index.faiss
        └── index.pkl
```


## Requirements 🛠️
To run the project, create a `requirements.txt` file with the following content:
```
transformers
torch
accelerate
bitsandbytes
langchain
langchain-community
sentence-transformers
faiss-cpu
langchain-huggingface
```

Then, run this command in your notebook or terminal:
```
!pip install -r requirements.txt
```


## Setup 🚀

1. Open Google Colab (recommended) or Jupyter Notebook with GPU support.  
2. Clone or download this repository and upload `main.ipynb` along with `syllabus.json`.  
3. Create a free [Hugging Face](https://huggingface.co) account and copy your access token.  
4. In Colab, click the 🔑 icon on the left, add a new secret named `HF_TOKEN`, and paste your token.  
5. Open `main.ipynb` and run all cells.  

This will:  
- Install required dependencies ⚙️
- Process the syllabus data 📑
- Build a FAISS index 📊
- Load the model 🤖
- Enable direct Q&A from the syllabus 💬

### Example 💡
```python
ask_question("What is the title for BECE309L?")
ask_question("What are the prerequisites for BECE309L?")
ask_question("What are the objectives of the Artifical Intelligence and Machine Learning course?")
```

✅ After completing these steps, your setup will be ready to query the syllabus!

## Limitations ⚠️

- The model is very **basic** and only lightly fine-tuned on a **small dataset**.  
- Data pre-processing is **raw and automated**, not carefully cleaned or standardized.  
- Because of this, the model can sometimes give **inaccurate or incomplete answers**.  
- It works best only for **specific, direct questions** related to the syllabus.  
- This project should be seen as a **beginner-friendly starting point**, not a production-ready system.  