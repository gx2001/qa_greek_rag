# Greek QA RAG System - README

## Overview

This is a Retrieval-Augmented Generation (RAG) system designed specifically for Greek museum artifacts. It combines document retrieval with language generation to answer questions about museum collections in both Greek and English.

## System Architecture

### STEP 1: Data Loading and Preprocessing

The system loads museum artifact data from a JSON file and performs comprehensive text cleaning:

- **Multilingual Field Extraction**: Extracts text from Greek/English fields, prioritizing Greek content
- **HTML Cleaning**: Removes HTML tags, decodes entities, and normalizes whitespace
- **Content Validation**: Filters out artifacts with insufficient content
- **Structured Output**: Creates clean artifact objects with title, creator, description, room, etc.


### STEP 2: Document Creation and Chunking

Converts artifacts into searchable document chunks optimized for Greek text:

- **Text Splitter Configuration**: 
  - Chunk size: 800 characters (larger for better context)
  - Overlap: 100 characters (preserves context between chunks)
  - Greek-aware separators: Handles Greek punctuation and sentence structures
- **Structured Content**: Combines all artifact fields into coherent text blocks
- **Metadata Preservation**: Maintains artifact ID, title, creator, room information
- **Quality Filtering**: Removes chunks shorter than 50 characters

**Technical Details:**
- Uses `RecursiveCharacterTextSplitter` with custom separators
- Preserves separators to maintain text flow
- Creates LangChain `Document` objects with rich metadata

### STEP 3: Embedding Setup

Configures multilingual embeddings optimized for Greek text:

- **Model**: `sentence-transformers/paraphrase-multilingual-mpnet-base-v2`
- **Optimizations**: 
  - CPU-based processing for stability
  - Normalized embeddings for better similarity computation
  - Multilingual support with strong Greek performance

**Why This Model:**
- Superior performance on Greek text compared to basic multilingual models
- Better semantic understanding for museum/art domain
- Efficient CPU processing without GPU requirements

### STEP 4: Vector Store Creation

Builds a FAISS vector database with advanced retrieval capabilities:

- **Vector Store**: FAISS with cosine similarity for semantic search
- **Retriever Configuration**:
  - Similarity score threshold: 0.3 (filters irrelevant results)
  - Top-k retrieval: 5 documents per query
  - Prevents retrieval of completely unrelated content
- **Persistence**: Saves vector store to disk for reuse

**Advanced Features:**
- Similarity thresholds prevent poor quality retrievals
- Cosine similarity for better semantic matching
- Local storage for fast system restart

### STEP 5: LLM Setup

Configures the Ollama language model for Greek responses:

- **Model**: `ilsp/meltemi-instruct` (Greek-optimized LLM)
- **Parameters**:
  - Temperature: 0.1 (focused, consistent responses)
  - Max tokens: 512 (reasonable response length)
  - Threads: 4 (optimized CPU usage)

**Greek Language Support:**
- Native Greek understanding and generation
- Cultural context awareness for museum content
- Maintains formality appropriate for educational content

### STEP 6: RAG Chain Creation

Assembles the complete question-answering pipeline:

- **Prompt Template**: Custom Greek/English bilingual prompt for museum context
- **Chain Type**: "stuff" chain that passes all retrieved documents to LLM
- **Language Detection**: Automatically responds in the same language as the question
- **Context Integration**: Seamlessly combines retrieved information with generated responses

**Prompt Features:**
- Museum curator persona for authoritative responses
- Bilingual instruction handling
- Educational and engaging response style
- Graceful handling of insufficient information

### STEP 7: Query Processing

Implements intelligent query preprocessing and answer cleaning:

**Question Preprocessing:**
- **Synonym Expansion**: Expands key terms (Ωνάσης → Ωνάσης Αριστοτέλης Onassis)
- **Domain Terms**: Museum-specific expansions (γλυπτό → γλυπτό sculpture άγαλμα)
- **Better Retrieval**: Increases chance of finding relevant documents

**Answer Cleaning:**
- Removes template artifacts and unwanted prefixes
- Ensures proper capitalization and punctuation
- Handles edge cases and error responses
- Maintains professional presentation

### STEP 8: System Integration

Orchestrates the complete workflow:

- **Sequential Initialization**: Steps execute in proper dependency order
- **Error Handling**: Graceful failure with informative messages
- **Progress Tracking**: Clear feedback on system initialization progress
- **Resource Management**: Efficient memory and CPU usage

**Return Components:**
- Fully configured QA chain for immediate use
- Vector store for direct querying if needed
- Embeddings model for potential extensions

## Key Technical Improvements

### Retrieval Quality
- **Similarity Thresholds**: Filters out irrelevant documents
- **Larger Chunks**: Better context preservation (800 vs 300 characters)
- **Smart Overlap**: Maintains context between chunks
- **Greek Optimization**: Handles Greek text characteristics

### Answer Quality  
- **Template Cleaning**: Removes system prompts from responses
- **Language Consistency**: Maintains question language in response
- **Professional Tone**: Museum-appropriate formality
- **Error Gracefully**: Informative error messages

### Performance
- **CPU Optimized**: No GPU requirements
- **Efficient Chunking**: Balanced size/context tradeoff
- **Fast Retrieval**: FAISS vector similarity search
- **Memory Efficient**: Reasonable model sizes

## Usage

### Basic Usage
```python
# Initialize the complete system
qa_chain, vectorstore, embeddings = create_greek_qa_system('xxx.json')

# Ask questions
result = query_with_rag(qa_chain, "your query")
print(result['answer'])
