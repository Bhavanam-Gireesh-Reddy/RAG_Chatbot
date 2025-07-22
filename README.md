# 💬 RAG-Powered Q&A Chatbot

This project is a sophisticated Question & Answer (Q&A) chatbot that leverages Retrieval-Augmented Generation (RAG) to answer questions based on the content of a specific web page. You provide a URL, and the chatbot uses its content as a knowledge base to provide accurate, context-aware answers.

## 📝 About The Project

The chatbot is built using the LangChain framework, integrating a powerful language model from Groq with a local vector store (ChromaDB). Instead of relying solely on its pre-trained knowledge, the agent can "read" the document you provide and retrieve relevant passages to formulate its answers, making it an expert on that specific document.

The key features include:

* **Dynamic Knowledge Base:** Can ingest content from any public URL.
* **Retrieval-Augmented Generation (RAG):** Finds the most relevant information within the document before generating an answer, reducing hallucinations and improving accuracy.
* **Efficient Vector Storage:** Uses ChromaDB to store document embeddings locally for fast retrieval.
* **Intelligent Agent:** Employs a LangChain agent that can intelligently decide when to use its retrieval tool versus its general knowledge.
* **Interactive Q&A:** Provides a simple and interactive command-line interface for users.

## 🚀 Getting Started

Follow these instructions to get a local copy up and running on your machine.

### Prerequisites

You need to have Python 3.7 or later installed.

### Installation

1.  **Clone the repository (or download the script):**
    ```bash
    git clone https://github.com/Bhavanam-Gireesh-Reddy/RAG_Chatbot.git
    ```

2.  **Create a virtual environment (recommended):**
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows use `venv\Scripts\activate`
    ```

3.  **Install the required libraries:**
    ```bash
    pip install langchain langchain-groq langchain-community python-dotenv chromadb sentence-transformers beautifulsoup4
    ```

4.  **Set up your environment variables:**
    You will need an API key from [Groq](https://console.groq.com/keys) to use their fast language models.
    * Create a file named `.env` in the same directory as your script.
    * Add your Groq API key to the `.env` file:
        ```
        GROQ_API_KEY="YOUR_API_KEY_HERE"
        ```

## ⚙️ How It Works

The system follows a complete Retrieval-Augmented Generation (RAG) pipeline from document ingestion to interactive Q&A.

1.  **Document Ingestion:** The user provides a URL. The `WebBaseLoader` fetches the HTML content from this URL.

2.  **Chunking:** The raw text is too long to fit into a model's context window. The `RecursiveCharacterTextSplitter` breaks the document down into smaller, overlapping text chunks. This ensures that semantic context is not lost at the boundaries of chunks.

3.  **Embedding:** Each text chunk is converted into a numerical representation (a vector embedding) using the `HuggingFaceEmbeddings` model (`all-MiniLM-L6-v2`). These embeddings capture the semantic meaning of the text.

4.  **Vector Storage:** The generated embeddings and their corresponding text chunks are stored in a `Chroma` vector database. This database is highly efficient at searching for vectors that are semantically similar to a query vector.

5.  **Agent and Retriever Tool Creation:**
    * A **Retriever** is created from the Chroma vector store. Its job is to search the vector store for chunks of text that are most relevant to a user's query.
    * This retriever is then wrapped into a **Tool** (`create_retriever_tool`). This makes the retrieval capability available to a LangChain agent.
    * An **Agent** (`create_tool_calling_agent`) is created. This agent is given the retriever tool and is prompted to be a helpful assistant. It can decide whether to answer a question from its own knowledge or use the `document_search` tool if the question seems to be about the provided document.

6.  **Interactive Q&A:**
    * The user asks a question.
    * The agent receives the question. It analyzes the query and decides if it should use the `document_search` tool.
    * If it uses the tool, the user's question is converted into an embedding, and the retriever finds the most similar text chunks from the vector store.
    * These relevant chunks are passed back to the language model along with the original question.
    * The LLM then generates a final, comprehensive answer based on the retrieved context.

## ▶️ Usage

Once the setup is complete, you can run the script and start asking questions.

1.  Make sure your virtual environment is activated.
2.  Run the Python script from your terminal:
    ```bash
    python RAG_Chatbot.py
    ```
3.  The script will first ask you for a URL to use as the knowledge base.
4.  After processing the document, you can start your Q&A session.

**Example Interaction:**

```
Groq API Key loaded successfully from .env file.
Groq LLM (Llama3-8b) initialized.
Please enter the URL of the document you want to query: [https://lilianweng.github.io/posts/2023-06-23-agent/](https://lilianweng.github.io/posts/2023-06-23-agent/)

Loaded 1 document(s) from the web.
Document split into 213 chunks.
Initializing embedding model and vector store (ChromaDB)...
Vector store created successfully.
Creating RAG tool and agent...
Agent with RAG tool is ready.

--- RAG Q&A System is Running ---
You can now ask questions about the content of the provided web page.
Type 'exit' to quit.

Your Question: What are the three key components of an LLM-powered agent?

> Entering new AgentExecutor chain...
Invoking: `document_search` with `{'query': 'key components of an LLM-powered agent'}`
[...retrieval happens here...]
> Finished chain.

Answer: The key components of an LLM-powered autonomous agent system are: the LLM as the core controller, a memory module for short-term and long-term information, and tools that the agent can use to interact with the environment.

Your Question: exit
Exiting the system. Goodbye!
```
To stop the agent, simply type `exit` and press Enter
