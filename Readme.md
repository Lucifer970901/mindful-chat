# RAG Chatbot with LangChain, Ollama, Pinecone, FastAPI, and React

This project implements a conversational AI chatbot with Retrieval-Augmented Generation (RAG), allowing it to answer questions based on a custom knowledge base. It uses a modern tech stack including LangChain for orchestration, Ollama for the Large Language Model (LLM) and embeddings, Pinecone for vector storage, FastAPI for the backend API, and React for the frontend user interface.

## Features

- **Conversational AI**: Engage in natural language conversations.
- **Retrieval-Augmented Generation (RAG)**: Utilize a custom knowledge base (stored in Pinecone) to provide informed responses.
- **Authentication**: Secure user registration and login using JWT tokens.
- **Chat History**: Store and retrieve conversation history per user.
- **Scalable Architecture**: Modular design with separate backend and frontend components.
- **Vector Database Integration**: Efficiently store and search vector embeddings of your knowledge base.
- **Local LLM Support**: Use Ollama to run open-source LLMs locally.

## Architecture

+-------------------+
| Frontend (React)  |
+-------------------+
          |
          v
+-------------------+
|  Backend (FastAPI)|
+-------------------+
     |          |
     v          v
+-----------+  +------------+
| Auth      |  | Chat       |
| Module    |  | Module     |
+-----------+  +------------+
     |              |
     v              v
+----------------+  +------------------------+
| PostgreSQL     |  | PostgreSQL             |
| authUsers DB   |  | chatHistory DB         |
+----------------+  +------------------------+
                        |
                        v
                 +--------------+
                 |  LangChain   |
                 +--------------+
                      |
        +-------------+-------------+
        |                           |
        v                           v
+-------------------+     +-----------------------+
| Ollama (LLM +     |     | Pinecone Vector DB    |
|  Embeddings)      |     +-----------------------+
+-------------------+
        |
        v
+------------------------+
| Local Model Files      |
| (e.g., mistral)        |
+------------------------+

## Explanation of Workflow

1. The **Frontend** sends requests to the **Backend API**.
2. The **Backend** routes requests to:
   - The **Auth Module** for registration/login.
   - The **Chat Module** for chat interactions.
3. The **Auth Module**:
   - Manages user credentials.
   - Interacts with the `authUsers` PostgreSQL database.
4. The **Chat Module**:
   - Manages conversations.
   - Interacts with the `chatHistory` PostgreSQL database.
   - Orchestrates the **RAG Pipeline** via **LangChain**.
5. **LangChain** handles:
   - Embedding the user's query with `OllamaEmbeddings(model="nomic-embed-text")`.
   - Searching for relevant documents in **Pinecone**.
   - Fetching and compiling document chunks.
   - Constructing a prompt combining the query, context, and history.
   - Sending the prompt to the **LLM** via **Ollama** and formatting the response.
6. **Ollama**:
   - Runs locally and loads models (e.g., `mistral`).
   - Powers both embeddings and language generation.
7. **Pinecone**:
   - Serves as the vector database.
   - Stores and retrieves embeddings of document chunks efficiently.

## Setup and Installation

### Prerequisites

- Python 3.8+
- Node.js and npm or yarn
- Docker (optional, for PostgreSQL and Ollama)
- PostgreSQL server
- Ollama with `mistral` model (`ollama pull mistral`)
- Pinecone account and API key

### 1. Clone the Repository

```bash
git clone <repository_url>
cd <repository_name>
```

### 2. Backend Setup

```bash
cd backend
python -m venv venv
source venv/bin/activate  # or .\venv\Scripts\activate on Windows
pip install -r requirements.txt
```

Create a `.env` file:

```
DB_USER=your_postgres_user
DB_PASSWORD=your_postgres_password
DB_HOST=localhost
DB_PORT=5432
PINECONE_API_KEY=your_pinecone_api_key
PINECONE_INDEX_NAME=your_pinecone_index_name
SECRET_KEY=a_strong_random_secret_key
```

Initialize the database:

```bash
python database.py
```

### 3. Frontend Setup

```bash
cd ../frontend
npm install
```

Ensure backend URL in frontend config matches: `http://localhost:8000/api`.

### 4. Ollama Setup

```bash
ollama pull mistral
```

Ensure Ollama is accessible at `http://localhost:11434`.

### 5. Pinecone Setup

Ensure the index is created with `768` dimensions for `nomic-embed-text`.

## Knowledge Base Ingestion Example

Upload documents to Pinecone using:

```python
PineconeVectorStore.from_documents(docs, embedding=embeddings, index_name="your_index")
```

> This ingestion process is data-dependent and should be customized based on your document types and domain.

## Technologies Used

| Component        | Technology                          |
|------------------|--------------------------------------|
| **LLM**          | Ollama with Mistral model           |
| **Embeddings**   | `nomic-embed-text` via Ollama       |
| **Orchestration**| LangChain                           |
| **Vector DB**    | Pinecone                            |
| **API Backend**  | FastAPI                             |
| **Frontend**     | React                               |
| **Auth & History**| PostgreSQL (via psycopg2/sqlalchemy) |

## Troubleshooting

### Ollama not responding?

- Make sure the Ollama server is running:

```bash
ollama run mistral
# or
ollama serve
```

- Verify it's accessible at: [http://localhost:11434](http://localhost:11434)

### Vector index mismatch?

- Ensure the Pinecone index is configured to use `768` dimensions (required by `nomic-embed-text`).

### Chat errors on the frontend?

- Confirm the backend URL is correctly set in your React app:

```env
VITE_API_BASE_URL=http://localhost:8000/api
```

## Future Improvements

- [ ] Add role-based access control
- [ ] Add a UI for uploading and managing documents
- [ ] Support additional LLMs through Ollama or LangChain
- [ ] Implement persistent conversation threads with search
- [ ] Enable one-command deployment with Docker Compose

## License

[Your License Name Here]

---

Made with ❤️ using open-source technologies.
