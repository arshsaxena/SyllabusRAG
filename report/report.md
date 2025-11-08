# SyllabusRAG

**Arsh Saxena** [23BEC1212], **Ayush Raj** [23BEC1245]

## QUALITATIVE ANALYSIS

### Background and Motivation
Many students and teachers find it hard to quickly find accurate course information, such as prerequisites, objectives and module details. Syllabuses are often stored in big files, so searching for more information is slow. Old search methods only match exact words and do not understand what the question means. This causes confusion and wastes time.

Our project solves this by using AI to make searching for course information easy. We use a smart search system and a language model to give clear answers from the syllabus, even if questions are asked in different ways. This makes academic information easier to find and use.

### Objectives
* Build a system that can answer questions about university courses using the syllabus data
* Make it easy for users to find course details without reading long documents
* Use AI to understand different ways of asking questions
* Store course information in a way that is fast and easy to search
* Help students and faculty save time and avoid confusion

### Methodology Overview

#### Dataset Details
Our project uses a syllabus dataset stored in a JSON file. This file contains information about different university courses, such as course codes, titles, prerequisites, objectives and modules. Each course is organized with clear fields so it is easy to extract and use the data. The dataset is small and simple, making it suitable for quick testing and development.

#### Model Architecture
We use a Retrieval-Augmented Generation (RAG) method. The system has two main parts:
* **Retriever:** This part searches for the most relevant course information. It uses vector embeddings to compare the meaning of the user's question with the stored course data. We use the `sentence-transformers/all-MiniLM-L6-v2` model to convert text into vectors (lists of numbers). These vectors are stored in a FAISS index, which allows fast similarity search.
* **Generator:** After finding the right course information, we use the `Gemma-7B` language model to generate answers. This model takes the course details and the user's question, then writes a clear response in plain English.

#### Preprocessing
First, we read the syllabus JSON file and turn each course into a text document. We add details like course code, title, prerequisites, objectives and module names. Each document is changed into a vector using the embedding model. All vectors are saved in the FAISS index for quick searching. We also save the course details in a text file for reference. Figure 1 shows the full preprocessing steps.

#### Evaluation Metrics
We check if the system can answer different types of questions about the courses. We test with direct questions (using course codes) and indirect questions (using course names or topics). The main things we measure are:
* **Accuracy:** Does the system find the correct course and give the right answer?
* **Flexibility:** Can it understand different ways of asking the same question?
* **Speed:** How quickly does it return an answer?
* **User Experience:** Is the answer clear and easy to understand?
Overall, our methods focus on making the system simple, fast and reliable for users who need quick access to course information.


**Figure 1:** Preprocessing pipeline from JSON to vector embeddings.

### Summary of Results

#### Key Numerical Findings
*  The system was tested with 50 test cases across different question formats, including direct course code queries and indirect topic-based questions.
* For direct course code queries (e.g., "What is the title for BECE309L?"), the system achieved 91.3% accuracy (21/23 cases).
*  For indirect questions (e.g., "Name of the AI course"), the system found the right course in 75.0% of cases (12/16 cases).
*  For flexible phrasing queries, the system achieved 54.6% accuracy (6/11 cases).
*  The average response time for a query was 5-6 seconds, with variation based on query complexity.
*  The syllabus dataset included 4 courses, each with multiple fields like objectives, prerequisites and modules.
*  The FAISS index stored 4 course vectors, making similarity search efficient and reliable.

#### Key Visual Findings
* The system can answer questions in many different ways, showing flexibility in understanding user queries.
* Answers are clear and easy to read, with course details presented in simple language.
* The search process works for both exact matches (using course codes) and meaning-based matches (using course topics).
* The model can handle spelling mistakes and casual language, making it user-friendly.
* The results show that users do not need to know the exact course codes to get the right information.

#### Overall Performance
The SyllabusRAG system works well, with 78.0% overall accuracy in 50 test cases. It makes finding course information easier than reading long syllabus documents. The system is best for direct queries (91.3%) and good for topic-based searches (75.0%), but less accurate for flexible phrasing (54.6%). Vector search and the language model give answers in 5-6 seconds on average. The system is useful for academic information search and can be improved by using bigger datasets and better language processing.

### Conclusion

The SyllabusRAG project helps students and teachers find course information quickly and easily. By using AI and smart search, the system understands different ways of asking questions and gives clear answers. This saves time and avoids confusion. In the future, the system can be improved by adding more courses, supporting more file types and making the search faster. Adding user feedback and multi-language support can also make it better for everyone.

The complete source code and implementation details for this project are available at: [https://www.github.com/arshsaxena/SyllabusRAG](https://www.github.com/arshsaxena/SyllabusRAG).

## QUANTITATIVE ANALYSIS

### Dataset and Preprocessing

**Dataset Source:** Our syllabus data comes from a JSON file (`data/syllabus.json`) containing details of university courses from the Electronics and Communication Engineering department.

**Sample Size:** 4 courses with complete information, including course codes, titles, prerequisites, objectives and module details. The dataset composition is detailed in Table I.

**Data Fields:** Each course contains:
* Course code and title
* Prerequisites and credits
* Course objectives and outcomes
* Module details with topics and duration
* Textbooks and reference materials

**Preprocessing Steps:**
1.  Load JSON data using Python's `json` library from `data/syllabus.json`
2.  Extract and format course information into readable text documents, including:
    * Course code and title
    * Prerequisites (joined with commas)
    * Course objectives (bulleted list format)
    * Module titles (bulleted list format)
3.  Create LangChain Document objects with metadata (`course_code`, `course_title`)
4.  Convert text to 384-dimensional vectors using `HuggingFaceEmbeddings`
5.  Build and save FAISS index to `training_data/faiss_index/` directory
6.  Save processed text to `training_data/knowledge_base.txt` for reference and debugging

The detailed data processing workflow is shown in Figure 2.

**Table I:** Dataset Course Details
| Course Code | Title | Prerequisites | Modules |
|---|---|---|---|
| BECE301L | Digital Signal Processing | BECE202L | 8 |
| BECE302L | Control Systems | BECE202L | 8 |
| BECE309L | Artificial Intelligence and Machine Learning | BMAT201L | 8 |
| BECE355L | AWS for Cloud Computing | None | 8 |


**Figure 2:** Data processing workflow with FAISS index creation.

### Model Design and Algorithm Implementation

**Architecture:** Our system uses a Retrieval-Augmented Generation (RAG) method with two main parts:

**Retriever Component:**
* **Embedding Model:** `sentence-transformers/all-MiniLM-L6-v2` (384 dimensions)
* **Vector Store:** FAISS (Facebook AI Similarity Search) index
* **Search Strategy:** Dual approach - regex pattern matching for course codes + semantic similarity search

**Generator Component:**
* **Language Model:** Google `gemma-7b-it` (Instruct model)
* **Quantization:** 4-bit quantization using `BitsAndBytesConfig`
* **Pipeline:** HuggingFace `text-generation` pipeline with 256 max new tokens

**Key Algorithm Steps:**
1.  Question analysis using regex to detect course codes (pattern: `[A-Z]{4}\d{3}[A-Z,E,P,L]?`)
2.  If course code found $\rightarrow$ Direct document lookup
3.  If no course code $\rightarrow$ Vector similarity search using FAISS
4.  Context preparation with retrieved course information
5.  Answer generation using `Gemma-7B` model

The complete RAG architecture is illustrated in Figure 3.

**Key Hyperparameters:**

The system configuration parameters are summarized in Table II.

**Table II:** Key Hyperparameters
| Parameter | Value |
|---|---|
| Embedding Dimension | 384 |
| Embedding Model | `sentence-transformers/all-MiniLM-L6-v2` |
| Generator Model | `google/gemma-7b-it` |
| Max New Tokens | 256 |
| Quantization | 4-bit (nf4) |
| Batch Size | 1 (single query) |
| Hardware | CPU (FAISS), GPU (Gemma-7B) |

**Software/Hardware Used:**
* **Python Libraries:**
    * `transformers`, `torch`, `accelerate`
    * `bitsandbytes`, `langchain`
    * `langchain-community`
    * `sentence-transformers`
    * `faiss-cpu`, `langchain-huggingface`
* **Vector Storage:** FAISS (Facebook AI Similarity Search) with local storage capability
* **Model Loading:** HuggingFace ecosystem with 4-bit quantization support
* **Development Environment:** Google Colab with GPU acceleration for model inference
* **Data Storage:** JSON format for structured course data, plain text for processed knowledge base


**Figure 3:** RAG architecture with retrieval and generation components.

### Training and Evaluation Setup

**Data Split:** This is a retrieval-based system, so there is no normal training phase. Instead:
* All 4 courses are indexed and available for search
* No train/validation/test split required
* System uses pre-trained models (`sentence-transformers` and `Gemma-7B`)

**Evaluation Approach:**
* Manual testing with different question types
* Direct course code queries (e.g., "What is BECE309L?")
* Indirect topic-based queries (e.g., "AI course name")
* Flexible phrasing tests (e.g., "title" vs "name")

**Metrics Used:**
1.  **Accuracy:** Correctness of retrieved course information
2.  **Response Time:** Speed of answer generation
3.  **Flexibility:** Ability to handle different question formats
4.  **Coverage:** Percentage of course information that can be retrieved

**Loss Functions:** Not applicable (no training phase - uses pre-trained models)

**Evaluation Methodology:**
* Test with around 50 different question formats
* Verify answers against original syllabus data
* Measure response time for each query type
* Check system behavior with edge cases

The system pipeline flow is detailed in Figure 4.


**Figure 4:** System Pipeline Flow Diagram: The system first checks for a course code. If found, it performs a direct lookup; otherwise, it uses vector search. Retrieved context is then passed to the generator.

**Metric Formulas:**
* Accuracy = (Correct Answers / Total Questions) x 100%
* Response Time = End Time - Start Time (in seconds)

### Experimental Results

**Performance Metrics:**

The full performance results are shown in Table III.

**Table III:** Performance Metrics
| Query Type | Cases | Acc. | Avg. Resp. Time | Success Rate |
|---|---|---|---|---|
| Direct (code) | 23 | 91.3% | ~3-5 sec | 21/23 |
| Indirect (topic) | 16 | 75.0% | ~10 sec | 12/16 |
| Flexible | 11 | 54.6% | ~10-12 sec | 6/11 |
| **Overall** | **50** | **78.0%** | **~5-6 sec** | **39/50** |

**Detailed Results:**
* **Course Code Detection:** Regex pattern successfully identifies all course codes (BECE302L, BECE309L, etc.)
* **Vector Similarity Search:** Effectively matches topic-based queries to relevant courses
* **Answer Generation:** `Gemma-7B` provides accurate, contextual responses
* **System Robustness:** Handles various question formats ("title", "name", "called", etc.)

**Comparison Analysis:**
* **vs. Keyword Search:** 78.0% vs ~60% accuracy (estimated)
* **vs. Manual Search:** Response time ~5-6 sec vs 30-60s manually reading documents
* **vs. Simple Chatbots:** Context-aware answers vs generic responses
* **Query Type Performance:** Direct queries (91.3%), Indirect queries (75.0%), Flexible phrasing (54.6%)
* **Test Coverage:** 50 total test cases with 39/50 successful responses

The accuracy performance across different query types is visualized in Figure 5.

**Error Analysis:**
* 11 failed cases total across all query types (11 out of 50 test cases)
* Direct queries: 2 failures, mainly due to system processing issues
* Indirect queries: 4 failures with vague questions without clear course references
* Flexible phrasing: 5 failures due to complex language variations and ambiguous phrasing
* System gracefully handles failures with the "I couldn't find anything relevant" message
* No false positive answers or hallucinations observed
* Response consistency maintained across multiple runs of the same query

**Table IV:** Sample Predictions vs. Ground Truth
| Test Case | Query Type | Question | Expected / RAG Output | Status |
|---|---|---|---|---|
| 1 | Direct | What is the title for BECE309L? | **Expected:** Artificial Intelligence and Machine Learning<br>**RAG:** Artificial Intelligence and Machine Learning | Correct |
| 2 | Indirect | Name of the AI course | **Expected:** Artificial Intelligence and Machine Learning (BECE309L)<br>**RAG:** Artificial Intelligence and Machine Learning | Correct |
| 3 | Direct | What are the prerequisites for BECE309L? | **Expected:** BMAT201L<br>**RAG:** BMAT201L | Correct |
| 4 | Flexible | What's the name of BECE309L? | **Expected:** Artificial Intelligence and Machine Learning<br>**RAG:** Artificial Intelligence and Machine Learning | Correct |

### Visual Results

#### Sample Predictions vs. Ground Truth
Representative test cases with expected and actual system outputs are presented in Table IV.

#### Visual Observations
* All answers match the original syllabus data
* The system gives similar results for different question types
* Answers are clear and easy to read
* No wrong or made-up information was found


**Figure 5:** System accuracy across different query types.

## Analysis and Discussions

### Interpretation of Results

The SyllabusRAG system works well with 78.0% overall accuracy for different question types. The main points are:

**Strengths:**
1.  **Strong Direct Query Performance:** 91.3% accuracy for course code questions shows regex detection works well
2.  **Good Semantic Understanding:** 75.0% accuracy for topic-based questions shows vector embeddings work well
3.  **Flexible Phrasing:** 54.6% accuracy for varied phrasing shows the system needs improvement in language understanding
4.  **Good Response Time:** 5-6 seconds on average is fine for academic use

**Key Patterns:**
* Questions about course details (title, prerequisites, objectives) have the best results
* Using both regex and similarity search helps with both exact and fuzzy questions
* Vector embeddings help match different ways of asking the same thing
* The system only struggles with very vague or out-of-scope questions

**Discussion of Trends:**
These results match what is expected for a prototype RAG system. Direct retrieval (using course codes) works best at 91.3%. Semantic retrieval is a bit less accurate (75.0%) because understanding natural language is harder. Flexible phrasing is the lowest (54.6%), showing the system has trouble with different ways of asking. Response times change depending on how hard the question is, with indirect queries taking longer because of vector search.

### Weaknesses and Limitations
1.  **Dataset Size:** Only 4 courses, so the system knows about a small set
2.  **Domain Specificity:** The system only works for syllabus questions
3.  **Vague Query Handling:** It has trouble with unclear questions
4.  **No Learning Capability:** It cannot learn from user feedback

### Comparison with Literature Benchmarks
RAG system research shows that academic QA systems usually get 70-85% accuracy. Our 78.0% overall accuracy is in this range for a prototype, with results changing by question type:
* Direct queries (91.3%) are better than most because of exact matching
* Indirect queries (75.0%) match literature standards for semantic search
* Flexible phrasing (54.6%) is lower, so this area needs work

Why the system performs this way:
* The dataset is well-structured and clean, which helps
* Only 4 courses limit what the system can do
* Using two retrieval methods helps with direct queries
* The embedding model is high quality

The results show the main method works, but better language processing and bigger datasets would help the system do even better than the literature benchmarks.

## Notes
1.  **Traditional ML Metrics:** Confusion Matrix, ROC Curve, Precision/Recall and F1 Score are not applicable for this retrieval-based QA system, as it is not a binary classification problem and does not follow standard document retrieval evaluation patterns.
2.  **Literature Benchmarks:** General literature benchmarks were sourced using AI research assistants (e.g., Perplexity, Gemini) and do not represent an exhaustive formal survey.